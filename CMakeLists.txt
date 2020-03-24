cmake_minimum_required(VERSION 3.5)

project(wifiheatmap-superbuild CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(ExternalProject)

if(ANDROID)
    ExternalProject_Add(BoostForAndroid
        GIT_REPOSITORY "https://github.com/moritz-wundke/Boost-for-Android.git"
        SOURCE_DIR ${CMAKE_BINARY_DIR}/BoostForAndroid
        CONFIGURE_COMMAND ""
        BINARY_DIR ${CMAKE_BINARY_DIR}/BoostForAndroid-build
        BUILD_COMMAND sh -c "[ ! \"$(ls -A ${CMAKE_BINARY_DIR}/BoostForAndroid-build)\" ] && cd ${CMAKE_BINARY_DIR}/BoostForAndroid-build   && ${CMAKE_BINARY_DIR}/BoostForAndroid/build-android.sh --boost=1.69.0 --arch=${ANDROID_ABI} ${ANDROID_NDK} --with-libraries=thread,system,atomic,chrono,date_time" || echo ""
        INSTALL_COMMAND ""
    )

    #set(BOOST_DIR ${CMAKE_BINARY_DIR}/BoostForAndroid-build/build/out/${ANDROID_ABI})
    #set(Boost_INCLUDE_DIR ${BOOST_DIR}/include/boost-1_69)
    #set(Boost_LIBRARY_DIRS ${BOOST_DIR}/lib)

    ExternalProject_Add(CGAL
      GIT_REPOSITORY "https://github.com/CGAL/cgal.git"
      GIT_TAG "releases/CGAL-5.0.2"
      SOURCE_DIR ${CMAKE_BINARY_DIR}/CGAL
      CONFIGURE_COMMAND ""
      BUILD_COMMAND ""
      INSTALL_COMMAND ""
#      CMAKE_ARGS
#        -DBoost_DIR=${CMAKE_BINARY_DIR}/BoostForAndroid-build/build/out/${ANDROID_ABI} #v1.70
#        -DCGAL_DISABLE_GMP:BOOL=TRUE
#        -DCGAL_NO_GMP:BOOL=ON
#        -DWITH_CGAL_Core:BOOL=OFF
#        -DWITH_CGAL_Qt5:BOOL=OFF
#        -DWITH_CGAL_ImageIO:BOOL=OFF
      DEPENDS BoostForAndroid
      )

    ExternalProject_Add(quazip
      GIT_REPOSITORY "https://github.com/stachenov/quazip.git"
      GIT_TAG "v0.8.1"
      SOURCE_DIR ${CMAKE_BINARY_DIR}/quazip
      BINARY_DIR ${CMAKE_BINARY_DIR}/quazip-build
      INSTALL_COMMAND ${CMAKE_COMMAND} --build ${CMAKE_BINARY_DIR}/quazip-build --target install -- DESTDIR=${CMAKE_BINARY_DIR}/quazip-install
      PATCH_COMMAND cd ${CMAKE_BINARY_DIR}/quazip/ && patch -p0 < ${CMAKE_CURRENT_SOURCE_DIR}/quazip-config.patch

      CMAKE_ARGS
          -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
          -DANDROID_ABI:STRING=${ANDROID_ABI}
          -DANDROID_BUILD_ABI_${ANDROID_ABI}:BOOL=ON
          -DANDROID_NATIVE_API_LEVEL:STRING=${ANDROID_NATIVE_API_LEVEL}
          -DANDROID_NDK:PATH=${ANDROID_NDK}
          -DANDROID_SDK:PATH=${ANDROID_SDK}
          -DANDROID_STL:STRING=${ANDROID_STL}

          -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
          -DCMAKE_CXX_FLAGS:STRING=${CMAKE_CXX_FLAGS}
          -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}

          -DCMAKE_C_COMPILER:PATH=${CMAKE_C_COMPILER}
          -DCMAKE_FIND_ROOT_PATH:PATH=${CMAKE_FIND_ROOT_PATH}
          -DCMAKE_PREFIX_PATH:PATH=${CMAKE_PREFIX_PATH}
          -DCMAKE_TOOLCHAIN_FILE:FILEPATH=${CMAKE_TOOLCHAIN_FILE}

          -DQt5Core_DIR=${Qt5Core_DIR}
      )
endif()

message("===")
message("===")
if(ANDROID)
    message("Boost_INCLUDE_DIR:PATH=${CMAKE_BINARY_DIR}/BoostForAndroid-build/build/out/${ANDROID_ABI}/include/boost-1_69")
    message("CGAL_DIR:PATH=${CMAKE_BINARY_DIR}/CGAL")
    message("QuaZip5_DIR:PATH=${CMAKE_BINARY_DIR}/quazip-install/usr/local/lib/cmake/QuaZip5")
    message("CGAL_DISABLE_GMP:BOOL=ON")
    message("CGAL_NO_GMP:BOOL=ON")
    message("WITH_CGAL_Core:BOOL=OFF")
    message("WITH_CGAL_Qt5:BOOL=OFF")
    message("WITH_CGAL_ImageIO:BOOL=OFF")
endif()
message("===")
message("===")

ExternalProject_Add(wifiheatmap
  PREFIX wifiheatmap
  SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../wifiheatmap-source"
  BINARY_DIR "${CMAKE_CURRENT_BINARY_DIR}/wifiheatmap-build"
  INSTALL_COMMAND ""
  CMAKE_CACHE_ARGS
    #-DBoost_DIR:PATH=${CMAKE_BINARY_DIR}/BoostForAndroid-build/build/out/${ANDROID_ABI} #v1.70
    -DBoost_INCLUDE_DIR:PATH=${CMAKE_BINARY_DIR}/BoostForAndroid-build/build/out/${ANDROID_ABI}/include/boost-1_69

    -DCGAL_DIR:PATH=${CMAKE_BINARY_DIR}/CGAL
    -DCGAL_DISABLE_GMP:BOOL=TRUE
    -DCGAL_NO_GMP:BOOL=ON
    -DWITH_CGAL_Core:BOOL=OFF
    -DWITH_CGAL_Qt5:BOOL=OFF
    -DWITH_CGAL_ImageIO:BOOL=OFF

    -DQuaZip5_DIR:PATH=${CMAKE_BINARY_DIR}/quazip-install/usr/local/lib/cmake/QuaZip5

    -DANDROID:BOOL=${ANDROID}
    -DANDROID_ABI:STRING=${ANDROID_ABI}
    -DANDROID_BUILD_ABI_${ANDROID_ABI}:BOOL=ON
    -DANDROID_NATIVE_API_LEVEL:STRING=${ANDROID_NATIVE_API_LEVEL}
    -DANDROID_NDK:PATH=${ANDROID_NDK}
    -DANDROID_SDK:PATH=${ANDROID_SDK}
    -DANDROID_STL:STRING=${ANDROID_STL}

    -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
    -DCMAKE_CXX_FLAGS:STRING=${CMAKE_CXX_FLAGS}
    -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}

    -DCMAKE_C_COMPILER:PATH=${CMAKE_C_COMPILER}
    -DCMAKE_FIND_ROOT_PATH:PATH=${CMAKE_FIND_ROOT_PATH}
    -DCMAKE_PREFIX_PATH:PATH=${CMAKE_PREFIX_PATH}
    -DCMAKE_TOOLCHAIN_FILE:FILEPATH=${CMAKE_TOOLCHAIN_FILE}

    -DQt5_DIR:PATH=${Qt5_DIR}
  DEPENDS quazip CGAL BoostForAndroid
)
