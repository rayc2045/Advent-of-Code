# Day 7: No Space Left On Device

You can hear birds chirping and raindrops hitting leaves as the expedition proceeds. Occasionally, you can even hear much louder sounds in the distance; how big do the animals get out here, anyway?

The device the Elves gave you has problems with more than just its communication system. You try to run a system update:

```console
$ system-update --please --pretty-please-with-sugar-on-top
Error: No space left on device
```

Perhaps you can delete some files to make space for the update?

You browse around the filesystem to assess the situation and save the resulting terminal output (your puzzle input). For example:

```console
$ cd /
$ ls
dir a
14848514 b.txt
8504156 c.dat
dir d
$ cd a
$ ls
dir e
29116 f
2557 g
62596 h.lst
$ cd e
$ ls
584 i
$ cd ..
$ cd ..
$ cd d
$ ls
4060174 j
8033020 d.log
5626152 d.ext
7214296 k
```

The filesystem consists of a tree of files (plain data) and directories (which can contain other directories or files). The outermost directory is called `/`. You can navigate around the filesystem, moving into or out of directories and listing the contents of the directory you're currently in.

Within the terminal output, lines that begin with `$` are *commands you executed*, very much like some modern computers:

- `cd` means *change directory*. This changes which directory is the current directory, but the specific result depends on the argument:
  - `cd x` moves *in* one level: it looks in the current directory for the directory named `x` and makes it the current directory.
  - `cd ..` moves *out* one level: it finds the directory that contains the current directory, then makes that directory the current directory.
  - `cd /` switches the current directory to the outermost directory, `/`.
- `ls` means *list*. It prints out all of the files and directories immediately contained by the current directory:
  - `123 abc` means that the current directory contains a file named `abc` with size `123`.
  - `dir xyz` means that the current directory contains a directory named `xyz`.

Given the commands and output in the example above, you can determine that the filesystem looks visually like this:

```console
- / (dir)
  - a (dir)
    - e (dir)
      - i (file, size=584)
    - f (file, size=29116)
    - g (file, size=2557)
    - h.lst (file, size=62596)
  - b.txt (file, size=14848514)
  - c.dat (file, size=8504156)
  - d (dir)
    - j (file, size=4060174)
    - d.log (file, size=8033020)
    - d.ext (file, size=5626152)
    - k (file, size=7214296)
```

Here, there are four directories: `/` (the outermost directory), `a` and `d` (which are in `/`), and `e` (which is in `a`). These directories also contain files of various sizes.

Since the disk is full, your first step should probably be to find directories that are good candidates for deletion. To do this, you need to determine the *total size* of each directory. The total size of a directory is the sum of the sizes of the files it contains, directly or indirectly. (Directories themselves do not count as having any intrinsic size.)

The total sizes of the directories above can be found as follows:

- The total size of directory `e` is *584* because it contains a single file `i` of size 584 and no other directories.
- The directory `a` has total size *94853* because it contains files `f` (size 29116), `g` (size 2557), and `h.lst` (size 62596), plus file `i` indirectly (`a` contains `e` which contains `i`).
- Directory `d` has total size *24933642*.
- As the outermost directory, `/` contains every file. Its total size is *48381165*, the sum of the size of every file.

To begin, find all of the directories with a total size of *at most 100000*, then calculate the sum of their total sizes. In the example above, these directories are `a` and `e`; the sum of their total sizes is `95437` (94853 + 584). (As in this example, this process can count files more than once!)

Find all of the directories with a total size of at most 100000. *What is the sum of the total sizes of those directories?*

## JavaScript Solution

```js
const input = `
$ cd /
$ ls
dir a
14848514 b.txt
8504156 c.dat
dir d
$ cd a
$ ls
dir e
29116 f
2557 g
62596 h.lst
$ cd e
$ ls
584 i
$ cd ..
$ cd ..
$ cd d
$ ls
4060174 j
8033020 d.log
5626152 d.ext
7214296 k
`;

Array.prototype.insert = function (index, value) {
  if (index === -1) return this.push(value);
  if (index < -1) index++;
  this.splice(index, 0, value);
};

function getFileTree(log) {
  let [tabNum, insertIdx] = [1, 0];
  const [resultArr, tab] = [['- / (dir)'], '  '];

  log.trim().split('\n').filter(line => !line.startsWith('$ ls')).forEach(line => {
    if (line.startsWith('$ cd /')) return (tabNum = 1);
    if (line.startsWith('$ cd ..')) return tabNum--;
    if (line.startsWith('$ cd')) {
      const dirName = line.replace('$ cd ', '');
      tabNum++;
      const index = resultArr.indexOf(
        resultArr.find(item => item.includes(`- ${dirName} (dir)`))
      );
      return (insertIdx = index);
    }

    if (line.startsWith('dir ')) {
      const dirName = line.replace('dir ', '');
      return resultArr.insert(insertIdx, `${tab.repeat(tabNum)}- ${dirName} (dir)`);
    }

    const splits = line.split(' ');
    const [fileName, fileSize] = [splits[1], splits[0]];
    resultArr.insert(insertIdx, `${tab.repeat(tabNum)}- ${fileName} (file, size=${fileSize})`);
  });

  return resultArr.reverse().join('\n');
}

function getDirTreeWithSize(fileTree) {
  const [resultArr, dirs, splitWord] = [[], [], '(dir, size='];
  let level = 0;

  fileTree.split('\n').forEach(item => {
    const currentLevel = item.indexOf('-') / 2;
    if (currentLevel < level) dirs.pop();
    level = currentLevel;

    if (item.includes('(dir)')) {
      dirs.push(item.replace('-', '').replace('(dir)', '').trim());
      return resultArr.push(item.replace('(dir)', splitWord));
    }

    const size = Number(item.split('(file, size=')[1].replace(')', ''));

    for (const dir of dirs) {
      const index = resultArr.indexOf(
        resultArr.find(item => item.includes(`- ${dir}`))
      );
      const splits = resultArr[index].split(splitWord);
      resultArr[index] = `${splits[0]}${splitWord}${Number(splits[1].replace(')', '')) + size})`;
    };
  });
  return resultArr.join('\n');
}

console.log(
  getDirTreeWithSize(getFileTree(input))
    .split('\n')
    .map(item => Number(item.split('(dir, size=')[1].replace(')', '')))
    .filter(size => size < 100_000)
    .reduce((acc, cur) => acc + cur) // 95437
);
```