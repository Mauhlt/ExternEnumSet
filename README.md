# Extern Enum Set
## Goal:
Takes in enum keys, outputs data structure that works with Vulkan extern vk structs.
Ex: InstanceCreateFlags

# How?
1. Data Structure combines EnumSet, BitSet, Extern Struct
- extern structs
    - gauranteed C ABI layout
- enumset
    - pass all keys
    - can convert to bits to pass as raw unsigned integer to C fns
- bitset
    - uses keys to modify bits
2. Convert Vulkan FlagBits to ExternEnumSet
- takes flagbit tag type -> converts to internal masked int
- take log2 of flagbit value and set that bit position

## Getting Started:
Add  `ExternEnumSet` to your `build.zig.zon` .dependencies with:
```
zig fetch --save git+https://github.com/bphilip777/ExternEnumSet.git
```

and in your build fn inside `build.zig` add:
```zig
const ExternEnumSet = b.dependency("ExternEnumSet", .{});
exe.root_module.addImport("ExternEnumSet", ExternEnumSet.module("ExternEnumSet"));
```

Now in your code, import `packedenumset`
```zig
const EES = @import("ExternEnumSet");
```

Example Use Case:
```zig
const std = @import("std");
const EES = @import("ExternEnumSet");
pub fn main() void {
    // Example taken from: https://github.com/ziglang/zig/blob/master/lib/std/enums.zig
    const Direction = enum(u8) {up, down, left, right};
    const diag_move = init: {
        var move: EES(Direction) = .empty;
        move.insert(.right);
        move.insert(.up);
        berak :init move;
    };
    var result: EES(Direction) = .empty;
    var it = diag_move.iterator();
    while (it.next()) |dir| {
        result.insert(dir);
    }
    std.debug.print("Matches: {}!", .{result.eql(diag_move)});
}
```
