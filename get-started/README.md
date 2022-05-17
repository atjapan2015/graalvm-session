### 安装 GraalVM

访问`https://www.oracle.com/downloads/graalvm-downloads.html`，下载Oracle GraalVM Enterprise Edition 22，Java Version：17，OS：Linux，Architecture：x86。
+ Oracle GraalVM Enterprise Edition Core
+ Oracle GraalVM Enterprise Edition Native Image
+ Ideal Graph Visualizer
+ GraalVM LLVM Toolchain Plugin
+ Oracle GraalVM Enterprise Edition Ruby Language Plugin
+ Oracle GraalVM Enterprise Edition Python Language Plugin
+ Oracle GraalVM Enterprise Edition WebAssembly Language Plugin
+ Oracle GraalVM Enterprise Edition Node.js Runtime Plugin
+ Oracle GraalVM Enterprise Edition Java on Truffle
+ Oracle GraalVM Enterprise Edition Java on Truffle LLVM Java libraries

安装GraalVM EE。
```
tar -zxf graalvm-ee-java17-linux-amd64-22.1.0.tar.gz
sudo mv graalvm-ee-java17-22.1.0 /opt/
```

设置环境变量。
```
vi ~/.bashrc
---add
export GRAALVM_HOME=/opt/graalvm-ee-java17-22.1.0
export PATH=$GRAALVM_HOME/bin:$PATH
export JAVA_HOME=$GRAALVM_HOME
---
source ~/.bashrc
```

安装其它组件。
```
gu install -L llvm-toolchain-installable-java17-linux-amd64-22.1.0.jar
gu install -L native-image-installable-svm-svmee-java17-linux-amd64-22.1.0.jar
gu install -L nodejs-installable-svm-svmee-java17-linux-amd64-22.1.0.jar
gu install -L python-installable-svm-svmee-java17-linux-amd64-22.1.0.jar
gu install -L ruby-installable-svm-svmee-java17-linux-amd64-22.1.0.jar
gu install -L wasm-installable-svm-svmee-java17-linux-amd64-22.1.0.jar
gu install -L espresso-installable-svm-svmee-java17-linux-amd64-22.1.0.jar
gu install -L espresso-llvm-installable-ee-java17-linux-amd64-22.1.0.jar
gu install R
```

确认安装结果。
```
gu list
```

输出结果。
```
ComponentId              Version             Component name                Stability                     Origin
---------------------------------------------------------------------------------------------------------------------------------
graalvm                  22.1.0              GraalVM Core                  Supported
R                        22.1.0              FastR                         Experimental                  github.com
espresso                 22.1.0              Java on Truffle               Supported
espresso-llvm            22.1.0              Java on Truffle LLVM Java librSupported
js                       22.1.0              Graal.js                      Supported
llvm-toolchain           22.1.0              LLVM.org toolchain            Supported
native-image             22.1.0              Native Image                  Early adopter
nodejs                   22.1.0              Graal.nodejs                  Supported
python                   22.1.0              Graal.Python                  Experimental
ruby                     22.1.0              TruffleRuby                   Experimental
wasm                     22.1.0              GraalWasm                     Experimental
```

### Run Java

```
cat > HelloWorld.java <<EOF
public class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Hello, World!");
  }
}
EOF
```

Compile and Run
```
javac HelloWorld.java
java HelloWorld
Hello World!
```

### Native Images 

```
native-image HelloWorld
```

```
./helloworld
Hello, World!
```

### Run JavaScript and Node.js

```
$JAVA_HOME/bin/js
> 1 + 2
3
> quit()
```

```
$JAVA_HOME/bin/npm install colors ansispan express
```

```
cat > app.js <<EOF
const http = require("http");
const span = require("ansispan");
require("colors");

http.createServer(function (request, response) {
    response.writeHead(200, {"Content-Type": "text/html"});
    response.end(span("Hello Graal.js!".green));
}).listen(8000, function() { console.log("Graal.js server running at http://127.0.0.1:8000/".red); });

setTimeout(function() { console.log("DONE!"); process.exit(); }, 2000);
EOF
```

```
$JAVA_HOME/bin/node app.js
```

### Run LLVM Languages

```
export LLVM_TOOLCHAIN=$(lli --print-toolchain-path)
```

```
cat > hello.c <<EOF
#include <stdio.h>

int main() {
    printf("Hello from GraalVM!\n");
    return 0;
}
EOF
```

```
$LLVM_TOOLCHAIN/clang hello.c -o hello
lli hello
```

### Run Python

```
$JAVA_HOME/bin/graalpython --version
```

```
$JAVA_HOME/bin/graalpython
...
>>> 1 + 2
3
>>> exit()
```

### Run Ruby

```
gem install chunky_png
$JAVA_HOME/bin/ruby -r chunky_png -e "puts ChunkyPNG::Color.to_hex(ChunkyPNG::Color('mintcream @ 0.5'))"
#f5fffa80
```

### Run R

```
> 1+1
[1] 2
> q()
```

### Run WebAssembly 

```
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh
cd ..
```

```
cat > floyd.c <<EOF
#include <stdio.h>

int main() {
  int number = 1;
  int rows = 10;
  for (int i = 1; i <= rows; i++) {
    for (int j = 1; j <= i; j++) {
      printf("%d ", number);
      ++number;
    }
    printf(".\n");
  }
  return 0;
}
EOF
```

```
emcc -o floyd.wasm floyd.c
$JAVA_HOME/bin/wasm --Builtins=wasi_snapshot_preview1 floyd.wasm
```

### Polyglot Capabilities of Native Images

```
cat > PrettyPrintJSON.java <<EOF
import java.io.*;
import java.util.stream.*;
import org.graalvm.polyglot.*;

public class PrettyPrintJSON {
  public static void main(String[] args) throws java.io.IOException {
    BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
    String input = reader.lines()
    .collect(Collectors.joining(System.lineSeparator()));
    try (Context context = Context.create("js")) {
      Value parse = context.eval("js", "JSON.parse");
      Value stringify = context.eval("js", "JSON.stringify");
      Value result = stringify.execute(parse.execute(input), null, 2);
      System.out.println(result.asString());
    }
  }
}
EOF
```

```
javac PrettyPrintJSON.java
native-image --language:js --initialize-at-build-time PrettyPrintJSON
```

```
./prettyprintjson <<EOF
{"GraalVM":{"description":"Language Abstraction Platform","supports":["combining languages","embedding languages","creating native images"],"languages": ["Java","JavaScript","Node.js", "Python", "Ruby","R","LLVM"]}}
EOF
```

```
{
  "GraalVM": {
    "description": "Language Abstraction Platform",
    "supports": [
      "combining languages",
      "embedding languages",
      "creating native images"
    ],
    "languages": [
      "Java",
      "JavaScript",
      "Node.js",
      "Python",
      "Ruby",
      "R",
      "LLVM"
    ]
  }
}
```

