The Fermat-Weber Bnb Optimizer 

(Solving spatial optimization for group logistics)

When planning a group trip, picking the optimal location for an Airbnb is usually handled by guessing the best neighborhood or finding the geographic center of your planned activities.

However, taking the simple average (the centroid) of your destinations is mathematically flawed for travel planning. A single outlier—like a remote beach you plan to visit only once—will drastically skew the mean, pulling your accommodation far away from the city center where you spend 90% of your time.

This tool solves the accommodation debate mathematically by modeling the trip as a Fermat-Weber facility location problem. It calculates the weighted geometric median of your itinerary and automatically generates an Airbnb search bounding box perfectly centered on the mathematical minimum.
The Mathematics

The goal of this engine is to find a single optimal coordinate point (the geometric median) that minimizes the total sum of the distances to all other planned destinations, taking into account how "important" (or frequent) each destination is.
The Objective Function

Because not all locations are equally important, the script assigns a weight wi​ to each destination xi​. For example, a central club we visit every night might have a weight of 10, while a distant castle we visit once has a weight of 4.

The script seeks to find the coordinate point p that minimizes the following objective function:
f(p)=i=1∑n​wi​∥xi​−p∥

Where:

    p is the optimal target coordinate (latitude,longitude).

    xi​ represents the coordinates of a specific destination.

    wi​ is the assigned scalar weight of that destination.

    ∥xi​−p∥ is the L2​ norm (Euclidean distance) between the optimal spot and the destination.

The Optimization Algorithm (L-BFGS-B)

Unlike finding the mean, finding the geometric median for more than three non-collinear points has no explicit, closed-form algebraic formula. It must be solved numerically.

To solve this, the script utilizes scipy.optimize.minimize.

    The Initial Guess: To give the algorithm a head start and reduce computation time, we calculate the standard centroid (the mean of the coordinates) and feed it to the solver as the initial starting point.

    The Descent: The script uses the L-BFGS-B (Limited-memory Broyden–Fletcher–Goldfarb–Shanno) algorithm. This is a quasi-Newton method that approximates the Hessian matrix to efficiently navigate the gradient of our objective function, iteratively stepping down until it finds the local minimum—our perfect location.


Calculating the coordinate is only half the battle, the output needs to be actionable.

Once the L-BFGS-B solver converges on the optimal point p, the script generates a bounding box (approximately a 500-meter search radius). It calculates the Southwest and Northeast corners of this box and injects them directly into Airbnb's hidden map-search parameters, alongside the trip dates and guest count.

The output is a single URL. Clicking it opens Airbnb, pre-filtered for your group size, perfectly centered on the mathematical optimum of your itinerary.
Setup & Usage
Requirements

The mathematical engine relies on standard scientific computing libraries:
Bash

pip install numpy scipy

Execution

    Open optimal_airbnb.py and modify the trip_destinations dictionary with your coordinates and custom weights.

    Update the checkin_date, checkout_date, and guest_count.

    Run the engine:
_________________________
Bash

python optimal_airbnb.py
_________________________
Example Output:
Plaintext

Calculating the best spot for the Airbnb...
Target coordinates: 38.35412, -0.46321
Airbnb Link:
https://www.airbnb.es/s/Alicante/homes?checkin=2026-04-11&checkout=2026-04-12&adults=4&sw_lat=38.34912&sw_lng=-0.46821&ne_lat=38.35912&ne_lng=-0.45821&zoom=16&search_type=USER_MAP_MOVE

Tech Stack

    Python 3

    NumPy: For handling coordinate arrays and vector-based Euclidean norm calculations.

    SciPy (scipy.optimize): For executing the L-BFGS-B minimization.
