load(":rules.bzl", "polygerrit_bundle")

package(default_visibility = ["//visibility:public"])

polygerrit_bundle(
    name = "polygerrit_ui",
    srcs = glob(
        [
            "**/*.js",
        ],
        exclude = [
            "node_modules/**",
            "node_modules_licenses/**",
            "test/**",
            "**/*_test.html",
            "**/*_test.js",
        ],
    ),
    outs = ["polygerrit_ui.zip"],
    entry_point = "elements/gr-app.html",
)

filegroup(
    name = "pg_code",
    srcs = glob(
        [
            "**/*.html",
            "**/*.js",
        ],
        exclude = [
            "node_modules/**",
            "node_modules_licenses/**",
        ],
    ),
)

filegroup(
    name = "pg_code_without_test",
    srcs = glob(
        [
            "**/*.html",
            "**/*.js",
        ],
        exclude = [
            "node_modules/**",
            "node_modules_licenses/**",
            "**/*_test.html",
            "test/**",
            "samples/**",
            "**/*_test.js",
        ],
    ),
)

# Workaround for https://github.com/bazelbuild/bazel/issues/1305
filegroup(
    name = "test-srcs-fg",
    srcs = [
        "test/common-test-setup.js",
        "test/index.html",
        ":pg_code",
        "@ui_dev_npm//:node_modules",
        "@ui_npm//:node_modules",
    ],
)

sh_test(
    name = "wct_test",
    size = "enormous",
    srcs = ["wct_test.sh"],
    args = [
        "$(location @ui_dev_npm//web-component-tester/bin:wct)",
    ],
    data = [
        ":test-srcs-fg",
        "@ui_dev_npm//web-component-tester/bin:wct",
    ],
    # Should not run sandboxed.
    tags = [
        "local",
        "manual",
    ],
)

# TODO(taoalpha): alias to `npm run eslint` or just remove once CI moved to npm
sh_test(
    name = "lint_test",
    size = "large",
    srcs = ["lint_test.sh"],
    data = [
        ".eslintrc.json",
        ":pg_code",
    ],
    # Should not run sandboxed.
    tags = [
        "local",
        "manual",
    ],
)

# Workaround for https://github.com/bazelbuild/bazel/issues/1305
filegroup(
    name = "polylint-fg",
    srcs = [
        ":pg_code_without_test",
        "@ui_npm//:node_modules",
    ],
)

sh_test(
    name = "polylint_test",
    size = "large",
    srcs = ["polylint_test.sh"],
    args = [
        "$(location @tools_npm//polymer-cli/bin:polymer)",
        "$(location polymer.json)",
    ],
    data = [
        "polymer.json",
        ":polylint-fg",
        "@tools_npm//polymer-cli/bin:polymer",
    ],
    # Should not run sandboxed.
    tags = [
        "local",
        "manual",
    ],
)

DIRECTORIES = [
    "admin",
    "change",
    "change-list",
    "core",
    "diff",
    "edit",
    "plugins",
    "settings",
    "shared",
    "gr-app",
]

[sh_test(
    name = "template_test_" + directory,
    size = "enormous",
    srcs = ["template_test.sh"],
    args = [directory],
    data = [
        ":pg_code",
        ":template_test_srcs",
    ],
    tags = [
        # Should not run sandboxed.
        "local",
        "template",
    ],
) for directory in DIRECTORIES]

filegroup(
    name = "template_test_srcs",
    srcs = [
        "template_test_srcs/convert_for_template_tests.py",
        "template_test_srcs/template_test.js",
    ],
)
