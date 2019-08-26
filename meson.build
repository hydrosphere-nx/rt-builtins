# Copyright (c) 2019 Hydrosphère Developers
#
# Licensed under the Apache License, Version 2.0 <LICENSE-APACHE or
# http://www.apache.org/licenses/LICENSE-2.0> or the MIT license
# <LICENSE-MIT or http://opensource.org/licenses/MIT>, at your
# option. This file may not be copied, modified, or distributed
# except according to those terms.

project('compiler-rt', 'c', 'cpp',
        license: ['Apache-2.0 WITH LLVM-exception'],
        version: '8.0.0'
    )

# Check we are using clang
cxx = meson.get_compiler('cpp')
cc = meson.get_compiler('c')
assert(cxx.get_id() == 'clang', 'This project only support the Clang compiler.')
assert(meson.is_cross_build(), 'This project is supposed to be cross compiled.')

target_cpu_familly = target_machine.cpu_family()

if target_cpu_familly == 'aarch64'
    compiler_rt_arch = 'aarch64'
    target_triplet = 'aarch64-none-linux-gnu'
elif target_cpu_familly == 'arm'
    compiler_rt_arch = 'armhf'
    target_triplet = 'armv7l-none-linux-gnueabihf'
else
    error('Unsuported target cpu_familly')
endif

extra_c_flags = ['-fuse-ld=lld',  '-fno-stack-protector', '-ffreestanding', '-nostdlib', '-nodefaultlibs', '-isystem', '@0@/include'.format(meson.current_source_dir()), '-Wno-unused-command-line-argument', '-Wall', '-Wextra']

c_args_array = []

next_to_patch = false
# Patch the target as compiler-rt expect something that it knowns
foreach arg : meson.get_cross_property('c_args')
    if arg == '-target'
        next_to_patch = true
        c_args_array += arg
    elif next_to_patch == true
        next_to_patch = false
        c_args_array += target_triplet
    else
        c_args_array += arg
    endif
endforeach

