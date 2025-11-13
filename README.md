# rustup target riscv64gc-unknown-uefi
rustup target for riscv64gc-unknown-uefi

## How to use

Clone the repository to the place you want the target to live.
Preferably a hidden directory in your home directory

`export RUST_TARGET_PATH=/path/to/your/rustup-riscv64gc-unknown-uefi`

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

Finally, I created a `config.toml` file in a `.cargo` subdirectory under my main projects root directory
which contained the following...

```
[build]
target = "riscv64gc-unknown-uefi"

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

This was sufficient to get my project to build with the custom RISC-V UEFI target...
