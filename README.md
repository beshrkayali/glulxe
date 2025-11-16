# Glulxe: the Glulx VM interpreter

This is **Glulxe**, the reference interpreter for Glulx, a portable VM for interactive fiction.

## LLM Integration Note

This repository uses a [modified CheapGlk library](https://github.com/beshrkayali/cheapglk) that includes experimental LLM-powered natural language input processing.

This experiment adds natural language command interpretation to interactive fiction games through an LLM API (OpenAI-compatible). When a player enters a command, it's intercepted at the Glk input layer, sent to the LLM for parsing and normalization, and then passed to the game as a standard command. This allows players to use more flexible, conversational input while games continue to work without any modifications. It should be completely transparent to existing games and can be toggled on or off by the interpreter.

### What Changed in Glulxe?

Almost nothing. The only changes to Glulxe itself are:

1. **OpenSSL build configuration** - Added to Makefile to support HTTPS in the Glk library
2. **Linking changes** - Links against the LLM-enabled CheapGlk library

The Glulxe VM code itself is completely unchanged.

## Building

### Requirements

- C compiler
- OpenSSL (for the LLM-enabled Glk library)
- [CheapGlk fork](https://github.com/beshrkayali/cheapglk) with LLM support

### Native Build (CLI)

```bash
cd ..
git clone https://github.com/beshrkayali/cheapglk.git
cd cheapglk
make
cd ../glulxe
make glulxe
```

Install OpenSSL if needed:
- macOS: `brew install openssl`
- Linux: `sudo apt-get install libssl-dev`

### WebAssembly Build (Browser)

Requires [Emscripten SDK](https://emscripten.org/):

```bash
# Install Emscripten in parallel directory
cd ..
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh

# Build CheapGlk for WASM
cd ../cheapglk
make wasm

# Build Glulxe for WASM
cd ../glulxe
make wasm
```

The WebAssembly build creates:
- `glulxe.js` - JavaScript loader
- `glulxe.wasm` - WebAssembly binary

An example html web page that makes use of the built js loader and wasm bin is provided in  `glulxe-web.html`, a browser interface with LLM configuration UI, where you can also load a game directly from the browser:

```bash
# Serve and open in browser
python3 -m http.server 8080
# Open: http://localhost:8080/glulxe-web.html
```

### Cleaning Builds

```bash
make clean        # Clean native build
make clean-wasm   # Clean WASM build
```

## Using the LLM Features

The LLM natural language processing is configured through the Glk library, not through Glulxe.

See the forked [CheapGLK README](https://github.com/beshrkayali/cheapglk) for full details on the configuration (`~/.glk_llm.conf`) and how the LLM interpretation works.

### Quick Start

1. Create `~/.glk_llm.conf`:
```ini
enabled=1
api_endpoint = https://openrouter.ai/api/v1/chat/completions
api_key=sk-api-key
model=google/gemini-2.5-flash
context_lines=10
timeout_ms=5000
echo_interpretation=1
```

2. Run any Glulx game:
```bash
./glulxe game.ulx
```

3. Play naturally:
```
> what do i have?
[LLM: "what do i have?" -> "inventory"]
You are carrying nothing.

> go to the bedroom
[LLM: "go to the bedroom" -> "north"]
```

### Disabling LLM Features

Set `enabled=0` in config, or delete `~/.glk_llm.conf`. Glulxe works exactly as before.

## This is an experiment

This build of Glulxe is part of an experiment to explore how LLMs can enhance interactive fiction through better natural language understanding.

Some of the questions I wanted to answer:

- Can LLM interpretation make IF more accessible?
- Does it preserve the puzzle-solving nature of IF?
- What's the right balance between interpretation and player control?
- Can spatial reasoning be extracted from text descriptions?
- Is it actually fun to play games like this?

This should not be considered anything more than a quick experiment, it's just a prototype to explore the above questions.

## Original Glulxe Documentation

For standard Glulxe documentation, interpreter commands, and debugging features, see the original documentation at:

- [Glulx specification](https://www.eblong.com/zarf/glulx/)
- [Glulxe github](https://github.com/erkyrath/glulxe)

## License

MIT License, same as the original Glulxe.

## Credits

- **Glulx VM and CheapGLK**: Andrew Plotkin
- **LLM-based Interpretation Experiment**: Beshr Kayali Reinholdsson