generic_sources = [
    'compiler-rt/lib/builtins/absvdi2.c',
    'compiler-rt/lib/builtins/absvsi2.c',
    'compiler-rt/lib/builtins/absvti2.c',
    'compiler-rt/lib/builtins/adddf3.c',
    'compiler-rt/lib/builtins/addsf3.c',
    'compiler-rt/lib/builtins/addtf3.c',
    'compiler-rt/lib/builtins/addvdi3.c',
    'compiler-rt/lib/builtins/addvsi3.c',
    'compiler-rt/lib/builtins/addvti3.c',
    'compiler-rt/lib/builtins/apple_versioning.c',
    'compiler-rt/lib/builtins/ashldi3.c',
    'compiler-rt/lib/builtins/ashlti3.c',
    'compiler-rt/lib/builtins/ashrdi3.c',
    'compiler-rt/lib/builtins/ashrti3.c',
    'compiler-rt/lib/builtins/clear_cache.c',
    'compiler-rt/lib/builtins/clzti2.c',
    'compiler-rt/lib/builtins/cmpdi2.c',
    'compiler-rt/lib/builtins/cmpti2.c',
    'compiler-rt/lib/builtins/comparedf2.c',
    'compiler-rt/lib/builtins/ctzdi2.c',
    'compiler-rt/lib/builtins/ctzsi2.c',
    'compiler-rt/lib/builtins/ctzti2.c',
    'compiler-rt/lib/builtins/divdc3.c',
    'compiler-rt/lib/builtins/divdf3.c',
    'compiler-rt/lib/builtins/divdi3.c',
    'compiler-rt/lib/builtins/divmoddi4.c',
    'compiler-rt/lib/builtins/divsc3.c',
    'compiler-rt/lib/builtins/divsf3.c',
    'compiler-rt/lib/builtins/divtc3.c',
    'compiler-rt/lib/builtins/divti3.c',
    'compiler-rt/lib/builtins/divtf3.c',
    'compiler-rt/lib/builtins/extendsfdf2.c',
    'compiler-rt/lib/builtins/extendhfsf2.c',
    'compiler-rt/lib/builtins/ffsdi2.c',
    'compiler-rt/lib/builtins/ffssi2.c',
    'compiler-rt/lib/builtins/ffsti2.c',
    'compiler-rt/lib/builtins/fixdfdi.c',
    'compiler-rt/lib/builtins/fixdfsi.c',
    'compiler-rt/lib/builtins/fixdfti.c',
    'compiler-rt/lib/builtins/fixsfdi.c',
    'compiler-rt/lib/builtins/fixsfsi.c',
    'compiler-rt/lib/builtins/fixsfti.c',
    'compiler-rt/lib/builtins/fixunsdfdi.c',
    'compiler-rt/lib/builtins/fixunsdfsi.c',
    'compiler-rt/lib/builtins/fixunsdfti.c',
    'compiler-rt/lib/builtins/fixunssfdi.c',
    'compiler-rt/lib/builtins/fixunssfsi.c',
    'compiler-rt/lib/builtins/fixunssfti.c',
    'compiler-rt/lib/builtins/floatsidf.c',
    'compiler-rt/lib/builtins/floatsisf.c',
    'compiler-rt/lib/builtins/floattidf.c',
    'compiler-rt/lib/builtins/floattisf.c',
    'compiler-rt/lib/builtins/floatunsidf.c',
    'compiler-rt/lib/builtins/floatunsisf.c',
    'compiler-rt/lib/builtins/floatuntidf.c',
    'compiler-rt/lib/builtins/floatuntisf.c',
    'compiler-rt/lib/builtins/int_util.c',
    'compiler-rt/lib/builtins/lshrdi3.c',
    'compiler-rt/lib/builtins/lshrti3.c',
    'compiler-rt/lib/builtins/moddi3.c',
    'compiler-rt/lib/builtins/modti3.c',
    'compiler-rt/lib/builtins/muldc3.c',
    'compiler-rt/lib/builtins/muldf3.c',
    'compiler-rt/lib/builtins/muldi3.c',
    'compiler-rt/lib/builtins/mulodi4.c',
    'compiler-rt/lib/builtins/mulosi4.c',
    'compiler-rt/lib/builtins/muloti4.c',
    'compiler-rt/lib/builtins/mulsc3.c',
    'compiler-rt/lib/builtins/mulsf3.c',
    'compiler-rt/lib/builtins/multi3.c',
    'compiler-rt/lib/builtins/multf3.c',
    'compiler-rt/lib/builtins/mulvdi3.c',
    'compiler-rt/lib/builtins/mulvsi3.c',
    'compiler-rt/lib/builtins/mulvti3.c',
    'compiler-rt/lib/builtins/negdf2.c',
    'compiler-rt/lib/builtins/negdi2.c',
    'compiler-rt/lib/builtins/negsf2.c',
    'compiler-rt/lib/builtins/negti2.c',
    'compiler-rt/lib/builtins/negvdi2.c',
    'compiler-rt/lib/builtins/negvsi2.c',
    'compiler-rt/lib/builtins/negvti2.c',
    'compiler-rt/lib/builtins/os_version_check.c',
    'compiler-rt/lib/builtins/paritydi2.c',
    'compiler-rt/lib/builtins/paritysi2.c',
    'compiler-rt/lib/builtins/parityti2.c',
    'compiler-rt/lib/builtins/popcountdi2.c',
    'compiler-rt/lib/builtins/popcountsi2.c',
    'compiler-rt/lib/builtins/popcountti2.c',
    'compiler-rt/lib/builtins/powidf2.c',
    'compiler-rt/lib/builtins/powisf2.c',
    'compiler-rt/lib/builtins/powitf2.c',
    'compiler-rt/lib/builtins/subdf3.c',
    'compiler-rt/lib/builtins/subsf3.c',
    'compiler-rt/lib/builtins/subvdi3.c',
    'compiler-rt/lib/builtins/subvsi3.c',
    'compiler-rt/lib/builtins/subvti3.c',
    'compiler-rt/lib/builtins/subtf3.c',
    'compiler-rt/lib/builtins/trampoline_setup.c',
    'compiler-rt/lib/builtins/truncdfhf2.c',
    'compiler-rt/lib/builtins/truncdfsf2.c',
    'compiler-rt/lib/builtins/truncsfhf2.c',
    'compiler-rt/lib/builtins/ucmpdi2.c',
    'compiler-rt/lib/builtins/ucmpti2.c',
    'compiler-rt/lib/builtins/udivdi3.c',
    'compiler-rt/lib/builtins/udivmoddi4.c',
    'compiler-rt/lib/builtins/udivmodti4.c',
    'compiler-rt/lib/builtins/udivti3.c',
    'compiler-rt/lib/builtins/umoddi3.c',
    'compiler-rt/lib/builtins/umodti3.c',
    'compiler-rt/lib/builtins/floatdidf.c',
    'compiler-rt/lib/builtins/floatdisf.c',
    'compiler-rt/lib/builtins/floatundidf.c',
    'compiler-rt/lib/builtins/floatundisf.c',
]

