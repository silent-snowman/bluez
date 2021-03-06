cmake_minimum_required(VERSION 2.8.3)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(Python_ADDITIONAL_VERSIONS 2.7 2.6 2.5)
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
set(Bluez_LIB bluez/lib)
set(Bluez_SHARED bluez/src/shared)

include_directories(bluez)
include_directories(bluez/lib)
include_directories(bluez/src/shared)

set( CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC" )
set( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fPIC" )

add_library(blueznative STATIC
  ${Bluez_LIB}/uuid.c
  ${Bluez_SHARED}/gatt-client.c
  ${Bluez_SHARED}/gatt-db.c
  ${Bluez_SHARED}/gatt-helpers.c
  ${Bluez_SHARED}/mainloop.c
  ${Bluez_SHARED}/timeout-mainloop.c
  ${Bluez_SHARED}/io-mainloop.c
  ${Bluez_SHARED}/att.c
  ${Bluez_SHARED}/gap.c
  ${Bluez_SHARED}/mgmt.c
  ${Bluez_SHARED}/queue.c
  ${Bluez_SHARED}/crypto.c
  ${Bluez_SHARED}/util.c
  linux/MainLoop.h
  linux/MainLoop.cpp
  linux/BtAdapter.h
  linux/BtAdapter.cpp
  linux/BleAdvertisement.h
  linux/BleAdvertisement.cpp
  linux/GattUtilities.h
  linux/GattUtilities.cpp
  linux/GattClient.h
  linux/GattClient.cpp
  linux/GattService.h
  linux/GattService.cpp
  linux/GattCharacteristic.h
  linux/GattCharacteristic.cpp
  linux/GattDescriptor.h
  linux/GattDescriptor.cpp
)
target_link_libraries(blueznative bluetooth)

find_package(PythonInterp)
find_package(PythonLibs)
find_package(Boost COMPONENTS python thread)

include_directories(${Boost_INCLUDE_DIRS} ${PYTHON_INCLUDE_DIRS})
include_directories(linux)

python_add_module(blueberrypy python_bindings/blueberrypy.cpp)
target_link_libraries(blueberrypy
  blueznative
  ${Boost_LIBRARIES}
  ${PYTHON_LIBRARIES}
)

file(COPY blueberrypyhelper.py DESTINATION .)
file(COPY exampleScanner.py DESTINATION .)
file(COPY exampleClient.py DESTINATION .)
