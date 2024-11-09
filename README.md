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
