# UNRAR SOURCES

set(UNRAR_SOURCE_BASE
  archive.cpp
  arcread.cpp
  blake2s.cpp
  cmddata.cpp
  consio.cpp
  crc.cpp
  crypt.cpp
  encname.cpp
  errhnd.cpp
  extinfo.cpp
  extract.cpp
  filcreat.cpp
  file.cpp
  filefn.cpp
  filestr.cpp
  find.cpp
  getbits.cpp
  global.cpp
  hash.cpp
  headers.cpp
  isnt.cpp
  list.cpp
  match.cpp
  options.cpp
  pathfn.cpp
  qopen.cpp
  rar.cpp
  rarpch.cpp
  rarvm.cpp
  rawread.cpp
  rdwrfn.cpp
  recvol.cpp
  resource.cpp
  rijndael.cpp
  rs.cpp
  rs16.cpp
  scantree.cpp
  secpassword.cpp
  sha1.cpp
  sha256.cpp
  smallfn.cpp
  strfn.cpp
  strlist.cpp
  system.cpp
  threadpool.cpp
  timefn.cpp
  ui.cpp
  unicode.cpp
  unpack.cpp
  volume.cpp
)

add_executable(unrar ${UNRAR_SOURCE_BASE} unrar.rc unrar.manifest)
add_library(unrardll SHARED ${UNRAR_SOURCE_BASE} dll.cpp dll.def dll.rc)
set_target_properties(unrardll PROPERTIES OUTPUT_NAME unrar)
target_compile_definitions(unrardll PRIVATE -DRARDLL -DSILENT)

install(TARGETS unrar DESTINATION bin)
install(TARGETS unrardll DESTINATION bin)
install(FILES license.txt DESTINATION share)