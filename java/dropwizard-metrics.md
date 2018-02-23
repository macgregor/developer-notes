# Drop Wizard Metrics
Dropwizard Metrics is a java library designed to abstract gathering and reporting of metrics. It supports several metric types: Gauge, Counters, Histograms, Meters and Timers and can report these metrics in a variety of ways including JMX, Console, CSV files, SLF4J loggers, and graphite. The integration with graphite makes it particularly powerful as you can then store and analyze your metrics with many different tools like grafana to visualize performance or Shinken for alerting off metrics.

## Metric Registry
The `MetricRegistry` class is a collection of all the metrics for your application. Each metric is associated with a `MetricRegistry` and has a unique name within that registry (e.g. com.example.Queue.size). There is a static helper method on the `MetricRegistry` class to help create names:
```Java
MetricRegistry.name(Queue.class, "requests", "size")
MetricRegistry.name(Queue.class, "responses", "size")
```

Global named registries can be shared through the static SharedMetricRegistries class. This allows the same registry to be used in different sections of code without explicitly passing a MetricRegistry instance around.

## Metric types

### Gauge
Records a single value at a given point in time, for example measuring the rate of cache evictions over time. Each tick we get and record the number of cache evictions.

```Java
registry.register(name(SessionStore.class, "cache-evictions"), new Gauge<Integer>() {
    @Override
    public Integer getValue() {
        return cache.getEvictionsCount();
    }
});
```

### Counters
A counter is a simple incrementing and decrementing 64-bit integer

```Java
final Counter evictions = registry.counter(name(SessionStore.class, "cache-evictions"));
evictions.inc();
evictions.inc(3);
evictions.dec();
evictions.dec(2);
```

### Histograms
Measures the distribution of values in a stream of data: e.g., the number of results returned by a search. Allow you to measure not just easy things like the min, mean, max, and standard deviation of values, but also quantiles like the median or 95th percentile.

```Java
final Histogram resultCounts = registry.histogram(name(ProductDAO.class, "result-counts");
resultCounts.update(results.size());
```

