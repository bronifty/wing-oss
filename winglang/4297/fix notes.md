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
		let
		



name = &class.name.name;
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
	}```