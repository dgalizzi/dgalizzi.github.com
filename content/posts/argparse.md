---
title: "Fish shell argparse examples"
date: 2021-05-23
draft: false
---

  While the [documentation](https://fishshell.com/docs/current/cmds/argparse.html) is pretty good, it doesn't have many examples.
  
  So I wrote some basic examples for reference.


### Short/Long flag
```fish
#!/usr/bin/env fish

argparse 'v/version' -- $argv

if set -q _flag_version
  echo 'version 1.0.0'
else
  echo 'no version flag passed'
end
```

Result:
```sh
$ ./argtest.fish -v
version 1.0.0
$ ./argtest.fish --version
version 1.0.0
$ ./argtest.fish
no version flag passed
```

### Short/Long parameter
```fish
#!/usr/bin/env fish

argparse 'i/input=' -- $argv

if set -q _flag_input
  echo 'input parameter passed:'$_flag_input
else
  echo 'no input parameter passed'
end
```

Result:
```sh
$ ./argtest.fish -i foo
input parameter passed:foo
$ ./argtest.fish --input foo
input parameter passed:foo
$ ./argtest.fish --input=foo
input parameter passed:foo
$ ./argtest.fish
no input parameter passed
```

