# try to find compiler-rt
function(get_compiler_rt_path path)
  set(CRT_SEARCH_PATHS "${CMAKE_SOURCE_DIR}/runtimes/compiler-rt;${CMAKE_SOURCE_DIR}/projects/compiler-rt;${CMAKE_SOURCE_DIR}/../compiler-rt/")
  foreach(entry ${CRT_SEARCH_PATHS})
     if (IS_DIRECTORY ${entry})
      set(${path} ${entry} PARENT_SCOPE)
      return()
    endif()
  endforeach()
endfunction()



SET ( VECMATH_LIB_DIR ${RV_BINARY_DIR}/lib )
# Sleef lib
SET ( SLEEF_DIR "${RV_SOURCE_DIR}/vecmath/sleef" CACHE STRING "Path to libsleef sources" FORCE )
SET ( SLEEF_ARCH_DIR "${SLEEF_DIR}/src/arch" )
SET ( SLEEF_COMMON_DIR "${SLEEF_DIR}/src/common" )
SET ( RV_LIB_SLEEF_DIR "${SLEEF_DIR}/src/libm" )
SET ( RV_LIB_EXTRAS_DIR "${RV_SOURCE_DIR}/vecmath/extras" )


##### SLEEF target modules #####

IF (RV_REBUILD_GENBC)
  Message("-- rv: Building the SLEEF BC library from source.")
  SET( VECMATH_GENBC_DIR ${VECMATH_LIB_DIR} )
ELSE()
  Message("-- rv: Using pre-built SLEEF BC files.")
  SET( VECMATH_GENBC_DIR ${RV_SOURCE_DIR}/vecmath/prebuilt_genbc )
ENDIF()

## Shared constant array
SET ( SLEEF_BC_REMPITAB "${VECMATH_LIB_DIR}/rempitab.bc" )
SET ( SLEEF_GENBC_REMPITAB "${VECMATH_GENBC_DIR}/rempitab.gen.cpp" )

## X86 ##
SET ( SLEEF_BC_AVX512_SP "${VECMATH_LIB_DIR}/avx512_sleef_sp.bc" )
SET ( SLEEF_GENBC_AVX512_SP "${VECMATH_GENBC_DIR}/avx512_sleef_sp.gen.cpp" )

SET ( SLEEF_BC_AVX512_DP "${VECMATH_LIB_DIR}/avx512_sleef_dp.bc" )
SET ( SLEEF_GENBC_AVX512_DP "${VECMATH_GENBC_DIR}/avx512_sleef_dp.gen.cpp" )

SET ( EXTRAS_AVX512_BC "${VECMATH_LIB_DIR}/avx512_extras.bc" )
SET ( EXTRAS_AVX512_GENBC "${VECMATH_GENBC_DIR}/avx512_extras.gen.cpp" )

SET ( SLEEF_BC_AVX2_SP "${VECMATH_LIB_DIR}/avx2_sleef_sp.bc" )
SET ( SLEEF_GENBC_AVX2_SP "${VECMATH_GENBC_DIR}/avx2_sleef_sp.gen.cpp" )

SET ( EXTRAS_AVX2_BC "${VECMATH_LIB_DIR}/avx2_extras.bc" )
SET ( EXTRAS_AVX2_GENBC "${VECMATH_GENBC_DIR}/avx2_extras.gen.cpp" )

SET ( SLEEF_BC_AVX_SP "${VECMATH_LIB_DIR}/avx_sleef_sp.bc" )
SET ( SLEEF_GENBC_AVX_SP "${VECMATH_GENBC_DIR}/avx_sleef_sp.gen.cpp" )

SET ( SLEEF_BC_SSE_SP "${VECMATH_LIB_DIR}/sse_sleef_sp.bc" )
SET ( SLEEF_GENBC_SSE_SP "${VECMATH_GENBC_DIR}/sse_sleef_sp.gen.cpp" )

SET ( SLEEF_BC_AVX2_DP "${VECMATH_LIB_DIR}/avx2_sleef_dp.bc" )
SET ( SLEEF_GENBC_AVX2_DP "${VECMATH_GENBC_DIR}/avx2_sleef_dp.gen.cpp" )

SET ( SLEEF_BC_AVX_DP "${VECMATH_LIB_DIR}/avx_sleef_dp.bc" )
SET ( SLEEF_GENBC_AVX_DP "${VECMATH_GENBC_DIR}/avx_sleef_dp.gen.cpp" )

SET ( SLEEF_BC_SSE_DP "${VECMATH_LIB_DIR}/sse_sleef_dp.bc" )
SET ( SLEEF_GENBC_SSE_DP "${VECMATH_GENBC_DIR}/sse_sleef_dp.gen.cpp" )
  