generic_nonarm_sources = [
    'compiler-rt/lib/builtins/bswapdi2.c',
    'compiler-rt/lib/builtins/bswapsi2.c',
    'compiler-rt/lib/builtins/clzdi2.c',
    'compiler-rt/lib/builtins/clzsi2.c',
    'compiler-rt/lib/builtins/comparesf2.c',
    'compiler-rt/lib/builtins/divmodsi4.c',
    'compiler-rt/lib/builtins/divsi3.c',
    'compiler-rt/lib/builtins/modsi3.c',
    'compiler-rt/lib/builtins/udivmodsi4.c',
    'compiler-rt/lib/builtins/udivsi3.c',
    'compiler-rt/lib/builtins/umodsi3.c',
]

generic_tf_sources = [
    'compiler-rt/lib/builtins/comparetf2.c',
    'compiler-rt/lib/builtins/extenddftf2.c',
    'compiler-rt/lib/builtins/extendsftf2.c',
    'compiler-rt/lib/builtins/fixtfdi.c',
    'compiler-rt/lib/builtins/fixtfsi.c',
    'compiler-rt/lib/builtins/fixtfti.c',
    'compiler-rt/lib/builtins/fixunstfdi.c',
    'compiler-rt/lib/builtins/fixunstfsi.c',
    'compiler-rt/lib/builtins/fixunstfti.c',
    'compiler-rt/lib/builtins/floatditf.c',
    'compiler-rt/lib/builtins/floatsitf.c',
    'compiler-rt/lib/builtins/floattitf.c',
    'compiler-rt/lib/builtins/floatunditf.c',
    'compiler-rt/lib/builtins/floatunsitf.c',
    'compiler-rt/lib/builtins/floatuntitf.c',
    'compiler-rt/lib/builtins/multc3.c',
    'compiler-rt/lib/builtins/trunctfdf2.c',
    'compiler-rt/lib/builtins/trunctfsf2.c',
]

arm_sources = [
    'compiler-rt/lib/builtins/arm/bswapdi2.S',
    'compiler-rt/lib/builtins/arm/bswapsi2.S',
    'compiler-rt/lib/builtins/arm/clzdi2.S',
    'compiler-rt/lib/builtins/arm/clzsi2.S',
    'compiler-rt/lib/builtins/arm/comparesf2.S',
    'compiler-rt/lib/builtins/arm/divmodsi4.S',
    'compiler-rt/lib/builtins/arm/divsi3.S',
    'compiler-rt/lib/builtins/arm/modsi3.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_add_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_add_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_and_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_and_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_max_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_max_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_min_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_min_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_nand_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_nand_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_or_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_or_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_sub_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_sub_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_umax_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_umax_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_umin_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_umin_8.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_xor_4.S',
    'compiler-rt/lib/builtins/arm/sync_fetch_and_xor_8.S',
    'compiler-rt/lib/builtins/arm/udivmodsi4.S',
    'compiler-rt/lib/builtins/arm/udivsi3.S',
    'compiler-rt/lib/builtins/arm/umodsi3.S',
]

arm_thumb1_sources = [
    'compiler-rt/lib/builtins/arm/addsf3.S',
    'compiler-rt/lib/builtins/arm/divsi3.S',
    'compiler-rt/lib/builtins/arm/udivsi3.S',
    'compiler-rt/lib/builtins/arm/comparesf2.S',
    'compiler-rt/lib/builtins/arm/switch16.S',
    'compiler-rt/lib/builtins/arm/switch32.S',
    'compiler-rt/lib/builtins/arm/switch8.S',
    'compiler-rt/lib/builtins/arm/switchu8.S',
    'compiler-rt/lib/builtins/arm/sync_synchronize.S',
]

