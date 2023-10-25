[github issue 4297](https://github.com/winglang/wing/pull/4297)

## Mark Mc Culloh
|   |
|---|
|[@bronifty](https://github.com/bronifty) Thanks for taking this on again. The root issue here is about what files the wing compiler output. The PR currently only adds `--format cjs` for `@winglang/compiler`, but that only changes the output for that library. In fact, I think it is already outputting cjs so this change shouldn't be needed.<br><br>The change needed would be to find any places we are outputting .js files and change it to .cjs. This will include changes in both the rust and typescript code. There will likely be several places to change. Good files to start in:<br><br>- [https://github.com/winglang/wing/blob/main/libs/wingc/src/jsify.rs](https://github.com/winglang/wing/blob/main/libs/wingc/src/jsify.rs)<br>    - This is where the compiler outputs js files<br>- [https://github.com/winglang/wing/blob/main/libs/wingsdk/src/shared/bundling.ts](https://github.com/winglang/wing/blob/main/libs/wingsdk/src/shared/bundling.ts)<br>    - This is where we run esbuild, which takes in and outputs js files for inflights|

-----

## Yoav Steinberg
Yoav Steinberg  [8:14 PM](https://winglang.slack.com/archives/D0628V8DZA7/p1698020056338429)  

Are you asking who uses the output of [jsify.rs](http://jsify.rs/)? It's something like this:  
[lib.rs](http://lib.rs/) uses jsify in its `compile()` function. This is the interface to using wingc.  
the `compile()` function is used by the type script library `wingcompiler` (see `libs/wingcopmiler`).  
This library is used by both the wing console (gui) and the `wing` command line application.

> Q: can you help me with an explanation of this in further detail?

1. lib.rs: This is the main entry point of the wingc library, which is a compiler for the Wing programming language. It defines the compile() function, which is the main function used to compile Wing code. This function uses many other modules and functions defined in the library, including the jsify module.  
  
2. jsify.rs: This module is responsible for converting the abstract syntax tree (AST) of a Wing program into JavaScript code. The main function in this module is jsify(), which takes a path to a source file and a reference to a Scope (which represents a block of Wing code) and generates JavaScript code. This function is used by the compile() function in lib.rs.  
  
3. wingcompiler: This is a TypeScript library that provides a JavaScript/TypeScript interface to the wingc compiler. It uses the compile() function from lib.rs to compile Wing code.  
  
4. wing command line application and Wing console (GUI): These are user-facing applications that allow developers to write and run Wing code. They use the wingcompiler library to compile the Wing code.  
  
In summary, when you write Wing code and run it using the Wing console or the wing command line application, your code is compiled to JavaScript using the wingc compiler. The jsify module is a key part of this process, as it is responsible for the actual conversion of Wing code into JavaScript.

> Q: please elaborate on the relationships between lib.rs and jsify.rs and wingcompiler to wingc

Sure, let's dive deeper into the relationships:  
  
1. lib.rs and jsify.rs: These two files are part of the wingc library, which is the compiler for the Wing programming language. lib.rs is the main entry point of the library, and it uses many other modules and functions defined in the library, including the jsify module. The jsify module, defined in jsify.rs, is responsible for converting the abstract syntax tree (AST) of a Wing program into JavaScript code. The compile() function in lib.rs uses the jsify() function from jsify.rs to perform this conversion.  
  
2. wingcompiler and wingc: wingcompiler is a TypeScript library that provides a JavaScript/TypeScript interface to the wingc compiler. It uses the compile() function from lib.rs to compile Wing code. In other words, wingcompiler is a wrapper around wingc that allows it to be used from JavaScript and TypeScript code.  
  
3. wingc and wing: The wing command line application and Wing console (GUI) are user-facing applications that allow developers to write and run Wing code. They use the wingcompiler library to compile the Wing code, which in turn uses the wingc compiler.  
  
In summary, lib.rs and jsify.rs are part of the wingc compiler's implementation, wingcompiler is a TypeScript interface to wingc, and wing is a user-facing application that uses wingcompiler (and therefore wingc) to compile and run Wing code.

> Q: the wing cli uses wingcompiler, a typescript wrapper around wingc, to compile wing code. Further, wingc uses lib.rs and jsify.rs. can you explain what part jsify plays in lib.rs? why does lib.rs refer to jsify? what are any upstream and downstream resources that are interacting directly or indirectly with jsify.rs?

The jsify.rs module in the wingc library plays a crucial role in the compilation process of Wing code. It is responsible for the conversion of the abstract syntax tree (AST) of a Wing program into JavaScript code. This is a key step in the compilation process because Wing is a language that compiles to JavaScript, which means that the end result of compiling Wing code is JavaScript code.  
  
The lib.rs file refers to jsify.rs because it uses the jsify() function defined in jsify.rs as part of the compile() function. Here's a simplified view of how it works:  
  
1. The compile() function in lib.rs takes Wing source code as input.  
2. It parses the Wing code into an AST.  
3. It then passes this AST to the jsify() function (from jsify.rs), which converts the AST into JavaScript code.  
  
In terms of upstream and downstream resources:  
  
- Upstream: The main upstream resource for jsify.rs is the lib.rs file, which calls the jsify() function. The input to jsify() is the AST of a Wing program, which is produced by the parsing and type-checking steps of the compile() function in lib.rs.  
  
- Downstream: The main downstream resource from jsify.rs is the JavaScript code it produces. This code is used by the wingcompiler TypeScript library, which provides it to the wing command line application or the Wing console (GUI) to be executed.

> Q: elaborate on the relationship between these two files @wingc.ts @compile.ts 

Sure, let's break down the relationship between these two files:  
  
1. wingc.ts: This file is part of the wingcompiler TypeScript library and it provides the interface to the wingc compiler. It defines the load() function, which is responsible for loading the wingc compiler and setting up the necessary environment for it to run. It also defines the WingCompilerFunction type, which lists the various functions that can be invoked on the wingc compiler.  

```ts
export type WingCompilerFunction =
  | "wingc_compile"
  | "wingc_on_did_open_text_document"
  | "wingc_on_did_change_text_document"
  | "wingc_on_completion"
  | "wingc_on_signature_help"
  | "wingc_on_goto_definition"
  | "wingc_on_document_symbol"
  | "wingc_on_semantic_tokens"
  | "wingc_on_hover";
```

2. compile.ts: This file is also part of the wingcompiler library and it uses the load() function from wingc.ts to load the wingc compiler. Once the compiler is loaded, it can be used to compile Wing code. The compile() function in compile.ts is responsible for this. It sets up the necessary environment variables, loads the wingc compiler, and then invokes the wingc_compile function on it to compile the Wing code.  
  
In summary, wingc.ts provides the interface to the wingc compiler, and compile.ts uses this interface to compile Wing code. The load() function in wingc.ts is used to load the wingc compiler, and the compile() function in compile.ts is used to compile the Wing code.

> Q: can you explain what part of jsify intersects with the interaction between compile.ts and wingc.ts

The jsify module, defined in jsify.rs, is part of the wingc compiler and is responsible for converting the abstract syntax tree (AST) of a Wing program into JavaScript code. This is a key step in the compilation process because Wing is a language that compiles to JavaScript.  
  
In the context of the interaction between compile.ts and wingc.ts, jsify comes into play when the wingc_compile function is invoked on the wingc compiler. Here's a simplified view of how it works:  
  
1. The compile() function in compile.ts loads the wingc compiler using the load() function from wingc.ts.  
2. It then invokes the wingc_compile function on the wingc compiler to compile the Wing code.  
3. Inside the wingc compiler, the compile() function in lib.rs is called, which parses the Wing code into an AST.  
4. This AST is then passed to the jsify() function from jsify.rs, which converts the AST into JavaScript code.  
  
So, while jsify doesn't directly interact with compile.ts or wingc.ts, it plays a crucial role in the process that these two files are part of, which is the compilation of Wing code.

> Q: can you help me with an arrow diagram pointing the directionality of the dependencies? I am interested in how each file and the files the comprise the modules are dependent on one another.

Sure, here's a simplified arrow diagram showing the directionality of the dependencies:  

```md
compile.ts --> wingc.ts --> lib.rs --> jsify.rs
```

Here's what each arrow represents:  
  
- compile.ts --> wingc.ts: The compile.ts file depends on wingc.ts because it uses the load() function defined in wingc.ts to load the wingc compiler.  
  
- wingc.ts --> lib.rs: The wingc.ts file depends on lib.rs because it interacts with the wingc compiler, which is defined in lib.rs.  
  
- lib.rs --> jsify.rs: The lib.rs file depends on jsify.rs because it uses the jsify() function defined in jsify.rs to convert the abstract syntax tree (AST) of a Wing program into JavaScript code.  
  
> 
> Q: which process is dropping wingc.wasm into the root of wingcompiler?

The wingc.wasm file is typically generated as part of the build process for the wingc compiler. This process involves compiling the Rust code of the wingc compiler into WebAssembly (WASM) format.  
  
The specific tool or script that moves or copies the wingc.wasm file into the root of the wingcompiler directory can vary depending on the project's build setup. It could be a custom script, a task in a build tool like Make or Gulp, or a step in a continuous integration (CI) pipeline.  
  
The purpose of placing the wingc.wasm file in the root of the wingcompiler directory is to make it easily accessible to the wingcompiler library. The load() function in wingc.ts reads the wingc.wasm file from disk when it loads the wingc compiler.

> Q: the wing-cli module uses the wingcompiler module which comprises 2 main files - compile.ts and wingc.ts - which use wingc.wasm which provides the wingc module which comprises 2 main files - lib.rs and jsify.rs. In compile.ts the load() function uses wingc.wasm. In wingc.ts what is the function which is called that compile.ts leverages either inside or in conjunction with the load() method?

The compile.ts file uses the load() function from wingc.ts to load the wingc compiler. Once the wingc compiler is loaded, compile.ts can invoke various functions on it.  
  
The specific function that compile.ts invokes on the wingc compiler is determined by the WingCompilerFunction argument passed to the invoke() function. The WingCompilerFunction type is defined in wingc.ts and includes various functions like wingc_compile, wingc_on_did_open_text_document, wingc_on_did_change_text_document, etc.  
  
Here's an example of how compile.ts might use the load() and invoke() functions from wingc.ts:  

```ts
import { load, WingCompilerFunction, invoke } from './wingc';

async function compileWingCode(code: string) {
  const wingc = await load({ /* load options */ });
  const result = invoke(wingc, WingCompilerFunction.wingc_compile, code);
  // handle result
}
```
  
In this example, compileWingCode() first loads the wingc compiler using the load() function. It then invokes the wingc_compile function on the wingc compiler to compile the Wing code. The wingc_compile function is part of the wingc compiler and is implemented in the lib.rs file.

![](./wing-compiler2.png)



-----

## Hasan Abu-Rayyan
Thanks so much for all the context! So I think you are on the right track.  

> const PREFLIGHT_FILE_NAME: &str = “preflight.cjs”;

Yes these preflight files are for sure going to need changed. I will say there are some extra files that get generated besides just the `preflight.js`If we take a look at this small multifile wing app below, it generates several `js` files.So ontop of the two places you pointed out I think `[jsify.rs](http://jsify.rs/)` also has a `emit_inflight_file()` method that will need updated.
![](./image.png)
as far as where they are imported I think this is mainly just throughout the code emitted in `preflight.js` But Im more than happy to dive a bit deeper into it with you

-----


> Error: Failed to compile.  
require() of ES Module /home/bronifty/codes/winglang/test/vite-project/target/main.wsim.800882.tmp/.wing/preflight.structs-1.js from /home/bronifty/codes/winglang/wing-dev/libs/wingcompiler/dist/index.js not supported.  
preflight.structs-1.js is treated as an ES module file as it is a .js file whose nearest parent package.json contains "type": "module" which declares all .js files in that package scope as ES modules.  
Instead rename preflight.structs-1.js to end in .cjs, change the requiring code to use dynamic import() which is available in all CommonJS modules, or change "type": "module" to "type": "commonjs" in /home/bronifty/codes/winglang/test/vite-project/package.json to treat all .js files as CommonJS (using .mjs for all ES modules instead).  
target/main.wsim.800882.tmp/.wing/preflight.js:6  
const std = $stdlib.std;  
const cloud = $stdlib.cloud;  
const lib = require("./preflight.structs-1.js")({ $stdlib });  
class $Root extends $stdlib.std.Resource {  
constructor(scope, id) {


## preflight.cjs
### wingc
- proposed change: update libs/wingc/src/jsify to use preflight.cjs
```rust
const PREFLIGHT_FILE_NAME: &str = "preflight.js";
const PREFLIGHT_FILE_NAME: &str = "preflight.cjs";
```

- proposed change: update preflight file generator to use format!("preflight.{}-{}.cjs",
- Before:
```rust
let preflight_file_name = if is_entrypoint_file {
	PREFLIGHT_FILE_NAME.to_string()
} else {
	// remove all non-alphanumeric characters
	let sanitized_name = source_path
		.file_stem()
		.unwrap()
		.chars()
		.filter(|c| c.is_alphanumeric())
		.collect::<String>();
	// add a number to the end to avoid name collisions
	let mut preflight_file_counter = self.preflight_file_counter.borrow_mut();
	*preflight_file_counter += 1;
	format!("preflight.{}-{}.js", sanitized_name, preflight_file_counter)
};
```
After:
```rust
format!("preflight.{}-{}.cjs",
```
### wingcompiler
- If libs/wingcompiler is emitting js files, its configuration should probably be updated to emit cjs files instead 
- Proposed update: change libs/wingcompiler/src/compile.ts to use preflight.cjs
```ts
-- const WINGC_PREFLIGHT = "preflight.js";
++ const WINGC_PREFLIGHT = "preflight.cjs";
```
- Error after the update 
> Error: ENOENT: no such file or directory, open '/home/bronifty/codes/winglang/test/vite-project/target/main.wsim.343655.tmp/.wing/preflight.cjs'


### Inflight*.cjs
- libs/wingc/src/jsify.rs
```rust
	fn emit_inflight_file(&self, class: &AstClass, inflight_class_code: CodeMaker, ctx: &mut JSifyContext) {
		let name = &class.name.name;
		let mut code = CodeMaker::default();

		let inputs = if let Some(lifts) = &ctx.lifts {
			lifts
				.captures
				.iter()
				.filter_map(|(token, cap)| if !cap.is_field { Some(token) } else { None })
				.join(", ")
		} else {
			Default::default()
		};

		code.open(format!("module.exports = function({{ {inputs} }}) {{"));
		code.add_code(inflight_class_code);
		code.line(format!("return {name};"));
		code.close("}");

		// emit the inflight class to a file
		match self
			.output_files
			.borrow_mut()
			.add_file(self.inflight_filename(class), code.to_string())
		{
			Ok(()) => {}
			Err(err) => report_diagnostic(err.into()),
		}
	}
```

### Target Resources
- preflight.cjs is created by 

![](./target-resources.png)