
## Specific Problems:


### Metabase fails to start due to PermGen OutOfMemoryErrors

On Java 7, Metabase may fail to launch with a message like

    java.lang.OutOfMemoryError: PermGen space

or one like

    Exception: java.lang.OutOfMemoryError thrown from the UncaughtExceptionHandler

If this happens, setting a few JVM options should fix your issue:

    java -XX:+CMSClassUnloadingEnabled -XX:+UseConcMarkSweepGC -XX:MaxPermSize=256m -jar target/uberjar/metabase.jar

You can also pass JVM arguments by setting the environment variable `JAVA_TOOL_OPTIONS`, e.g.

    JAVA_TOOL_OPTIONS='-XX:+CMSClassUnloadingEnabled -XX:+UseConcMarkSweepGC -XX:MaxPermSize=256m'

Alternatively, you can upgrade to Java 8 instead, which will fix the issue as well.


### Metabase fails to start due to Heap Space OutOfMemoryErrors

Normally, the JVM can figure out how much RAM is available on the system and automatically set a sensible upper bound for heap memory usage. On certain shared hosting
environments, however, this doesn't always work perfectly. If Metabase fails to start with an error message like

    java.lang.OutOfMemoryError: Java heap space

You'll just need to set a JVM option to let it know explicitly how much memory it should use for the heap space:

    java -Xmx2g -jar metabase.jar

Adjust this number as appropriate for your shared hosting instance. Make sure to set the number lower than the total amount of RAM available on your instance, because Metabase isn't the only process that'll be running. Generally, leaving 1-2 GB of RAM for these other processes should be enough; for example, you might set `-Xmx` to `1g` for an instance with 2 GB of RAM, `2g` for one with 4 GB of RAM, `6g` for an instance with 8 GB of RAM, and so forth. You may need to experiment with these settings a bit to find the right number.

As above, you can use the environment variable `JAVA_TOOL_OPTIONS` to set JVM args instead of passing them directly to `java`. This is useful when running the Docker image,
for example.

    docker run -d -p 3000:3000 -e "JAVA_TOOL_OPTIONS=-Xmx2g" metabase/metabase