arm_vfp_common_sources = [
    'compiler-rt/lib/builtins/arm/restore_vfp_d8_d15_regs.S',
    'compiler-rt/lib/builtins/arm/save_vfp_d8_d15_regs.S',
    'compiler-rt/lib/builtins/arm/addsf3vfp.S',
    'compiler-rt/lib/builtins/arm/divsf3vfp.S',
    'compiler-rt/lib/builtins/arm/eqdf2vfp.S',
    'compiler-rt/lib/builtins/arm/eqsf2vfp.S',
    'compiler-rt/lib/builtins/arm/fixsfsivfp.S',
    'compiler-rt/lib/builtins/arm/fixunssfsivfp.S',
    'compiler-rt/lib/builtins/arm/floatsisfvfp.S',
    'compiler-rt/lib/builtins/arm/floatunssisfvfp.S',
    'compiler-rt/lib/builtins/arm/gedf2vfp.S',
    'compiler-rt/lib/builtins/arm/gesf2vfp.S',
    'compiler-rt/lib/builtins/arm/gtdf2vfp.S',
    'compiler-rt/lib/builtins/arm/gtsf2vfp.S',
    'compiler-rt/lib/builtins/arm/ledf2vfp.S',
    'compiler-rt/lib/builtins/arm/lesf2vfp.S',
    'compiler-rt/lib/builtins/arm/ltdf2vfp.S',
    'compiler-rt/lib/builtins/arm/ltsf2vfp.S',
    'compiler-rt/lib/builtins/arm/mulsf3vfp.S',
    'compiler-rt/lib/builtins/arm/nedf2vfp.S',
    'compiler-rt/lib/builtins/arm/negdf2vfp.S',
    'compiler-rt/lib/builtins/arm/negsf2vfp.S',
    'compiler-rt/lib/builtins/arm/nesf2vfp.S',
    'compiler-rt/lib/builtins/arm/subsf3vfp.S',
    'compiler-rt/lib/builtins/arm/unorddf2vfp.S',
    'compiler-rt/lib/builtins/arm/unordsf2vfp.S',
]

arm_vfp_double_precision_sources = [
    'compiler-rt/lib/builtins/arm/adddf3vfp.S',
    'compiler-rt/lib/builtins/arm/divdf3vfp.S',
    'compiler-rt/lib/builtins/arm/extendsfdf2vfp.S',
    'compiler-rt/lib/builtins/arm/fixdfsivfp.S',
    'compiler-rt/lib/builtins/arm/fixunsdfsivfp.S',
    'compiler-rt/lib/builtins/arm/floatunssidfvfp.S',
    'compiler-rt/lib/builtins/arm/floatsidfvfp.S',
    'compiler-rt/lib/builtins/arm/muldf3vfp.S',
    'compiler-rt/lib/builtins/arm/subdf3vfp.S',
    'compiler-rt/lib/builtins/arm/truncdfsf2vfp.S',
]

arm_eabi_sources = [
    'compiler-rt/lib/builtins/arm/aeabi_cdcmp.S',
    'compiler-rt/lib/builtins/arm/aeabi_cdcmpeq_check_nan.c',
    'compiler-rt/lib/builtins/arm/aeabi_cfcmp.S',
    'compiler-rt/lib/builtins/arm/aeabi_cfcmpeq_check_nan.c',
    'compiler-rt/lib/builtins/arm/aeabi_dcmp.S',
    'compiler-rt/lib/builtins/arm/aeabi_div0.c',
    'compiler-rt/lib/builtins/arm/aeabi_drsub.c',
    'compiler-rt/lib/builtins/arm/aeabi_fcmp.S',
    'compiler-rt/lib/builtins/arm/aeabi_frsub.c',
    'compiler-rt/lib/builtins/arm/aeabi_idivmod.S',
    'compiler-rt/lib/builtins/arm/aeabi_ldivmod.S',
    'compiler-rt/lib/builtins/arm/aeabi_memcmp.S',
    'compiler-rt/lib/builtins/arm/aeabi_memcpy.S',
    'compiler-rt/lib/builtins/arm/aeabi_memmove.S',
    'compiler-rt/lib/builtins/arm/aeabi_memset.S',
    'compiler-rt/lib/builtins/arm/aeabi_uidivmod.S',
    'compiler-rt/lib/builtins/arm/aeabi_uldivmod.S',
]

target_sources = [
    generic_sources,
]

arm_sources_default = [
    arm_sources,
    arm_eabi_sources,
    arm_thumb1_sources,
]

if target_cpu_familly == 'aarch64'
    target_sources += [
        generic_tf_sources,
        generic_nonarm_sources
    ]
elif target_cpu_familly == 'arm'
    target_sources += arm_sources_default

    arm_fp_setting = cxx.get_define('__ARM_FP')

    is_single_and_double_precision_supported = arm_fp_setting == '12' or arm_fp_setting == '14' or arm_fp_setting == '0xc' or arm_fp_setting == '0xe'
    is_single_precision_supported = arm_fp_setting == '4' or arm_fp_setting == '0x4' or is_single_and_double_precision_supported
    is_double_precision_supported = arm_fp_setting == '8' or arm_fp_setting == '0x8' or is_single_and_double_precision_supported

    if is_single_precision_supported
        target_sources += arm_vfp_common_sources
    endif

    if is_double_precision_supported
        target_sources += arm_vfp_double_precision_sources
    endif
endif

compiler_rt_builtins = static_library('clang_rt.builtins',
    target_sources,
    c_args: c_args_array + extra_c_flags,
    cpp_args: c_args_array + extra_c_flags,
    pic: true,
    install: true
)