```aidl
docker build --platform linux/amd64 --build-arg GRAALVM_VERSION=22.1.0 --build-arg JAVA_VERSION=java17 -t atjapan2015/graalvm-ce-java17:22.1.0 --output=type=docker .
docker push atjapan2015/graalvm-ce-java17:22.1.0
```
