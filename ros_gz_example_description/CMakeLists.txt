cmake_minimum_required(VERSION 3.5)

project(ros_gz_example_description)

find_package(ament_cmake REQUIRED)
find_package(xacro REQUIRED)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

# Xacro fies
file(GLOB xacro_files ${CMAKE_CURRENT_SOURCE_DIR}/models/*/model.sdf.xacro)

foreach(it ${xacro_files})
  # remove .xacro extension
  string(REGEX MATCH "(.*)[.]xacro$" unused ${it})
  set(output_filename ${CMAKE_MATCH_1})

  # create a rule to generate ${output_filename} from {it}
  xacro_add_xacro_file(${it} ${output_filename})

  list(APPEND world_files ${output_filename})
endforeach(it)

# add an abtract target to actually trigger the builds
add_custom_target(media_files ALL DEPENDS ${world_files})

install(
  DIRECTORY
    models/
  DESTINATION share/${PROJECT_NAME}/models
)

ament_environment_hooks("${CMAKE_CURRENT_SOURCE_DIR}/hooks/${PROJECT_NAME}.dsv.in")
ament_environment_hooks("${CMAKE_CURRENT_SOURCE_DIR}/hooks/${PROJECT_NAME}.sh.in")

ament_package()
