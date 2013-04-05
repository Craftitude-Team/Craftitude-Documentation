Package Metadata file (``metadata.yml``)
========================================

This file must contain all essential data to identify the package and its
properties. Contents are as following:

- "ID" (optional): The ID of the package. If not used, will be automatically
  defaulted to whatever the client finds first, the ID in one of the
	Repository Packages file variants or in the URL.
- "Description": The description of what the package contains, does or implies.
	Should usually not exceed 2048 chars.
- "Maintainers": A list of package maintainers.
- "Developers": A list of developers of the files within the package.
- "License": Can be either a url to, the name of or the text of a license, or a
	combination of them, delimited by a semicolon and a space ("; ").
- "Dependencies" (optional): A list of IDs of all packages on which the current
	package's version depends on.
- "Platform dependency": Indicates whether the package should serve different
	files seperated for each operating system platform. More about
	platform-dependent packages in the respective section Platform-dependent
	packages. Accepted values are "True" and "False".

