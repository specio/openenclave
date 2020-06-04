# Copyright (c) Open Enclave SDK contributors.
# Licensed under the MIT License.

add_subdirectory(host)

if (BUILD_ENCLAVES)
	add_subdirectory(enc)
endif()

add_enclave_test(tests/memory memory_host memory_enc)
set_enclave_tests_properties(tests/memory PROPERTIES SKIP_RETURN_CODE 2)
