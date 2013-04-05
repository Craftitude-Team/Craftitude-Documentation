Package Install (``install``) / Uninstall (``uninstall``) Instruction files
===========================================================================

PII/PUI files tell the client how to handle the contents of a package to in-
stall or uninstall it by giving it instructions. Those instructions are given
in form of a LUA script. Following functions are available besides the basic
LUA features available in each standard LUA script:

- SetMetaInfIgnore(value)
  Manipulates behaviour for META-INF data in JAR source archives. <value> can
	be true or false, where true forces META-INF data to be handles as normal
	directories/files and false forces META-INF data to be completely ignored.
- UnpackAllFromSource(targetdir)
	Unpacks all files from the source archive into <targetdir>.
- UnpackAllFromSource(targetdir, filter)
	Unpacks all files from the source archive matching the file filter into
	<targetdir>. The filter has the same form as the glob() function in PHP.
	Example: "*.*" for all files or "*.class" for only files which end with
	  ".class".
- InjectAllFromSource(targetfile)
	Injects all files from the source archive into the archive at <targetfile>.
- InjectAllFromSource(targetfile, filter)
	Injects all files from the source archive matching the glob filter into the
	archive at <targetfile>.
- DeleteFile(targetfile)
	Deletes <targetfile>.
- DeleteDir(targetdir)
	Deletes <targetdir> and all its subdirectories.
- Move(source, target)
	Moves a directory or a file from <source> to <target>. Can be also used
	as an explicit absolute path rename.
- Rename [not implemented yet]
- GetPackageMetadata(propertyname)
	Gets the currently processed package's version's metadata property
	<propertyname>'s value. The return type is the same one served by the YAML
	data of the package metadata.
- GetPackageVersion()
	Gets the currently processed package's version.
- GetBaseDir()
	Gets the base directory into which the client installs all files.

Simple example of a typical fully working install instruction:

```lua
UnpackAllFromSource(GetBaseDir()) -- unpack all source files into base dir
```