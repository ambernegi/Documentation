# Reference Guide

Complete reference documentation for all Google Maps API endpoints, parameters, and responses.

## API Overview

### Base URL
All Google Maps API requests use the base URL:
```
https://maps.googleapis.com/maps/api/
```

### Authentication
All requests require an API key passed as the `key` parameter.

### Response Format
All APIs return JSON responses with this structure:
```json
{
  "status": "OK",
  "results": [...],
  "error_message": "..."
}
```

## Geocoding API

### Endpoint
```
GET https://maps.googleapis.com/maps/api/geocode/json
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `address` | string | Yes* | Address to geocode |
| `latlng` | string | Yes* | Coordinates to reverse geocode |
| `components` | string | No | Component filtering |
| `bounds` | string | No | Viewport biasing |
| `language` | string | No | Response language |
| `region` | string | No | Region biasing |
| `key` | string | Yes | Your API key |

*Either `address` or `latlng` is required

### Example Request

=== "Python"
    ```python
    import requests
    
    def geocode_address(address, api_key):
        url = "https://maps.googleapis.com/maps/api/geocode/json"
        params = {
            "address": address,
            "key": api_key
        }
        
        response = requests.get(url, params=params)
        return response.json()
    ```

=== "Java"
    ```java
    public JsonObject geocodeAddress(String address, String apiKey) throws Exception {
        String url = "https://maps.googleapis.com/maps/api/geocode/json";
        Map<String, String> params = new HashMap<>();
        params.put("address", address);
        params.put("key", apiKey);
        
        // Implementation details...
        return response;
    }
    ```

### Response Structure

```json
{
  "status": "OK",
  "results": [
    {
      "formatted_address": "1600 Amphitheatre Parkway, Mountain View, CA 94043, USA",
      "geometry": {
        "location": {
          "lat": 37.4221,
          "lng": -122.0841
        },
        "location_type": "ROOFTOP",
        "viewport": {
          "northeast": {
            "lat": 37.4234499802915,
            "lng": -122.0827500197085
          },
          "southwest": {
            "lat": 37.4207520197085,
            "lng": -122.0854499802915
          }
        }
      },
      "place_id": "ChIJN1t_tDeuEmsRUsoyG83frY4",
      "types": ["street_address"]
    }
  ]
}
```

### Status Codes

| Status | Description |
|--------|-------------|
| `OK` | Request was successful |
| `ZERO_RESULTS` | No results found |
| `OVER_QUERY_LIMIT` | Quota exceeded |
| `REQUEST_DENIED` | API key invalid or restricted |
| `INVALID_REQUEST` | Missing required parameters |

## Directions API

### Endpoint
```
GET https://maps.googleapis.com/maps/api/directions/json
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `origin` | string | Yes | Starting location |
| `destination` | string | Yes | Ending location |
| `waypoints` | string | No | Intermediate locations |
| `mode` | string | No | Transportation mode |
| `alternatives` | boolean | No | Return alternative routes |
| `avoid` | string | No | Route restrictions |
| `language` | string | No | Response language |
| `units` | string | No | Distance units |
| `key` | string | Yes | Your API key |

### Transportation Modes

| Mode | Description |
|------|-------------|
| `driving` | Car directions |
| `walking` | Walking directions |
| `bicycling` | Bicycle directions |
| `transit` | Public transit directions |

### Example Request

=== "Python"
    ```python
    def get_directions(origin, destination, mode="driving"):
        url = "https://maps.googleapis.com/maps/api/directions/json"
        params = {
            "origin": origin,
            "destination": destination,
            "mode": mode,
            "key": api_key
        }
        
        response = requests.get(url, params=params)
        return response.json()
    ```

=== "Java"
    ```java
    public JsonObject getDirections(String origin, String destination, String mode) throws Exception {
        String url = "https://maps.googleapis.com/maps/api/directions/json";
        Map<String, String> params = new HashMap<>();
        params.put("origin", origin);
        params.put("destination", destination);
        params.put("mode", mode);
        params.put("key", apiKey);
        
        // Implementation details...
        return response;
    }
    ```

### Response Structure

```json
{
  "status": "OK",
  "routes": [
    {
      "bounds": {
        "northeast": {"lat": 42.3601, "lng": -71.0589},
        "southwest": {"lat": 40.7128, "lng": -74.0060}
      },
      "legs": [
        {
          "distance": {
            "text": "215 mi",
            "value": 345600
          },
          "duration": {
            "text": "3 hours 45 mins",
            "value": 13500
          },
          "start_address": "New York, NY, USA",
          "end_address": "Boston, MA, USA",
          "steps": [...]
        }
      ],
      "overview_polyline": {
        "points": "encoded_polyline_string"
      }
    }
  ]
}
```

## Places API

