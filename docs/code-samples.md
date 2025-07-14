# Code Samples

This page provides complete, working code samples for different Google Maps API requests in Python and Java.

## Basic Setup

=== "Python"
    ```python
    import requests
    import os
    from typing import Dict, List, Optional
    from dotenv import load_dotenv

    # Load environment variables
    load_dotenv()

    class GoogleMapsAPI:
        def__init__(self, api_key: str = None):
            self.api_key = api_key or os.getenv("GOOGLE_MAPS_API_KEY")
            if not self.api_key:
                raise ValueError("API key not provided and GOOGLE_MAPS_API_KEY not found in environment")

    self.base_url = "https://maps.googleapis.com/maps/api"

    def make_request(self, endpoint: str, params: Dict) -> Optional[Dict]:
            """Make a request to Google Maps API."""
            url = f"{self.base_url}/{endpoint}"
            params["key"] = self.api_key

    try:
                response = requests.get(url, params=params)
                response.raise_for_status()
                return response.json()
            except requests.RequestException as e:
                print(f"Request failed: {e}")
                return None
    ```

=== "Java"
    ```java
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.URI;
    import java.net.URLEncoder;
    import java.nio.charset.StandardCharsets;
    import java.util.Map;
    import com.google.gson.Gson;
    import com.google.gson.JsonObject;

    public class GoogleMapsAPI {
        private final String apiKey;
        private final String baseUrl = "https://maps.googleapis.com/maps/api";
        private final HttpClient client = HttpClient.newHttpClient();
        private final Gson gson = new Gson();

    public GoogleMapsAPI(String apiKey) {
            this.apiKey = apiKey != null ? apiKey : System.getenv("GOOGLE_MAPS_API_KEY");
            if (this.apiKey == null || this.apiKey.isEmpty()) {
                throw new IllegalStateException("API key not provided and GOOGLE_MAPS_API_KEY not found in environment");
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

## Geocoding Examples

### Forward Geocoding

=== "Python"
    ```python
    def geocode_address(address: str) -> Optional[Dict]:
        """Convert address to coordinates."""
        api = GoogleMapsAPI()

    params = {"address": address}
        response = api.make_request("geocode/json", params)

    if response and response["status"] == "OK":
            result = response["results"][0]
            location = result["geometry"]["location"]

    return {
                "address": result["formatted_address"],
                "latitude": location["lat"],
                "longitude": location["lng"],
                "place_id": result["place_id"]
            }
        return None

    # Example usage
    result = geocode_address("1600 Amphitheatre Parkway, Mountain View, CA")
    if result:
        print(f"Address: {result['address']}")
        print(f"Coordinates: {result['latitude']}, {result['longitude']}")
    ```

=== "Java"
    ```java
    public GeocodingResult geocodeAddress(String address) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("address", address);

    JsonObject response = api.makeRequest("geocode/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            JsonObject result = response.getAsJsonArray("results").get(0).getAsJsonObject();
            JsonObject geometry = result.getAsJsonObject("geometry");
            JsonObject location = geometry.getAsJsonObject("location");

    return new GeocodingResult(
                result.get("formatted_address").getAsString(),
                location.get("lat").getAsDouble(),
                location.get("lng").getAsDouble(),
                result.get("place_id").getAsString()
            );
        }
        return null;
    }

    public class GeocodingResult {
        private final String address;
        private final double latitude;
        private final double longitude;
        private final String placeId;

    // Constructor and getters
    }
    ```

### Reverse Geocoding

=== "Python"
    ```python
    def reverse_geocode(lat: float, lng: float) -> Optional[str]:
        """Convert coordinates to address."""
        api = GoogleMapsAPI()

    params = {"latlng": f"{lat},{lng}"}
        response = api.make_request("geocode/json", params)

    if response and response["status"] == "OK":
            return response["results"][0]["formatted_address"]
        return None

    # Example usage
    address = reverse_geocode(37.4221, -122.0841)
    if address:
        print(f"Address: {address}")
    ```

=== "Java"
    ```java
    public String reverseGeocode(double lat, double lng) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("latlng", lat + "," + lng);

    JsonObject response = api.makeRequest("geocode/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            return response.getAsJsonArray("results").get(0).getAsJsonObject()
                .get("formatted_address").getAsString();
        }
        return null;
    }
    ```

## Directions Examples

### Basic Directions

=== "Python"
    ```python
    def get_directions(origin: str, destination: str, mode: str = "driving") -> Optional[Dict]:
        """Get directions between two locations."""
        api = GoogleMapsAPI()

    params = {
            "origin": origin,
            "destination": destination,
            "mode": mode
        }

    response = api.make_request("directions/json", params)

    if response and response["status"] == "OK":
            route = response["routes"][0]
            leg = route["legs"][0]

    return {
                "distance": leg["distance"]["text"],
                "duration": leg["duration"]["text"],
                "start_address": leg["start_address"],
                "end_address": leg["end_address"],
                "steps": [step["html_instructions"] for step in leg["steps"]]
            }
        return None

    # Example usage
    directions = get_directions("New York, NY", "Boston, MA", "driving")
    if directions:
        print(f"Distance: {directions['distance']}")
        print(f"Duration: {directions['duration']}")
    ```

=== "Java"
    ```java
    public DirectionsResult getDirections(String origin, String destination, String mode) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("origin", origin);
        params.put("destination", destination);
        params.put("mode", mode);

    JsonObject response = api.makeRequest("directions/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            JsonObject route = response.getAsJsonArray("routes").get(0).getAsJsonObject();
            JsonObject leg = route.getAsJsonArray("legs").get(0).getAsJsonObject();

    List`<String>` steps = new ArrayList<>();
            JsonArray stepsArray = leg.getAsJsonArray("steps");
            for (int i = 0; i < stepsArray.size(); i++) {
                steps.add(stepsArray.get(i).getAsJsonObject().get("html_instructions").getAsString());
            }

    return new DirectionsResult(
                leg.getAsJsonObject("distance").get("text").getAsString(),
                leg.getAsJsonObject("duration").get("text").getAsString(),
                leg.get("start_address").getAsString(),
                leg.get("end_address").getAsString(),
                steps
            );
        }
        return null;
    }

    public class DirectionsResult {
        private final String distance;
        private final String duration;
        private final String startAddress;
        private final String endAddress;
        private final List`<String>` steps;

    // Constructor and getters
    }
    ```

### Multi-Waypoint Directions

=== "Python"
    ```python
    def get_multi_waypoint_directions(origin: str, destination: str, waypoints: List[str]) -> Optional[Dict]:
        """Get directions with multiple waypoints."""
        api = GoogleMapsAPI()

    params = {
            "origin": origin,
            "destination": destination,
            "waypoints": "|".join(waypoints),
            "optimize": "true"
        }

    response = api.make_request("directions/json", params)

    if response and response["status"] == "OK":
            route = response["routes"][0]
            legs = route["legs"]

    return {
                "total_distance": sum(leg["distance"]["value"] for leg in legs),
                "total_duration": sum(leg["duration"]["value"] for leg in legs),
                "waypoint_order": route.get("waypoint_order", []),
                "legs": [
                    {
                        "start": leg["start_address"],
                        "end": leg["end_address"],
                        "distance": leg["distance"]["text"],
                        "duration": leg["duration"]["text"]
                    }
                    for leg in legs
                ]
            }
        return None

    # Example usage
    waypoints = ["Sacramento, CA", "Fresno, CA"]
    route = get_multi_waypoint_directions("San Francisco, CA", "Los Angeles, CA", waypoints)
    if route:
        print(f"Total distance: {route['total_distance']} meters")
        print(f"Total duration: {route['total_duration']} seconds")
    ```

=== "Java"
    ```java
    public MultiWaypointResult getMultiWaypointDirections(String origin, String destination, List `<String>` waypoints) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("origin", origin);
        params.put("destination", destination);
        params.put("waypoints", String.join("|", waypoints));
        params.put("optimize", "true");

    JsonObject response = api.makeRequest("directions/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            JsonObject route = response.getAsJsonArray("routes").get(0).getAsJsonObject();
            JsonArray legs = route.getAsJsonArray("legs");

    int totalDistance = 0;
            int totalDuration = 0;
            List`<LegInfo>` legInfos = new ArrayList<>();

    for (int i = 0; i < legs.size(); i++) {
                JsonObject leg = legs.get(i).getAsJsonObject();
                totalDistance += leg.getAsJsonObject("distance").get("value").getAsInt();
                totalDuration += leg.getAsJsonObject("duration").get("value").getAsInt();

    legInfos.add(new LegInfo(
                    leg.get("start_address").getAsString(),
                    leg.get("end_address").getAsString(),
                    leg.getAsJsonObject("distance").get("text").getAsString(),
                    leg.getAsJsonObject("duration").get("text").getAsString()
                ));
            }

    return new MultiWaypointResult(totalDistance, totalDuration, legInfos);
        }
        return null;
    }

    public class MultiWaypointResult {
        private final int totalDistance;
        private final int totalDuration;
        private final List`<LegInfo>` legs;

    // Constructor and getters
    }

    public class LegInfo {
        private final String start;
        private final String end;
        private final String distance;
        private final String duration;

    // Constructor and getters
    }
    ```

## Places Examples

### Find Place

=== "Python"
    ```python
    def find_place(input_text: str, input_type: str = "textquery") -> Optional[Dict]:
        """Find a place using text input."""
        api = GoogleMapsAPI()

    params = {
            "input": input_text,
            "inputtype": input_type,
            "fields": "formatted_address,name,geometry,place_id"
        }

    response = api.make_request("place/findplacefromtext/json", params)

    if response and response["status"] == "OK":
            candidate = response["candidates"][0]
            location = candidate["geometry"]["location"]

    return {
                "name": candidate["name"],
                "address": candidate["formatted_address"],
                "latitude": location["lat"],
                "longitude": location["lng"],
                "place_id": candidate["place_id"]
            }
        return None

    # Example usage
    place = find_place("Museum of Modern Art")
    if place:
        print(f"Found: {place['name']}")
        print(f"Address: {place['address']}")
    ```

=== "Java"
    ```java
    public PlaceResult findPlace(String inputText, String inputType) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("input", inputText);
        params.put("inputtype", inputType);
        params.put("fields", "formatted_address,name,geometry,place_id");

    JsonObject response = api.makeRequest("place/findplacefromtext/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            JsonObject candidate = response.getAsJsonArray("candidates").get(0).getAsJsonObject();
            JsonObject location = candidate.getAsJsonObject("geometry").getAsJsonObject("location");

    return new PlaceResult(
                candidate.get("name").getAsString(),
                candidate.get("formatted_address").getAsString(),
                location.get("lat").getAsDouble(),
                location.get("lng").getAsDouble(),
                candidate.get("place_id").getAsString()
            );
        }
        return null;
    }

    public class PlaceResult {
        private final String name;
        private final String address;
        private final double latitude;
        private final double longitude;
        private final String placeId;

    // Constructor and getters
    }
    ```

### Nearby Search

=== "Python"
    ```python
    def nearby_search(lat: float, lng: float, radius: int, place_type: str = None) -> List[Dict]:
        """Find places near a location."""
        api = GoogleMapsAPI()

    params = {
            "location": f"{lat},{lng}",
            "radius": str(radius)
        }

    if place_type:
            params["type"] = place_type

    response = api.make_request("place/nearbysearch/json", params)

    if response and response["status"] == "OK":
            places = []
            for place in response["results"]:
                places.append({
                    "name": place["name"],
                    "rating": place.get("rating"),
                    "vicinity": place["vicinity"],
                    "types": place["types"]
                })
            return places
        return []

    # Example usage
    places = nearby_search(37.7749, -122.4194, 1000, "restaurant")
    for place in places:
        print(f"Restaurant: {place['name']}")
        print(f"Rating: {place['rating']}")
        print(f"Address: {place['vicinity']}")
        print("---")
    ```

=== "Java"
    ```java
    public List `<NearbyPlace>` nearbySearch(double lat, double lng, int radius, String placeType) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("location", lat + "," + lng);
        params.put("radius", String.valueOf(radius));

    if (placeType != null) {
            params.put("type", placeType);
        }

    JsonObject response = api.makeRequest("place/nearbysearch/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            List`<NearbyPlace>` places = new ArrayList<>();
            JsonArray results = response.getAsJsonArray("results");

    for (int i = 0; i < results.size(); i++) {
                JsonObject place = results.get(i).getAsJsonObject();

    List`<String>` types = new ArrayList<>();
                JsonArray typesArray = place.getAsJsonArray("types");
                for (int j = 0; j < typesArray.size(); j++) {
                    types.add(typesArray.get(j).getAsString());
                }

    places.add(new NearbyPlace(
                    place.get("name").getAsString(),
                    place.has("rating") ? place.get("rating").getAsDouble() : 0.0,
                    place.get("vicinity").getAsString(),
                    types
                ));
            }
            return places;
        }
        return new ArrayList<>();
    }

    public class NearbyPlace {
        private final String name;
        private final double rating;
        private final String vicinity;
        private final List`<String>` types;

    // Constructor and getters
    }
    ```

## Distance Matrix Examples

### Calculate Distances

=== "Python"
    ```python
    def calculate_distances(origins: List[str], destinations: List[str], mode: str = "driving") -> List[Dict]:
        """Calculate distances between multiple origins and destinations."""
        api = GoogleMapsAPI()

    params = {
            "origins": "|".join(origins),
            "destinations": "|".join(destinations),
            "mode": mode
        }

    response = api.make_request("distancematrix/json", params)

    if response and response["status"] == "OK":
            results = []
            rows = response["rows"]

    for i, row in enumerate(rows):
                origin = response["origin_addresses"][i]
                elements = row["elements"]

    for j, element in enumerate(elements):
                    destination = response["destination_addresses"][j]

    if element["status"] == "OK":
                        results.append({
                            "origin": origin,
                            "destination": destination,
                            "distance": element["distance"]["text"],
                            "duration": element["duration"]["text"]
                        })

    return results
        return []

    # Example usage
    origins = ["New York, NY", "Boston, MA"]
    destinations = ["Washington, DC", "Philadelphia, PA"]

    distances = calculate_distances(origins, destinations)
    for result in distances:
        print(f"{result['origin']} → {result['destination']}")
        print(f"  Distance: {result['distance']}")
        print(f"  Duration: {result['duration']}")
        print()
    ```

=== "Java"
    ```java
    public List `<DistanceResult>` calculateDistances(List `<String>` origins, List `<String>` destinations, String mode) throws Exception {
        GoogleMapsAPI api = new GoogleMapsAPI();

    Map<String, String> params = new HashMap<>();
        params.put("origins", String.join("|", origins));
        params.put("destinations", String.join("|", destinations));
        params.put("mode", mode);

    JsonObject response = api.makeRequest("distancematrix/json", params);

    if (response != null && "OK".equals(response.get("status").getAsString())) {
            List`<DistanceResult>` results = new ArrayList<>();
            JsonArray rows = response.getAsJsonArray("rows");
            JsonArray originAddresses = response.getAsJsonArray("origin_addresses");
            JsonArray destinationAddresses = response.getAsJsonArray("destination_addresses");

    for (int i = 0; i < rows.size(); i++) {
                String origin = originAddresses.get(i).getAsString();
                JsonObject row = rows.get(i).getAsJsonObject();
                JsonArray elements = row.getAsJsonArray("elements");

    for (int j = 0; j < elements.size(); j++) {
                    String destination = destinationAddresses.get(j).getAsString();
                    JsonObject element = elements.get(j).getAsJsonObject();

    if ("OK".equals(element.get("status").getAsString())) {
                        results.add(new DistanceResult(
                            origin,
                            destination,
                            element.getAsJsonObject("distance").get("text").getAsString(),
                            element.getAsJsonObject("duration").get("text").getAsString()
                        ));
                    }
                }
            }
            return results;
        }
        return new ArrayList<>();
    }

    public class DistanceResult {
        private final String origin;
        private final String destination;
        private final String distance;
        private final String duration;

    // Constructor and getters
    }
    ```

## Error Handling Examples

=== "Python"
    ```python
    def safe_api_call(endpoint: str, params: Dict) -> Dict:
        """Make a safe API call with comprehensive error handling."""
        api = GoogleMapsAPI()

    try:
            response = api.make_request(endpoint, params)

    if not response:
                return {"success": False, "error": "No response from API"}

    status = response.get("status")

    if status == "OK":
                return {"success": True, "data": response}
            elif status == "ZERO_RESULTS":
                return {"success": False, "error": "No results found"}
            elif status == "OVER_QUERY_LIMIT":
                return {"success": False, "error": "Quota exceeded"}
            elif status == "REQUEST_DENIED":
                return {"success": False, "error": "API key invalid or restricted"}
            elif status == "INVALID_REQUEST":
                return {"success": False, "error": "Invalid request parameters"}
            else:
                return {"success": False, "error": f"Unknown error: {status}"}

    except requests.RequestException as e:
            return {"success": False, "error": f"Network error: {e}"}
        except Exception as e:
            return {"success": False, "error": f"Unexpected error: {e}"}

    # Usage
    result = safe_api_call("geocode/json", {"address": "Test Address"})
    if result["success"]:
        print("API call successful")
        # Process result["data"]
    else:
        print(f"Error: {result['error']}")
    ```

=== "Java"
    ```java
    public ApiResult safeApiCall(String endpoint, Map<String, String> params) {
        GoogleMapsAPI api = new GoogleMapsAPI();

    try {
            JsonObject response = api.makeRequest(endpoint, params);

    if (response == null) {
                return new ApiResult(false, null, "No response from API");
            }

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
                case "INVALID_REQUEST":
                    return new ApiResult(false, null, "Invalid request parameters");
                default:
                    return new ApiResult(false, null, "Unknown error: " + status);
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

## Next Steps

- Read the [How-to Guide](how-to-guide.md) for step-by-step tutorials
- Check the [Reference Guide](reference-guide.md) for detailed API documentation
- Review [Change History](change-history.md) for recent updates
