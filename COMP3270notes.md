# Searching

## Terminology
- Informed search: available guidance on where to find solutions
    - e.g Distance from destination
- Uninformed Search: no such information

### Problem-Solving Agents
- Goal: objective the agent is trying to achieve
- Problem: process of deciding what actions and states to consider
- Search: process of looking for the best sequence of actions

### Problem Components
- Initial state
- Possible actions
    - Successor function: state reached by applying an action
    - \<action, successor\> pair
    - e.g \<Go(HKU), In(HKU)\>
- Goal test: determines if the state is a goal state
- Path cost: numeric cost for each path (can be all 0)
- e.g 8 sliding block puzzle
- e.g 8 queens problem

## Tree Search Algorithm
- Root: node corresponding to the initial state
- Expanding current node: generating successors of already-explored states
- Search strategy: choice of which state to expand
- BFS, DFS, etc are the same algorithm with different state management

### State vs Node
- State: representation of physical configuration
- Node: data structure used for solving
    - Components:
        - State: corresponding state of the node
        - Parent node
        - Action: Action applied to parent to generate this node
        - Path-cost: cost g(n) from initial state to this node
        - Depth: number of steps from initial state
    - Nodes are put in a queue, called 'fringe'

## Performance Considerations
- Completeness
- Optimality
- Time complexity
- Space complexity
- Complexity is measured in terms of 
    - `b` Max branching factor
    - `d` Depth of least cost solution
    - `m` Max depth of the state space

## Uninformed Search Strategies
![image](03_search_complexity.png)

- Breadth-first Search
    - fringe = FIFO queue
    - Optimal if cost is constant
    - Time complexity: O(b^(d+1))
    - Space complexity: O(b^(d+1))
- Uniform-cost Search
    - fringe = queue ordered ascending by path cost
    - Complete if step cost is non-zero
    - Optimal
    - Time complexity: number of nodes with g <= cost of optimal solution
        - O(b^(1+\floor{C^*/\epsilon}))
    - Space complexity: number of nodes with g <= cost of optimal solution
        - O(b^(1+\floor{C^*/\epsilon}))
- Depth-first Search
    - fringe = LIFO queue
    - Complete if space is finite
    - Optimal if cost is constant
    - Time complexity: O(b^m), m is the max depth
    - Space complexity: O(bm)
- Depth-limited Search
    - Limit search depth, fail if that limit is reached
- Iterative Deepening Search
    - Depth-limited search with increasing limit
    - Combines benefits of BFS and DFS
    - Complete
    - Optimal if step cost constant
    - Time: (d+1)b^0 + db^1 + (d-1)b^2 + ... + b^d = O(b^d)
    - Space: O(bd)
- Note: Make sure repeated states are skipped
    

    
    
# Informed Search Algorithms

## Best-first search
- Heuristic funtion h(n): estimates the cheapest path from node n to a goal node

### Greedy Search
- h(n) = estimated cost from n to the closest goal
- e.g Straight-line distance between two cities
- Not complete (can get stuck in loops)
    - Complete with repeated-state checking in a finite space
- Not optimal
- Time: O(b^m), a good heuristic can give dramatic improvement
- Space: O(b^m)

### A* Search
- Look at previous cost in addition to future cost
- f(n) = g(n) + h(n)
    - g(n) = cost so far to reach n
