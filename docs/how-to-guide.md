# How-to Guide

This guide provides step-by-step tutorials for using Google Maps API, from basic setup to advanced queries.

## Prerequisites

Before you begin, ensure you have:

### Required Accounts

- **Google Cloud Account**: Free account with billing enabled
- **Google Cloud Project**: Active project with APIs enabled
- **API Keys**: Generated and restricted API keys

### Development Environment

- **Python 3.7+** or **Java 11+**
- **HTTP client library** (requests for Python, HttpClient for Java)
- **JSON parser** (built-in for Python, Gson for Java)
- **Text editor or IDE**

### Knowledge Requirements

- Basic understanding of HTTP requests
- Familiarity with JSON data format
- Understanding of RESTful APIs
- Basic programming concepts

## Getting Started

### 1. Basic Authentication Setup

=== "Python"
    ```python
    import requests
    import os
    from dotenv import load_dotenv

    # Load environment variables
    load_dotenv()

    class GoogleMapsClient:
        def__init__(self):
            self.api_key = os.getenv("GOOGLE_MAPS_API_KEY")
            if not self.api_key:
                raise ValueError("GOOGLE_MAPS_API_KEY not found in environment")

    self.base_url = "https://maps.googleapis.com/maps/api"

    def make_request(self, endpoint, params):
            """Make a request to Google Maps API."""
            url = f"{self.base_url}/{endpoint}"
            params["key"] = self.api_key

    response = requests.get(url, params=params)
            response.raise_for_status()
            return response.json()

    # Usage
    client = GoogleMapsClient()
    ```

=== "Java"
    ```java
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.URI;
    import java.net.URLEncoder;
    import java.nio.charset.StandardCharsets;
    import com.google.gson.Gson;
    import com.google.gson.JsonObject;

    public class GoogleMapsClient {
        private final String apiKey;
        private final String baseUrl = "https://maps.googleapis.com/maps/api";
        private final HttpClient client = HttpClient.newHttpClient();
        private final Gson gson = new Gson();

    public GoogleMapsClient() {
            this.apiKey = System.getenv("GOOGLE_MAPS_API_KEY");
            if (this.apiKey == null || this.apiKey.isEmpty()) {
                throw new IllegalStateException("GOOGLE_MAPS_API_KEY not found in environment");
            }
        }

    public JsonObject makeRequest(String endpoint, Map<String, String> params) throws Exception {
            StringBuilder urlBuilder = new StringBuilder(baseUrl + "/" + endpoint + "?");
            params.put("key", apiKey);

    for (Map.Entry<String, String> entry : params.entrySet()) {
                urlBuilder.append(URLEncoder.encode(entry.getKey(), StandardCharsets.UTF_8))
                         .append("=")
                         .append(URLEncoder.encode(entry.getValue(), StandardCharsets.UTF_8))
                         .append("&");
            }

    String url = urlBuilder.toString();
            if (url.endsWith("&")) {
                url = url.substring(0, url.length() - 1);
            }

    HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .build();

    HttpResponse`<String>` response = client.send(request,
                HttpResponse.BodyHandlers.ofString());

    return gson.fromJson(response.body(), JsonObject.class);
        }
    }
    ```

### 2. Your First API Call

=== "Python"
    ```python
    def first_geocoding_call():
        """Make your first API call to geocode an address."""
        client = GoogleMapsClient()

    # Geocode an address
        params = {
            "address": "1600 Amphitheatre Parkway, Mountain View, CA"
        }

    try:
            response = client.make_request("geocode/json", params)

    if response["status"] == "OK":
                result = response["results"][0]
                location = result["geometry"]["location"]

    print(f"Successfully geocoded address!")
                print(f"Address: {result['formatted_address']}")
                print(f"Latitude: {location['lat']}")
                print(f"Longitude: {location['lng']}")
            else:
                print(f"Geocoding failed: {response['status']}")

    except Exception as e:
            print(f"Error: {e}")

    # Run your first API call
    first_geocoding_call()
    ```

