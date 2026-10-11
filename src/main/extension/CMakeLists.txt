add_library(
  duckdb_main_extension OBJECT
  extension_alias.cpp extension_helper.cpp extension_install.cpp
  extension_load.cpp extension_loader.cpp)

set(DUCKDB_OPERATOR_PUBLIC_KEY_FILE "" CACHE FILEPATH
    "RSA-2048 PEM public key trusted in addition to DuckDB's extension signing keys")
set(DUCKDB_OPERATOR_PUBLIC_KEY "")
if(DUCKDB_OPERATOR_PUBLIC_KEY_FILE)
  file(READ "${DUCKDB_OPERATOR_PUBLIC_KEY_FILE}" DUCKDB_OPERATOR_PUBLIC_KEY)
  string(STRIP "${DUCKDB_OPERATOR_PUBLIC_KEY}" DUCKDB_OPERATOR_PUBLIC_KEY)
  if(NOT DUCKDB_OPERATOR_PUBLIC_KEY MATCHES
      "^-----BEGIN PUBLIC KEY-----[\r\n]+[A-Za-z0-9+/=\r\n]+-----END PUBLIC KEY-----$")
    message(FATAL_ERROR "DUCKDB_OPERATOR_PUBLIC_KEY_FILE must contain one PEM public key")
  endif()
endif()
configure_file(operator_public_key.hpp.in operator_public_key.hpp @ONLY)
target_include_directories(duckdb_main_extension PRIVATE "${CMAKE_CURRENT_BINARY_DIR}")

# Escape ; in the string to ensure this is treated as a plain string instead of
# a list
string(REPLACE ";" "\;" EXT_DIR_ESCAPED "${EXTENSION_DIRECTORIES}")

target_compile_definitions(
  duckdb_main_extension
  PRIVATE "DUCKDB_EXTENSION_DIRECTORIES=\"${EXT_DIR_ESCAPED}\"")

set(ALL_OBJECT_FILES
    ${ALL_OBJECT_FILES} $<TARGET_OBJECTS:duckdb_main_extension>
    PARENT_SCOPE)