## ARM ##
SET ( SLEEF_BC_ADVSIMD_DP "${VECMATH_LIB_DIR}/advsimd_sleef_dp.bc" )
SET ( SLEEF_GENBC_ADVSIMD_DP "${VECMATH_GENBC_DIR}/advsimd_sleef_dp.gen.cpp" )

SET ( SLEEF_BC_ADVSIMD_SP "${VECMATH_LIB_DIR}/advsimd_sleef_sp.bc" )
SET ( SLEEF_GENBC_ADVSIMD_SP "${VECMATH_GENBC_DIR}/advsimd_sleef_sp.gen.cpp" )

SET ( EXTRAS_ADVSIMD_BC "${VECMATH_LIB_DIR}/advsimd_extras.bc" )
SET ( EXTRAS_ADVSIMD_GENBC "${VECMATH_GENBC_DIR}/advsimd_extras.gen.cpp" )
  
## VLA: Vector-length Agnostic ##
SET ( SLEEF_BC_VLA_SP "${VECMATH_LIB_DIR}/vla_sleef_sp.bc" )
SET ( SLEEF_GENBC_VLA_SP "${VECMATH_GENBC_DIR}/vla_sleef_sp.gen.cpp" )

SET ( SLEEF_BC_VLA_DP "${VECMATH_LIB_DIR}/vla_sleef_dp.bc" )
SET ( SLEEF_GENBC_VLA_DP "${VECMATH_GENBC_DIR}/vla_sleef_dp.gen.cpp" )

# compiler-rt
SET ( CRT_BC "${VECMATH_LIB_DIR}/crt.bc" )
SET ( CRT_GENBC "${VECMATH_GENBC_DIR}/crt.gen.cpp" )

### SLEEF Build rules ###

IF (RV_ENABLE_SLEEF)
   # Shared
   SET (RV_GENBC_SOURCES ${SLEEF_GENBC_REMPITAB})
   SET (SLEEF_BC_LIBS ${SLEEF_BC_REMPITAB})

  ## X86 ##
  IF (RV_ENABLE_X86)
    Message("-- rv: SLEEF with x86 support.")
    SET ( SLEEF_BC_LIBS ${SLEEF_BC_LIBS} ${SLEEF_BC_AVX2_SP} ${SLEEF_BC_AVX_SP} ${SLEEF_BC_SSE_SP} ${SLEEF_BC_AVX2_DP} ${SLEEF_BC_AVX_DP} ${SLEEF_BC_SSE_DP} ${EXTRAS_AVX2_BC} ${EXTRAS_AVX512_BC} )
    SET ( RV_GENBC_SOURCES ${RV_GENBC_SOURCES} ${SLEEF_GENBC_AVX2_SP} ${SLEEF_GENBC_AVX_SP} ${SLEEF_GENBC_SSE_SP} ${SLEEF_GENBC_AVX2_DP} ${SLEEF_GENBC_AVX_DP} ${SLEEF_GENBC_SSE_DP}  ${SLEEF_GENBC_AVX512_SP} ${SLEEF_GENBC_AVX512_DP} ${EXTRAS_AVX2_GENBC} ${EXTRAS_AVX512_GENBC} )
  ENDIF()

  ## ARM ##
  IF (RV_ENABLE_ADVSIMD)
    Message("-- rv: SLEEF with Advanced SIMD support.")
  
    SET ( SLEEF_BC_LIBS ${SLEEF_BC_LIBS} ${SLEEF_BC_ADVSIMD_SP} ${SLEEF_BC_ADVSIMD_DP} ${EXTRAS_ADVSIMD_BC} )
    SET ( RV_GENBC_SOURCES ${RV_GENBC_SOURCES} ${SLEEF_GENBC_ADVSIMD_SP} ${SLEEF_GENBC_ADVSIMD_DP} ${EXTRAS_ADVSIMD_GENBC} )
  ENDIF()

  ## VLA##
  SET ( SLEEF_BC_LIBS ${SLEEF_BC_LIBS} ${SLEEF_BC_VLA_DP} ${SLEEF_BC_VLA_SP} )
  SET ( RV_GENBC_SOURCES ${RV_GENBC_SOURCES} ${SLEEF_GENBC_VLA_SP} ${SLEEF_GENBC_VLA_DP} )
ENDIF()

