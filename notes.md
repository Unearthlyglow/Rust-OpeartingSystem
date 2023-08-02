1. This means that we can’t use most of the Rust standard library, but there are a lot of Rust features that we can use. For example, we can use iterators, closures, pattern matching, option and result, string formatting, and of course the ownership system. These features make it possible to write a kernel in a very expressive, high level way without worrying about undefined behavior or memory safety.

Set a Default Target
To avoid passing the --target parameter on every invocation of cargo build, we can override the default target. To do this, we add the following to our cargo configuration file at .cargo/config.toml:

# in .cargo/config.toml
[build]
target = "x86_64-blog_os.json"

This tells cargo to use our x86_64-blog_os.json target when no explicit --target argument is passed. This means that we can now build our kernel with a simple cargo build. For more information on cargo configuration options, check out the official documentation.

------

```Rust
static HELLO: &[u8] = b"Hello World!";
```

The value assigned to HELLO is b"Hello World!". In Rust, the b prefix before a string literal indicates that it should be treated as a byte string, where each character represents a single byte. This is different from a normal string literal, which is a Unicode string that contains a sequence of UTF-8 encoded code points. We use byte strings here because the VGA buffer is a sequence of 16-bit words, where each 16-bit word represents an ASCII character and a foreground and background color for that character. We can’t use Unicode strings here because they are not ASCII-compatible.

---
Bootimage 

How does it work?
The bootimage tool performs the following steps behind the scenes:

It compiles our kernel to an ELF file.
It compiles the bootloader dependency as a standalone executable.
It links the bytes of the kernel ELF file to the bootloader.
When booted, the bootloader reads and parses the appended ELF file. It then maps the program segments to virtual addresses in the page tables, zeroes the .bss section, and sets up a stack. Finally, it reads the entry point address (our _start function) and jumps to it.