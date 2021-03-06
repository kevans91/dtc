message(STATUS "Adding tests")
file(GLOB TESTS "*.dts")
file(GLOB FILECHECK_TESTS "*.dts.fc")

foreach(TEST ${TESTS})
	get_filename_component(TEST_NAME ${TEST} NAME_WE)
	add_test(${TEST_NAME}_expected "${CMAKE_CURRENT_SOURCE_DIR}/testexpected.sh" "${CMAKE_BINARY_DIR}/dtc" ${TEST})
	add_test(${TEST_NAME}_roundtrip "${CMAKE_CURRENT_SOURCE_DIR}/testviabin.sh" "${CMAKE_BINARY_DIR}/dtc" ${TEST})
endforeach()

find_program(FILECHECK names "FileCheck")
if(FILECHECK STREQUAL "FILECHECK-NOTFOUND")
	message(WARNING "FileCheck not found, disabling FileCheck tests")
	message(WARNING "Set FILECHECK to the path to FileCheck to enable them")
else()
	foreach(TEST ${FILECHECK_TESTS})
		get_filename_component(TEST_NAME ${TEST} NAME_WE)
		add_test(${TEST_NAME}_filecheck "${CMAKE_CURRENT_SOURCE_DIR}/testfilecheck.sh" "${CMAKE_BINARY_DIR}/dtc" ${TEST} ${FILECHECK})
	endforeach()
endif()