SET( FLAGS_SSE -m64 -msse2 -msse4.1 -DENABLE_BUILTIN_MATH -DENABLE_SSE2=ON )
SET( FLAGS_AVX -mavx -mfma -DENABLE_BUILTIN_MATH -DENABLE_AVX=ON )
SET( FLAGS_AVX2 -march=skylake -mavx2 -mfma -DENABLE_BUILTIN_MATH -DENABLE_AVX2=ON )
SET( FLAGS_AVX512 -mfma -mavx512f -mavx512dq -mavx512cd -mavx512bw -mavx512vl -march=skylake -DENABLE_AVX512F=ON -DENABLE_BUILTIN_MATH )
SET( FLAGS_ADVSIMD -mfma -target aarch64-unknown-linux-gnu -mcpu=cortex-a53 -DACCURATE_SQRT -DENABLE_ADVSIMD=ON -DENABLE_BUILTIN_MATH )
SET( FLAGS_VLA -DACCURATE_SQRT -DENABLE_BUILTIN_MATH -m64 )

IF (RV_ENABLE_CRT)
  SET ( RV_GENBC_SOURCES ${RV_GENBC_SOURCES} ${CRT_GENBC} )
ENDIF()

### Commands to generate 'bc.gencpp' files from C/CPP sources ###
# conditionally provide extra sources
SET ( RV_LIB_SLEEF_OUT_DIR ${VECMATH_LIB_DIR} )

SET ( GENCPP_TOOL "${RV_SOURCE_DIR}/tools/gen_cpp.py" )