### Nearby Search Endpoint
```
GET https://maps.googleapis.com/maps/api/place/nearbysearch/json
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `location` | string | Yes | Center point (lat,lng) |
| `radius` | integer | Yes* | Search radius in meters |
| `type` | string | No | Place type filter |
| `keyword` | string | No | Search keyword |
| `minprice` | integer | No | Minimum price level |
| `maxprice` | integer | No | Maximum price level |
| `opennow` | boolean | No | Only open places |
| `key` | string | Yes | Your API key |

*Required unless `rankby=distance`

### Find Place Endpoint
```
GET https://maps.googleapis.com/maps/api/place/findplacefromtext/json
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `input` | string | Yes | Text input |
| `inputtype` | string | Yes | Input type |
| `fields` | string | Yes | Response fields |
| `locationbias` | string | No | Location biasing |
| `key` | string | Yes | Your API key |

### Example Request

=== "Python"
    ```python
    def nearby_search(lat, lng, radius, place_type=None):
        url = "https://maps.googleapis.com/maps/api/place/nearbysearch/json"
        params = {
            "location": f"{lat},{lng}",
            "radius": radius,
            "key": api_key
        }
        
        if place_type:
            params["type"] = place_type
        
        response = requests.get(url, params=params)
        return response.json()
    ```

=== "Java"
    ```java
    public JsonObject nearbySearch(double lat, double lng, int radius, String placeType) throws Exception {
        String url = "https://maps.googleapis.com/maps/api/place/nearbysearch/json";
        Map<String, String> params = new HashMap<>();
        params.put("location", lat + "," + lng);
        params.put("radius", String.valueOf(radius));
        params.put("key", apiKey);
        
        if (placeType != null) {
            params.put("type", placeType);
        }
        
        // Implementation details...
        return response;
    }
    ```

### Response Structure

```json
{
  "status": "OK",
  "results": [
    {
      "name": "Restaurant Name",
      "place_id": "ChIJN1t_tDeuEmsRUsoyG83frY4",
      "geometry": {
        "location": {
          "lat": 37.4221,
          "lng": -122.0841
        }
      },
      "vicinity": "123 Main St, City, State",
      "rating": 4.5,
      "types": ["restaurant", "food", "establishment"]
    }
  ]
}
```

## Distance Matrix API

### Endpoint
```
GET https://maps.googleapis.com/maps/api/distancematrix/json
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `origins` | string | Yes | Starting locations |
| `destinations` | string | Yes | Ending locations |
| `mode` | string | No | Transportation mode |
| `language` | string | No | Response language |
| `units` | string | No | Distance units |
| `key` | string | Yes | Your API key |

### Example Request

=== "Python"
    ```python
    def distance_matrix(origins, destinations, mode="driving"):
        url = "https://maps.googleapis.com/maps/api/distancematrix/json"
        params = {
            "origins": "|".join(origins),
            "destinations": "|".join(destinations),
            "mode": mode,
            "key": api_key
        }
        
        response = requests.get(url, params=params)
        return response.json()
    ```

=== "Java"
    ```java
    public JsonObject distanceMatrix(List<String> origins, List<String> destinations, String mode) throws Exception {
        String url = "https://maps.googleapis.com/maps/api/distancematrix/json";
        Map<String, String> params = new HashMap<>();
        params.put("origins", String.join("|", origins));
        params.put("destinations", String.join("|", destinations));
        params.put("mode", mode);
        params.put("key", apiKey);
        
        // Implementation details...
        return response;
    }
    ```

### Response Structure

```json
{
  "status": "OK",
  "origin_addresses": ["New York, NY, USA"],
  "destination_addresses": ["Boston, MA, USA"],
  "rows": [
    {
      "elements": [
        {
          "distance": {
            "text": "215 mi",
            "value": 345600
          },
          "duration": {
            "text": "3 hours 45 mins",
            "value": 13500
          },
          "status": "OK"
        }
      ]
    }
  ]
}
```

## Maps JavaScript API

### Loading the API

```html
<!DOCTYPE html>
<html>
<head>
    <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY"></script>
</head>
<body>
    <div id="map" style="height: 400px; width: 100%;"></div>
    <script>
        function initMap() {
            const map = new google.maps.Map(document.getElementById("map"), {
                center: { lat: -34.397, lng: 150.644 },
                zoom: 8,
            });
        }
    </script>
