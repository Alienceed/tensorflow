licenses(["notice"])  # Apache 2.0

package(default_visibility = ["//tensorflow:internal"])

load("//tensorflow/core:platform/default/build_config.bzl", "pyx_library")
load("//tensorflow/compiler/xla:xla.bzl", "xla_python_default_plugins")
load("//tensorflow:tensorflow.bzl", "tf_pybind_extension")
load("//tensorflow:tensorflow.bzl", "tf_cc_test")

py_library(
    name = "xla_client",
    srcs = [
        "xla_client.py",
        "xrt.py",
    ],
    srcs_version = "PY2AND3",
    visibility = ["//visibility:public"],
    deps = [":xla_extension"],
)

pyx_library(
    name = "custom_call_for_test",
    testonly = True,
    srcs = ["custom_call_for_test.pyx"],
)

py_test(
    name = "xla_client_test",
    srcs = ["xla_client_test.py"],
    main = "xla_client_test.py",
    python_version = "PY2",
    srcs_version = "PY2AND3",
    tags = ["no_oss"],
    deps = [
        ":custom_call_for_test",
        ":xla_client",
        "//tensorflow/compiler/xla:xla_data_proto_py",
        "//tensorflow/python:platform_test",
    ],
)

cc_library(
    name = "worker_thread",
    srcs = ["worker_thread.cc"],
    hdrs = ["worker_thread.h"],
    deps = [
        "//tensorflow/core:lib",
        "@com_google_absl//absl/synchronization",
    ],
)

cc_library(
    name = "types",
    srcs = ["types.cc"],
    hdrs = ["types.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
        "-Wno-c++98-c++11-compat",
    ],
    features = ["-use_header_modules"],
    deps = [
        "//tensorflow/compiler/xla:literal",
        "//tensorflow/compiler/xla:shape_util",
        "//tensorflow/compiler/xla:status",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/compiler/xla:statusor",
        "//tensorflow/compiler/xla:types",
        "//tensorflow/compiler/xla:xla_data_proto",
        "//tensorflow/core:lib",
        "//tensorflow/stream_executor:device_memory_allocator",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/types:optional",
        "@pybind11",
    ],
)

cc_library(
    name = "xrt",
    srcs = ["xrt.cc"],
    hdrs = ["xrt.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
        "-Wno-c++98-c++11-compat",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":types",
        "//tensorflow/compiler/xla:status",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/compiler/xla:statusor",
        "//tensorflow/compiler/xla:util",
        "//tensorflow/compiler/xla:xla_data_proto",
        "//tensorflow/compiler/xla/service:computation_placer",
        "//tensorflow/compiler/xrt/client:xrt_client",
        "//tensorflow/compiler/xrt/client:xrt_grpc_eager_client",
        "//tensorflow/core:lib",
        "//tensorflow/core/distributed_runtime/rpc:grpc_channel",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/types:optional",
        "@pybind11",
    ],
)

cc_library(
    name = "shared_device_buffer",
    srcs = ["shared_device_buffer.cc"],
    hdrs = ["shared_device_buffer.h"],
    deps = [
        "//tensorflow/compiler/xla:shape_util",
        "//tensorflow/compiler/xla/service:shaped_buffer",
        "//tensorflow/compiler/xla/service:transfer_manager",
        "//tensorflow/stream_executor:device_memory_allocator",
        "@com_google_absl//absl/container:flat_hash_set",
    ],
)

tf_cc_test(
    name = "shared_device_buffer_test",
    srcs = ["shared_device_buffer_test.cc"],
    deps = [
        ":shared_device_buffer",
        "//tensorflow/compiler/xla:literal_util",
        "//tensorflow/compiler/xla:shape_util",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/compiler/xla:test",
        "//tensorflow/compiler/xla/client:client_library",
        "//tensorflow/compiler/xla/service:cpu_plugin",
        "//tensorflow/core:test_main",
    ],
)

