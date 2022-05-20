```aidl
source ~/.graalvm_java17
./mvnw spring-boot:build-image

java -jar ./target/spring-native-1.0.0.jar

docker run --rm -p 8080:8080 spring-native:1.0.0
```