</body>
</html>
```

### Basic Map

=== "JavaScript"
    ```javascript
    function createBasicMap() {
        const map = new google.maps.Map(document.getElementById("map"), {
            center: { lat: 37.7749, lng: -122.4194 },
            zoom: 12,
            mapTypeId: google.maps.MapTypeId.ROADMAP
        });
    }
    ```

### Adding Markers

=== "JavaScript"
    ```javascript
    function addMarker(map, position, title) {
        const marker = new google.maps.Marker({
            position: position,
            map: map,
            title: title
        });
        
        return marker;
    }
    
    // Usage
    const map = createBasicMap();
    const marker = addMarker(map, 
        { lat: 37.7749, lng: -122.4194 }, 
        "San Francisco"
    );
    ```

### Info Windows

=== "JavaScript"
    ```javascript
    function addInfoWindow(marker, content) {
        const infoWindow = new google.maps.InfoWindow({
            content: content
        });
        
        marker.addListener("click", () => {
            infoWindow.open(map, marker);
        });
        
        return infoWindow;
    }
    ```

## Error Handling

### Common Error Responses

```json
{
  "status": "REQUEST_DENIED",
  "error_message": "This API project is not authorized. Please ensure this API is enabled in the Google Cloud Console."
}
```

### Error Status Codes

| Status | HTTP Code | Description |
|--------|-----------|-------------|
| `OK` | 200 | Request successful |
| `ZERO_RESULTS` | 200 | No results found |
| `OVER_QUERY_LIMIT` | 429 | Quota exceeded |
| `REQUEST_DENIED` | 403 | API key invalid or restricted |
| `INVALID_REQUEST` | 400 | Missing required parameters |
| `UNKNOWN_ERROR` | 500 | Server error |

### Error Handling Implementation

=== "Python"
    ```python
    def handle_api_error(response):
        status = response.get("status")
        
        error_messages = {
            "ZERO_RESULTS": "No results found for the given query.",
            "OVER_QUERY_LIMIT": "Quota exceeded. Please try again later.",
            "REQUEST_DENIED": "API key invalid or restricted.",
            "INVALID_REQUEST": "Invalid request parameters.",
            "UNKNOWN_ERROR": "An unknown error occurred."
        }
        
        if status in error_messages:
            raise Exception(error_messages[status])
        elif status != "OK":
            raise Exception(f"API Error: {status}")
    ```

=== "Java"
    ```java
    public void handleApiError(JsonObject response) throws Exception {
        String status = response.get("status").getAsString();
        
        switch (status) {
            case "ZERO_RESULTS":
                throw new Exception("No results found for the given query.");
            case "OVER_QUERY_LIMIT":
                throw new Exception("Quota exceeded. Please try again later.");
            case "REQUEST_DENIED":
                throw new Exception("API key invalid or restricted.");
            case "INVALID_REQUEST":
                throw new Exception("Invalid request parameters.");
            case "UNKNOWN_ERROR":
                throw new Exception("An unknown error occurred.");
            case "OK":
                return;
            default:
                throw new Exception("API Error: " + status);
        }
    }
    ```

## Rate Limiting

### Quotas

| Service | Free Tier | Paid Tier |
|---------|-----------|-----------|
| Geocoding API | 2,500 requests/day | 100,000 requests/day |
| Directions API | 2,500 requests/day | 100,000 requests/day |
| Places API | 1,000 requests/day | 150,000 requests/day |
| Distance Matrix API | 100 requests/day | 100,000 requests/day |
| Maps JavaScript API | 28,500 map loads/month | 850,000 map loads/month |

### Rate Limiting Implementation

=== "Python"
    ```python
    import time
    from collections import defaultdict
    
    class RateLimiter:
        def __init__(self, max_requests_per_day):
            self.max_requests = max_requests_per_day
            self.requests_today = defaultdict(int)
        
        def can_make_request(self):
            today = time.strftime("%Y-%m-%d")
            if self.requests_today[today] < self.max_requests:
                self.requests_today[today] += 1
                return True
            return False
        
        def wait_if_needed(self):
            while not self.can_make_request():
                time.sleep(60)  # Wait 1 minute
    ```

=== "Java"
    ```java
    import java.time.LocalDate;
    import java.util.concurrent.ConcurrentHashMap;
    
    public class RateLimiter {
        private final int maxRequestsPerDay;
        private final ConcurrentHashMap<LocalDate, Integer> requestsToday = new ConcurrentHashMap<>();
        
        public RateLimiter(int maxRequestsPerDay) {
            this.maxRequestsPerDay = maxRequestsPerDay;
        }
        
        public boolean canMakeRequest() {
            LocalDate today = LocalDate.now();
            int currentRequests = requestsToday.getOrDefault(today, 0);
            
            if (currentRequests < maxRequestsPerDay) {
                requestsToday.put(today, currentRequests + 1);
                return true;
            }
            return false;
        }
        
        public void waitIfNeeded() throws InterruptedException {
            while (!canMakeRequest()) {
                Thread.sleep(60000); // Wait 1 minute
            }
        }
    }
    ```

## Next Steps

- Check [Code Samples](code-samples.md) for implementation examples
- Read the [How-to Guide](how-to-guide.md) for step-by-step tutorials
- Review [Change History](change-history.md) for recent updates 