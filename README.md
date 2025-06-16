# eco-supply-chain

Creating an eco-friendly supply chain optimization program involves several components, including route optimization, logistics planning, and carbon emission calculations. Below is a simplified Python program outline that aims to address these components. This program uses a combination of optimization techniques and provides error handling where appropriate.

```python
import random
import heapq

class SupplyChainOptimizer:
    def __init__(self, destinations, distances, emissions_per_km, max_distance):
        """
        Initializes the optimizer with given parameters.
        
        :param destinations: List of destination names.
        :param distances: 2D list (matrix) of distances between destinations.
        :param emissions_per_km: CO2 emissions per km in grams.
        :param max_distance: Maximum allowable distance for any single route.
        """
        self.destinations = destinations
        self.distances = distances
        self.emissions_per_km = emissions_per_km
        self.max_distance = max_distance

    def get_emissions(self, route):
        """
        Calculates total emissions for a given route.

        :param route: List of indices representing the route.
        :return: Total emissions in grams.
        """
        try:
            total_emissions = 0
            for i in range(len(route) - 1):
                segment_distance = self.distances[route[i]][route[i+1]]
                total_emissions += segment_distance * self.emissions_per_km
            return total_emissions
        except Exception as e:
            print(f"Error calculating emissions: {e}")
            return float('inf')

    def find_shortest_path(self, start, end):
        """
        Implements Dijkstra's algorithm to find the shortest path from start to end.

        :param start: Starting index.
        :param end: Ending index.
        :return: Shortest path as a list of indices.
        """
        try:
            num_destinations = len(self.distances)
            visited = [False] * num_destinations
            distance = [float('inf')] * num_destinations
            previous = [None] * num_destinations

            distance[start] = 0
            priority_queue = [(0, start)]

            while priority_queue:
                current_distance, current_index = heapq.heappop(priority_queue)
                if visited[current_index]:
                    continue
                visited[current_index] = True

                for neighbor_index in range(num_destinations):
                    if visited[neighbor_index]:
                        continue
                    distance_through_current = current_distance + self.distances[current_index][neighbor_index]
                    if distance_through_current < distance[neighbor_index]:
                        distance[neighbor_index] = distance_through_current
                        previous[neighbor_index] = current_index
                        heapq.heappush(priority_queue, (distance_through_current, neighbor_index))

            # Reconstruct path
            path = []
            current = end
            while current is not None:
                path.append(current)
                current = previous[current]
            path.reverse()

            # Validate path distance
            if distance[end] > self.max_distance:
                print("No feasible route found within max distance constraint.")
                return None
            return path

        except Exception as e:
            print(f"Error finding shortest path: {e}")
            return None

def main():
    # Example setup
    destinations = ['Warehouse', 'Store A', 'Store B', 'Store C']
    distances = [
        [0, 10, 20, 30],
        [10, 0, 15, 25],
        [20, 15, 0, 10],
        [30, 25, 10, 0]
    ]
    emissions_per_km = 200  # grams per km
    max_distance = 40  # The limit of any route

    try:
        optimizer = SupplyChainOptimizer(destinations, distances, emissions_per_km, max_distance)
        start_index = 0  # Warehouse
        end_index = 3    # Store C
        route = optimizer.find_shortest_path(start_index, end_index)
        
        if route:
            print("Optimal Route:", ' -> '.join(destinations[i] for i in route))
            total_emissions = optimizer.get_emissions(route)
            print(f"Total Emissions: {total_emissions} grams")
        else:
            print("No feasible route found.")

    except Exception as e:
        print(f"Error in main execution: {e}")

if __name__ == "__main__":
    main()
```

### Key Features of the Program:
- **Dijkstra's Algorithm**: The program uses Dijkstra's algorithm to find the shortest path between a start and end location.
- **Emissions Calculation**: It calculates the carbon emissions for a given route based on the distance and the emission factor.
- **Constraints**: It ensures that the path found does not exceed the `max_distance` constraint, simulating efforts to optimize not just for distance but also practical transport needs.
- **Error Handling**: Basic error handling is included to catch potential issues in emissions calculation and pathfinding.

### Enhancements
For a more robust solution, consider integrating real-world data on distances and a more sophisticated optimization technique over this simple implementation. Additionally, consider integrating an API or library for handling real maps and geolocation data for more accuracy.