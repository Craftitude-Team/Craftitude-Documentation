# Craftitude Repository Documentation
Git Draft *Apr 13 2013, Version 0.7b*
by Icedream (Carl Kittelberger)

## Filesystem Tree

This section describes how a complete functioning repository will look like in means of how
the filesystem will be structured.

Note that server systems can add additional files to this system, which won't be noticed by
the client in any way, for example a metadata.yml for each package which can then be parsed
by a server-side script into the packages.yml.

Text prefixed with "#" is only there for commenting reasons. "..." indicates a repeating
pattern which may be continued.

```
[DIR] repositoryname
	[DIR] <minecraft version>
		[FILE] packages.yml
		[FILE] packages.yml.gz
		[FILE] packages.yml.bz2
		[DIR] patches
			# This directory will contain patches of packages.yml. Example:
			[FILE] packages_0_to_1.patch
			[FILE] packages_1_to_2.patch
			...
		[DIR] packages
			[DIR] <package name> # at least one of these
				[DIR] <version> # at least one of these
					[FILE] instructions.lua
				...
			...
	...
...
```

## Distribution Packages file (``packages.yml``)

The ``packages.yml`` file is a YAML-compatible file. It's contents are:

- The current repository version (a number)
- All packages in the current repository
	- Package metadata
	- Available version IDs
	- Reference to most current version ID

Note: ``packages.yml.gz`` is a GZIP-compressed copy or mirror of this file.
Note: ``packages.yml.bz`` is a BZIP2-compressed copy or mirror of this file.

Typical plain content (old YAML format):

```yaml
Version: 1
Packages:
  Package1:
    # Metadata starts here
    Name: Craftitude Test Package
    Description: |
      This is a simple multiline description.
      It even supports empty lines.
    Maintainers:
    - Icedream
    - Railcrafti
    Developers:
    - Icedream
    License:
      Name: GNU General Public License Version 3
      URL: http://opensource.org/licenses/GPL-3.0
    Platforms:
    - all # supports all platforms
    Dependencies:
    - requires another-test-package
    # Version list with optional metadata changes
    Versions:
      0.5: # top version is the current one
        Dependencies: # this OVERWRITES the parent metadata
        - requires another-test-package
        - suggests yet-another-test-package # this is the new dependency
		Date: 2013-03-03T14:00:00Z
      0.4: [] # no overwrites for this version
      0.3: []
      0.2: []
      0.1: [] # bottom version is the oldest one
  # repeat the same as above for every package...
```

This file can also be in the newer YAML 1.2/higher-format, as the client is supposed to
always support the newest YAML version.

The version should increase by 1 on every update.

The components of the ``packages.yml`` are explained in the sections below:

## Package Metadata

The package metadata is supposed to reveal information about the package's identity,
origin, dependencies, license and compatibility with specific/all platforms.

The following explains the specific parts of a package's metadata further:

### Name

Defines the title or name of a package.

Limitations:

- Is a string.
- Must be a single line.
- Must contain min. 3 characters and max. 128 characters.
- Is _required_ to be included.

### Description

Defines the description of a package.

Limitations:

- Is a string.
- Can be multiline.
- Maximum length is 2048 characters.
- Is _recommended_ to be included.

### Maintainers

Defines a list of people which maintain the package in the current repository.

Limitations:

- Is a list of strings.
- Must contain at least 1 (one) entry.
- No maximum length.
- Is _required_ to be included (see above).

### Developers

Defines a list of people which develop the package's content (for example the mod itself).

Limitations:

- Is a list of strings.
- If defined, should at contain at least 1 (one) entry.
- No maximum length.
- Is _recommended_ to be included (see above).

### License

Defines the license under which the package or - more exact - the package's contents is licensed.
Can be defined as URL to a license, the name of a license or the text of a license.

Limitations:

- Should contain at least one of its subcomponents, if defined. The more subcomponents defined,
  the better a license is described.
- Is _recommended_ to be included (see above).

#### Name

The license's name.

Limitations:

- Is a string
- Should contain max. 128 chars.

#### URL

The license's URL.

Limitations:

- Is a string
- Should contain max. 256 chars.

#### Text

The license's text.

Limitations:

- Is a string
- Should contain max. 8192 chars.
- Is _not recommended_ to be included; instead use Name/URL if possible.

### Platforms

Defines under which platforms this package can be installed.

"All" defines that the package is installable on all of the platforms listed below.
Otherwise, the array must describe each platform it supports by a keyword. Each keyword
is an own entry:

- Windows: Keyword is "windows".
- Mac OS X: Keyword is "osx".
- Unix/Linux: Keyword is "linux".

Limitations:

- Is a list of strings.
- Only values to be recognized by the client are "all" and listed keywords above.
- Is _required_ to be included.

### Date

Defines when a version has been published.

Limitations:

- Is a string.
- Must be conform to ISO 8601.
- Is _required_ to be included.

### Dependencies

Defines - if defined - the packages which this package needs in order to be installed
properly.

The syntax of each entry is as following:

	type-of-dependency package-name[ (=|>|<|>=|<=)version-id]

This means the dependency needs the type of dependency, the package's ID on which to
depend, but you can provide multiple conditions to specify the version(s) which can
be used for dependency.

Following types of dependencies are available:

- "requires": Specifies a package or a package version which is needed for this
  package in order to be properly installed or working.
- "recommends": Specifies a package or a package version which is needed for this
  package to ideally work, but isn't absolutely depended on.
- "suggests": Specifies a package or a package version which could be useful in
  combination with this package.
- "enhances": Specifies a package or a package version which functionality is being
  extended by this package.
- "excludes": Specifies a package or a package version which must not be installed in
  order for this package to be properly installed or working.

