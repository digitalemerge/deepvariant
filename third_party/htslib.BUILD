# Description:
# C library for high-throughput sequencing data formats
#

licenses(["notice"])  # MIT/Expat, cram uses BSD

exports_files(["LICENSE"])

package(
    default_visibility = ["//visibility:public"],
    features = [
        "-layering_check",
        "-parse_headers",
    ],
)

version = "1.9"

version_dir = "htslib_1_9"

include_htslib = "htslib/" + version_dir

# The hermetic version uses labels, the non-hermetic version uses flags.

cc_library(
    name = "htslib_deps",
    linkopts = [
        "-llzma",
        "-lbz2",
        "-lz",
        "-lm",
        "-lpthread",
        # The following libraries are not needed, because we disable the GCS,
        # http and S3 file handlers:
        # "-lcurl",
        # "-lcrypto",
        # "-lzlib",
    ],
)

extra_libs = []

includes = [
    include_htslib,
    ".",
]

htslib_srcs = [
    # This is made with a genrule.  See below.
    "config.h",
] + [
    "bcf_sr_sort.c",
    "bcf_sr_sort.h",
    "bgzf.c",
    "hfile.c",
    "hfile_internal.h",
    "hfile_net.c",
    # Disabled hfile modes:
    # "hfile_gcs.c",
    # "hfile_libcurl.c",
    # "hfile_s3.c",
    "hts.c",
    "hts_internal.h",
    "hts_os.c",
    "kfunc.c",
    "knetfile.c",
    "kstring.c",
    "md5.c",
    "multipart.c",
    "plugin.c",
    "regidx.c",
    "sam.c",
    "synced_bcf_reader.c",
    "tbx.c",
    "textutils.c",
    "textutils_internal.h",
    "thread_pool.c",
    "thread_pool_internal.h",
    "vcf.c",
    "vcf_sweep.c",
    "vcfutils.c",
    "version.h",
    "faidx.c",
    "errmod.c",
    "probaln.c",
    "realn.c",
] + [
    "cram/cram_codecs.c",
    "cram/cram_decode.c",
    "cram/cram_encode.c",
    "cram/cram_external.c",
    "cram/cram_index.c",
    "cram/cram_io.c",
    "cram/cram_samtools.c",
    "cram/cram_stats.c",
    "cram/files.c",
    "cram/mFILE.c",
    "cram/open_trace_file.c",
    "cram/pooled_alloc.c",
    "cram/rANS_static.c",
    "cram/sam_header.c",
    "cram/string_alloc.c",
    "cram/mFILE.h",
    "cram/misc.h",
    "cram/open_trace_file.h",
    "cram/os.h",
    "cram/rANS_byte.h",
    "cram/rANS_static.h",
    "cram/string_alloc.h",
    "os/lzma_stub.h",
]

# These need to be included in the right order, cf cram.h
textual_hdrs = [
    "cram/cram_samtools.h",
    "cram/sam_header.h",
    "cram/cram_structs.h",
    "cram/cram_io.h",
    "cram/cram_encode.h",
    "cram/cram_decode.h",
    "cram/cram_stats.h",
    "cram/cram_codecs.h",
    "cram/cram_index.h",
]

# These become the exported API, c.f. ../BUILD.
htslib_hdrs = glob(["htslib/*.h"])

cram_hdrs = [
    "cram/cram.h",
    "cram/pooled_alloc.h",
]

# Just the headers required for the hfile API.
filegroup(
    name = "hfile_hdrs",
    srcs = [
        "hfile_internal.h",
        "htslib/hfile.h",
        "htslib/hts_defs.h",
    ],
)

cc_library(
    name = "hfile_api",
    hdrs = [":hfile_hdrs"],
    visibility = ["//visibility:public"],
)

copts = [
    "-Wno-implicit-function-declaration",  # cram_io.c
    "-Wno-unused-variable",  # cram_encode.c
    "-Wno-error",
]

# This is the exported hdrs api.
filegroup(
    name = "htslib_hdrs",
    srcs = htslib_hdrs,
)

filegroup(
    name = "cram_hdrs",
    srcs = cram_hdrs,
)

