# How to bing sqlcpher
You can install from github:  
```
npm install --save --build-from-source https://github.com/liubiggun/node-sqlite3/tarball/master
```
or follow step by step(each step correspond to a tag):

 * Run command 'npm install sqlite3' in your project.
 
 * [tag vSQLCIPHERHOWTO-1] Go to path '.\node_modules\sqlite3\deps', we will modify 'sqlite-autoconf-3090100.tar.gz' and 'sqlite3.gyp'. Now extract 'sqlite-autoconf-3090100.tar.gz' to this directory, rename 'sqlite-autoconf-3090100.tar.gz' to 'sqlite-autoconf-3090100-backup.tar.gz'.Now we have a dirtory 'sqlite-autoconf-3090100' which contains source files of sqlite3.
 
 * [tag vSQLCIPHERHOWTO-2] Go to sqlite source directory('sqlite-autoconf-3090100'), replace source files of sqlite. For now, we can use [source from this](http://download.csdn.net/detail/herorazor/9001997), but unfortunately we can't find the reference of sqlite source files. After download, replace 'sqlite3.h', 'sqlite3.c', 'sqlite3ext.h' with new.
 
 * [tag vSQLCIPHERHOWTO-3] Install openssl for windows because sqlcipher need it. You can [download  from this](http://slproweb.com/products/Win32OpenSSL.html). After install, openssl libraries and directory which contains openssl headers will append to environment variables, however, sometime it maybe not happen. So in this HOWTO, we assume that the compiler can not find the libraries and headers, just like we download two libraries('libeay32.lib', 'ssleay32.lib') and headers, then copy them to the sqlite source directory. Be careful, we should download both x86 libraries and x64 libraries. Now, we add two directories 'OpenSSL-Win32' and 'OpenSSL-Win64' which both contain 'libeay32.lib' and 'ssleay32.lib', and directory 'openssl' which contain headers.
 
 * [tag vSQLCIPHERHOWTO-4] Go to path '..' and compress directory to 'sqlite-autoconf-3090100.tar.gz' 
 
 * [tag vSQLCIPHERHOWTO-5] Modify 'sqlite3.gyp'. We should let gyp know the libraries we need to link and where the libraries are, so we add some config in condition 'OS == "win"':  
  
  before:
  ```
        ['OS == "win"', {
            'defines': [
              'WIN32'
            ],
        }]
  ```  
  after:
  ```
        ['OS == "win"', {
          'defines': [
            'WIN32'
          ],
  		
          'link_setting': {
            'libraries': [
              '-llibeay32.lib',
              '-lssleay32.lib',
            ],
            'lirary_dirs': [
              '<(SHARED_INTERMEDIATE_DIR)/sqlite-autoconf-<@(sqlite_version)/OpenSSL-Win32'
            ]
          
          }
        }]
  ```
  For x64, replace 'OpenSSL-Win32' with 'OpenSSL-Win64'. Fow now, the compiler is able to find libraries.
  
* [tag vSQLCIPHERHOWTO-6] Add some defines in 'target_name': 'sqlite3'  
  
  before:
  ```
        'defines': [
          '_REENTRANT=1',
          'SQLITE_THREADSAFE=1',
          'SQLITE_ENABLE_FTS3',
          'SQLITE_ENABLE_JSON1',
          'SQLITE_ENABLE_RTREE'
        ],
  ```
  after:
  ```
        'defines': [
          '_REENTRANT=1',
          'SQLITE_THREADSAFE=1',
          'SQLITE_ENABLE_FTS3',
          'SQLITE_ENABLE_JSON1',
          'SQLITE_ENABLE_RTREE',
  		
          'SQLITE_HAS_CODEC=1',
          'CODEC_TYPE=CODEC_TYPE_AES128',
          'SQLITE_CORE',
          'THREADSAFE',
          'SQLITE_SECURE_DELETE',
          'SQLITE_SOUNDEX',
          'SQLITE_ENABLE_COLUMN_METADATA'
        ],
  ```
  
  
* Return '$(PROJECT)\\node_modules\sqlite3', run command 'npm i --build-from--source'. If there is an error 'node.lib is corrupt', just download a new lib from [http://nodejs.org/dist/](http://nodejs.org/dist/) and replace it, then use VS201X to open '$(PROJECT)\node_modules\sqlite3\build\binding.sln' and rebuild the solution.
  
