Filesystem Tree
===============

[DIR] repositoryname
  [DIR] <minecraft version>
  	[FILE] packages.yml*
		[FILE] packages.yml?since=<date>*
		[FILE] packages.yml.gz*
		[FILE] packages.yml.gz?since=<date>*
		[DIR] packages
			[DIR] <platform-dependent package name>
				[FILE] metadata.yml
				[DIR] <version>
					[DIR] <platform: windows/macosx/solaris/linux>
						[FILE] install
						[FILE] uninstall
						[FILE] source
				[DIR] <version>
					[FILE] install
					[FILE] uninstall
					[FILE] source
				...
			[DIR] <platform-independent package name>
				[DIR] <version>
				  [FILE] metadata
					[FILE] install
					[FILE] uninstall
					[FILE] source
				[DIR] <version>
				  [FILE] metadata
					[FILE] install
					[FILE] uninstall
					[FILE] source
				...
			...
	...
...

* This file is recommended to be generated dynamically either by cron (every day
  at least) or per request (using some weak sort of caching at least) from other
  available data in the repository directory.
