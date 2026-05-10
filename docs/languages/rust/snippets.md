# Snippets

## Function pointer

To have a function pointer inside a structure field

```rust
struct ExampleStruct {
    function_pt: fn(u8, u8) -> Result<u8>,
}
```