The optional version condition contains an operator and a version ID. Operators are
defines as following:

- "=" (equals): Version must be equal to the most recent version found for the version ID.
- ">" (greater than): Version must be higher than the most recent version found for the version ID.
- "<" (less than): Version must be lower than the most recent version found for the version ID.
- ">=" (greater than or equals): Version must be equal or higher than the most recent version found for the version ID.
- "<=" (less than or equals): Version must be equal or lower than the most recent version found for the version ID.

The version ID must be an actually existing version ID of the dependency package.

Examples:

	- requires minecraft-forge					# Requires most recent version of minecraft-forge.
	- requires minecraft-forge =7.7.0.559		# Requires minecraft-forge 7.7.0.559
	- requires minecraft-forge <5.0.0.320		# Requires most recent minecraft-forge before 5.0.0.320
	- suggests modloadermp						# Suggests modloadermp being installed.
	- excludes modloadermp						# This package is not compatible with modloadermp

## Package Versions

"Versions" contains a list of available versions where the newest version is always the first one in the list and the
oldest being the last on the list. Every version itself can contain additional or overwriting metadata which will then
be merged with the package's non-version-specific metadata.

Note: Version-specific metadata won't _edit_ the old metadata, but instead it _overwrites_ or - if not existing yet -
_adds to_ the old metadata.

If no version-specific metadata is provided, use an empty array. See the example.

## Package Instructions file (``instructions.lua``)

Package Instructions files are scripts which are not contained within the packages.yml.
They tell the client how to handle the contents of a package to install or uninstall it
by giving it instructions. Those instructions are given in form of a LUA script.

The client differences between two instruction types:

- Install (for installing packages)
- Uninstall (for uninstalling packages)

Each instruction type is an own function named after the instruction type and
will be called for this specific process

Following functions are available besides the
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
	Fetches a resolver by its name. "parameters" is a hashtable.

All resolvers will have these functions:

- ToStream():
	Resolves to a stream.
- ToArchive():
	Resolves to an archive, this is usally the function you want to call with the last resolver.
- ToString():
	Resolves to a text.

Simple example of a typical fully working instruction set.

```lua
-- Installs the mod
function Install()
	-- Download a file via the download resolver
	var download = GetResolver("download", { url = "http://this.is.a.websi.te/with/an/archive/on/it.zip" })
	 
	-- Convert the download to a usable archive via the archive resolver
	var archive = GetResolver("archive", { input = download })
	 
	-- Install JAR into "mods" folder
	archive:InjectTo("mods/thisisamod.jar")
end

-- Uninstalls the mod
function Uninstall()
	-- Delete JAR from "mods" folder
	DeleteFile("mods/thisisamod.jar")
end
```

Security measurements:
- You can not do file operations outside the base target directory (the directory to which the client installs all files of all packages).

### Resolvers

Following resolvers are available:

#### Download resolver (``download``)
[Subject to be changed without notice.]

The download resolver enables a script to download a file, usually a ZIP or JAR file (the same) to later access it.

Parameters for GetResolver:

- "url": Specifies the URL from which to download.

Functions available after construction:

- ToStream(): Outputs the content of the download as a stream (see C# documentation).
- ToString(): Outputs the content of the download as a string.
- GetUrl(): Outputs the origin URL as a string.
- GetSize(): Outputs the content size in bytes as a long integer.

#### Archive resolver (``archive``)
[Subject to be changed without notice.]

The archive resolver enables a script to convert content into a usable Archive instance, for example to extract ZIP
files.

Entries are in the syntax ``folder/folder2/file``. This means there is no root slash and the directory seperation char
is "/" (unix-style).

Parameters for GetResolver:

- "input": The stream/download resolver to convert.

Properties available after construction:

- "IgnoreMetaInf": Ignore the META-INF folder in JAR files. Default is "false".

Functions available after construction:

- ExtractAll(folderpath[, filter]): Extracts all contents (or filtered out content) to the specified target path. Note: This
  overwrites any already existing files.
- Extract(entry, filepath): Extracts a single entry to the specified file path. Note: This overwrites any already existing
  files.
- InjectAll(filepath[, filter]): Writes all files contained in the archive into the archive file located at the
  specified target path. Note: This does not _overwrite_ the file, but adds/overwrites the _entry within the archive file_.
- Inject(entry, filepath): Writes a single entry contained in the archive into the archive file located at the specified
  target path. Note: This does not _overwrite_ te file, but adds/overwrites the _entry within the archive file_.
- GetEntryList(): Outputs a string array containing all available entries.

#### Mediafire resolver (``mediafire``)
[Subject to be changed without notice.]

The mediafire resolver enables a script to directly download files from mediafire without the need to parse any download
pages, as the backend will do all the dirty work.

Parameters for GetResolver:

- "id": The ID of the download on mediafire.

Properties available after construction:

- "UseContentForString": Don't output the URL via ToString, instead use the download content.

Functions available after construction:

- ToString(): Outputs either the URL or the content as a string, depending on "UseContentForString" property.
- ToStream(): Outputs the content as a stream.

## Patches

Patches are seperated files from ``packages.yml``. Their filenames after going after the format ``packages_<oldversion>_to_<newversion>.patch`` where
always ``newversion = oldversion + 1`` and should be generated automatically after every update in the ``packages.yml``.

All patch files must be in the unified diff format and should be kept as short as possible. If performance suffers, longer versions are okay too, but
they will use up more bandwidth/disk space later on.

Patches which are 100% not needed anymore can be deleted without risks. Should a client fail to apply patches until the newest version of a repository,
the client must be a able to reset its package cache so it downloads all ``packages.yml`` files from all repositories again.
