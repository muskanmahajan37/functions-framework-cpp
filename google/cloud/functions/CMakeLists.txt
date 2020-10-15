# ~~~
# Copyright 2020 Google LLC
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
# ~~~

include(BuildMetadata)
configure_file(internal/build_info.cc.in internal/build_info.cc)

configure_file(internal/version_info.h.in
        ${CMAKE_CURRENT_SOURCE_DIR}/internal/version_info.h)

add_library(googleapis_functions_framework
    # cmake-format: sort
        ${CMAKE_CURRENT_BINARY_DIR}/internal/build_info.cc
        internal/build_info.h
        internal/compiler_info.cc
        internal/compiler_info.h
        internal/version_info.h
        version.cc
        version.h)
target_include_directories(googleapis_functions_framework PUBLIC ${PROJECT_SOURCE_DIR})

if (BUILD_TESTING)
    find_package(GTest CONFIG REQUIRED)
    set(googleapis_functions_framework_unit_tests
            # cmake-format: sort
            internal/compiler_info_test.cc
            version_test.cc)

    foreach (fname ${googleapis_functions_framework_unit_tests})
        string(REPLACE "/" "_" target "${fname}")
        string(REPLACE ".cc" "" target "${target}")
        add_executable("${target}" ${fname})
        target_link_libraries(
                ${target}
                PRIVATE googleapis_functions_framework
                GTest::gmock_main GTest::gmock GTest::gtest)
        add_test(NAME ${target} COMMAND ${target})
    endforeach ()

endif ()