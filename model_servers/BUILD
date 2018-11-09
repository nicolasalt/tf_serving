# Description: Model Server

package(
    default_visibility = [
        "//tensorflow_serving:internal",
    ],
    features = ["-layering_check"],
)

licenses(["notice"])  # Apache 2.0

# Visibility group for clients that link in custom ops, while using the
# vanilla TensorFlow ModelServer without any other modifications. Exclusively
# for adding custom TensorFlow ops. Does not grant access to private headers
# or other implementation details.
package_group(name = "tensorflow_model_server_custom_op_clients")

filegroup(
    name = "all_files",
    srcs = glob(
        ["**/*"],
        exclude = [
            "**/METADATA",
            "**/OWNERS",
        ],
    ),
)

cc_library(
    name = "model_platform_types",
    hdrs = ["model_platform_types.h"],
    visibility = [
        "//visibility:public",
    ],
)

cc_library(
    name = "platform_config_util",
    srcs = ["platform_config_util.cc"],
    hdrs = ["platform_config_util.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "//tensorflow_serving/config:platform_config_proto",
        "//tensorflow_serving/model_servers:model_platform_types",
        "//tensorflow_serving/servables/tensorflow:saved_model_bundle_source_adapter_proto",
        "//tensorflow_serving/servables/tensorflow:session_bundle_config_proto",
        "//tensorflow_serving/servables/tensorflow:session_bundle_source_adapter_proto",
    ],
)

cc_library(
    name = "server_core",
    srcs = ["server_core.cc"],
    hdrs = ["server_core.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":model_platform_types",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/config:logging_config_proto",
        "//tensorflow_serving/config:model_server_config_proto",
        "//tensorflow_serving/config:platform_config_proto",
        "//tensorflow_serving/core:aspired_versions_manager",
        "//tensorflow_serving/core:dynamic_source_router",
        "//tensorflow_serving/core:load_servables_fast",
        "//tensorflow_serving/core:servable_state_monitor",
        "//tensorflow_serving/core:server_request_logger",
        "//tensorflow_serving/core:source",
        "//tensorflow_serving/core:source_adapter",
        "//tensorflow_serving/core:storage_path",
        "//tensorflow_serving/resources:resource_values",
        "//tensorflow_serving/servables/tensorflow:saved_model_bundle_source_adapter",
        "//tensorflow_serving/servables/tensorflow:session_bundle_source_adapter",
        "//tensorflow_serving/servables/tensorflow:session_bundle_source_adapter_proto",
        "//tensorflow_serving/sources/storage_path:file_system_storage_path_source",
        "//tensorflow_serving/sources/storage_path:file_system_storage_path_source_proto",
        "//tensorflow_serving/util:event_bus",
        "//tensorflow_serving/util:optional",
        "//tensorflow_serving/util:unique_ptr_with_deps",
        "@org_tensorflow//tensorflow/core:lib",
        "@protobuf_archive//:cc_wkt_protos",
    ],
)

