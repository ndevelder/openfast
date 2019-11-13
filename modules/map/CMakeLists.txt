#
# Copyright 2016 National Renewable Energy Laboratory
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

if(WIN32 OR CYGWIN OR MINGW)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DMAP_DLL_EXPORTS -DCMINPACK_NO_DLL -DNDEBUG -D_WINDOWS -D_USRDLL")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DMAP_DLL_EXPORTS -DCMINPACK_NO_DLL -DNDEBUG -D_WINDOWS -D_USRDLL")
endif()

if (GENERATE_TYPES)
  generate_f90_types(src/MAP_Registry.txt ${CMAKE_CURRENT_LIST_DIR}/src/MAP_Types.f90 -ccode)
endif()

file(GLOB MAP_CLIB_SOURCES src/*.c src/*.cc src/*/*.c src/*/*.cc)
file(GLOB MAP_C_HEADERS src/*.h src/*/*.h)

add_library(mapcpplib ${MAP_CLIB_SOURCES} src/MAP_Types.f90)
target_include_directories(mapcpplib PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/bstring>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/cminpack>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/lapack>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/simclist>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>)
target_link_libraries(mapcpplib nwtclibs)

add_library(maplib
  src/map.f90
  src/MAP_Types.f90
)
target_link_libraries(maplib mapcpplib)

install(TARGETS maplib mapcpplib
  EXPORT "${CMAKE_PROJECT_NAME}Libraries"
  RUNTIME DESTINATION lib
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib)

install(FILES
  src/MAP_Types.h
  DESTINATION include)