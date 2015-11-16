add_executable(autocompact_test autocompact_test.cc) 
target_link_libraries(autocompact_test fastleveldbStatic)

add_executable(corruption_test corruption_test.cc) 
target_link_libraries(corruption_test fastleveldbStatic)

add_executable(dbformat_test dbformat_test.cc) 
target_link_libraries(dbformat_test fastleveldbStatic)

add_executable(filename_test filename_test.cc) 
target_link_libraries(filename_test fastleveldbStatic)

add_executable(log_test log_test.cc) 
target_link_libraries(log_test fastleveldbStatic)


add_executable(skiplist_test skiplist_test.cc) 
target_link_libraries(skiplist_test fastleveldbStatic)

add_executable(version_edit_test version_edit_test.cc) 
target_link_libraries(version_edit_test fastleveldbStatic)

add_executable(version_set_test version_set_test.cc) 
target_link_libraries(version_set_test fastleveldbStatic)

add_executable(write_batch_test write_batch_test.cc) 
target_link_libraries(write_batch_test fastleveldbStatic)