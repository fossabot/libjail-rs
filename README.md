# libjail-rs

[![Crates.io](https://img.shields.io/crates/v/jail.svg)](https://crates.io/crates/jail)
[![Travis](https://img.shields.io/travis/fubarnetes/libjail-rs.svg)](https://travis-ci.org/fubarnetes/libjail-rs)
[![Docs: x86_64-unknown-freebsd](https://img.shields.io/badge/docs-x86__64--unknown--freebsd-blue.svg)](https://fubarnetes.github.io/libjail-rs/x86_64-unknown-freebsd/jail/index.html)
[![Docs: i686-unknown-freebsd](https://img.shields.io/badge/docs-i686--unknown--freebsd-blue.svg)](https://fubarnetes.github.io/libjail-rs/i686-unknown-freebsd/jail/index.html)

libjail-rs aims to be a rust implementation of the FreeBSD [jail(3)](https://www.freebsd.org/cgi/man.cgi?query=jail&sektion=3&manpath=FreeBSD+11.1-stable) library. While feature parity is a goal, a one-to-one implementation of all functions in [jail(3)](https://www.freebsd.org/cgi/man.cgi?query=jail&sektion=3&manpath=FreeBSD+11.1-stable) is not.

# Is it any good?
[Yes.](https://news.ycombinator.com/item?id=3067434)

# Can I use it?

This library is still under heavy development, but seems to work so far.
No stability guarantees are made.

# How do I use it?

```
jail = "*"
```

Execute a command in a jail:
```rust
use jail::process::Jailed;
use jail::StoppedJail;
use std::process::Command;

fn main() {
    let stopped = StoppedJail::new("/path/to/root")
        .name("alcatraz")
        .ip("127.0.1.2".parse().unwrap())
        .ip("fe80::2".parse().unwrap * ())
        .param("allow.raw_sockets", param::Value::Int(1));

    let running = stopped.start().expect("Couldn't start Jail");

    let output = Command::new("hostname")
        .jail(running.jid)
        .output()
        .expect("Failed to execute command");

    println!("output: {:?}", output.stdout);

    running.kill();
}
```

# Is it fast?

There are a few benchmarks included. Run them with `sudo cargo bench` (yes,
starting a jail requires being `root`).

These are some results on my laptop, on slow, spinning disks:

```
test echo_helloworld_free       ... bench:     271,418 ns/iter (+/- 17,522)
test echo_helloworld_jailed     ... bench:     461,749 ns/iter (+/- 26,267)
test get_ips                    ... bench:      29,591 ns/iter (+/- 3,315)
test start_echo_helloworld_stop ... bench:     504,978 ns/iter (+/- 23,717)
test start_stop_ipjail          ... bench:      27,220 ns/iter (+/- 2,141)
test start_stop_ipv4jail        ... bench:      26,307 ns/iter (+/- 2,159)
test start_stop_ipv6jail        ... bench:      26,988 ns/iter (+/- 2,486)
test start_stop_jail            ... bench:      25,760 ns/iter (+/- 2,244)
```
