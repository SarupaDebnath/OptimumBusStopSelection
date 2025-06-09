# OptimumBusStopSelection
Report can be found here:
https://docs.google.com/document/d/1k7vyr567UzdVJ-2U02xCyPIEql4P9Kxv5wd_QBtEDBQ/edit?tab=t.0

Finding the 10 best stops for a shuttle bus

Data science in Transportation optimization

Introduction

Problem Statement

Tech companies in Silicon Valley often use shuttle buses to transport employees between home and office. The challenge is determining the 10 best bus stops for a shuttle from San Francisco to Mountain View, ensuring minimal travel time while maximizing employee convenience. This is a classic transportation optimization problem, ideal for applying data science techniques.
My understanding

At first glance, the task of determining shuttle stops resembles the Traveling Salesman Problem (TSP), where all locations must be visited, and the route returns to the origin. However, in this case, the goal is different: to select the best stops from a total of 119 possible bus stop locations for 2,191 employees, minimizing the total distance to employee addresses.

The mathematical complexity is significant, involving a combination of (119C10~ 1.064E+14​) possibilities. This enormous number of possibilities makes a direct approach, such as finding the minimum distance for all combinations, nearly intractable. Also, this makes directly solving it through integer linear programming (ILP) optimization computationally intensive and impractical for real-world implementation. While ILP guarantees a globally optimal solution if solved to completion, the computational time and resources required for large-scale problems make it challenging to obtain the global optimum in a reasonable timeframe. As a result, heuristic or approximate methods offer scalable and near-optimal solutions that are more practical for real-world applications.
Defining the challenge provided

The problem defines efficiency as minimizing the overall walking distance between employee homes and the closest bus stop. To achieve this, we need to: 
Calculate the walking distance (or straight-line distance) between each employee's home and each bus stop candidate. 
Assign each employee to the closest bus stop. 
Optimize the selection of 10 bus stops to minimize the total walking distance for all employees.

Data processing and mining
Data preprocessing

The bus stops and employee address data involve multiple steps, including handling incomplete or missing coordinates and ensuring data accuracy. 

Geocoding with Geopy:
Using the Python package geopy, the coordinates (latitude and longitude) of employee addresses and intersections of specific streets (e.g., Mission Street and another cross street) were identified. This automated geocoding provided a significant portion of the required data.

Handling Missing Coordinates:
Additional research was required for addresses or intersections that could not be resolved by geopy. Missing or incomplete data were manually cross-referenced using online tools like latlong.net to find the exact coordinates.

Intersection Mapping:
Approximately 50% of street intersection locations required manual verification. These were studied using Google Maps to determine the exact geographical points. This step was necessary for intersections where automated geocoding failed or provided ambiguous results.

Address Updates:
Some employees address required updates due to formatting errors, outdated information, or missing details. 

These are the geographical locations for the employee addresses and possible bus stops joining Mission Street.

Observations:
Green Dots: Represent the latitude and longitude of employee addresses scattered across the region.
Blue Triangles: Represent potential bus stop locations specifically along Mission Street, forming a clear line that follows the path of this street.
The line formed by the blue triangles represents the trajectory of Mission Street, with bus stops along its path.
The clustering of green dots near the blue triangles shows that many employees are well-served by stops along Mission Street. Sparse or distant green dots indicate areas that might require a bit of walking routes to reach the closest bus stop. The clustering of green dots shows areas with a high density of employee residences, which can inform stop placement decisions.

Proposed approach

The approach starts by using K-Means clustering to group employee locations into dense areas where most people live. The center of each group (centroid) represents the best location for a bus stop to minimize walking distance for employees in that group. These centroids are then matched to the closest potential bus stop from the given list, ensuring all the centroids of the clusters are served. Finally, we refine iteratively the selection of at max 10 stops by checking the total walking distance from the centroids and making small adjustments to make the solution better. 

While the K-Means approach is chosen for its simplicity, computational efficiency, and extensive use it is important to acknowledge that it does not guarantee the discovery of the optimal number of clusters. The limitation arises from its tendency to converge to local optima solutions, a consequence of the iterative nature of the algorithm starting from random initial centroids. The other possible clustering approaches are Hierarchical Clustering, Density-Based Clustering (DBSCAN), and Agglomerative Clustering. It is difficult to comment on how these methods will perform without testing them on the specific dataset, but they are worth a shot to explore alternative solutions and potentially improve results.


