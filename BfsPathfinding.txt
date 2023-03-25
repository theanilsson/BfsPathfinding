#include <iostream>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <queue>

bool FindPath(std::pair<int, int> Start, 
              std::pair<int, int> Target, 
              const std::vector<int>& Map, 
              std::pair<int, int> MapDimensions, 
              std::vector<int>& OutPath);

int main(){
    // Take input for map dimensions
    std::cout << "Welcome to my pathfinding solution using the BFS pathfinding algorithm." << std::endl;
    std::pair<int, int> MapDimensions;
    std::cout << "Enter grid width: ";
    std::cin >> MapDimensions.first;
    std::cout << "Enter grid height: ";
    std::cin >> MapDimensions.second;

    // Use map dimensions to define grid size and take input for grid nodes
    std::vector<int> Map (MapDimensions.first * MapDimensions.second);
    std::cout << "Your input will result in a grid of size " << Map.capacity() << ". Please enter " << Map.capacity() << " zeroes or ones. Zeroes represent impassable terrain and ones represent traversible terrain. Enter the values one by one" << std::endl;
    for (int i = 0; i < Map.capacity(); i++){
        std::cin >> Map[i];
    }
    
    // Display a visual representation of the grid entered to make start and target point selection easier
    std::cout << "You have entered the following grid:" << std::endl;
    for (int i = 0; i < Map.capacity(); i++){
        if((MapDimensions.first - 1) == (i % MapDimensions.first)){
            std::cout << Map[i] << std::endl;
        }
        else{
            std::cout << Map[i];
        }
    }

    std::pair<int, int> Start;
    std::pair<int, int> Target;
    std::cout << "Enter the start point x coordinate: ";
    std::cin >> Start.first;
    std::cout << "Enter the start point y coordinate: ";
    std::cin >> Start.second;

    std::cout << "Enter the target point x coordinate: ";
    std::cin >> Target.first;
    std::cout << "Enter the target point y coordinate: ";
    std::cin >> Target.second;

    // std::cout << "Starting point (" << Start.first << ", " << Start.second << ")";
    // std::cout << "Target point (" << Target.first << ", " << Target.second << ")";

    // Find the shortest path using breadth first search
    std::vector<int> OutPath;
    if(FindPath(Start, Target, Map, MapDimensions, OutPath)){
        // Print out found path
        std::cout << "Path found: ";
        for (int i = 0; i < OutPath.size(); i++){
            std::cout << "Index " << OutPath[i] << " (" << OutPath[i] % MapDimensions.first << ", " << OutPath[i] / MapDimensions.first << ")";
            if (i != OutPath.size() - 1) {
                std::cout << " -> ";
            }
        }
        std::cout << std::endl;
    }
    else{
        // Tell user that no valid path was found
        std::cout << "No path found." << std::endl;
    }
    return 0;
}

bool FindPath(std::pair<int, int> Start, 
              std::pair<int, int> Target, 
              const std::vector<int>& Map, 
              std::pair<int, int> MapDimensions, 
              std::vector<int>& OutPath){
    // Convert start and target positions to indices in Map
    int startIndex = Start.second * MapDimensions.first + Start.first;
    int targetIndex = Target.second * MapDimensions.first + Target.first;

    // Initialize visited and parent maps for the algorithm
    std::unordered_map<int, bool> visited;
    std::unordered_map<int, int> parent;
    for (int i = 0; i < Map.size(); i++) {
        visited[i] = false;
        parent[i] = -1;
    }

    // Define quadridirectional direction vectors used to check adjacent cells while searching
    std::vector<int> directionsX = {1, 0, -1, 0};
    std::vector<int> directionsY = {0, 1, 0, -1};

    // Initialize queue with start position and set start position to visited
    std::queue<int> q;
    q.push(startIndex);
    visited[startIndex] = true;
    
    // BFS loop
    while (!q.empty()) {
        int currentIndex = q.front();
        q.pop();
        
        // Check if we've reached the target
        if (currentIndex == targetIndex) {
            // Construct path from target to start using parent map to fetch traversible vertices discovered for the path
            int index = targetIndex;
            while (index != startIndex) {
                OutPath.push_back(index);
                index = parent[index];
            }

            std::reverse(OutPath.begin(), OutPath.end());
            return true;
        }

        // Add adjacent unvisited vertices to the queue
        int currentX = currentIndex % MapDimensions.first;
        int currentY = currentIndex / MapDimensions.first;
        for (int i = 0; i < 4; i++) {
            int adjacentX = currentX + directionsX[i];
            int adjacentY = currentY + directionsY[i];
            if (adjacentX >= 0 && adjacentX < MapDimensions.first && adjacentY >= 0 && adjacentY < MapDimensions.second) {
                int adjacentIndex = adjacentY * MapDimensions.first + adjacentX;
                // Add adjacent traversible vertex to queue as next to be visited, mark it as visited and add current vertex to the parent map
                if (Map[adjacentIndex] == 1 && !visited[adjacentIndex]) {
                    q.push(adjacentIndex);
                    visited[adjacentIndex] = true;
                    parent[adjacentIndex] = currentIndex;
                }
            }
        }
    }
    
    // No path was found
    return false;
}