- Requires an 'admissible' heuristic (always <= actual value)
- Optimality
    - Consistency: h(n) is consistent if for every node n and every successor n', h(n) <= c(n, a, n') + h(n')
    - A* is optimal if h(n) is consistent
    - Consistency is stricter than admissibility
    - Consistency is required for graph search
    - Admissibility is required for tree search
- Properties
    - Complete
    - Optimal (proof in slides)
    - Time: Exponential
    - Space: Exponential (risky)
- Optimally efficient for any given heuristic function (no other optimal algorithm is guaranteed to expand fewer nodes)

### Heuristic Functions
- Effective branching factor (b*): estimates the branching factor accross all nodes
- If for any node n, $h_2(n) \geq h_1(n)$, then h\_2 **dominates** h\_1 (h_2 is always better)
    - A* will never expand more nodes using h\_2 than h_1

#### 8 Puzzle Problem

- h_1(n) = number of misplaced tiles
- h_2(n) = total Manhattan distance (number of squares from correct position for each tile)

### Finding Heuristics

- The cost of an optimal solution to a relaxed problem is an admissible heuristic for the original solution

### Generating Heuristics

- Construct heuristics by defining the problem formally
- Generate relaxed problems by removing problem constraints
- Optimal solution of a relaxed problem <= optimal solution of the real problem
- $h(n) = \max(h_a(n), h_b(n))$


### Learning Heuristics

- Inductive learning, reinforcement learning

### Interactive Improvement Algorithms

- Try to improve current state step by step
- e.g n-queens: given any state, try to move a queen to reduce the number of conflicts
- e.g Hill-climbing: trying to climb to the highest point
  - Problem: Can be stuck at a local maxima
  - Plateau: Can escape with random sideways moves

### Genetic Algorithm

- Start with population of k randomly generated states
- Create the next generation, and keep the fittest k
- Next generation is chosen through:
  - Crossover: Two parents are broken at a random crossover point and concatentated
  - Mutation: Randomly change 1 symbol of the states
  - Random probabilities are weighted

# Adversarial Search - Games

## Games vs Search Problems
- Zero-sum game: Utility for two opponents at the end is equal and opposite (+1 and -1)
- Opponent is unpredictable, so assume they are perfect
- Finding the optimal solution is infeasible due to size of search tree

### Types of Games
- Perfect information vs Imperfect information
  - How much information about the game state do we know
- Deterministic vs Chance

### Game Tree
- Initial state: beginning board position
- Successor function: (move, state)
- Terminal test: is game over? 
  - Terminal state = end states
- Utility function: numerical value of each terminal state (win, loss, draw)
  - You try to maximize utility
  - Opponent tries to minimize utility

### Optimal Strategies
- Fair game: Perfect play results in a draw
- Assume game is fair and the root node has utility 0
- $\text{MINIMAX-VALUE}(n)=\begin{cases}\text{UTILITY}(n) & \text{if n is a terminal state}\\\ \max_{s \in Successor(n)} \text{MINIMAX-VALUE}(s) & \text{if n is a MAX node}\\\ \min_{s \in Successor(n)} \text{MINIMAX-VALUE}(s) & \text{if n is a MAX node}\end{cases}$
  - Complete if tree is finite
  - Optimal
  - Time: $O(b^m)$, b is number of legal moves at each point, m is bax depth
  - Space: $O(bm)$ if all successors are generated at the same time. $O(m)$ if only one successor is generated at the same time

### Multiplayer Games
- Need to calculate utlity for each player
- Complications: Alliances, alliance breakups

### Improvements

#### $\alpha$-$\beta$ Pruning
- Prune away branches that will not influence the final decision
- $\alpha$: highest-value found so far along the MAX path
- $\beta$: lowest-value found so far along MIN path
- If cost of $n$ is worse than minimax($\alpha$), that branch can be pruned for MAX
- If cost of $n$ is worse than minimax($\beta$), that branch can be pruned for MIN

#### Evaluation Function
- Infeasible to expand until goal state
- Use Evaluation Function to estimate the expected utility of the distance to the goal
- A good evaulation function
  - Orders terminal states the same way as the utility function
  - Easy to compute
  - Strongly correlated with the actual chance of winning
- How it works
  - Identify features of the configuration
  - Compute numerical value of each feature and combine
- Evaluation function can be learned by the program

#### Cutting off Search
- Test for cut-off point instead of terminal node
- e.g. Only look forward 5 steps
- Should only be used for quiescent states (states unlikely to have wild swings in future)

#### Horizon Effect
- Unavoidable losing move in the future
- Stalling moves are available that delay the losing move past the 'horizon' (search depth)

### Non-Deterministic Games
- Random element in game (e.g dice throw)
- Model using **chance nodes** which have the possible outcomes
- Value of the evaluation function becomes important
    - Should be a *positive linear* transformation to the actual probabilty of winning

#### Card Games
- Opponent's cards are unknown (chance)
- Use probabilty of card deals and compute expected return
- Have a expected risk to prevent disastrous decisions (e.g Die if you choose wrong)

# Constraint Satisfaction Problems

## Definition
- Set of _variables_ (`X_1, X_2, ... X_n`) from the domain D
    - Boolean variables = Boolean satisfiability problem
    - Discrete variables = finite domains
    - Continuous variables = linear programming
- Set of _constraints_ (`C_1, C_2, ... C_m`)
    - Unary contraints: involves a single variable
    - Binary contraints: invloves pairs of variables
    - Higher-order contraints: 3 or more variables
    - Preferences (Soft contraint): Cost for each assignment, optimization problem
- A _problem state_ is an assignment of variable values
    - An assignment that does not violate any constraints is _consistent_
    - An assignment with every variable is _complete_
- A _solution_ is a complete assignment that satisfies all constraints
- e.g Map colouring
    - Variables are region nodes
    - Constraints are edges between nodes

### Incremental Formulation
- Formulate as a search problem
    - Initial State: Empty assignment
    - Successor function: A value is assigned to an unassigned variable without breaking constraints
    - Goal Test: Is assignment complete?
    - Path Cost: Constant cost for every step
- Depth of tree = number of variables
    - Use DFS
- e.g Cryptarithmetic Puzzle (TWO + TWO = FOUR)

### Backtracking Search
- CSPs are _commutative_ (Order of actions have no effect on outcome)
- Use _backtracking_ where you backtrack if there are no legal value assignments left

#### Improvements to Backtracking
- Variable ordering
    - Minimum remaining values (fail-first): 
        - Choose the variable with the fewest legal moves
        - Prunes the search tree earlier
    - Degree Heuristics:
        - Choose variable involved in the largest number of contraints
        - Reduce branching factor
- Value ordering
    - Least Constraining Value
        - Choose value that rules out the fewest choices
        - Keep options open for future assignments
- Propagate information through constraints
    - Forward Checking
        - Keep track of availiable legal values for unassigned variables
        - When assigning X, check unassigned Y related to X and delete inconsistent values
        - Terminate search when no legal values exist
    - Arc Consistency
        - Forward checking through all connected nodes

### k-consistency
- A CSP is _k_-consistent if for any consistent assignment of `k-1` variables, a consistent value can be assigned to any kth variable
- Strong k-consistancy: k, k-1, ..., 1-consistent
- 1-consistency = node consistency
- 2-consistency = arc consistency

### Local Search
- Allow states with unsatisfied constraints
- Operator to reassign variable values
- Heuristic: min-conflict (violates the fewest constraints)

### Tree-structured CSPs
- Convert tree to a Directed Acylic Graph and process in that order
- 1. Backward removal to ensure parents are consistent with child
- 2. Forward assignment to assign values

### Near Tree-Structured CSPs
- If deleting node X creates a tree, assign a value to SA and delete that value from inconsistent variables


# Logical Agents

## Knowledge Base
- Set of sentences representing assertations about the world
- e.g
    - TELL: Add new sentences to knowledge base
    - ASK: query knowledge base
- Agents have two levels
    - Knowledge level: what the agents know
    - Implementation level: data structures and algorithms

## Knolwedge-Based Agent
- Represents states, actions, etc.
- Incorporates new percepts
- Updates internal representation of the world
- Deduces hidden properties of the world
- Deduces appropriate actions

### Approaches
- Declarative Approach: Knowledge is a set of sentences describing the knowledge
    - Needs a reasoning module to process queries
- Procedural Approach: Encodes the desired behaviour directly
    - More efficient

#### Example: Wumpus World
- Cave with rooms connected by passageways
- A room can contain:
    - The 'wumpus' that eats anyone who enters its room
        - Agent can shoot the wumpus, but it only has 1 arrow
    - A deep pit trapping anyone except the wumpus
    - The heap of gold

##### PEAS Description    
- Performance measure: 
    - +1000 for picking up gold, -1000 for falling into pit or being eaten, -1 per step and -10 for using the arrow. 
- Environment:
    - A 4×4 grid of rooms.
    - Agent always starts at [1, 1], facing to the right.
    - Location of wumpus and gold is chosen randomly, other than the start square.
    - Each square, other than start, can be a pit with prob. 0.2
- Actuator:
    - agent can move forward, turn left or right.
    - agent dies when entering a square with a pit or live wumpus.
    - Grab — pick up an object in the same square.
    - Shoot — fire an arrow in the direction the agent is facing, only the first shoot has effect, because agent has only one arrow.
- Sensors
    - Stench: Wumpus is adjacent
    - Breeze: Pit is adjacent
    - Glitter: Room contains gold
    - Bump: Wall
    - Scream: Wumpus is killed

## Logic
- Just read your CS245 notes...
- Formal languages for representing information to draw conclusions
- Syntax: sentences in the language
- Semantics: meaning of sentences
- **Entailment**: one idea semantically follows from another
    - e.g KB |= α iff α is true in all states where KB is true
- Models: formally structured worlds used to evaluate truth

### Inference
- Soundness: If there is a proof, φ logically follows
	- Σ ⊢ φ implies Σ ⊨ φ
- Completeness: If φ logically follows, there is a proof
	- Σ ⊨ φ implies Σ ⊢ φ

### Propositional Logic Syntax
- Atomic sentences: single boolean propositional symbol
    - e.g P_1, q, TRUE
- Complex sentences: connected atomic sentences
    - e.g P ∧ Q, ¬TRUE

### Inference by Enumeration
- Truth table with all possible states
- For every state where KB is true, check if α is true
- Sound by definition of entailment
- Complete as it works for every KB and α, and is guarenteed to terminate (finite states)
- 2^n models for n symbols

### Validity and Satisfiability
- Valid: True for all models
- Satisfiable: True in some models
- Unsatisfiable: True in no models

### Proof Methods
- Application of inference rules
    - Modus Ponen: (α => β, α) => β
    - AND Elimination: (α ∧ β) => β
    - Other rules
- Model Checking
    - Check every state

### Forward and Backward Chaining
- Automated inference rule application
- Require KB to contain **Horn Clauses**
- Definite Clause: disjuction of literals where exactly literal is positive
- Horn Clause: disjunction of literals where at most one is positive
- Forward Chaining
    - Apply any rules whose premises are satisfied until query is found
- Backward Chaining
    - Check if query q is known already
    - If not, use premises of rules containing q

### Resolution
- Conjunctive Normal Form (CNF)
    - Literal - Propositional variable or its negation
    - Clause - Disjunction of literals
    - CNF - Conjunction of clauses (A bunch of OR's joined by AND's)
        - Only connectives in CNF are ¬ ∨ ∧
- Resolution Proof Procedure
    - To prove φ from Σ, via a Resolution refutation:
    1. Convert each formula in Σ to CNF.
    2. Convert ¬φ to CNF.
    3. Split the CNF formulas at the ∧s, yielding a set of clauses. Call them assumptions
    4. From the resulting set of clauses, keep applying the resolution inference rule until either:
        - The empty clause ⊥ results. In this case, φ is a theorem.
            - Σ ∪ {¬φ} is a contradiction
        - The rule can no longer be applied to give a new formula. In this case, φ is not a theorem.
            - Σ ∪ {¬φ} is satisfiable
- e.g {(q ∨ r) → s ⊨ q → s}
    1. ¬q ∨ s   assumption
    2. ¬r ∨ s   assumption
    3. q        assumption (from negated conclusion)
    4. ¬s       assumption (from negated conclusion)
    5. ¬q       1, 4
    6. ⊥        3, 5
    - Refutation complete

# First-Order Logic

# Uncertainty
- First-Order logic can't deal with uncertainty due to
    - Laziness: too much work to list out all possibilities
    - Theoretical ignorance
    - Practical ignorance
- Use probability to come up with a degree of belief between 0 and 1
- Probability depends on the knowledge state

## Utility Theory
- Utility is used to represent preferences for various outcomes
- Decision Theory takes into account probability and utility
    - Choose action with the highest expected value

## Probability
### Axioms of Probability
- $0 \leq P(a) \leq 1$
- $P(true) = 1 \quad P(false) = 0$
- $P(a \lor b) = P(a) + P(b) - P(a \land b)$

###  Bayes' Rule
$P(a \vert b) = \frac{P(b \vert a) P(a)}{P(b)}$

### Prior Probability
- Unconditional probability
- Degree of belief in the absence of any other information

### Full joint probability distribution
- Table of probability of each assignment of random variables

# Learning

## Types of Feedback
- Supervised: supervisor is there to tell if decision if correct or not
- Unsupervised: no training data
- Reinforcement: learn from rewards

## Inductive Learning
- Given a set of observations, derive a hypothesis to predict unseen observations
- Ockham's Razor: Choose the simplest hypothesis

## Learning Decision Trees
- Decision is reached by performing a sequence of tests
- Input: set of attributes
- Output: decision
    - Discrete-value = classification
    - Continuous function = regression
- Constructing the tree:
    - Choose the best splitting attribute
    - If no attributes are left, the data is noisy

## Training Methodology
1. Construct large set of examples
2. Divide data into *training* set and *test* set
3. Apply learning algorithm to training set to obtain a hypothesis
4. Measure percentage of correctly classified examples in the test set

### Overfitting
- If data is overfitted, it is only good for the training data, and not generally
- Use a third dataset: validation data
    - Tunes the parameters


# Statistical Learning

## Naive Bayes Model
$P(C \vert x_1, \dots, x_m) = \alpha P(x_1, \dots, x_n\vert C)P({}C) = \alpha P({}C) \prod_iP(x_i \vert C)$

## Bayes Classifier
- Does not assume that attributes are independent
- Use a multivariate distribution
- Must have amount of data >> number of parameters

### Procedure of Classification
1. Collect large amount of training data
2. Feature extraction of representative features
3. Data is point in d-dimensional vector space
    - Perform classification
    - Dimension reduction if d is too large
6. Divide data into training and test data
7. Train on training data
    - Find decision serfaces for different classes
    - Find the probability distribution of the objects
8. Test on testing data to determine performance

### Small Data Set problems
- Overfitting may occur
    - Solve with dimension reduction
- Test set will not be a good indicator
    - Solve with cross validation
        - Divide training data into k subsets
        - Train with k-1 subsets and test using the remaining subset
        - Repeat k times

## Neural Networks
- Output of previous neuron is linked to input of current neuron
- Input: linear combination of input signals
- Activation function: non-linear function that maps input to a range (e.g (0,1))
- Output: linked to other neurons
- Bias Weight (W_0): provides a constant term

### Network Structures
- Feed-forward networks
    - e.g. Single-layer perceptrons
    - e.g. Multi-layer perceptrons
    - Represents a function of its current input
    - Has no internal state other than the weights
- Recurrent networks
    - Contains cycles for feedback
    - Internal states for short-term memory

### Single-Layer Perceptrons
- 1 input layer, 1 output layer
- Output node only depends on links leading to it
- Output unit with largest output value wins
- Non-linear function must be monotonic, increasing
    - Cannot change ordering of values
- Cannot implement XOR

### Perceptron Learning
- Adject weights of network to reduce erroro n the training set
    - Calculus
- Better at majority function vs decision tree

### Multilayer Perceptrons
- Layers are (usually) fully connected
- Hidden units are usually chosen by hand
- Learn through back-propagation
    
### Neural Network Training
1. Select next training pair from the training set and apply input to the network
2. Calculate the output of network
3. Calculate error between network output and desired output
4. Adjust weights of network by gradient descent
5. If error is larger than the threshold, repeat

### Improvments
- Momentum
    - Keep a fraction of weights from the previous cycle
- Weight Decay
    - Decay weights every cycle
    - Unneeded weights will eventually become insignificant
# Deep Learning and Convolutional Neural Networks

## Convolutional Neural Networks
- Nodes are not fully connected
- Used for image recognition

### Convolution
- Combine local features into a larger  feature information

### Pooling
- Input is partitioned into non-overlapping sub-regions that each output a pooled value
- Images are 'stationary', meaning features useful in one region are useful in another
- Pooled stats are lower in dimension and result in less over-fitting

### Layering
- Convolutional layers alternate with pooling layers
- Then classified by fully-connected layers
- Softmax layer squaches a vector to add up to 1

### Parameters
- Number of filters: Number of features and pixel positions influence cost
- Filter Shape: Find the right granularity for abstraction
- Max pooling shape