Results and plots

K-Means clustering and finding optimal clusters

The algorithm is run with default parameters, experimenting with cluster counts from 2 to 25. Metrics such as inertia, silhouette score, and Davies-Bouldin Index are analyzed to identify the optimal clustering, balancing intra-cluster distance (cluster compactness), and meaningful separations. The selection of the optimal number of clusters is guided by analyzing multiple metrics:
Inertia: Measures the within-cluster sum of squared distances. Lower inertia values indicate more compact clusters; however, it tends to decrease with more clusters, so an "elbow point" is often used to find the optimal trade-off.
Silhouette Score: Evaluates how well-separated the clusters are. It ranges from -1 to 1, where higher values indicate better-defined clusters with minimal overlap.
Davies-Bouldin Index: Assesses the average similarity ratio between clusters, with lower values representing more distinct and well-separated clusters.








Observation: Based on the highest Silhouette score, lowest Davis-Bouldin, and low Inertia the optimal number of clusters is 16

Let’s visualize the 16 clusters and their centroids along with the all bus stops



The K-Means algorithm effectively grouped nearby employees. The bus stops (marked as triangles) are distributed along a linear path, suggesting a geographic constraint. Some clusters (e.g., clusters near the bottom left) are tightly packed, while others (e.g., clusters near the top) are more spread out, likely due to variations in employee density in those areas. A few points are located far from their cluster centers (e.g., isolated points in some clusters).

 Finding out the nearest bus stop to each cluster center 

The nearest bus stop to each cluster center is determined based on the shortest distance between the centroid and the provided bus stops. We identified 12 bus stops that serve all the 16 cluster centers effectively. Let’s visualize these closest bus stops with the cluster centers.



Some bus stops serve multiple clusters, as seen in their central positions relative to nearby cluster centers. We assigned bus stop id numbers from 1 to 12. To visualize it properly, the cluster has given the bus stop id number on it. The plot not only assigns numbers to the bus stops but also labels the clusters with the corresponding bus stop IDs, making the relationship between clusters and their respective bus stops clear and easy to interpret. For eg. bus stop 6th serves both the red and brown clusters.


Observation: We initially selected 12 bus stops, but our goal is to identify the 10 most optimal locations. From the plot, it is evident that certain bus stops, such as 1 and 10, as well as 8, 9, and 12, are located very close to each other. This grouping of bus stops creates redundancy. By iteratively removing the redundant stops while ensuring all clusters remain adequately served, we can achieve the optimal selection of 10 bus stops. We removed bus stop 10 and 9. The cluster which was earlier served by 10 will be served by 1 and the cluster 9 which earlier served by 9 will be served by 8. Lets see updated plot again.


We observe that bus stop 1 serves three cluster centers, while bus stop 8 serves two cluster centers. This selection process was carried out iteratively by identifying the minimum distance and systematically removing redundant bus stops one at a time to optimize the overall solution.

This optimal centroid approach allows us to iteratively select 10 clusters. In other cases, when the number of clusters is fixed at exactly 10, the resulting bus stops may be fewer than 10. By first selecting optimal centroids using K-Means and then identifying 10 bus stops, this method introduces flexibility in choosing the most effective stops while maintaining efficient coverage.
Conclusion

The details of the total 10 bus stops are provided below:
	
Longitude
Latitude
Street_Two
Bus_ID
-122.433348
37.726461
EXCELSIOR AVE
1
-122.415024
37.776122
09TH ST
2
-122.418068
37.773699
LAFAYETTE ST
3
-122.450097
37.709592
OLIVER ST
4
-122.420084
37.768036
14TH ST
5
-122.426808
37.733437
BOSWORTH ST
6
-122.399852
37.787838
02ND ST
7
-122.420837
37.744220
29TH ST
8
-122.440708
37.716777
GENEVA AVE
11
-122.423125
37.740814
BROOK ST
12


In conclusion, the shuttle bus optimization approach ensures both efficiency and practicality by selecting bus stops that minimize walking distances for employees. Using clustering methods like K-Means to find optimal locations and refining the choices iteratively, this method reduces redundancy and ensures all areas are covered. It provides a simple, flexible, and effective solution for improving transportation systems.