# Genrules in lieu of ./configure.  Minimum viable linux.
# Generated by running
# ./configure --disable-libcurl --disable-gcs --disable-s3
genrule(
    name = "config_h",
    outs = ["config.h"],
    cmd = """
        exec > "$@"
        echo '#define HAVE_DRAND48 1'
        echo '#define HAVE_FDATASYNC 1'
        echo '#define HAVE_FSEEKO 1'
        echo '#define HAVE_FSYNC 1'
        echo '#define HAVE_GETPAGESIZE 1'
        echo '#define HAVE_GMTIME_R 1'
        echo '#define HAVE_INTTYPES_H 1'
        echo '#define HAVE_LIBBZ2 1'
        echo '#define HAVE_LIBLZMA 1'
        echo '#define HAVE_LIBZ 1'
        echo '#define HAVE_MEMORY_H 1'
        echo '#define HAVE_MMAP 1'
        echo '#define HAVE_STDINT_H 1'
        echo '#define HAVE_STDLIB_H 1'
        echo '#define HAVE_STRING_H 1'
        echo '#define HAVE_STRINGS_H 1'
        echo '#define HAVE_SYS_PARAM_H 1'
        echo '#define HAVE_SYS_STAT_H 1'
        echo '#define HAVE_SYS_TYPES_H 1'
        echo '#define HAVE_UNISTD_H 1'
        echo '#define PACKAGE_BUGREPORT "samtools-help@lists.sourceforge.net"'
        echo '#define PACKAGE_NAME "HTSlib"'
        echo '#define PACKAGE_STRING "HTSlib %s"'
        echo '#define PACKAGE_TARNAME "htslib"'
        echo '#define PACKAGE_URL "http://www.htslib.org/"'
        echo '#define PACKAGE_VERSION "%s"'
        echo '#define PLUGIN_EXT ".so"'
        echo '#define STDC_HEADERS 1'
    """ % (version, version),
)

genrule(
    name = "version",
    outs = ["version.h"],
    cmd = """echo '#define HTS_VERSION "%s"' > "$@" """ % version,
)

# Vanilla htslib, no extensions.
cc_library(
    name = "htslib",
    srcs = htslib_srcs,
    hdrs = htslib_hdrs + cram_hdrs,
    copts = copts,
    includes = includes,
    linkopts = extra_libs,
    textual_hdrs = textual_hdrs,
    visibility = ["//visibility:public"],
    deps = [":htslib_deps"],
)

# Misc binaries that might be exported.

cc_binary(
    name = "bgzip",
    srcs = ["bgzip.c"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "htsfile",
    srcs = ["htsfile.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "tabix",
    srcs = ["tabix.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "sam",
    srcs = ["test/sam.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

#  Things for testing.

cc_binary(
    name = "fieldarith",
    srcs = [
        "test/fieldarith.c",
    ],
    copts = [
        "-w",
    ],
    includes = [
        include_htslib,
    ],
    linkopts = extra_libs,
    deps = [
        ":htslib",
    ],
)

cc_binary(
    name = "hfile",
    srcs = ["test/hfile.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test_regidx",
    srcs = ["test/test-regidx.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test_view",
    srcs = ["test/test_view.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test_vcf_api",
    srcs = ["test/test-vcf-api.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test_vcf_sweep",
    srcs = ["test/test-vcf-sweep.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

# This captures some source files too, but they are small,
# so keep things simple with bare "*"s.
filegroup(
    name = "test_data",
    srcs = glob(
        [
            "test/*",
            "test/*/*",
        ],
        exclude_directories = 0,
    ),
)

cc_binary(
    name = "hts_endian",
    srcs = ["test/hts_endian.c"],
    copts = ["-w"],
    includes = [include_htslib],
    deps = [":htslib"],
)

cc_binary(
    name = "test_bgzf",
    srcs = ["test/test_bgzf.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test/thrash_threads6",
    srcs = ["test/thrash_threads6.c"],
    copts = ["$(STACK_FRAME_UNLIMITED)"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test/thrash_threads5",
    srcs = ["test/thrash_threads5.c"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test/thrash_threads4",
    srcs = ["test/thrash_threads4.c"],
    copts = ["$(STACK_FRAME_UNLIMITED)"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test/thrash_threads3",
    srcs = ["test/thrash_threads3.c"],
    copts = ["$(STACK_FRAME_UNLIMITED)"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test/thrash_threads2",
    srcs = ["test/thrash_threads2.c"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test/thrash_threads1",
    srcs = ["test/thrash_threads1.c"],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test_bcf_sr",
    srcs = ["test/test-bcf-sr.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)

cc_binary(
    name = "test_bcf_translate",
    srcs = ["test/test-bcf-translate.c"],
    copts = ["-w"],
    includes = [include_htslib],
    linkopts = extra_libs,
    deps = [":htslib"],
)
