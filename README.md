# rustup target riscv64gc-unknown-uefi
rustup target for riscv64gc-unknown-uefi

## How to use

Clone the repository inside of your target project.

Since I am building from an x86_64 host, I had to run the following command
to get the Rust nightly sources...

`rustup component add rust-src --toolchain nightly-x86_64-unknown-linux-gnu`

In order to get the linker to work, I had to define a custom linker file as follows

```
ENTRY(start)

SECTIONS
{
    . = 0x200000;
    
    .text : {
        *(.text .text.*)
    }
    
    .data : {
        *(.data .data.*)
        *(.rodata .rodata.*)
    }
    
    .bss : {
        *(COMMON)
        *(.bss .bss.*)
    }
    
    /DISCARD/ : {
        *(.note.*)
        *(.comment)
    }
}
```

I created a `config.toml` file in a `.cargo` subdirectory under my main projects root directory
which contained the following...

```
[build]
target = "rustup-riscv64gc-unknown-uefi/riscv64gc-unknown-uefi.json"

[unstable]
build-std = ["core", "alloc"]
build-std-features = ["compiler-builtins-mem"]

[target.riscv64gc-unknown-uefi]
linker = "rust-lld"
rustflags = [
    "-C", "link-arg=-nostdlib",
    "-C", "link-arg=-Tlinker.ld",
]
```

Finally, I ran the following command to build my project...

`cargo +nightly build -Z build-std=core,alloc`

This was sufficient to get my project to build with the custom RISC-V UEFI target...