=== "Java"
    ```java
    public void firstGeocodingCall() {
        GoogleMapsClient client = new GoogleMapsClient();

    Map<String, String> params = new HashMap<>();
        params.put("address", "1600 Amphitheatre Parkway, Mountain View, CA");

    try {
            JsonObject response = client.makeRequest("geocode/json", params);

    if ("OK".equals(response.get("status").getAsString())) {
                JsonObject result = response.getAsJsonArray("results").get(0).getAsJsonObject();
                JsonObject geometry = result.getAsJsonObject("geometry");
                JsonObject location = geometry.getAsJsonObject("location");

    System.out.println("Successfully geocoded address!");
                System.out.println("Address: " + result.get("formatted_address").getAsString());
                System.out.println("Latitude: " + location.get("lat").getAsDouble());
                System.out.println("Longitude: " + location.get("lng").getAsDouble());
            } else {
                System.out.println("Geocoding failed: " + response.get("status").getAsString());
            }

    } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
    ```

### 3. Error Handling

=== "Python"
    ```python
    def robust_api_call(endpoint, params):
        """Make an API call with proper error handling."""
        client = GoogleMapsClient()

    try:
            response = client.make_request(endpoint, params)

    # Check API response status
            status = response.get("status")

    if status == "OK":
                return {"success": True, "data": response}
            elif status == "ZERO_RESULTS":
                return {"success": False, "error": "No results found"}
            elif status == "OVER_QUERY_LIMIT":
                return {"success": False, "error": "Quota exceeded"}
            elif status == "REQUEST_DENIED":
                return {"success": False, "error": "API key invalid or restricted"}
            else:
                return {"success": False, "error": f"API Error: {status}"}

    except requests.RequestException as e:
            return {"success": False, "error": f"Network error: {e}"}
        except Exception as e:
            return {"success": False, "error": f"Unexpected error: {e}"}

    # Usage
    result = robust_api_call("geocode/json", {"address": "Test Address"})
    if result["success"]:
        print("API call successful")
    else:
        print(f"Error: {result['error']}")
    ```

=== "Java"
    ```java
    public ApiResult robustApiCall(String endpoint, Map<String, String> params) {
        GoogleMapsClient client = new GoogleMapsClient();

    try {
            JsonObject response = client.makeRequest(endpoint, params);

    String status = response.get("status").getAsString();

    switch (status) {
                case "OK":
                    return new ApiResult(true, response, null);
                case "ZERO_RESULTS":
                    return new ApiResult(false, null, "No results found");
                case "OVER_QUERY_LIMIT":
                    return new ApiResult(false, null, "Quota exceeded");
                case "REQUEST_DENIED":
                    return new ApiResult(false, null, "API key invalid or restricted");
                default:
                    return new ApiResult(false, null, "API Error: " + status);
            }

    } catch (Exception e) {
            return new ApiResult(false, null, "Error: " + e.getMessage());
        }
    }

    public class ApiResult {
        private final boolean success;
        private final JsonObject data;
        private final String error;

    // Constructor and getters
    }
    ```

## Advanced Queries

### 1. Complex Geocoding

=== "Python"
    ```python
    def advanced_geocoding():
        """Advanced geocoding with multiple parameters."""
        client = GoogleMapsClient()

    # Geocode with region biasing and language
        params = {
            "address": "Toledo",
            "region": "es",  # Bias towards Spain
            "language": "es",  # Spanish results
            "components": "country:ES"  # Restrict to Spain
        }

    response = client.make_request("geocode/json", params)

    if response["status"] == "OK":
            for result in response["results"]:
                print(f"Address: {result['formatted_address']}")
                print(f"Types: {', '.join(result['types'])}")
                print("---")
    ```

=== "Java"
    ```java
    public void advancedGeocoding() throws Exception {
        GoogleMapsClient client = new GoogleMapsClient();

    Map<String, String> params = new HashMap<>();
        params.put("address", "Toledo");
        params.put("region", "es");  // Bias towards Spain
        params.put("language", "es");  // Spanish results
        params.put("components", "country:ES");  // Restrict to Spain

    JsonObject response = client.makeRequest("geocode/json", params);

    if ("OK".equals(response.get("status").getAsString())) {
            JsonArray results = response.getAsJsonArray("results");
            for (int i = 0; i < results.size(); i++) {
                JsonObject result = results.get(i).getAsJsonObject();
                System.out.println("Address: " + result.get("formatted_address").getAsString());

    JsonArray types = result.getAsJsonArray("types");
                StringBuilder typesStr = new StringBuilder();
                for (int j = 0; j < types.size(); j++) {
                    if (j > 0) typesStr.append(", ");
                    typesStr.append(types.get(j).getAsString());
                }
                System.out.println("Types: " + typesStr.toString());
                System.out.println("---");
            }
        }
    }
    ```

