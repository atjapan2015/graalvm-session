1. Build
```aidl
source ~/.graalvm_java11
./mvnw clean package
```

2. Test with GraalVM
```aidl
source ~/.graalvm_java11
java -jar target/benchmarks.jar -rf json
```

3. Upload jmh-result.json to JMH Visualizer
```aidl
https://jmh.morethan.io/
```

4. Test with OracleJDK
```aidl
source ~/.oraclejdk_java11
java -jar target/benchmarks.jar -rf json
```

or
```aidl
source ~/.graalvm_java11
java -XX:-UseJVMCICompiler -jar target/benchmarks.jar -rf json
```

5. Upload jmh-result.json to JMH Visualizer
```aidl
https://jmh.morethan.io/
```