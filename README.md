import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

@Service
public class CacheService {

    private final Map<String, CachedApiResponse> globalCache = new ConcurrentHashMap<>();

    public String getCachedResponse(String url) {
        if (globalCache.containsKey(url)) {
            CachedApiResponse cachedResponse = globalCache.get(url);
            if (cachedResponse.isValid()) {
                return cachedResponse.getResponse();
            } else {
                globalCache.remove(url);
            }
        }
        return null; // Return null if no cached response found or expired
    }

    public void cacheResponse(String url, String response) {
        globalCache.put(url, new CachedApiResponse(response, System.currentTimeMillis()));
    }

    // Helper class to store cached API response along with timestamp
    private static class CachedApiResponse {
        private final String response;
        private final long timestamp;

        public CachedApiResponse(String response, long timestamp) {
            this.response = response;
            this.timestamp = timestamp;
        }

        public String getResponse() {
            return response;
        }

        public boolean isValid() {
            return System.currentTimeMillis() - timestamp <= 300000; // 5 minutes in milliseconds
        }
    }
}