How a histogram gathers and calculates metrics can be changed, see [reservoir](http://metrics.dropwizard.io/3.1.0/apidocs/index.html?com/codahale/metrics/class-use/Reservoir.html).

### Meters
Measures the rate at which a set of events occur. Meters measure the rate of the events in a few different ways. The mean rate is the average rate of events. It’s generally useful for trivia, but as it represents the total rate for your application’s entire lifetime (e.g., the total number of requests handled, divided by the number of seconds the process has been running), it does’t offer a sense of recency.

```Java
final Meter getRequests = registry.meter(name(WebProxy.class, "get-requests", "requests"));
getRequests.mark();
getRequests.mark(requests.size());
```

### Timers
A timer is basically a histogram of the duration of a type of event and a meter of the rate of its occurrence. Can be used to time things like how long it takes for a database request to take and do statistical analysis of this data.

```Java
final Timer timer = registry.timer(name(WebProxy.class, "get-requests"));

final Timer.Context context = timer.time();
try {
    // handle request
} finally {
    context.stop();
}
```

## Health Checks
When an application relies on some external service (a database, a rest service, etc) to monitor the health of that external service explicitly. [`metrics-healthchecks`](https://mvnrepository.com/artifact/io.dropwizard.metrics/metrics-healthchecks) provides a way to do this with the `HealthCheck` class.

```Java
public class DatabaseHealthCheck extends HealthCheck {
    private final Database database;

    public DatabaseHealthCheck(Database database) {
        this.database = database;
    }

    @Override
    protected Result check() throws Exception {
        if (database.ping()) {
            return Result.healthy();
        }
        return Result.unhealthy("Can't ping database");
    }
}

registry.register("database", new DatabaseHealthCheck(database));
```

## JVM Instrumentation
The [`metrics-jvm`](https://mvnrepository.com/artifact/io.dropwizard.metrics/metrics-jvm) module contains a number of reusable gauges and metric sets which allow you to easily instrument JVM internals such as garbage collection time, memory usage, thread usage etc.

```Java
import com.codahale.metrics.MetricRegistry;
import com.codahale.metrics.jvm.GarbageCollectorMetricSet;
import com.codahale.metrics.jvm.MemoryUsageGaugeSet;

MetricRegistry registry = new MetricRegistry();

registry.registerAll(new MemoryUsageGaugeSet());  registry.registerAll(new GarbageCollectorMetricSet());
```

```bash
7/26/15 10:42:14 AM ============================================================  

-- Gauges ----------------------------------------------------------------------  
PS-MarkSweep.count  
       value = 1  
PS-MarkSweep.time  
       value = 40  
PS-Scavenge.count  
       value = 7  
PS-Scavenge.time  
       value = 52
```

[This blog](http://www.heapwhisperer.com/2015/07/exposing-jvm-metrics-in-spring-boot.html) has a good demo application using spring boot and dropwizard metrics, including a section explaining what JVM metrics are provided and what they mean.

## Instrumenting Web Applications
The [`metrics-servlet`](https://mvnrepository.com/artifact/io.dropwizard.metrics/metrics-servlet) module provides a Servlet filter which has meters for status codes, a counter for the number of active requests, and a timer for request duration.

In `web.xml`:
```xml
<filter>
    <filter-name>instrumentedFilter</filter-name>
    <filter-class>com.codahale.metrics.servlet.InstrumentedFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>instrumentedFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

You will need to add your `MetricRegistry` to the servlet context as an attribute named `com.codahale.metrics.servlet.InstrumentedFilter.registry` you can do this several ways, like extending `InstrumentedFilterContextListener` or dependency injection

### metrics-servlet vs metrics-servlets
Here are two very similar and confusingly named modules. I'm still a little confused without having worked with it first hand, but my understanding is that `metrics-servlet` (sans 's') will automatically gather metrics around your various request endpoints while `metrics-servlets` (sans-sans 's') provides a sort of admin interface into your metrics which you can access via http. It also provides a couple convenience endpoints like `/ping` which provides you a ping endpoint and `/healthcheck` which runs all your healthchecks but it is primarily about access to metrics.

So `metrics-servlet` helps gathering data while `metrics-servlets` helps access data.

## Spring Integration
The [`metrics-spring`](https://mvnrepository.com/artifact/com.ryantenney.metrics/metrics-spring) module is a third-party module which enables the use of Metrics annotations with Spring AOP, complete with simple XML configuration

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:metrics="http://www.yammer.com/schema/metrics"
       xsi:schemaLocation="
           http://www.yammer.com/schema/metrics http://www.yammer.com/schema/metrics/metrics.xsd
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd">

    <metrics:metrics-registry id="metrics"/>
    <metrics:health-check-registry id="health"/>

    <metrics:annotation-driven metrics-registry="metrics" health-check-registry="health"/>

    <metrics:jmx-reporter id="metricsJmxReporter" metrics-registry="metrics"/>

    <!-- other beans -->

</beans>
```

## Graphite Integration
The [`metrics-graphite`](https://mvnrepository.com/artifact/io.dropwizard.metrics/metrics-graphite) module provides GraphiteReporter, which allows your application to constantly stream metric values to a Graphite server

```Java
final Graphite graphite = new Graphite(new InetSocketAddress("graphite.example.com", 2003));
final GraphiteReporter reporter = GraphiteReporter.forRegistry(registry)
                                                  .prefixedWith("web1.example.com")
                                                  .convertRatesTo(TimeUnit.SECONDS)
                                                  .convertDurationsTo(TimeUnit.MILLISECONDS)
                                                  .filter(MetricFilter.ALL)
                                                  .build(graphite);
reporter.start(1, TimeUnit.MINUTES);
```

It can easily be combined with the `metrics-spring` module to get all the spring DI goodies as seen in
[this blog](https://craftsmen.nl/application-monitoring-with-graphite-an-example-how-to-integrate-dropwizard-metrics-in-a-spring-boot-application/).

# Alternative - Netflix Servo

[Netflix Servo](https://github.com/Netflix/servo) was created apparently because dropwizard metrics wasn't satisfying the people at Netflix who decided to write their own library in the same vein. It offers many of the same concepts as Dropwizard Metrics including annotation drive metrics, automatic JMX exposure and publishing to external systems. Not sure if the ecosystem is as large as metrics, it doesn't seem to be at first glance.

Most of the other alternatives I found like [javasimon](https://github.com/virgo47/javasimon) and [ERMA](https://github.com/erma/erma/wiki) seemed far less developed, documented, supported, etc. They honestly don't warrant very much examination compared to Dropwizard Metrics and Servo. I could be wrong/lazy though.

# Additional Resources
* [Dropwizard Metrics](http://metrics.dropwizard.io/3.2.2/)
* [Dropwizard Metrics 101 (blog)](http://lcbecker.github.io/blog/2015/11/18/dropwizard-metrics-101/)
* [Spring + Dropwizard Metrics (blog)](http://www.heapwhisperer.com/2015/07/exposing-jvm-metrics-in-spring-boot.html)
* [nice, broad, practical example of industrial drop wizard metrics use](http://www.baeldung.com/dropwizard-metrics)
* [good demo project](https://github.com/markmclaren/metrics-spring-example) - uses metrics-servlet, metrics-servlets, metrics-annotation, metrics-jvm, metrics-spring
* [demo showing spring + graphite config](https://craftsmen.nl/application-monitoring-with-graphite-an-example-how-to-integrate-dropwizard-metrics-in-a-spring-boot-application/)
