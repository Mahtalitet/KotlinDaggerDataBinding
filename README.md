# Kotlin + Dagger2 + DataBinding + Orma sample

This is my sample project to use Dagger2 + Kotlin.

But this could not compiled when generated code provide with Dagger2 in Kotlin.

This replicate when recompile after clean project.

Error message:

```
error: cannot access NonExistentClass
  class file for error.NonExistentClass not found
  Consult the following stack trace for details.
  com.sun.tools.javac.code.Symbol$CompletionFailure: class file for error.NonExistentClass not found
1 error

:app:compileDebugJavaWithJavac FAILED

FAILURE: Build failed with an exception.
```

See [stacktrace](stacktrace) by `./gradlew :app:compileDebugJavaWithJavac --stacktrace`.

## Environment

+ Android Studio 2.1
+ jdk 1.8.0_77
+ Kotlin 1.0.2

## Check

+ I can inject with Dagger2 in Kotlin (checkout branch dagger_kotlin_exclude_orma)
+ I can inject generated code with Dagger2 in Java (checkout branch dagger_java)

## What causes

~~I seems to be a bug in kapt.
Because i can compile after comment out `provideOrmaDatabase()` and `@Inject lateinit var orma:OrmaDatabase`.
Dagger2 can not resolve generated code by annotation processing in kapt?~~

~~I think this is similar to [issue KT-10352](https://youtrack.jetbrains.com/issue/KT-10352).~~

It is limitation of kapt.

> You have types generated by Annotation Processing in your declaration signatures, like fun provideOrmaDatabase(context: Context) or lateinit var orma: OrmaDatabase. Because Kotlin makes its stubs before Java Annotation Processing runs, Kotlin knows just nothing about OrmaDatabase, and the name of the declaration in stubs will be error.NonExistentClass. This breaks the Annotation Processing tool.
 It's a kind of kapt limitation, and we currently doesn't know a way to fix it without reimplementing Annotation Processing in Kotlin.
 But as a workaround, you can change the return type to DatabaseHandle, and make an explicit cast inside your function, this fixes the build.
 
Avoid to provide generated code by kapt directly with Dagger2 then we can avoid this issue.


See below.

- [https://kotlinlang-jp.slack.com/archives/android/p1464803567000032](https://kotlinlang-jp.slack.com/archives/android/p1464803567000032)
- [http://qiita.com/chibatching/items/e5a0f5016093abec884b](http://qiita.com/chibatching/items/e5a0f5016093abec884b)
