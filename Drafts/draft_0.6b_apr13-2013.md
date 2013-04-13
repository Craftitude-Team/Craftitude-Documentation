﻿# Craftitude Repository Documentation
Gist Draft *Apr 13 2013, Version 0.6b*
by Icedream (Carl Kittelberger)

Filesystem Tree
===============

```

[DIR] repositoryname
	[DIR] <minecraft version>
		[FILE] packages.yml*
		[FILE] packages.yml?since=<date>*
		[FILE] packages.yml.gz*
		[FILE] packages.yml.gz?since=<date>*
		[FILE] packages.yml.bz2*
		[FILE] packages.yml.bz2?since=<date>*
		[DIR] packages
			[DIR] <platform-dependent package name>
				[FILE] metadata.yml
				[DIR] <version>
					[FILE] version.yml
					[DIR] <platform: windows/osx/linux/all>
						[FILE] instructions.lua
					[DIR] <platform>
						[FILE] instructions.lua
					...
				[DIR] <version>
					[FILE] version.yml
					[DIR] <platform>
						[FILE] instructions.lua
					[DIR] <platform>
						[FILE] instructions.lua
					...
				...
			[DIR] <platform-independent package name>
				[FILE] metadata.yml
				[DIR] <version>
					[FILE] version.yml
					[DIR] all
						[FILE] instructions.lua
				[DIR] <version>
					[FILE] version.yml
					[DIR] all
						[FILE] instructions.lua
				...
			...
	...
...

* This file is recommended to be generated dynamically either by cron (every day
  at least) or per request (using some weak sort of caching at least) from other
  available data in the repository directory.

```

Distribution Packages file (``packages.yml``)
=====================================

The ``packages.yml`` file is a YAML 1.2-compatible file. It's contents are:

- All packages in the current repository
	o Package metadata
	o Available version IDs
	o Reference to most current version ID

Note: ``packages.yml.gz`` is a GZIP-compressed copy or mirror of this file.
Note: ``packages.yml.bz`` is a BZIP2-compressed copy or mirror of this file.

Typical plain content:

```yaml
---
Package1:
  Metadata:
    # metadata.yml content copy here
  CurrentVersion: Current version ID here
  Versions:
    # All version IDs available for this package should be listed here
Package2:
  # repeat
...
```

Recommendation: Generate this file dynamically when needed (by request) or in a periodic time interval (by cron) from the available Package Metadata files.

Distribution Packages Patch file (``packages.yml?since=...``)
=============================================================

This file can be generated by a script sorting out only the updated packages
since a specific date and only transfer those to the client. This is more
efficient than downloading the full contents every time something changes in
general.

Note: ``packages.yml.gz?since=...`` is a GZIP-compressed copy or mirror of this file.
Note: ``packages.yml.bz?since=...`` is a BZIP2-compressed copy or mirror of this file.

The contents and the typical plain content are equal to those of the complete
Distribution Packages file, though the file will only contain packages updated
since the specified date.

In case the server doesn't support the since parameter and therefore can't serve
updates as patches, the client will just use a fallback and compare all cached
entries with those which the server sends (which is every package with every
metadata as it accesses the full ``packages.yml``).

Parameters:
- "since":
	Specifies the date until when packages should be seen as "old" and should
	not be transferred anymore. The date must be formatted after ISO 8601
	standards.

Recommendation: Generate this file dynamically when needed (by request) or in a periodic time interval (by cron) from the available Package Metadata files.

Package Metadata file (``metadata.yml``)
========================================

This file must contain all essential data to identify the package and its
properties. It must be YAML 1.2-compatible. Contents are as following:

- "Name": The full name of the package, can also be seen as package title.
- "Description": The description of what the package contains, does or implies.
	Should usually not exceed 2048 chars.
- "Maintainers": A list of package maintainers.
- "Developers": A list of developers of the files within the package.
- "License": Can be either a url to, the name of or the text of a license, or a
	combination of them, delimited by a semicolon and a space ("; ").

Example:

```yaml
---
Name: Craftitude Test Package
Description: |
  This is a simple multiline description.
  
  It even supports empty lines.
UpdateTimestamp: 2013-04-08T09:00:00+02:00
Maintainers:
- Icedream
- Railcrafti
Developers:
- Icedream
License: GNU General Public License Version 3; http://opensource.org/licenses/GPL-3.0
Dependencies:
- another-test-package
...
```

Version Metadata file (``version.yml``)
=======================================

This file must contain all essential data to identify a package version and
its properties. It must be YAML 1.2-compatible. Contents are as following:

- "Dependencies" (optional): A list of IDs of all packages on which the current
	package's version depends on.
- "Platforms": Indicates for which platforms the package contains files for.
	Subjected files must be filed under a subfolder with the name equal to the
	platform ID. Possible platforms are "windows", "osx", "linux" and - for
	platform-independent files - "all". Platform-dependent files have
	priority in means of overwriting platform-independent files.
- "UpdateTimestamp": The ISO-formatted time and date of when this version of
    the package has been published.

Example:

```yaml
---
UpdateTimestamp: 2013-04-08T09:00:00+02:00
Dependencies:
- another-test-package
- yet-another-test-package:0.4
Platforms:
- windows # For windows there are specific files
- all
...
```

Package Instruction file (``instructions.lua``)
===============================================

Package Instruction files tell the client how to handle the contents of a
package to install or uninstall it by giving it instructions. Those instructions
are given in form of a LUA script. Following functions are available besides the
basic LUA features available in each standard LUA script:

- DeleteFile(targetfile):
	Deletes <targetfile>.
- DeleteDir(targetdir):
	Deletes <targetdir> and all its subdirectories.
- Move(source, target):
	Moves a directory or a file from <source> to <target>. Can be also used
	as an explicit absolute path rename.
- Rename(source, target):
	Alias to Move(...).
- GetPackageMetadata(propertyname):
	Gets the currently processed package's version's metadata property
	<propertyname>'s value. The return type is the same one served by the YAML
	data of the package metadata.
- GetPackageVersion():
	Gets the currently processed package's version.
- GetResolver(resolvername[, parameters]):
	Fetches a resolver by its name.

All resolvers will have these functions:
- ResolveToStream()
	Resolves to a stream.
- ResolveToArchive()
	Resolves to an archive, this is usally the function you want to call with the last resolver.
- ResolveToString()
	Resolves to a text.

[Resolver documentation following soon.]

Simple example of a typical fully working instruction set.

```lua
function Install()
	-- Download the mod's jar file from a website
	-- and resolve it to a stream for the ZIP resolver
	local download = GetResolver("download", {
		url = "http://somesite.com/downloads/mod.jar"
	}):ResolveToStream()

	-- Resolve it to an archive
	local archive = GetResolver("zip", {
		input = download
	}):ResolveToArchive()
	
	-- unpack all files from the archive into "<basedir>\my_mod_folder\"
	archive:UnpackAllTo("my_mod_folder")
end

function Uninstall()
	-- delete all the content from and the directory "<basedir>\my_mod_folder\" itself
	DeleteFolder("my_mod_folder")
end
```

Security measurements:
- You can not do file operations outside the base target directory (the directory to which the client installs all files of all packages).