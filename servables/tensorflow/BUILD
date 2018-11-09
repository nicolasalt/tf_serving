# Description: TensorFlow servables.

package(
    default_visibility = [
        "//tensorflow_serving:internal",
    ],
    features = ["-layering_check"],
)

licenses(["notice"])  # Apache 2.0

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

load("//tensorflow_serving:serving.bzl", "serving_proto_library")

serving_proto_library(
    name = "session_bundle_config_proto",
    srcs = ["session_bundle_config.proto"],
    cc_api_version = 2,
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@protobuf_archive//:cc_wkt_protos",
    ],
)

cc_library(
    name = "bundle_factory_util",
    srcs = ["bundle_factory_util.cc"],
    hdrs = ["bundle_factory_util.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":serving_session",
        ":session_bundle_config_proto",
        "//tensorflow_serving/batching:batching_session",
        "//tensorflow_serving/resources:resource_values",
        "//tensorflow_serving/resources:resources_proto",
        "//tensorflow_serving/util:file_probing_env",
        "@org_tensorflow//tensorflow/contrib/batching:batch_scheduler",
        "@org_tensorflow//tensorflow/contrib/batching:shared_batch_scheduler",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@protobuf_archive//:cc_wkt_protos",
    ],
)

cc_library(
    name = "session_bundle_factory",
    srcs = ["session_bundle_factory.cc"],
    hdrs = ["session_bundle_factory.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":bundle_factory_util",
        ":session_bundle_config_proto",
        "//tensorflow_serving/batching:batching_session",
        "//tensorflow_serving/resources:resources_proto",
        "@org_tensorflow//tensorflow/contrib/batching:shared_batch_scheduler",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/contrib/session_bundle:bundle_shim",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "saved_model_bundle_factory",
    srcs = ["saved_model_bundle_factory.cc"],
    hdrs = ["saved_model_bundle_factory.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":bundle_factory_util",
        ":curried_session",
        ":session_bundle_config_proto",
        "//tensorflow_serving/batching:batching_session",
        "//tensorflow_serving/resources:resources_proto",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/cc/saved_model:tag_constants",
        "@org_tensorflow//tensorflow/contrib/batching:shared_batch_scheduler",
        "@org_tensorflow//tensorflow/contrib/session_bundle:bundle_shim",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "session_bundle_source_adapter",
    srcs = ["session_bundle_source_adapter.cc"],
    hdrs = ["session_bundle_source_adapter.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":session_bundle_factory",
        ":session_bundle_source_adapter_proto",
        "//tensorflow_serving/core:loader",
        "//tensorflow_serving/core:simple_loader",
        "//tensorflow_serving/core:source_adapter",
        "//tensorflow_serving/core:storage_path",
        "//tensorflow_serving/util:optional",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
    ],
    alwayslink = 1,
)

cc_library(
    name = "saved_model_bundle_source_adapter",
    srcs = ["saved_model_bundle_source_adapter.cc"],
    hdrs = ["saved_model_bundle_source_adapter.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":saved_model_bundle_factory",
        ":saved_model_bundle_source_adapter_proto",
        ":saved_model_warmup",
        ":session_bundle_source_adapter_proto",
        "//tensorflow_serving/core:loader",
        "//tensorflow_serving/core:simple_loader",
        "//tensorflow_serving/core:source_adapter",
        "//tensorflow_serving/core:storage_path",
        "//tensorflow_serving/resources:resource_util",
        "//tensorflow_serving/resources:resource_values",
        "//tensorflow_serving/resources:resources_proto",
        "//tensorflow_serving/util:optional",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/core:lib",
    ],
    alwayslink = 1,
)

serving_proto_library(
    name = "session_bundle_source_adapter_proto",
    srcs = ["session_bundle_source_adapter.proto"],
    cc_api_version = 2,
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":session_bundle_config_proto",
    ],
)

serving_proto_library(
    name = "saved_model_bundle_source_adapter_proto",
    srcs = ["saved_model_bundle_source_adapter.proto"],
    cc_api_version = 2,
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":session_bundle_config_proto",
    ],
)