### 2. Multi-Waypoint Directions

=== "Python"
    ```python
    def multi_waypoint_directions():
        """Get directions with multiple waypoints."""
        client = GoogleMapsClient()

    # Route with multiple waypoints
        params = {
            "origin": "San Francisco, CA",
            "destination": "Los Angeles, CA",
            "waypoints": "optimize:true|Sacramento, CA|Fresno, CA",
            "mode": "driving",
            "avoid": "highways"
        }

    response = client.make_request("directions/json", params)

    if response["status"] == "OK":
            route = response["routes"][0]
            legs = route["legs"]

    print("Multi-waypoint route:")
            for i, leg in enumerate(legs):
                print(f"Leg {i+1}: {leg['start_address']} → {leg['end_address']}")
                print(f"  Distance: {leg['distance']['text']}")
                print(f"  Duration: {leg['duration']['text']}")
                print()
    ```

=== "Java"
    ```java
    public void multiWaypointDirections() throws Exception {
        GoogleMapsClient client = new GoogleMapsClient();

    Map<String, String> params = new HashMap<>();
        params.put("origin", "San Francisco, CA");
        params.put("destination", "Los Angeles, CA");
        params.put("waypoints", "optimize:true|Sacramento, CA|Fresno, CA");
        params.put("mode", "driving");
        params.put("avoid", "highways");

    JsonObject response = client.makeRequest("directions/json", params);

    if ("OK".equals(response.get("status").getAsString())) {
            JsonObject route = response.getAsJsonArray("routes").get(0).getAsJsonObject();
            JsonArray legs = route.getAsJsonArray("legs");

    System.out.println("Multi-waypoint route:");
            for (int i = 0; i < legs.size(); i++) {
                JsonObject leg = legs.get(i).getAsJsonObject();
                System.out.println("Leg " + (i+1) + ": " +
                    leg.get("start_address").getAsString() + " → " +
                    leg.get("end_address").getAsString());
                System.out.println("  Distance: " +
                    leg.getAsJsonObject("distance").get("text").getAsString());
                System.out.println("  Duration: " +
                    leg.getAsJsonObject("duration").get("text").getAsString());
                System.out.println();
            }
        }
    }
    ```

### 3. Advanced Place Search

=== "Python"
    ```python
    def advanced_place_search():
        """Advanced place search with filters."""
        client = GoogleMapsClient()

    # Search for restaurants near a location
        params = {
            "location": "37.7749,-122.4194",  # San Francisco
            "radius": "5000",  # 5km radius
            "type": "restaurant",
            "keyword": "pizza",
            "minprice": "1",
            "maxprice": "3",
            "opennow": "true"
        }

    response = client.make_request("place/nearbysearch/json", params)

    if response["status"] == "OK":
            places = response["results"]
            print(f"Found {len(places)} pizza restaurants:")

    for place in places:
                print(f"  - {place['name']}")
                print(f"    Rating: {place.get('rating', 'N/A')}")
                print(f"    Price Level: {'$' * place.get('price_level', 0)}")
                print(f"    Address: {place['vicinity']}")
                print()
    ```

