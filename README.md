# Fixed Window Rate Limiter

```java
import java.time.Instant;
import java.util.concurrent.atomic.AtomicInteger;

public class FixedWindowRateLimiter {
    private final int maxRequests; // Maximum requests allowed per window
    private final long windowDurationMillis; // Duration of each window in milliseconds
    private AtomicInteger requestCount; // Count of requests in the current window
    private long windowStartTime; // Start time of the current window

    public FixedWindowRateLimiter(int maxRequests, long windowDurationMillis) {
        this.maxRequests = maxRequests;
        this.windowDurationMillis = windowDurationMillis;
        this.requestCount = new AtomicInteger(0);
        this.windowStartTime = Instant.now().toEpochMilli();
    }

    public synchronized boolean allowRequest() {
        long currentTime = Instant.now().toEpochMilli();
        // Check if the current time is outside the current window
        if (currentTime - windowStartTime >= windowDurationMillis) {
            // Reset the window
            windowStartTime = currentTime;
            requestCount.set(0);
        }

        // Check if the request can be allowed
        if (requestCount.incrementAndGet() <= maxRequests) {
            return true; // Request allowed
        } else {
            return false; // Request throttled
        }
    }

    public static void main(String[] args) throws InterruptedException {
        // Example: Allow up to 5 requests per 10 seconds window
        FixedWindowRateLimiter rateLimiter = new FixedWindowRateLimiter(5, 10000);
        // Simulate 10 incoming requests
        for (int i = 0; i < 22; i++) {
            if (rateLimiter.allowRequest()) {
                System.out.println("Request " + (i + 1) + ": Allowed");
            } else {
                System.out.println("Request " + (i + 1) + ": Throttled");
            }
            Thread.sleep(1000); // Sleep for 1 second between requests
        }
    }
}

```

### Sleep randomly between requests : Thread.sleep((long)(Math.random() * 3500));
Sleep randomly between 0 to 3.5 second between requests
```
Request 1: Allowed at 2024-11-09T01:48:54.155444200Z
Request 2: Allowed at 2024-11-09T01:48:56.627097500Z
Request 3: Allowed at 2024-11-09T01:48:59.404685200Z
Request 4: Allowed at 2024-11-09T01:48:59.560314200Z
Request 5: Allowed at 2024-11-09T01:49:02.487695700Z
Request 6: Throttled at 2024-11-09T01:49:03.102031400Z
Request 7: Throttled at 2024-11-09T01:49:04.114203700Z
Request 8: Allowed at 2024-11-09T01:49:05.489638Z
Request 9: Allowed at 2024-11-09T01:49:07.868572500Z
Request 10: Allowed at 2024-11-09T01:49:08.069096400Z
Request 11: Allowed at 2024-11-09T01:49:09.860865Z
Request 12: Allowed at 2024-11-09T01:49:13.226450800Z
Request 13: Allowed at 2024-11-09T01:49:16.537556200Z
Request 14: Allowed at 2024-11-09T01:49:19.958231400Z
Request 15: Allowed at 2024-11-09T01:49:22.875345400Z
Request 16: Allowed at 2024-11-09T01:49:24.022313200Z
Request 17: Allowed at 2024-11-09T01:49:26.143643200Z
Request 18: Allowed at 2024-11-09T01:49:29.561869100Z
Request 19: Allowed at 2024-11-09T01:49:30.477400500Z
Request 20: Allowed at 2024-11-09T01:49:31.424657900Z
Request 21: Allowed at 2024-11-09T01:49:33.937672600Z
Request 22: Allowed at 2024-11-09T01:49:36.796995200Z
```
