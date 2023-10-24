[4297](https://github.com/winglang/wing/pull/4297)
ERROR: Cannot find module './preflight.api-1.cjs.cjs'  
Require stack:  
- /home/bronifty/codes/wing/libs/wingcompiler/dist/index.js  
- /home/bronifty/codes/wing/apps/wing-console/console/server/dist/index.js  
- /home/bronifty/codes/wing/apps/wing-console/console/app/dist/index.js  
- /home/bronifty/codes/wing/apps/wing/dist/commands/run.js  
- /home/bronifty/codes/wing/apps/wing/dist/cli.js  
- /home/bronifty/codes/wing/apps/wing/bin/wing  
  
target/main.wsim.643328.tmp/.wing/preflight.cjs:6  
const std = $stdlib.std;  
const cloud = $stdlib.cloud;  
>> const wingpressoApi = require("./preflight.api-1.cjs.cjs")({ $stdlib });  
class $Root extends $stdlib.std.Resource {  
constructor(scope, id) {



