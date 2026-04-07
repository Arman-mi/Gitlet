# Gitlet (mini Git in Java)

Gitlet is a small,  version-control system inspired by Git. It tracks file snapshots via commits, supports branching, and stores all repository state in a `.gitlet/` folder inside the working directory you run it from.



## Features

- Content-addressed storage for file contents (“blobs”) using SHA‑1.
- Snapshot-based commits with parent pointers (`log` walks history).
- Staging area (`add` / `rm`) before committing.
- Branch pointers and switching branches.
- Reset to a previous commit.

## Command-line usage

All commands are invoked as:

```sh
java gitlet.Main <command> [args...]
```

Supported commands (implemented in `gitlet/Main.java` and `gitlet/Repository.java`):

- `init`  
  Create a new `.gitlet/` repository in the current directory.
- `add <file>`  
  Stage a file for the next commit.
- `commit "<message>"`  
  Create a commit from the staging area.
- `rm <file>`  
  Unstage a file, and/or mark a tracked file for removal.
- `restore -- <file>`  
  Restore `<file>` from the current `HEAD` commit.
- `restore <commitId> -- <file>`  
  Restore `<file>` from a specific commit.
- `log`  
  Print commit history starting at `HEAD`.
- `global-log`  
  Print all commits found in `.gitlet/commits/`.
- `find "<message>"`  
  Print commit ids whose message exactly matches.
- `status`  
  Print branches, staged files, removed files, modifications, and untracked files.
- `branch <name>`  
  Create a new branch pointer at the current `HEAD`.
- `switch <name>`  
  Switch the working directory and `HEAD` to another branch.
- `rm-branch <name>`  
  Delete a branch pointer.
- `reset <commitId>`  
  Move current branch + `HEAD` to a specific commit and update the working directory.

## Quickstart example

```sh
# in a folder with some files you want to track
java gitlet.Main init

java gitlet.Main add wug.txt
java gitlet.Main commit "Add wug"

java gitlet.Main log
java gitlet.Main status
```

## Building and running (no build tool required)

This repo is a plain Java project (no Maven/Gradle config checked in). You can compile it with `javac` and run with `java`.

### Windows (PowerShell)

```powershell
mkdir out -ErrorAction SilentlyContinue | Out-Null
javac -d out (Get-ChildItem gitlet\*.java)
java -cp out gitlet.Main init
```

### macOS/Linux

```sh
mkdir -p out
javac -d out gitlet/*.java
java -cp out gitlet.Main init
```

## Repository layout

When you run `init`, Gitlet creates a `.gitlet/` directory in your current working directory containing:

- `.gitlet/commits/`: serialized `Commit` objects, named by commit id (SHA‑1 of the serialized commit)
- `.gitlet/blobs/`: file contents, named by SHA‑1 of the file bytes
- `.gitlet/staging/stagingArea`: serialized `HashMap<String, String>` staging map (`filename -> blobId`, or `null` for removal)
- `.gitlet/branches/`: one file per branch; each contains that branch’s head commit id
- `.gitlet/HEAD`: current head commit id
- `.gitlet/currentBranch`: the current branch name

Tip: if you use Git to version this project, you probably want to add `.gitlet/` to your `.gitignore` in any directory where you run Gitlet.

## Running the included tests (JUnit 4)

There’s a large JUnit test suite in `GitletTests.java`. The repo also includes JUnit jars under `Library/`.

On Windows (PowerShell), one way to run tests is:

```powershell
mkdir out -ErrorAction SilentlyContinue | Out-Null
javac -d out (Get-ChildItem gitlet\*.java)
javac -cp "out;Library\junit-4.13.2.jar;Library\hamcrest-core-1.3.jar" -d out GitletTests.java

Push-Location testing
java -cp "..\out;..\Library\junit-4.13.2.jar;..\Library\hamcrest-core-1.3.jar" org.junit.runner.JUnitCore GitletTests
Pop-Location
```


