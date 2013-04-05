Distribution Packages file (``packages.yml``)
=====================================

The ``packages.yml`` file is a YAML-compatible file. It's contents are:

- All packages in the current repository
  o Package metadata
  o Available version IDs
  o Reference to most current version ID

Note: ``packages.yml.gz`` is a GZIP-compressed copy or mirror of this file.

Typical plain content:

```yaml
Package1:
  Metadata:
  <Version 1>:
	  # Metadata, look in the Package Metadata file section for the contents
	<Version 2>:
	  # Metadata, look in the Package Metadata file section for the contents
	...
  Current Version: Current version ID here
Package2:
  ...
```

Recommendation: Generate this file dynamically when needed (by request) or in
				a periodic time interval (by cron) from the available Package
				Metadata files.

