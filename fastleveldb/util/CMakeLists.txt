add_executable(arena_test arena_test.cc) 
target_link_libraries(arena_test fastleveldbStatic)

add_executable(bloom_test bloom_test.cc) 
target_link_libraries(bloom_test fastleveldbStatic)

add_executable(cache_test cache_test.cc) 
target_link_libraries(cache_test fastleveldbStatic)

add_executable(coding_test coding_test.cc) 
target_link_libraries(coding_test fastleveldbStatic)

add_executable(env_test env_test.cc) 
target_link_libraries(env_test fastleveldbStatic)

add_executable(hash_test hash_test.cc) 
target_link_libraries(hash_test fastleveldbStatic)

add_executable(crc32c_test crc32c_test.cc) 
target_link_libraries(crc32c_test fastleveldbStatic)