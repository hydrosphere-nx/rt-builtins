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

c_args = ' '.join(c_args_array + extra_c_flags)

cxx_path = cxx.cmd_array()[0]
cc_path = cc.cmd_array()[0]

cmake_program = find_program('cmake')
ninja_program = find_program('ninja')
cp_program = find_program('cp')

compiler_rt_cmake_flags = [
                            '-DCOMPILER_RT_BAREMETAL_BUILD=ON',
                            '-DCOMPILER_RT_STANDALONE_BUILD=ON',
                            '-DCOMPILER_RT_BUILD_BUILTINS=ON',
                            '-DCOMPILER_RT_BUILD_SANITIZERS=OFF',
                            '-DCOMPILER_RT_BUILD_XRAY=OFF',
                            '-DCOMPILER_RT_BUILD_PROFILE=OFF',
                            '-DCOMPILER_RT_BUILD_LIBFUZZER=OFF',
                            '-DCMAKE_C_COMPILER=@0@'.format(cc_path),
                            '-DCMAKE_C_FLAGS=@0@'.format(c_args),
                            '-DCMAKE_C_COMPILER_TARGET=@0@'.format(target_triplet),
                            '-DCMAKE_CXX_COMPILER=@0@'.format(cxx_path),
                            '-DCMAKE_CXX_FLAGS=@0@'.format(c_args),
                            '-DCMAKE_CXX_COMPILER_TARGET=@0@'.format(target_triplet),
                            '-DCMAKE_ASM_FLAGS=@0@'.format(c_args),
                            '-DCMAKE_EXE_LINKER_FLAGS=-fuse-ld=lld',
                            '-DCOMPILER_RT_DEFAULT_TARGET_ONLY=ON',
                            '-DCMAKE_SYSTEM_NAME=Linux',
                            '-DCMAKE_INSTALL_PREFIX=@0@'.format(meson.current_build_dir()),
                            '-DCOMPILER_RT_OS_DIR=.'
                          ]

configure_project = custom_target('Configuring compiler-rt',
    input: '@0@/compiler-rt/CMakeLists.txt'.format(meson.current_source_dir()),
    output: 'cmake_build',
    command: [cmake_program, '-G', 'Ninja', '@INPUT@', '-B', '@OUTPUT@'] + compiler_rt_cmake_flags,
    console: true
)

build_project = custom_target('Building compiler-rt',
    output: 'build-compiler-rt',
    command: [ninja_program, '-C', 'cmake_build'],
    depends : [configure_project],
    console: true
)

# Because I cannot do that with meson directly, we do it externally...
custom_target('compiler-rt copy fixup',
    build_by_default: true,
    install: true,
    output: 'libclang_rt.builtins.a',
    depends : [build_project],
    command: [cp_program, '@0@/cmake_build/lib/libclang_rt.builtins-@1@.a'.format(meson.current_build_dir(), compiler_rt_arch), '@OUTPUT@'],
    console: true,
    install_dir: get_option('libdir')
)
