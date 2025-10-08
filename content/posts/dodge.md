+++
date = '2025-10-03T11:34:57+02:00'
title = 'DHM 2025 - Dodge the Creeps'
keywords = ['ctf']
+++

In the second iteration of the [German Hacking Championship,](https://hacking-meisterschaft.de/) one of the challenges featured a 2D game called "Dodge the Creeps".
This game is based on one of the sample projects from the [Godot game engine,](https://docs.godotengine.org/en/3.1/getting_started/step_by_step/your_first_game.html) but written in Rust and compiled to WebAssembly.
The goal of the challenge was to reach level 1337 in the game, which is practically impossible.
<!--more-->

{{< centerimage src="https://user-images.githubusercontent.com/5276727/74889350-634f7c80-5389-11ea-9810-9e0557cfe3bd.png" maxWidth="300px" >}}

By inspecting the page, we can find the Godot config:
```js
const GODOT_CONFIG = {
    "args": [],
    "canvasResizePolicy": 2,
    "ensureCrossOriginIsolationHeaders": true,
    "executable": "game",
    "experimentalVK": false,
    "fileSizes": {"game.pck": 344672, "game.wasm": 1626559},
    "focusCanvas": true,
    "gdextensionLibs": ["dodge_the_creeps.wasm"]
};
```

`game.pck` contains all assets and resources of the game, while `game.wasm` contains the Godot game engine.
`dodge_the_creeps.wasm`, a Godot extension library, contains game-specific code.
We download this file from the networks tab, and decompile it using Ghidra.
Since we know that we must reach level 1337, we search for this constant in the decompilation, and quickly find an interesting function:

```cpp {hl_lines=["71-138"]}
void dodge_the_creeps::main_scene::Main::on_score_timer_timeout::h4e8f2321eedea7b6(int param1) {
    undefined4 uVar1;
    ulonglong uVar2;
    uint uVar3;
    longlong lVar4;
    undefined4 local_170;
    undefined4 local_16c;
    undefined4 local_168;
    undefined4 local_164;
    undefined4 local_160;
    undefined4 local_15c;
    undefined4 local_158;
    undefined4 local_154;
    uint local_150;
    byte *local_14c;
    undefined4 local_148;
    undefined4 local_144;
    undefined1 local_140[12];
    undefined1 local_134;
    undefined1 local_133;
    undefined1 local_132;
    undefined1 local_131;
    undefined1 local_130;
    undefined1 local_12f;
    undefined1 local_12e;
    undefined1 local_12d;
    undefined1 local_12c;
    undefined1 local_12b;
    undefined1 local_12a;
    undefined1 local_129;
    undefined1 local_128;
    undefined1 local_127;
    undefined1 local_126;
    undefined1 local_125;
    undefined1 local_124;
    undefined1 local_123;
    undefined1 local_122;
    undefined1 local_121;
    undefined1 local_120;
    undefined1 local_11f;
    undefined1 local_11e;
    undefined1 local_11d;
    undefined1 local_11c;
    undefined1 local_11b;
    undefined1 local_11a;
    undefined1 local_119;
    undefined8 local_118;
    undefined4 local_110;
    undefined1 local_10c[12];
    undefined8 local_100;
    undefined4 local_f8;
    uint local_f0;
    byte *local_ec;
    undefined1 local_e8[24];
    undefined1 local_d0[28];
    undefined1 local_b4[12];
    undefined1 local_a8[12];
    undefined1 local_9c[12];
    undefined1 local_90[12];
    undefined1 local_84[12];
    undefined1 local_78[12];
    undefined1 local_6c[12];
    undefined1 local_60[24];
    undefined1 local_48[36];
    int local_24;
    ulonglong local_18;
    uint local_c;
    byte *local_8;
    byte local_1;

    local_24 = param1;
    _$LT$rapidhash..rapid_hasher..RapidHasher$u20$as$u20$core..hash..Hasher$GT$::write_i64::hc96a9a7e1938e6e8(param1 + 0xd0, *(undefined8 *)(param1 + 0xb0));
    lVar4 = *(longlong *)(param1 + 0xb0) + 1;
    if (lVar4 < *(longlong *)(param1 + 0xb0)) {
    core::panicking::panic_const::panic_const_add_overflow::hdc8446c77d668fe6(import::env::__memory_base + 0x258d0);
        do {
            halt_trap();
        } while( true );
    }
    *(longlong *)(param1 + 0xb0) = lVar4;
    uVar1 = _$LT$godot_core..obj..on_ready..OnReady$LT$T$GT$$u20$as$u20$core..ops..deref..DerefMut$GT$ ::deref_mut::hf43d99878b637a59(param1 + 0x48);
    godot_core::obj::gd::Gd$LT$T$GT$::bind_mut::h45f28452b3a1fd45(local_140, uVar1);
    uVar1 = _$LT$godot_core..obj..guards..GdMut$LT$T$GT$$u20$as$u20$core..ops..deref..Deref$GT$::deref ::h9b2dbc80c3c1036c(local_140);
    dodge_the_creeps::hud::Hud::update_score::h708a2c82b7b3691f(uVar1, *(undefined8 *)(param1 + 0xb0));
    core::ptr::drop_in_place$LT$godot_core..obj..guards..GdMut$LT$dodge_the_creeps..hud..Hud$GT$$GT$:: h9811f3e4a9bc0dfd(local_140);
    if (*(longlong *)(param1 + 0xb0) == 0x539) {
        local_134 = 0xf0;
        local_133 = 0xf7;
        local_132 = 0x90;
        local_131 = 0xcd;
        local_130 = 0x53;
        local_12f = 0xab;
        local_12e = 0x39;
        local_12d = 0x23;
        local_12c = 0xd1;
        local_12b = 0xdb;
        local_12a = 0x82;
        local_129 = 0xd1;
        local_128 = 0xb;
        local_127 = 0xfb;
        local_126 = 0x35;
        local_125 = 0x3c;
        local_124 = 0xeb;
        local_123 = 0xd3;
        local_122 = 0xec;
        local_121 = 0xdd;
        local_120 = 0x5e;
        local_11f = 0xc0;
        local_11e = 0x3b;
        local_11d = 0x17;
        local_11c = 0xd3;
        local_11b = 0x8f;
        local_11a = 0xb9;
        local_119 = 0xcb;
        uVar2 = _$LT$rapidhash..rapid_hasher..RapidHasher$u20$as$u20$core..hash..Hasher$GT$::finish::h9b34b560abc46256(param1 + 0xd0);
        local_18 = uVar2;
        core::slice::_$LT$impl$u20$$u5b$T$u5d$$GT$::iter_mut::h7d2141c7400a4e24(&local_148, &local_134, 0x1c);
        core::iter::traits::iterator::Iterator::enumerate::hf695d432ca6d8afa(local_10c, local_148, local_144);
        _$LT$I$u20$as$u20$core..iter..traits..collect..IntoIterator$GT$::into_iter::h3e89a74714bc08d2(&local_118, local_10c);
        local_f8 = local_110;
        local_100 = local_118;
        while (true) {
            _$LT$core..iter..adapters..enumerate..Enumerate$LT$I$GT$$u20$as$u20$core..iter..traits..iterat or..Iterator$GT$::next::hb751954891f703fe(&local_150, &local_100);
            local_f0 = local_150;
            local_ec = local_14c;
            if (local_14c == (byte *)0x0) break;
            local_c = local_150;
            local_8 = local_14c;
            uVar3 = (local_150 & 7) << 3;
            if (0x3f < uVar3) {
            core::panicking::panic_const::panic_const_shr_overflow::h271ff6741146e2a9(import::env::__memory_base + 0x25900);
                do {
                    halt_trap();
                } while( true );
            }
            local_1 = (byte)(uVar2 >> uVar3);
            *local_14c = *local_14c ^ local_1;
        }
        dodge_the_creeps::main_scene::Main::score_timer::h47899ac7c195df31(local_e8, param1);
        uVar1 = _$LT$godot_core..obj..gd..Gd$LT$T$GT$$u20$as$u20$core..ops..deref..DerefMut$GT$::deref_m ut::h3f3b2efaaef0a951(local_e8);
        godot_core::gen::classes::timer::re_export::Timer::stop::h2d30f3aef1d33eef(uVar1);
        core::ptr::drop_in_place$LT$godot_core..obj..gd..Gd$LT$godot_core..gen..classes..timer..re_expor t..Timer$GT$$GT$::h60a700c2662fbda0(local_e8);
        dodge_the_creeps::main_scene::Main::mob_timer::h885b0be29c481da5(local_d0, param1);
        uVar1 = _$LT$godot_core..obj..gd..Gd$LT$T$GT$$u20$as$u20$core..ops..deref..DerefMut$GT$::deref_m ut::h3f3b2efaaef0a951(local_d0);
        godot_core::gen::classes::timer::re_export::Timer::stop::h2d30f3aef1d33eef(uVar1);
        core::ptr::drop_in_place$LT$godot_core..obj..gd..Gd$LT$godot_core..gen..classes..timer..re_expor t..Timer$GT$$GT$::h60a700c2662fbda0(local_d0);
        uVar1 = _$LT$godot_core..obj..on_ready..OnReady$LT$T$GT$$u20$as$u20$core..ops..deref..DerefMut$G T$::deref_mut::hf43d99878b637a59(param1 + 0x48);
        godot_core::obj::gd::Gd$LT$T$GT$::bind_mut::h45f28452b3a1fd45(local_b4, uVar1);
        alloc::string::String::from_utf8_lossy::h3a43c106188979ef(local_6c, &local_134, 0x1c);
        _$LT$alloc..borrow..Cow$LT$B$GT$$u20$as$u20$core..ops..deref..Deref$GT$::deref::h3e99ade0dd8e53b b(&local_158, local_6c);
        core::str::_$LT$impl$u20$str$GT$::chars::hd2f9fab320ebf02b(&local_160, local_158, local_154);
        core::iter::traits::iterator::Iterator::collect::hc0a2711751286398(local_78, local_160, local_15c);
        _$LT$alloc..vec..Vec$LT$T$C$A$GT$$u20$as$u20$core..ops..deref..Deref$GT$::deref::he16dd8c6c1bd52 f1(&local_168, local_78);
        core::slice::_$LT$impl$u20$$u5b$T$u5d$$GT$::chunks::h45368dba2179b345(local_84, local_168, local_164, 8, import::env::__memory_base + 0x258e0);
        core::iter::traits::iterator::Iterator::map::h7a77fd1e754228c2(local_90, local_84);
        core::iter::traits::iterator::Iterator::collect::hffb025cbebd6854e(local_9c, local_90);
        _$LT$alloc..vec..Vec$LT$T$C$A$GT$$u20$as$u20$core..ops..deref..Deref$GT$::deref::h9cec666d58029a 95(&local_170, local_9c);
        alloc::slice::_$LT$impl$u20$$u5b$T$u5d$$GT$::join::ha350b85d80462fc8(local_a8, local_170, local_16c, import::env::__memory_base + 0x7c4c, 1);
        core::ptr::drop_in_place$LT$alloc..vec..Vec$LT$alloc..string..String$GT$$GT$::h56936bd92dbd1ea9(local_9c);
        core::ptr::drop_in_place$LT$alloc..vec..Vec$LT$char$GT$$GT$::h694d2bfe07ee1ab2(local_78);
        core::ptr::drop_in_place$LT$alloc..borrow..Cow$LT$str$GT$$GT$::h9778ac989c55d58e(local_6c);
        uVar1 = _$LT$godot_core..obj..guards..GdMut$LT$T$GT$$u20$as$u20$core..ops..deref..Deref$GT$::der ef::h9b2dbc80c3c1036c(local_b4);
        godot_core::obj::traits::WithBaseField::base::haf70a3abc39dab39(local_48, uVar1);
        uVar1 = _$LT$godot_core..obj..guards..BaseRef$LT$T$GT$$u20$as$u20$core..ops..deref..Deref$GT$::d eref::he9d58fe3554873a9(local_48);
        uVar1 = _$LT$godot_core..obj..gd..Gd$LT$T$GT$$u20$as$u20$core..ops..deref..Deref$GT$::deref::h7a e47765fdd7ed0d(uVar1);
        uVar1 = _$LT$godot_core..gen..classes..canvas_layer..re_export..CanvasLayer$u20$as$u20$core..ops ..deref..Deref$GT$::deref::hf841a26af70aa86b(uVar1);
		godot_core::classes::manual_extensions::_$LT$impl$u20$godot_core..gen..classes..node..re_export. .Node$GT$::get_node_as::h1bee986f23ed3eae(local_60, uVar1, import::env::__memory_base + 0x7c4d, 0xc);
        core::ptr::drop_in_place$LT$godot_core..obj..guards..BaseRef$LT$dodge_the_creeps..hud..Hud$GT$$G T$::h7268b7e11b4025f4(local_48);
        uVar1 = _$LT$godot_core..obj..gd..Gd$LT$T$GT$$u20$as$u20$core..ops..deref..DerefMut$GT$::deref_m ut::hdc15534bfc9ed928(local_60);
        godot_core::gen::classes::label::re_export::Label::set_text::h44e22ab124c0c0b2(uVar1, local_a8);
        uVar1 = _$LT$godot_core..obj..gd..Gd$LT$T$GT$$u20$as$u20$core..ops..deref..DerefMut$GT$::deref_m ut::hdc15534bfc9ed928(local_60);
        uVar1 = _$LT$godot_core..gen..classes..label..re_export..Label$u20$as$u20$core..ops..deref..Dere fMut$GT$::deref_mut::hbe73c212050eab2c(uVar1);
        uVar1 = _$LT$godot_core..gen..classes..control..re_export..Control$u20$as$u20$core..ops..deref.. DerefMut$GT$::deref_mut::hf1a3e65c784f9602(uVar1);
        godot_core::gen::classes::canvas_item::re_export::CanvasItem::show::h01ee0ae01ced305f(uVar1);
        core::ptr::drop_in_place$LT$godot_core..obj..gd..Gd$LT$godot_core..gen..classes..label..re_expor t..Label$GT$$GT$::hbf0e88116ae4c813(local_60);
        export::core::ptr::drop_in_place$LT$alloc..string..String$GT$::h3b3a7f25db6b1f46(local_a8);
        core::ptr::drop_in_place$LT$godot_core..obj..guards..GdMut$LT$dodge_the_creeps..hud..Hud$GT$$GT$ ::h9811f3e4a9bc0dfd(local_b4);
    }
    return;
}
```

I have highlighted the parts of the function that computes the flag.
We can see that  [rapidhash](https://docs.rs/rapidhash/latest/rapidhash/) is being used.
Since the `write_i64` method operates on a `RapidHasher` object, the first parameter (`param1 + 0xd0`) is a pointer to `self`.
The second argument is the score (which we can already infer from the comparison).
By cleaning up the decompilation, we can obtain the following Pseudo-C code:

```cpp
void win(int* score) {
    Hasher* hasher = RapidHasher();
    hasher.write_i64(*score);
    int new_score = *score + 1;
    if (new_score < *score) {
        panic_overflow();
    }
    *score = new_score;
    if (*score == 1337) {
        uint8_t buffer[28] = {
            0xf0, 0xf7, 0x90, 0xcd, 0x53, 0xab, 0x39, 0x23, 0xd1, 0xdb, 0x82, 0xd1, 0x0b, 0xfb,
            0x35, 0x3c, 0xeb, 0xd3, 0xec, 0xdd, 0x5e, 0xc0, 0x3b, 0x17, 0xd3, 0x8f, 0xb9, 0xcb,
        };
        uint64_t hash = hasher.finish();
        for (size_t i = 0; i < sizeof(buffer); i++) {
            uint8_t shift = (i & 7) << 8;
            if (shift >= 64) {
                panic_shift_overflow();
            }
            uint8_t xor_byte = (hash >> shift) & 0xFF;
            buffer[i] ^= xor_byte;
        }
    }
}
```

We can infer that the hash is computed by feeding the `RapidHasher` instance each score up to 1337.
The final hash value is used as a key for decrypting the flag.
Since `rapidhash` is available as a crate, we finally translate the cleaned up code to Rust:

```rust
use rapidhash::RapidHasher;
use std::hash::Hasher;

fn decrypt() -> String {
    let mut data: [u8; 28] = [
        0xf0, 0xf7, 0x90, 0xcd, 0x53, 0xab, 0x39, 0x23, 0xd1, 0xdb, 0x82, 0xd1, 0x0b, 0xfb,
        0x35, 0x3c, 0xeb, 0xd3, 0xec, 0xdd, 0x5e, 0xc0, 0x3b, 0x17, 0xd3, 0x8f, 0xb9, 0xcb,
    ];
    let mut rapid_hasher = RapidHasher::default();
    for score in 0..1337 {
        rapid_hasher.write_i64(score);
    }
    let hash = rapid_hasher.finish();
    for (i, byte) in data.iter_mut().enumerate() {
        let shift = ((i & 7) << 3) as u64;
        let key = ((hash >> shift) & 0xFF) as u8;
        *byte ^= key;
    }
    String::from_utf8_lossy(&data).into_owned()
}

fn main() {
    println!("{}", decrypt())
}
```

This prints us the flag `DHM{h4cked_g0dot_l1ke_a_g0d}`.