IF (RV_ENABLE_SLEEF)
  MESSAGE("-- rv: Building with SLEEF")

  IF (RV_REBUILD_GENBC)
    IF(TARGET clang)
      SET(LLVM_TOOL_CLANG clang)
    ELSE(TARGET clang)
      SET(LLVM_TOOL_CLANG ${RV_BINARY_DIR}/../../bin/clang)
    ENDIF(TARGET clang)
  
    # add SLEEF bc generators as extra source
    # ADD_CUSTOM_TARGET ( libsleef_x64 DEPENDS ${RV_GENBC_SOURCES} )
  
    # common build option for BC libraries from SLEEF sources
    SET( RVLIB_BUILD_OPTS -DNDEBUG -O3 -fno-vectorize -ffp-contract=fast -I${RV_LIB_SLEEF_DIR}/../arch -I${RV_LIB_SLEEF_DIR}/../common)
    # extra libs

    ## Shared constant table
    ADD_CUSTOM_COMMAND (
      OUTPUT ${SLEEF_GENBC_REMPITAB}
      COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
      COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/rempitab.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_VLA} -o ${SLEEF_BC_REMPITAB}
      COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_REMPITAB} \"rempitab\" ${SLEEF_BC_REMPITAB}
      DEPENDS ${RV_LIB_SLEEF_DIR}/rempitab.c ${LLVM_TOOL_CLANG}
    )
  
    ### ADVSIMD
    IF (RV_ENABLE_ADVSIMD)
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_ADVSIMD_DP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimddp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_ADVSIMD} -o ${SLEEF_BC_ADVSIMD_DP}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_ADVSIMD_DP} \"advsimd_dp\" ${SLEEF_BC_ADVSIMD_DP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimddp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_ADVSIMD_SP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_ADVSIMD} -o ${SLEEF_BC_ADVSIMD_SP}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_ADVSIMD_SP} \"advsimd_sp\" ${SLEEF_BC_ADVSIMD_SP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${EXTRAS_ADVSIMD_GENBC}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_EXTRAS_DIR}/vrand_advsimd.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_ADVSIMD} -o ${EXTRAS_ADVSIMD_BC}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${EXTRAS_ADVSIMD_GENBC} \"advsimd_extras\" ${EXTRAS_ADVSIMD_BC}
        DEPENDS ${RV_LIB_EXTRAS_DIR}/vrand_advsimd.c ${LLVM_TOOL_CLANG}
      )
    ENDIF()  # !RV_ENABLE_ADVSIMD
  
    if (RV_ENABLE_X86)
    ### AVX512
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_AVX512_SP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX512} -o ${SLEEF_BC_AVX512_SP}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_AVX512_SP} \"avx512_sp\" ${SLEEF_BC_AVX512_SP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimddp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_AVX512_DP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimddp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX512} -o ${SLEEF_BC_AVX512_DP}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_AVX512_DP} \"avx512_dp\" ${SLEEF_BC_AVX512_DP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimddp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${EXTRAS_AVX512_GENBC}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_EXTRAS_DIR}/vrand_avx512.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX512} -o ${EXTRAS_AVX512_BC}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${EXTRAS_AVX512_GENBC} \"avx512_extras\" ${EXTRAS_AVX512_BC}
        DEPENDS ${RV_LIB_EXTRAS_DIR}/vrand_avx512.c ${LLVM_TOOL_CLANG}
      )
    ### AVX2
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_AVX2_DP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimddp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX2} -o ${SLEEF_BC_AVX2_DP}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_AVX2_DP} \"avx2_dp\" ${SLEEF_BC_AVX2_DP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimddp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_AVX2_SP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX2} -o ${SLEEF_BC_AVX2_SP}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_AVX2_SP} \"avx2_sp\" ${SLEEF_BC_AVX2_SP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${EXTRAS_AVX2_GENBC}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_EXTRAS_DIR}/vrand_avx2.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX2} -o ${EXTRAS_AVX2_BC}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${EXTRAS_AVX2_GENBC} \"avx2_extras\" ${EXTRAS_AVX2_BC}
        DEPENDS ${RV_LIB_EXTRAS_DIR}/vrand_avx2.c ${LLVM_TOOL_CLANG}
      )
    ### AVX
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_AVX_DP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimddp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX} -o ${SLEEF_BC_AVX_DP}#.tmp
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_AVX_DP} \"avx_dp\" ${SLEEF_BC_AVX_DP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimddp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_AVX_SP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_AVX} -o ${SLEEF_BC_AVX_SP}#.tmp
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_AVX_SP} \"avx_sp\" ${SLEEF_BC_AVX_SP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c ${LLVM_TOOL_CLANG}
      )
    ### SSE
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_SSE_SP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_SSE} -o ${SLEEF_BC_SSE_SP}#.tmp
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_SSE_SP} \"sse_sp\" ${SLEEF_BC_SSE_SP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimdsp.c ${LLVM_TOOL_CLANG}
      )
      ADD_CUSTOM_COMMAND (
        OUTPUT ${SLEEF_GENBC_SSE_DP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsimddp.c -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_SSE} -o ${SLEEF_BC_SSE_DP}#.tmp
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_SSE_DP} \"sse_dp\" ${SLEEF_BC_SSE_DP}
        DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsimddp.c ${LLVM_TOOL_CLANG}
      )
    ENDIF() # !RV_ENABLE_X86
    
    ### vector-length agnostic
    ADD_CUSTOM_COMMAND (
      OUTPUT ${SLEEF_GENBC_VLA_SP}
      COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
      COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefsp.c -O3 -fno-vectorize -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_VLA} -o ${SLEEF_BC_VLA_SP}
      COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_VLA_SP} \"vla_sp\" ${SLEEF_BC_VLA_SP}
      DEPENDS ${RV_LIB_SLEEF_DIR}/sleefsp.c ${LLVM_TOOL_CLANG}
    )
    ADD_CUSTOM_COMMAND (
      OUTPUT ${SLEEF_GENBC_VLA_DP}
      COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
      COMMAND ${LLVM_TOOL_CLANG} ${RV_LIB_SLEEF_DIR}/sleefdp.c -O3 -fno-vectorize -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} ${FLAGS_VLA} -o ${SLEEF_BC_VLA_DP}
      COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${SLEEF_GENBC_VLA_DP} \"vla_dp\" ${SLEEF_BC_VLA_DP}
      DEPENDS ${RV_LIB_SLEEF_DIR}/sleefdp.c ${LLVM_TOOL_CLANG}
    )
    
    IF (RV_ENABLE_CRT) 
      # look for compiler-rt source folder
      get_compiler_rt_path(CRT_SOURCES)
    
      Message("-- rv: Building compiler-rt BC libs using ${CRT_SOURCES}.")
    
      SET ( CRT_INC "${CRT_SOURCES}/lib/builtins" )
      IF (NOT (IS_DIRECTORY ${CRT_INC}))
        message(FATAL_ERROR "--rv: RV_ENABLE_CRT options requires compiler-rt sources to be available!")
      ENDIF()
    
      # compiler-rt runtime library
      ADD_CUSTOM_COMMAND (
        OUTPUT ${CRT_GENBC}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${RV_LIB_SLEEF_OUT_DIR}
        COMMAND ${LLVM_TOOL_CLANG} ${RV_SOURCE_DIR}/vecmath/crt.c -I${CRT_INC} -m64 -emit-llvm -c -Wall -Wno-unused ${RVLIB_BUILD_OPTS} -o ${CRT_BC}
        COMMAND ${PYTHON_EXECUTABLE} ${GENCPP_TOOL} ${CRT_GENBC} \"crt\" ${CRT_BC}
        DEPENDS ${RV_SOURCE_DIR}/vecmath/crt.c ${LLVM_TOOL_CLANG}
      )
    ELSE()  # !RV_ENABLE_CRT
      Message("-- rv: Building without compiler-rt BC libs.")
    ENDIF()

  ENDIF() # RV_REBUILD_GENBC
  
  add_custom_target(sleef_genbc DEPENDS ${RV_GENBC_SOURCES})
  SET(RV_SLEEF_OBJECTS ${RV_GENBC_SOURCES} PARENT_SCOPE)

ELSE() # !RV_ENABLE_SLEEF
    Message("-- rv: Buildling without SLEEF.")
ENDIF()
