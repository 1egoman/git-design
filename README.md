# Git design: convert design files into a format that git can track

If you've ever tried to manage files from sketch or Invision studio in git, you know it's a pain.
Since they are binary files, git doesn't know how to properly diff them and fixing merge conflicts
is practically impossible.

A little known fact about sketch and invision studio files is that they are actually zip archives!
And, when decompressed, they contain a bunch of plain text json that can easily be diffed / merged
using the algorithms that git uses. When I discovered this, I realized that it'd be pretty easy to
use this to write an addon for git that would make managing designs much more reasonable.

## Installation
First, ensure that you have git installed: https://git-scm.com/downloads

Then, copy and paste the below into a terminal:
```sh
curl https://raw.githubusercontent.com/1egoman/git-design/master/git-design > /tmp/git-design && install -m 0755 /tmp/git-design /usr/local/bin
```

Finally, run `git design`

## Usage
`git-design` can do two things: pack or unpack a design file. Unpacking takes a compressed design
file such as a sketch file or an invision studio file and converts it into a directory named
`<FILENAME>.<EXTENSION>-unpacked`. This unpacked file is what you want to check-in - in fact, you'll
probably want to add `*.sketch` and `*.studio` to your `.gitignore` file.

```sh
$ ls
my-file.sketch
$ git design unpack my-file.sketch
 => my-file.sketch
  . meta.json (72d61262d73cc4eaf1b89a179c3dd16f)
  . user.json (93bce8e9fcf388d40a76fba41addc532)
  . pages/B0CF1FDE-8F93-4C3F-9B3D-0871EFDE97EC.json (e32c9614a533c94b5cf0bd7ca84f31ce)
$ ls my-file.sketch-unpacked
document.json   images          meta.json       pages           previews        user.json
$ # Now, commit my-file.sketch-unpacked
```

The `pack` action does the opposite, and is typically what you'd want to do after pulling down new
changes, merging, or doing any other action that modifies the unpacked design file:

```sh
$ ls
my-file.sketch-unpacked
$ git design pack my-file.sketch
 => my-file.sketch
  . meta.json (72d61262d73cc4eaf1b89a179c3dd16f)
  . user.json (93bce8e9fcf388d40a76fba41addc532)
  . pages/B0CF1FDE-8F93-4C3F-9B3D-0871EFDE97EC.json (e32c9614a533c94b5cf0bd7ca84f31ce)
$ ls
my-file.sketch my-file.sketch-unpacked
$ open my-file.sketch
$ # Opened in sketch!
```