cc_library(
    name = "local_client",
    srcs = ["local_client.cc"],
    hdrs = ["local_client.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
        "-Wno-c++98-c++11-compat",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":shared_device_buffer",
        ":types",
        ":worker_thread",
        "//tensorflow/compiler/xla:executable_run_options",
        "//tensorflow/compiler/xla:literal",
        "//tensorflow/compiler/xla:literal_util",
        "//tensorflow/compiler/xla:shape_util",
        "//tensorflow/compiler/xla:status",
        "//tensorflow/compiler/xla:statusor",
        "//tensorflow/compiler/xla:util",
        "//tensorflow/compiler/xla:xla_data_proto",
        "//tensorflow/compiler/xla/client:client_library",
        "//tensorflow/compiler/xla/client:executable_build_options",
        "//tensorflow/compiler/xla/client:local_client",
        "//tensorflow/compiler/xla/client:xla_computation",
        "//tensorflow/compiler/xla/service:computation_placer",
        "//tensorflow/compiler/xla/service:custom_call_target_registry",
        "//tensorflow/compiler/xla/service:platform_util",
        "//tensorflow/compiler/xla/service:shaped_buffer",
        "//tensorflow/core:lib",
        "//tensorflow/core/profiler/lib:traceme",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/time",
        "@com_google_absl//absl/types:span",
        "@pybind11",
    ],
)

tf_pybind_extension(
    name = "xla_extension",
    srcs = [
        "xla.cc",
    ],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
        "-Wno-c++98-c++11-compat",
    ],
    features = ["-use_header_modules"],
    module_name = "xla_extension",
    deps = [
        ":local_client",
        ":shared_device_buffer",
        ":types",
        ":worker_thread",
        ":xrt",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:optional",
        "@com_google_absl//absl/types:span",
        "@pybind11",
        "//third_party/python_runtime:headers",  # buildcleaner: keep
        "//tensorflow/compiler/xla:literal",
        "//tensorflow/compiler/xla:shape_util",
        "//tensorflow/compiler/xla:status",
        "//tensorflow/compiler/xla:statusor",
        "//tensorflow/compiler/xla:util",
        "//tensorflow/compiler/xla:xla_data_proto",
        "//tensorflow/compiler/xla/client:client_library",
        "//tensorflow/compiler/xla/client:executable_build_options",
        "//tensorflow/compiler/xla/client:local_client",
        "//tensorflow/compiler/xla/client:xla_builder",
        "//tensorflow/compiler/xla/client:xla_computation",
        "//tensorflow/compiler/xla/client/lib:comparators",
        "//tensorflow/compiler/xla/client/lib:math",
        "//tensorflow/compiler/xla/client/lib:qr",
        "//tensorflow/compiler/xla/client/lib:self_adjoint_eig",
        "//tensorflow/compiler/xla/client/lib:svd",
        "//tensorflow/compiler/xla/service:computation_placer",
        "//tensorflow/stream_executor:device_memory_allocator",
        "//tensorflow/compiler/xla/service:hlo",
        "//tensorflow/compiler/xla/service:hlo_graph_dumper",
        "//tensorflow/compiler/xla/service:name_uniquer",
        "//tensorflow/compiler/xla/service:platform_util",
        "//tensorflow/compiler/xla/service:shaped_buffer",
        "//tensorflow/compiler/xla/service:transfer_manager",
        "//tensorflow/compiler/xla/service:cpu_plugin",
        "//tensorflow/core:lib",
        # Do NOT remove this dependency. The XLA Python extension must not
        # depend on any part of TensorFlow at runtime, **including**
        # libtensorflow_framework.so. The XLA module is deployed self-contained
        # without any TF dependencies as "jaxlib" on Pypi, and "jaxlib" does
        # not require Tensorflow.
        "//tensorflow/core:lib_internal_impl",  # buildcleaner: keep
    ] + xla_python_default_plugins(),
)

# TODO(phawkins): enable this test.
# py_test(
#     name = "xrt_test",
#     srcs = ["xrt_test.py"],
#     deps = [
#         ":xla_client",
#         "//third_party/py/numpy",
#         "//tensorflow/compiler/jit:xla_cpu_device",
#         "//tensorflow/compiler/xrt:xrt_server",
#         "//tensorflow/python:client_testlib",
#     ],
# )
