# lucky-commit

Make your git commits lucky!

## What?

With this simple command, you can change the start of your git commit hashes to whatever you want.

```bash
$ git log
1f6383a Some commit
$ lucky-commit
$ git log
0000000 Some commit
```

As a demonstration, see the latest commit in this repository.

## How?

lucky-commit amends your commits by adding a few characters of various types of whitespace, and keeps hashing new messages until it gets the right value. By default, it will keep searching until it finds a hash starting with "0000000", but this can be changed by simply passing the desired hash as an argument.

```bash
$ lucky-commit 1010101
$ git log
1010101 Some commit
```

## Why?

¯\\\_(ツ)_/¯

## Installation

I've rewritten the `lucky-commit` project several times as a method to learn new programming languages. As a result, there are multiple different implementations of `lucky-commit` in different languages.

The latest version is written in **Rust**. To install it:

* Make sure you have `rustc` and `cargo` installed. Installation instructions can be found [here](https://doc.rust-lang.org/book/second-edition/ch01-01-installation.html).

    ```
    $ git clone https://github.com/not-an-aardvark/lucky-commit
    $ cd lucky-commit/
    $ cargo build --release
    ```

    This will create the `lucky_commit` binary in the `target/release` directory. You can move this to wherever you want, or set up an alias for it.

To install an older version, see the instructions in the `README.md` file on the corresponding branch:

* **C** (see the `C` branch of this repository)
* **Node.js** (see the `nodejs` branch of this repository)

## Performance

* `lucky-commit`'s main performance bottleneck is SHA1 throughput. On a single core of a 2015 MacBook Pro, OpenSSL's SHA1 implementation has a throughput of about 370 MB/s.
* Long hash prefixes require more hash computations. The default hash prefix of `0000000` has length 7, so an average of 16<sup>7</sup> (or 2<sup>28</sup>) hashes are needed.
* Large git commit objects increase the amount of data that needs to be hashed on each iteration.
    * A git commit object with a short commit message is typically about 300 bytes.
    * Adding a GPG signature to a commit increases the size by about 850 bytes.
* Machines with more CPUs can compute more hashes. Hash searching is very parallelizable, so performance scales linearly with the number of CPUs.

This means that on a 2015 MacBook Pro with 4 cores, searching for a `0000000` prefix on a commit with no GPG signature will ideally take an average of

```
(2^28 hashes) * (300 bytes/hash) / (370 MB/s/core) / (4 cores) = 52 seconds
```

Note that this calculation ignores the effect of any other processes running on the machine. In practice, I've found that `lucky-commit` takes about 100 seconds to run when there are a few other applications open.
