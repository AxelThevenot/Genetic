## Genetic Algorithm principle

The genetic algorithms (GA) are evolutionnary algorithms inspired by [Darwin's theory](https://www.darwins-theory-of-evolution.com/). This theory is about the evolution of species. According to him, animal and plant species had to change to survive, adapting to changes in their environment. Only those who survive have descendants, this is called natural selection. The GA's goal is to obtain an approximate solution to an optimization problem, when there is no exact method (or if the solution is unknown) to solve it in a reasonable time.
That is what we will try to implement taking the example of the famous Travelling Salesman Problem

## Travelling salesman problem (TSP)

The [Travelling Salesman Problem](https://en.wikipedia.org/wiki/Travelling_salesman_problem) is easy to understand. The goal is to find the best route, which have the shortest distance, to link each points the salesman wants to visit from a starting point and to this same starting point (here purple on the GIF below). Yet it is an hard tark if we try to resolve it by trying each possibilities. Take the example of trying to find the best route to link 100 points. There is 9.332622e+157 possibilities. To be more precise there is 93 326 215 443 944 152 681 699 238 856 266 700 490 715 968 264 381 621 468 592 963 895 217 599 993 229 915 608 941 463 976 156 518 286 253 697 920 827 223 758 251 185 210 916 864 000 000 000 000 000 000 000 000 possibilities. To compare, the estimate age of the universe is 4,320432e+17 seconds. So we can infer that the solution is impossible to find (in a human time of course) and we will try to approximate the shortest route as the GIF below shows. There is a GA for the TSP of 100 points and that is working... Staggering ! So thank you nature for inspiring us. 

![TSP for 100 points](100points.gif)

## IT and biology mingle

As I said, the GA are evoultionnary algorithms inspired by the biology. The principles of population evolution are selection the crossover and mutation. The selection in a real life population is the natural selection. The "best" individuals give their genes to their children and they form the new generation and this process is repeated. That is on this point the GA algorithms are inspired by the evolutionnary principles. 

To answer a problem, each individual has a cost or a fitness. These two are similar in inherently but different in the way we see them. For example in the TSP case, we well talk about costs as we are interested in how much the route costs (in distance). On the contrary, in a craking-password case, we will be interested in how much the string found fits with the password, so we talk about fitness.

![](Evolution.png)

First of all there is the selection process. It is a selection according to the best individuals of a generation.
The selection process in an algorithm can be done by several ways :
* Proportionate to the cost/fitness : each individual has a weight proportionate to its cost or fitness. The selection is done by choosing randmly individuals according to their weigth. It is an usual way to do the selection. Yet this selection process in some case can approach [local extrema](https://thinkingandcomputing.com/posts/genetic-algorithms-neural-networks.html) without reaching the global one.
* Proportionate to the rank : this process is really similar to the previous one. It sorts the individuals according to their cost or fitness. The each individuals has a weight proportionate to their rank. The worst has a 1 weight, the second worst has a 2 weight, ..., the best has a N weight.
* By tournament : laucnch several tournaments of size k composed by random individuals of the generation. Take the firsts. The advantage of this method is that it can work on parallel architectures.
* Elitism : The easiest way to do the selection. It takes the k-bests individuals of the generation.
* There is [more diffrent process](https://fr.slideshare.net/riyadparvez/selection-in-evolutionary-algorithm) of course.

crossover

mutation


.

## Biology in our program

individuals paragraph

population paragraph

cost or fitness ? paragraph

En théorie : une population ne peut tomber dans un optimum
local indéfiniment si des individus aléatoirement sélectionnés
sont ajoutés à chaque génération.
En pratique : On considère qu'une population a convergé s'il n'y
a plus d'amélioration après quelques générations. On peut
relancer plusieurs populations et vérifier (ou non) la stabilité des
meilleurs individus obtenus

mutation paragraphe

## Pseudo Code
![To inspire the pseudo code](https://www.google.com/search?q=*+selection+*+crossover+*+mutation&rlz=1C1CHBF_frFR699FR699&source=lnms&tbm=isch&sa=X&ved=0ahUKEwibw7O00-7gAhWSz4UKHZdECyoQ_AUIDigB&biw=1536&bih=706#imgrc=KSuxUC9PhUMzdM:.png)
```

```

## Let's start with python

### Individuals

Individuals and cost

```python 
import random

class Individual:

    def __init__(self, _points):
        """
        Initialize a individual from a list of point
        :param _points: list of point the individual is made from
        """
        self.points = [list(point) for _, point in enumerate(_points)]
        self.cost = self.costFunction()
    
    
    def costFunction(self):
        """
        Calculate the distance needed to link each point
        :return:
        """

        # only create the distance function between two points (Pythagoras)
        def distance(point_1, point_2):
            return (point_1[0] - point_2[0]) ** 2 + (point_1[1] - point_2[1]) ** 2

        # and sum the distances between each point to have the cost value
        cost = 0
        for i in range(len(self.points) - 1):
            cost += distance(self.points[i], self.points[(i + 1)])
        return cost
```
crossWith 
```python 
    def crossWith(self, other):
        """
        breed self with other as a 4-points crossover
        :param other: other individuals
        :return: child
        """
        # select randomly the 4-point of te crossover
        genes = [random.randint(0, len(self.points) - 1) for _ in range(4)]
        genes.sort()  # sort them for the use

        points_from_self = self.points[genes[0]:genes[1]]  # first part of self's points
        points_from_self += self.points[genes[2]:genes[3]]  # second part of self's points
        # looking for the missing points
        points_from_other = [point for _, point in enumerate(other.points) if point not in points_from_self]

        # add the parent's point to create the child's list of point
        child_points = points_from_self + points_from_other
        return Individual(child_points)
```

mutate

```python 
    def mutate(self):
        """
        mutate an individual
        """
        # select a random gene
        rand_index = random.randint(0, len(self.points) - 1)
        # mutate [a, b, c, d, e, f, g] with rand_index = 2 become [c, d, e, f, g, a, b,]
        point_mutated = self.points[rand_index:]  # [c, d, e, f, g] in the example
        point_mutated += self.points[:rand_index]  # add [a, b] in the example
        self.points = point_mutated[:]
```

toXY you'll see with when whe will plot 

```python 
    def toXY(self):
        """
        to return X and Y array of coordinates to plot the individual
        :return: X coordinates, Y coordinates
        """
        # isolate the X and the Y array to return them
        X = [self.points[p][0] for p in range(len(self.points))]
        Y = [self.points[p][1] for p in range(len(self.points))]
        # as the starting point is (0, 0) point, we are looking to the first nearest point
        minDistance = {"index": 0, "distance": X[0] ** 2 + Y[0] ** 2}  # dictionary to easy use
        for i in range(len(X)):
            distance_i = X[i] ** 2 + Y[i] ** 2  # take the distance from (0, 0) f each point
            if distance_i < minDistance["distance"]:  # compare to the best point already found
                # replace it if there is a new nearest point from (0, 0)
                minDistance["index"] = i
                minDistance["distance"] = distance_i

        # surround X, Y by the starting point (0, 0), which is also the ending point
        X = [0] + X[minDistance["index"]:] + X[:minDistance["index"]] + [0]
        Y = [0] + Y[minDistance["index"]:] + Y[:minDistance["index"]] + [0]
        return X, Y
```

### Population

population init, newInidviduals,  randomPopulation

```python 
from individual import *

class Population:

    def __init__(self, size, pointList):
        """
        Initialize population of size individuals from a pointList
        :param size: size of the population
        :param pointList: pointList from which the individuals are made from
        """
        self.size = size
        # randomly create individuals from pointList
        self.individuals = [Individual(random.sample(pointList, len(pointList)))
                                       for _ in range(self.size)]
        self.best = self.individuals[0]


    def newIndividuals(self):
        """
        Create a random new Individual while the population size is not equal to the size chosen
        """
        while len(self.individuals) < self.size:
            points = self.individuals[0].points
            new_individual = Individual(random.sample(points, len(points)))
            self.individuals.append(new_individual)


def randomPopulation(size_population, size_plot, n_point):
    """
    Create a new random population
    :param size_population: size of population
    :param size_plot: size of the plot side
    :param n_point: number of point each individuals is made from
    :return: population created
    """
    # create a random array of point to define a new individual
    points = [(random.random() * size_plot, random.random() * size_plot)
              for _ in range(n_point)]
    return Population(size_population, points)
```
sort and selection

```python 
    def sort(self):
        """
        sort the individuals according to their cost
        """
        self.individuals.sort(key=lambda individual: individual.cost)
        # change the best individuals if the new best one beats the latest one
        if self.best.cost >= self.individuals[0].cost:
            self.best = self.individuals[0]

    def selection(self, ratio_selection):
        """
        The selection here is to keep the ratio_selection% bests
        :param ratio_selection: selection ratio interested in
        """
        self.individuals = [self.individuals[individual]
                            for individual in range(int(ratio_selection*len(self.individuals)))]
```

crossover

```python 
    def crossover(self, ratio_crossbreeding, ratio_selection):
        """
        Crossover the selected individuals
        :param ratio_crossbreeding: child ratio from crossbreeding interested in
        :param ratio_selection: selection ratio interested in
        """
        # count the number of parents after the selection
        potential_parent_count = int(ratio_selection * self.size - 1)
        for _ in range(int(ratio_crossbreeding * self.size)):
            # select a first parent
            parent_1_index = random.randint(0,potential_parent_count)
            # select a second parent (different than the first one)
            parent_2_index = random.randint(0,potential_parent_count)
            # verify they are different or change the second parent
            while parent_1_index == parent_2_index:
                parent_2_index = random.randint(0, potential_parent_count)
            self.individuals.append(self.individuals[parent_1_index].crossWith(self.individuals[parent_2_index]))
```

mutation

```python 
    def mutation(self, ratio_mutation):
        """
        Mutate randomly the population according to the ratio chosen
        :param ratio_mutation: mutation ratio interested in
        """
        for _ in range(int(ratio_mutation * self.size)):
            # select a random individual
            individual_index = random.randint(0, self.size - 1)
            self.individuals[individual_index].mutate()
```

nextGeneration

```python 
    def nextGeneration(self, ratio_selection, ratio_crossbreeding, ratio_mutation):
        """
        Next generation process
        :param ratio_selection: selection ratio interested in
        :param ratio_crossbreeding: child ratio from crossbreeding interested in
        :param ratio_mutation: mutation ratio interested in
        """
        self.sort()  # sort the individuals according to their cost
        self.selection(ratio_selection)  # selection process according to the ratio chosen
        self.crossover(ratio_crossbreeding, ratio_selection)  # crossbreeding process according to the ratio chosen
        self.newIndividuals()  # create new individuals if needed
        self.mutation(ratio_mutation)  # mutation process according to the ratio chosen
```

### Genetic Generation and plot

imports

```python
import matplotlib.pyplot as plt
import matplotlib.animation as animation
from population import *
```

variables

```python
SIZE_PLOT = 10
DISPLAY_GENERATION = 5  # Display the best individuals each DISPLAY_GENERATION
N_POINT = 30  # Number of point to link
SIZE_POPULATION = 150  # Size of each population
MAX_GENERATION = 500  # Stop running at MAX_GENERATION
# each new generation is composed by :
# --> RATIO_SELECTION of the best individuals in the previous generation
# --> RATIO_CROSSBREEDING of children of the best individuals children
# --> The rest is new random individuals to minimize the chance to be in a local minimum
RATIO_SELECTION = 0.3
RATIO_CROSSBREEDING = 0.65
# each new generation is subject to a RATIO_MUTATION
RATIO_MUTATION = 0.7

# Initialize the first randomized population
population = randomPopulation(SIZE_POPULATION, SIZE_PLOT, N_POINT)
generation_count = 0
# costs array to keep the best cost value of each generation
costs = []

# Initialize the two plot
figGen = plt.figure(1)  # display the best cost according to the generation number
fig = plt.figure(0)  # display the best cost for actual generation

ax = fig.add_subplot(1,1,1)  # plot for the post visualization
axGen = figGen.add_subplot(1,1,1)  # plot for the costs evolution

ani = None  # to animate (update) the two plots
started = False
```

plot

```python 
def display():
    """
    Udpdates the two plots
    """
    # First, clear the plots
    ax.clear()
    axGen.clear()

    # actualize plot title and axes labels
    ax.set_title("Generation : {0}".format(generation_count))
    axGen.set_xlabel("Generation")
    axGen.set_ylabel("best cost")
    # Ask for the best cost in two vectors (X coord and Y coord)
    X, Y = population.best.toXY()

    # place the N_POINT point (except starting-ending point coord(0, 0))
    plt.scatter(X[1:-1], Y[1:-1], c='#474747')
    # then place the starting-ending point
    plt.scatter(0, 0, c='#8800FF')

    if started:
        # show how the N_POINT are linked in the best cost case found
        ax.plot(X, Y, c='#2F9599', )
        # actualize the plot of costs
        axGen.plot([i for i in range(len(costs))], costs, c='#2F9599')
    axGen.set_xlim([0, MAX_GENERATION])  # to keep the same abscissa
    # the show the plots
    fig.canvas.draw()
    figGen.canvas.draw()

def key_pressed(event):
    """
    To start/pause running the programme
    :param event: key_press_event
    """
    if event.key == 'enter':
        global started
        started = not started
```

Generations 

```python
def nextGeneration(frame_number):
    """
    Update for new generation of the population
    :param frame_number:
    """
    if started:
        global generation_count
        while generation_count < MAX_GENERATION:
            population.nextGeneration(RATIO_SELECTION, RATIO_CROSSBREEDING, RATIO_MUTATION)
            generation_count += 1
            costs.append(population.best.cost)  # actualize the best costs array
            # then display the new update
            if generation_count % DISPLAY_GENERATION == 0:
                display()
        figGen.canvas.draw()
```

and then launch the fucking fuck

```python
if __name__ == "__main__":
    # to animate the plot and launch the population update
    ani = animation.FuncAnimation(fig, nextGeneration)
    # connect to the key press event to start/pause the programme
    fig.canvas.mpl_connect('key_press_event', key_pressed)
    display()
    plt.show()
```