cc_library(
    name = "get_model_status_impl",
    srcs = ["get_model_status_impl.cc"],
    hdrs = ["get_model_status_impl.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":server_core",
        "//tensorflow_serving/apis:get_model_status_proto",
        "//tensorflow_serving/core:servable_state",
        "//tensorflow_serving/core:servable_state_monitor",
        "//tensorflow_serving/util:status_proto",
        "//tensorflow_serving/util:status_util",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "model_service_impl",
    srcs = ["model_service_impl.cc"],
    hdrs = ["model_service_impl.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":get_model_status_impl",
        ":grpc_status_util",
        ":server_core",
        "//tensorflow_serving/apis:model_management_proto",
        "//tensorflow_serving/apis:model_service_proto",
        "//tensorflow_serving/util:status_util",
        "@grpc//:grpc++",
    ],
)

cc_library(
    name = "prediction_service_impl",
    srcs = ["prediction_service_impl.cc"],
    hdrs = ["prediction_service_impl.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":grpc_status_util",
        ":server_core",
        "//tensorflow_serving/apis:prediction_service_proto",
        "//tensorflow_serving/servables/tensorflow:classification_service",
        "//tensorflow_serving/servables/tensorflow:get_model_metadata_impl",
        "//tensorflow_serving/servables/tensorflow:multi_inference_helper",
        "//tensorflow_serving/servables/tensorflow:predict_impl",
        "//tensorflow_serving/servables/tensorflow:regression_service",
        "//tensorflow_serving/util:status_util",
        "@grpc//:grpc++",
    ],
)

cc_library(
    name = "grpc_status_util",
    srcs = ["grpc_status_util.cc"],
    hdrs = ["grpc_status_util.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "@grpc//:grpc++",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "http_server",
    srcs = ["http_server.cc"],
    hdrs = ["http_server.h"],
    visibility = ["//tensorflow_serving:internal"],
    deps = [
        ":http_rest_api_handler",
        ":server_core",
        "//tensorflow_serving/config:monitoring_config_proto",
        "//tensorflow_serving/util:prometheus_exporter",
        "//tensorflow_serving/util:threadpool_executor",
        "//tensorflow_serving/util/net_http/server/public:http_server",
        "//tensorflow_serving/util/net_http/server/public:http_server_api",
        "@com_google_absl//absl/strings",
        "@com_googlesource_code_re2//:re2",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "http_rest_api_handler",
    srcs = ["http_rest_api_handler.cc"],
    hdrs = ["http_rest_api_handler.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":get_model_status_impl",
        ":server_core",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/apis:predict_proto",
        "//tensorflow_serving/core:servable_handle",
        "//tensorflow_serving/servables/tensorflow:classification_service",
        "//tensorflow_serving/servables/tensorflow:predict_impl",
        "//tensorflow_serving/servables/tensorflow:regression_service",
        "//tensorflow_serving/util:json_tensor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/time",
        "@com_google_absl//absl/types:optional",
        "@com_googlesource_code_re2//:re2",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/cc/saved_model:signature_constants",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

SUPPORTED_TENSORFLOW_OPS = [
    "@org_tensorflow//tensorflow/contrib:contrib_kernels",
    "@org_tensorflow//tensorflow/contrib:contrib_ops_op_lib",
]

TENSORFLOW_DEPS = [
    "@org_tensorflow//tensorflow/core:tensorflow",
    "//tensorflow_serving/servables/tensorflow:classification_service",
    "//tensorflow_serving/servables/tensorflow:get_model_metadata_impl",
    "//tensorflow_serving/servables/tensorflow:multi_inference",
    "//tensorflow_serving/servables/tensorflow:regression_service",
    "//tensorflow_serving/servables/tensorflow:saved_model_bundle_source_adapter",
    "//tensorflow_serving/servables/tensorflow:session_bundle_source_adapter",
    "//tensorflow_serving/servables/tensorflow:predict_impl",
]

cc_library(
    name = "server_lib",
    srcs = [
        "server.cc",
    ],
    hdrs = ["server.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":http_server",
        ":model_platform_types",
        ":platform_config_util",
        ":prediction_service_impl",
        ":server_core",
        ":grpc_status_util",
        ":model_service_impl",
        "@protobuf_archive//:cc_wkt_protos",
        "@grpc//:grpc++",
        "@org_tensorflow//tensorflow/c:c_api",
        "@org_tensorflow//tensorflow/cc/saved_model:tag_constants",
        "@org_tensorflow//tensorflow/core:lib",
        "@com_google_absl//absl/memory",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "//tensorflow_serving/config:model_server_config_proto",
        "//tensorflow_serving/config:monitoring_config_proto",
        "//tensorflow_serving/config:ssl_config_proto",
        "//tensorflow_serving/core:availability_preserving_policy",
        "//tensorflow_serving/servables/tensorflow:session_bundle_config_proto",
    ] + TENSORFLOW_DEPS + SUPPORTED_TENSORFLOW_OPS,
)

cc_library(
    name = "tensorflow_model_server_main_lib",
    srcs = [
        "main.cc",
    ],
    hdrs = ["version.h"],
    visibility = [
        ":tensorflow_model_server_custom_op_clients",
        "//tensorflow_serving:internal",
    ],
    deps = [
        ":server_lib",
        "@org_tensorflow//tensorflow/c:c_api",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core/platform/cloud:gcs_file_system",
        "@org_tensorflow//tensorflow/core/platform/hadoop:hadoop_file_system",
        "@org_tensorflow//tensorflow/core/platform/s3:s3_file_system",
    ],
)

py_binary(
    name = "tensorflow_model_server_test_client",
    srcs = ["tensorflow_model_server_test_client.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//tensorflow_serving/apis:prediction_service_proto_py_pb2",
        "@org_tensorflow//tensorflow:tensorflow_py",
    ],
)
