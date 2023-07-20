# Background

This software is an experimental Morello port of protobuf 3.20.1.

## Build instructions

The official build instructions fori protobuf describe building with
`bazel`, However, as a port of bazel is not currently distributed in binary
form for CheriBSD protobuf can instead be built with autotools:

```
$ sh autogen.sh
$ sh configure
$ make
$ sudo make install
```

## Testing

### Unit testing

Unit testing has been performed using `make check`:

```
$ make check 
In-address space security exception (core dumped)
FAIL: protobuf-test
In-address space security exception (core dumped)
FAIL: protobuf-lazy-descriptor-test
PASS: protobuf-lite-test
PASS: google/protobuf/compiler/zip_output_unittest.sh
PASS: google/protobuf/io/gzip_stream_unittest.sh
PASS: protobuf-lite-arena-test
PASS: no-warning-test
============================================================================
Testsuite summary for Protocol Buffers 3.20.1
============================================================================
# TOTAL: 7
# PASS:  5
# SKIP:  0
# XFAIL: 0
# FAIL:  2
# XPASS: 0
# ERROR: 0
============================================================================
See src/test-suite.log
Please report to protobuf@googlegroups.com
============================================================================
```

## Notes and Limitations

The unit test `protbuf-lazy-descriptor-test` is currently crashes with a
CHERI protection fault. As many of th unit tests are passing and the its function
was suffficient to work gRPC this issue hasn't been investigated.

Adaptations to nginx to the memory-safe CheriABI have been driven by:
compiler warnings and errors, and dynamic testing. Where the compiler
emits a warning or error we are able to rigorously review this and
correct. However, some issues only manifest dynamically (at runtime),
such as invalidation of capabilities by pointer arithmetic,
non-blessed memory copies, or insufficient pointer alignment.
Enhancements such as CHERI UBsan have modestly improved the ability to
identify problems previously only found during dynamic testing. However,
 we are still greatly reliant on dynamic testing. This testing is
constrained by both the completeness of the test suites (which in some
cases provide poor coverage) and the time available within the project
to perform testing. Whilst it is know that errors remain outside the
core http module we are not able to estimate what problems might
remain beyond those resolved in the scope of the project.

## Acknowledgement

This work has been undertaken within DSTL contract
ACC6036483: CHERI-based compartmentalisation for web services on Morello.