cc_library(
    name = "simple_servers",
    srcs = ["simple_servers.cc"],
    hdrs = ["simple_servers.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":saved_model_bundle_source_adapter",
        ":session_bundle_source_adapter_proto",
        "//tensorflow_serving/core:aspired_versions_manager_builder",
        "//tensorflow_serving/core:availability_preserving_policy",
        "//tensorflow_serving/core:loader",
        "//tensorflow_serving/core:manager",
        "//tensorflow_serving/core:source",
        "//tensorflow_serving/core:source_adapter",
        "//tensorflow_serving/core:storage_path",
        "//tensorflow_serving/core:target",
        "//tensorflow_serving/sources/storage_path:file_system_storage_path_source",
        "//tensorflow_serving/sources/storage_path:file_system_storage_path_source_proto",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "serving_session",
    srcs = ["serving_session.cc"],
    hdrs = ["serving_session.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@org_tensorflow//tensorflow/core:tensorflow",
    ],
)

cc_library(
    name = "curried_session",
    srcs = ["curried_session.cc"],
    hdrs = ["curried_session.h"],
    deps = [
        ":serving_session",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "predict_impl",
    srcs = ["predict_impl.cc"],
    hdrs = ["predict_impl.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":predict_util",
        "//tensorflow_serving/apis:predict_proto",
        "//tensorflow_serving/core:servable_handle",
        "//tensorflow_serving/model_servers:server_core",
        "//tensorflow_serving/servables/tensorflow:util",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "predict_util",
    srcs = ["predict_util.cc"],
    hdrs = ["predict_util.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "//tensorflow_serving/apis:predict_proto",
        "//tensorflow_serving/servables/tensorflow:util",
        "//tensorflow_serving/util:optional",
        "@com_google_absl//absl/strings",
        "@org_tensorflow//tensorflow/cc/saved_model:signature_constants",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/contrib/session_bundle:signature",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "get_model_metadata_impl",
    srcs = ["get_model_metadata_impl.cc"],
    hdrs = ["get_model_metadata_impl.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "//tensorflow_serving/apis:get_model_metadata_proto",
        "//tensorflow_serving/core:servable_handle",
        "//tensorflow_serving/model_servers:server_core",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/contrib/session_bundle:bundle_shim",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "classifier",
    srcs = ["classifier.cc"],
    hdrs = ["classifier.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":util",
        "//tensorflow_serving/apis:classification_proto",
        "//tensorflow_serving/apis:classifier",
        "//tensorflow_serving/apis:input_proto",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/util:optional",
        "@org_tensorflow//tensorflow/cc/saved_model:loader_lite",
        "@org_tensorflow//tensorflow/cc/saved_model:signature_constants",
        "@org_tensorflow//tensorflow/contrib/session_bundle:session_bundle_lite",
        "@org_tensorflow//tensorflow/contrib/session_bundle:signature_lite",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "classification_service",
    srcs = ["classification_service.cc"],
    hdrs = ["classification_service.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":classifier",
        "//tensorflow_serving/apis:classification_proto",
        "//tensorflow_serving/apis:classifier",
        "//tensorflow_serving/core:servable_handle",
        "//tensorflow_serving/model_servers:server_core",
        "//tensorflow_serving/servables/tensorflow:util",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/contrib/session_bundle:signature",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "regression_service",
    srcs = ["regression_service.cc"],
    hdrs = ["regression_service.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":regressor",
        "//tensorflow_serving/apis:regression_proto",
        "//tensorflow_serving/apis:regressor",
        "//tensorflow_serving/core:servable_handle",
        "//tensorflow_serving/model_servers:server_core",
        "//tensorflow_serving/servables/tensorflow:util",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/contrib/session_bundle:signature",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "regressor",
    srcs = ["regressor.cc"],
    hdrs = ["regressor.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":util",
        "//tensorflow_serving/apis:input_proto",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/apis:regression_proto",
        "//tensorflow_serving/apis:regressor",
        "//tensorflow_serving/util:optional",
        "@org_tensorflow//tensorflow/cc/saved_model:loader_lite",
        "@org_tensorflow//tensorflow/cc/saved_model:signature_constants",
        "@org_tensorflow//tensorflow/contrib/session_bundle:session_bundle_lite",
        "@org_tensorflow//tensorflow/contrib/session_bundle:signature_lite",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)

cc_library(
    name = "multi_inference",
    srcs = ["multi_inference.cc"],
    hdrs = ["multi_inference.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "//tensorflow_serving/apis:inference_proto",
        "//tensorflow_serving/apis:input_proto",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/servables/tensorflow:classifier",
        "//tensorflow_serving/servables/tensorflow:regressor",
        "//tensorflow_serving/servables/tensorflow:util",
        "@org_tensorflow//tensorflow/cc/saved_model:signature_constants",
        "@org_tensorflow//tensorflow/contrib/session_bundle",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@protobuf_archive//:protobuf",
    ],
)

cc_library(
    name = "multi_inference_helper",
    srcs = ["multi_inference_helper.cc"],
    hdrs = ["multi_inference_helper.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        ":multi_inference",
        "//tensorflow_serving/apis:inference_proto",
        "//tensorflow_serving/apis:input_proto",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/model_servers:server_core",
        "@org_tensorflow//tensorflow/cc/saved_model:loader",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@protobuf_archive//:protobuf",
    ],
)

cc_library(
    name = "util",
    srcs = ["util.cc"],
    hdrs = ["util.h"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "//tensorflow_serving/apis:input_proto",
        "//tensorflow_serving/apis:model_proto",
        "//tensorflow_serving/apis/internal:serialized_input_proto",
        "//tensorflow_serving/util:optional",
        "@org_tensorflow//tensorflow/cc/saved_model:signature_constants",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@protobuf_archive//:cc_wkt_protos",
    ],
)

cc_library(
    name = "saved_model_warmup",
    srcs = ["saved_model_warmup.cc"],
    hdrs = ["saved_model_warmup.h"],
    deps = [
        ":classifier",
        ":multi_inference",
        ":predict_util",
        ":regressor",
        "//tensorflow_serving/apis:classification_proto",
        "//tensorflow_serving/apis:inference_proto",
        "//tensorflow_serving/apis:predict_proto",
        "//tensorflow_serving/apis:prediction_log_proto",
        "//tensorflow_serving/apis:regression_proto",
        "//tensorflow_serving/core:loader",
        "@org_tensorflow//tensorflow/cc/saved_model:constants",
        "@org_tensorflow//tensorflow/core:core_cpu",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
    ],
)