=== "Java"
    ```java
    public void advancedPlaceSearch() throws Exception {
        GoogleMapsClient client = new GoogleMapsClient();

    Map<String, String> params = new HashMap<>();
        params.put("location", "37.7749,-122.4194");  // San Francisco
        params.put("radius", "5000");  // 5km radius
        params.put("type", "restaurant");
        params.put("keyword", "pizza");
        params.put("minprice", "1");
        params.put("maxprice", "3");
        params.put("opennow", "true");

    JsonObject response = client.makeRequest("place/nearbysearch/json", params);

    if ("OK".equals(response.get("status").getAsString())) {
            JsonArray places = response.getAsJsonArray("results");
            System.out.println("Found " + places.size() + " pizza restaurants:");

    for (int i = 0; i < places.size(); i++) {
                JsonObject place = places.get(i).getAsJsonObject();
                System.out.println("  - " + place.get("name").getAsString());
                System.out.println("    Rating: " +
                    (place.has("rating") ? place.get("rating").getAsString() : "N/A"));

    int priceLevel = place.has("price_level") ? place.get("price_level").getAsInt() : 0;
                StringBuilder priceStr = new StringBuilder();
                for (int j = 0; j < priceLevel; j++) {
                    priceStr.append("$");
                }
                System.out.println("    Price Level: " + priceStr.toString());
                System.out.println("    Address: " + place.get("vicinity").getAsString());
                System.out.println();
            }
        }
    }
    ```

### 4. Batch Processing

=== "Python"
    ```python
    import asyncio
    import aiohttp

    async def batch_geocoding(addresses):
        """Geocode multiple addresses concurrently."""
        client = GoogleMapsClient()

    async def geocode_address(session, address):
            params = {"address": address}
            url = f"{client.base_url}/geocode/json"
            params["key"] = client.api_key

    async with session.get(url, params=params) as response:
                data = await response.json()
                return {"address": address, "result": data}

    async with aiohttp.ClientSession() as session:
            tasks = [geocode_address(session, addr) for addr in addresses]
            results = await asyncio.gather(*tasks)

    for result in results:
                if result["result"]["status"] == "OK":
                    location = result["result"]["results"][0]["geometry"]["location"]
                    print(f"{result['address']}: {location['lat']}, {location['lng']}")
                else:
                    print(f"{result['address']}: {result['result']['status']}")

    # Usage
    addresses = [
        "1600 Amphitheatre Parkway, Mountain View, CA",
        "1 Infinite Loop, Cupertino, CA",
        "3500 Deer Creek Road, Palo Alto, CA"
    ]

    asyncio.run(batch_geocoding(addresses))
    ```

=== "Java"
    ```java
    import java.util.concurrent.CompletableFuture;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.List;
    import java.util.stream.Collectors;

    public void batchGeocoding(List`<String>` addresses) {
        GoogleMapsClient client = new GoogleMapsClient();
        ExecutorService executor = Executors.newFixedThreadPool(10);

    List<CompletableFuture`<GeocodingResult>`> futures = addresses.stream()
            .map(address -> CompletableFuture.supplyAsync(() -> {
                try {
                    Map<String, String> params = new HashMap<>();
                    params.put("address", address);
                    JsonObject response = client.makeRequest("geocode/json", params);

    if ("OK".equals(response.get("status").getAsString())) {
                        JsonObject result = response.getAsJsonArray("results").get(0).getAsJsonObject();
                        JsonObject location = result.getAsJsonObject("geometry").getAsJsonObject("location");
                        return new GeocodingResult(address,
                            location.get("lat").getAsDouble(),
                            location.get("lng").getAsDouble(),
                            true, null);
                    } else {
                        return new GeocodingResult(address, 0, 0, false,
                            response.get("status").getAsString());
                    }
                } catch (Exception e) {
                    return new GeocodingResult(address, 0, 0, false, e.getMessage());
                }
            }, executor))
            .collect(Collectors.toList());

    // Wait for all results
        CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();

    // Process results
        for (CompletableFuture`<GeocodingResult>` future : futures) {
            GeocodingResult result = future.get();
            if (result.isSuccess()) {
                System.out.println(" " + result.getAddress() + ": " +
                    result.getLatitude() + ", " + result.getLongitude());
            } else {
                System.out.println(" " + result.getAddress() + ": " + result.getError());
            }
        }

    executor.shutdown();
    }

    public class GeocodingResult {
        private final String address;
        private final double latitude;
        private final double longitude;
        private final boolean success;
        private final String error;

    // Constructor and getters
    }
    ```

## Next Steps

- Explore [Code Samples](code-samples.md) for more implementation examples
- Check the [Reference Guide](reference-guide.md) for detailed API documentation
- Review [Change History](change-history.md) for recent updates
