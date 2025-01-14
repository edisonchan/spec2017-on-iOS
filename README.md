# spec2017-on-iOS
Minimum iOS version iOS16

This is only an experimental playground to run spec2017 on iOS. It supports all the specRate2017 Interger and FP Benchmarks. Use at your own risk!

# How to install
1. Download ipa file on release page
2. Use sideload app to sideload it.(if you don't have a computer, you can use scarlet or flekstore.)

# How to compile it yourself
Pre requirement: Xcode Version >=14, llvm-objcopy, make.
1. Copy the input folder from the ipa file and paste it under `Spec2017/` folder.
3. Add specbin/rary.a as static lib

# TODO: How to compile spec benchmarks
1. Modify all the benchmarks main Function/Program's name to name inside /specbin/specEntry.c file
2. Here is the list of benchmark entry points
```
intRate

500.perlbench     -perlmainc.c       line 61
502.gcc           -main.c            line 35
505.mcf           -mcf.c             line 134
520.omnetpp_r     -simulator/main.cc line 32
523.xalancbmk_r   -XalanExe.cpp      line 870
525.x264_r        -x264_src/x264.c   line 152
531.deepsjeng_r   -sjeng.cpp         line 44
541.leela_r       -Leela.cpp         line 20
548.exchange2_r   -exchange2.f90     line 1383
557.xz_r          -spec.c            line 25

fpRate

503.bwaves_r     -flow_lam.F                              line 107
507.cactuBSSN_r  -Cactus/main/flesh.cc                    line 57
508.namd_r       -spec_namd.C                             line 35
510.parest_r     -source/me-tomography/me_tomography.cc   line 215
511.povray_r     -povray.cpp                              line 190
519.lbm_r        -main.c                                  line 25
521.wrf_r        -wrf.f90                                 line 6
526.blender_r    -blender/source/creator/creator.c        line 1530
527.cam4_r       -ccsm_driver.f90                         line 25
538.imagick_r    -utilities/convert.c                     line 91
544.nab_r        -nabmd.c                                 line 40
549.fotonik3d_r  -yeemain.f90                             line 37
554.roms_r       -master.f90                              line 1241
```

# How to compile static library(llvm flang) for Fortran benchmark
Note: You can find the pre-built library inside ``fortran_lib`` folder(All build with Flang17.0.0).
1. Follow the instruction on offical repo (https://github.com/llvm/llvm-project/tree/main/flang) to build the flang compiler for your own platform.
2. Clone the repo again for building ios lib
3. Next run the following commands(PATH_TO_LLVM_BUILD_FOLDER is the folder of the native platform build)
```
rm -rf build
mkdir build
rm -rf install
mkdir install
ROOTDIR=`pwd`
INSTALLDIR=$ROOTDIR/install

cd build
cmake \
  -G Ninja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=$INSTALLDIR \
  -DCMAKE_CXX_STANDARD=17 \
  -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
  -DCMAKE_CXX_LINK_FLAGS="-Wl,-rpath,$LD_LIBRARY_PATH" \
  -DLLVM_TABLEGEN=/PATH_TO_LLVM_BUILD_FOLDER/bin/llvm-tblgen \
  -DCLANG_TABLEGEN=/PATH_TO_LLVM_BUILD_FOLDERbin/clang-tblgen \
  -DMLIR_TABLEGEN=/PATH_TO_LLVM_BUILD_FOLDER/bin/mlir-tblgen \
  -DMLIR_TABLEGEN_EXE=/PATH_TO_LLVM_BUILD_FOLDER/bin/mlir-tblgen \
  -DMLIR_LINALG_ODS_YAML_GEN=/PATH_TO_LLVM_BUILD_FOLDER/bin/mlir-linalg-ods-yaml-gen \
  -DMLIR_LINALG_ODS_YAML_GEN_EXE=/PATH_TO_LLVM_BUILD_FOLDER/bin/mlir-linalg-ods-yaml-gen \
  -DFLANG_INCLUDE_TESTS=FALSE \
  -DFLANG_BUILD_EXAMPLES=FALSE \
  -DFLANG_BUILD_TOOLS=OFF \
  -DLLVM_ENABLE_ASSERTIONS=ON \
  -DLLVM_TARGETS_TO_BUILD=AArch64 \
  -DLLVM_LIT_ARGS=-v \
  -DLLVM_ENABLE_PROJECTS="clang;mlir;flang;openmp" \
  -DLLVM_TARGET_ARCH=AArch64 \
  -DLLVM_DEFAULT_TARGET_TRIPLE=arm64-apple-darwin-macho \
  -DLLVM_BUILD_RUNTIME=Off -DLLVM_INCLUDE_TESTS=Off \
  -DLLVM_INCLUDE_EXAMPLES=Off -DLLVM_ENABLE_BACKTRACES=Off \
  -DCMAKE_OSX_ARCHITECTURES="arm64" \
  -DLLVM_ENABLE_PIC=False \
  -DCMAKE_OSX_SYSROOT=/Library/Developer/CommandLineTools/SDKs/iPhoneOS16.2.sdk \
  -DCMAKE_C_FLAGS="-arch arm64 -target arm64-apple-darwin-macho -miphoneos-version-min=14" \
  -DCMAKE_CXX_FLAGS="-arch arm64 -target arm64-apple-darwin-macho -miphoneos-version-min=14" \
  ../llvm-project/llvm
  
  && ninja
  ```
  5. You will find the static lib under lib/ folder. (Mainly libFortranCommon.a
libFortranDecimal.a
libFortranEvaluate.a
libFortranEvaluateTesting.a
libFortranLower.a
libFortran_main.a
libFortranParser.a
libFortranRuntime.a
libFortranSemantics.a)

![IMG_0033](https://user-images.githubusercontent.com/86281724/231185271-e2694e60-e83b-48d5-af06-3f0928b41c40.PNG)
