# CMake project file for feff8.5light
# Based on example taken from https://cmake.org/Wiki/CMakeForFortranExample

cmake_minimum_required (VERSION 2.6)
project (feff8.5light)
enable_language (Fortran)

# make sure that the default is a RELEASE
if (NOT CMAKE_BUILD_TYPE)
  set (CMAKE_BUILD_TYPE RELEASE CACHE STRING
      "Choose the type of build, options are: None Debug Release."
      FORCE)
endif (NOT CMAKE_BUILD_TYPE)

# default installation
get_filename_component (default_prefix "." ABSOLUTE)
set (CMAKE_INSTALL_PREFIX ${default_prefix} CACHE STRING
      "Choose the installation directory; by default it installs in the top-level directory."
      FORCE)

# FFLAGS depend on the compiler
get_filename_component (Fortran_COMPILER_NAME ${CMAKE_Fortran_COMPILER} NAME)

MESSAGE("CMAKE_Fortran_COMPILER=${CMAKE_Fortran_COMPILER}")
MESSAGE("Fortran_COMPILER_NAME=${Fortran_COMPILER_NAME}")

if (Fortran_COMPILER_NAME MATCHES "gfortran.*")
  # gfortran
  if (DEFINED ${MKLROOT})
      set (CMAKE_Fortran_FLAGS_RELEASE "-funroll-all-loops -fno-f2c -O3  -m64 -I${MKLROOT}/include -Wl,--no-as-needed -L${MKLROOT}/lib/intel64 -lmkl_gf_lp64 -lmkl_core -lmkl_sequential -lpthread -lm -ldl")
  else(DEFINED ${MKLROOT})
      set (CMAKE_Fortran_FLAGS_RELEASE "-funroll-all-loops -fno-f2c -O3")
  endif(DEFINED ${MKLROOT})
  set (CMAKE_Fortran_FLAGS_DEBUG   "-fno-f2c -O0 -g")
elseif (Fortran_COMPILER_NAME MATCHES "ifort.*")
  set (CMAKE_Fortran_FLAGS_RELEASE "-f77rtl -O3 -m64 -I${MKLROOT}/include -L${MKLROOT}/lib/intel64 -lmkl_intel_lp64 -lmkl_core -lmkl_sequential -lpthread -lm -ldl")
  set (CMAKE_Fortran_FLAGS_DEBUG   "-f77rtl -O0 -g")
elseif (Fortran_COMPILER_NAME MATCHES "g77")
  # g77
  set (CMAKE_Fortran_FLAGS_RELEASE "-funroll-all-loops -fno-f2c -O3 -m32")
  set (CMAKE_Fortran_FLAGS_DEBUG   "-fno-f2c -O0 -g -m32")
else (Fortran_COMPILER_NAME MATCHES "gfortran.*")
  message ("CMAKE_Fortran_COMPILER full path: " ${CMAKE_Fortran_COMPILER})
  message ("Fortran compiler: " ${Fortran_COMPILER_NAME})
  message ("No optimized Fortran compiler flags are known, we just try -O2...")
  set (CMAKE_Fortran_FLAGS_RELEASE "-O2")
  set (CMAKE_Fortran_FLAGS_DEBUG   "-O0 -g")
endif (Fortran_COMPILER_NAME MATCHES "gfortran.*")


# build executables
add_executable ("feff85L" "src/feff85L.f")

# install executables and scripts
install (TARGETS "feff85L" RUNTIME DESTINATION "bin")
