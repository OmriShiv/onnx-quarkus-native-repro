# onnx-quarkus-native-repro
Small repository to reproduce issue with building native image with Quarkus and Onnx

## Steps to reproduce
```
./gradlew clean build -Dquarkus.package.type=native -Dquarkus.native.container-build=true
docker build -f src/main/docker/Dockerfile.native -t quarkus/quarkus-onnx-test .
docker run -i --rm -p 8080:8080 quarkus/quarkus-onnx-test
curl localhost:8080/hello
```

## Expectation
Output of `ortEnvironment.toString()` or at least no link errors. 

## Issue
```
 ERROR [io.qua.ver.htt.run.QuarkusErrorHandler] (executor-thread-1) HTTP Request to /hello failed, error id: 012f95af-09cd-48aa-839b-2cb6d476b5f6-1: org.jboss.resteasy.spi.UnhandledException: java.lang.UnsatisfiedLinkError: ai.onnxruntime.OrtEnvironment.createHandle(JILjava/lang/String;)J [symbol: Java_ai_onnxruntime_OrtEnvironment_createHandle or Java_ai_onnxruntime_OrtEnvironment_createHandle__JILjava_lang_String_2]
        at org.jboss.resteasy.core.ExceptionHandler.handleApplicationException(ExceptionHandler.java:106)
        at org.jboss.resteasy.core.ExceptionHandler.handleException(ExceptionHandler.java:372)
        at org.jboss.resteasy.core.SynchronousDispatcher.writeException(SynchronousDispatcher.java:216)
        at org.jboss.resteasy.core.SynchronousDispatcher.invoke(SynchronousDispatcher.java:515)
        at org.jboss.resteasy.core.SynchronousDispatcher.lambda$invoke$4(SynchronousDispatcher.java:259)
        at org.jboss.resteasy.core.SynchronousDispatcher.lambda$preprocess$0(SynchronousDispatcher.java:160)
        at org.jboss.resteasy.core.interception.jaxrs.PreMatchContainerRequestContext.filter(PreMatchContainerRequestContext.java:364)
        at org.jboss.resteasy.core.SynchronousDispatcher.preprocess(SynchronousDispatcher.java:163)
        at org.jboss.resteasy.core.SynchronousDispatcher.invoke(SynchronousDispatcher.java:245)
        at io.quarkus.resteasy.runtime.standalone.RequestDispatcher.service(RequestDispatcher.java:73)
        at io.quarkus.resteasy.runtime.standalone.VertxRequestHandler.dispatch(VertxRequestHandler.java:131)
        at io.quarkus.resteasy.runtime.standalone.VertxRequestHandler.access$000(VertxRequestHandler.java:37)
        at io.quarkus.resteasy.runtime.standalone.VertxRequestHandler$1.run(VertxRequestHandler.java:94)
        at org.jboss.threads.ContextClassLoaderSavingRunnable.run(ContextClassLoaderSavingRunnable.java:35)
        at org.jboss.threads.EnhancedQueueExecutor.safeRun(EnhancedQueueExecutor.java:2046)
        at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.doRunTask(EnhancedQueueExecutor.java:1578)
        at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.run(EnhancedQueueExecutor.java:1452)
        at org.jboss.threads.DelegatingRunnable.run(DelegatingRunnable.java:29)
        at org.jboss.threads.ThreadLocalResettingRunnable.run(ThreadLocalResettingRunnable.java:29)
        at java.lang.Thread.run(Thread.java:834)
        at org.jboss.threads.JBossThread.run(JBossThread.java:479)
        at com.oracle.svm.core.thread.JavaThreads.threadStartRoutine(JavaThreads.java:517)
        at com.oracle.svm.core.posix.thread.PosixJavaThreads.pthreadStartRoutine(PosixJavaThreads.java:192)
Caused by: java.lang.UnsatisfiedLinkError: ai.onnxruntime.OrtEnvironment.createHandle(JILjava/lang/String;)J [symbol: Java_ai_onnxruntime_OrtEnvironment_createHandle or Java_ai_onnxruntime_OrtEnvironment_createHandle__JILjava_lang_String_2]
        at com.oracle.svm.jni.access.JNINativeLinkage.getOrFindEntryPoint(JNINativeLinkage.java:153)
        at com.oracle.svm.jni.JNIGeneratedMethodSupport.nativeCallAddress(JNIGeneratedMethodSupport.java:57)
        at ai.onnxruntime.OrtEnvironment.createHandle(OrtEnvironment.java)
        at ai.onnxruntime.OrtEnvironment.<init>(OrtEnvironment.java:122)
        at ai.onnxruntime.OrtEnvironment.getEnvironment(OrtEnvironment.java:83)
        at ai.onnxruntime.OrtEnvironment.getEnvironment(OrtEnvironment.java:45)
        at quarkus.ExampleResource.hello(ExampleResource.java:16)
        at java.lang.reflect.Method.invoke(Method.java:566)
        at org.jboss.resteasy.core.MethodInjectorImpl.invoke(MethodInjectorImpl.java:167)
        at org.jboss.resteasy.core.MethodInjectorImpl.invoke(MethodInjectorImpl.java:130)
        at org.jboss.resteasy.core.ResourceMethodInvoker.internalInvokeOnTarget(ResourceMethodInvoker.java:638)
        at org.jboss.resteasy.core.ResourceMethodInvoker.invokeOnTargetAfterFilter(ResourceMethodInvoker.java:504)
        at org.jboss.resteasy.core.ResourceMethodInvoker.lambda$invokeOnTarget$2(ResourceMethodInvoker.java:454)
        at org.jboss.resteasy.core.interception.jaxrs.PreMatchContainerRequestContext.filter(PreMatchContainerRequestContext.java:364)
        at org.jboss.resteasy.core.ResourceMethodInvoker.invokeOnTarget(ResourceMethodInvoker.java:456)
        at org.jboss.resteasy.core.ResourceMethodInvoker.invoke(ResourceMethodInvoker.java:417)
        at org.jboss.resteasy.core.ResourceMethodInvoker.invoke(ResourceMethodInvoker.java:391)
        at org.jboss.resteasy.core.ResourceMethodInvoker.invoke(ResourceMethodInvoker.java:68)
        at org.jboss.resteasy.core.SynchronousDispatcher.invoke(SynchronousDispatcher.java:488)
        ... 19 more

```