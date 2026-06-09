# linux-kernel-cmdline

A parser for the Linux kernel command line (`/proc/cmdline`), supporting both
byte-level and UTF-8 parsing with proper quote handling and dash/underscore
equivalence for parameter keys.

## Usage

Add the dependency to your `Cargo.toml`:

```toml
[dependencies]
linux-kernel-cmdline = "0.1"
```

### Parsing the current system's kernel command line

```rust
use linux_kernel_cmdline::utf8::Cmdline;

let cmdline = Cmdline::from_proc()?;

if let Some(value) = cmdline.value_of("root") {
    println!("root={value}");
}

for param in cmdline.iter() {
    println!("{param}");
}
```

### Parsing a command line from a string

```rust
use linux_kernel_cmdline::utf8::Cmdline;

let cmdline = Cmdline::from("console=ttyS0,115200 root=/dev/sda1 rd.break");

// Find a specific parameter
if let Some(param) = cmdline.find("console") {
    assert_eq!(param.value(), Some("ttyS0,115200"));
}

// Dashes and underscores in keys are treated as equivalent
let cmdline = Cmdline::from("net.ifnames=0 rd.auto-lvm=1");
assert_eq!(cmdline.value_of("rd.auto_lvm"), Some("1"));
```

### Working with non-UTF-8 data

For environments where the kernel command line may contain arbitrary bytes,
use the `bytes` module:

```rust
use linux_kernel_cmdline::bytes::Cmdline;

let cmdline = Cmdline::from_proc()?;

if let Some(value) = cmdline.value_of("root") {
    println!("root={}", String::from_utf8_lossy(value));
}
```

## License

Licensed under either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or <http://www.apache.org/licenses/LICENSE-2.0>)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or <http://opensource.org/licenses/MIT>)

at your option.

