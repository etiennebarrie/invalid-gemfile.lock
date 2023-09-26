# Invalid Gemfile.lock on merges

This repository demonstrates how a Bundler lockfile (`Gemfile.lock`) can become invalid due to a Git merge.

* [e5ddb56](https://github.com/etiennebarrie/invalid-gemfile.lock/commit/e5ddb56) starts with a valid Gemfile, with
  dependencies on `aaa` and `zzz`, `aaa` also depending on `zzz`.
* [583c0c7](https://github.com/etiennebarrie/invalid-gemfile.lock/commit/583c0c7) updates `zzz` to a new version 0.2.0
* [6e82847](https://github.com/etiennebarrie/invalid-gemfile.lock/commit/6e82847) on a different `update-aaa` branch,
  updates `aaa` to a new version which limits the `zzz` dependency to versions strictly lower than 0.2.0
* [557b284](https://github.com/etiennebarrie/invalid-gemfile.lock/commit/557b284) even though `aaa (0.2.0)` and `zzz
  (0.2.0)` are incompatible, Git is able to merge the two changes because the chunks are not overlapping, thanks to the
  `bbb`, `ccc`, and `ddd` dependencies.

This results in an invalid `Gemfile.lock`, and while Bundler outputs a warning, it allows the installation and does not
return an exit status code.

One solution is to use [Git attributes][gitattributes] to disallow all merges to Gemfile.lock. This can be seen in the
[`gitattributes` branch][gitattributes branch], which defines the merge driver for `Gemfile.lock` to be `binary`. This
prevents automatic three-way merges from happening, and results in conflicts during merges instead.

[gitattributes]: https://git-scm.com/docs/gitattributes
[gitattributes branch]: https://github.com/etiennebarrie/invalid-gemfile.lock/tree/gitattributes
