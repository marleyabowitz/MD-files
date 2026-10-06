# COMS W4701 Exam 1 Study Guide

Fall 2026. Built from Lectures 1–8, Recitations 2–5 (including the Exam 1 review), Conceptual Homework 1 and its solutions, and the search coding assignment.

Exam 1 is October 8 (Sections 1–3) and October 9 (Section 4), in your regular classroom. The exam-review recitation says explicitly that it does **not** cover every topic from class and does **not** match the number or style of every exam question. This guide covers every topic in the lectures and recitations through constraint satisfaction.

Lecture 1 also previews Bayesian networks, machine learning, deep learning, and modern LLM systems. Those are the rest of the course. They are summarized in the last section so a definition question cannot surprise you, but the problems you will actually be asked to solve are in Sections 1–8.

---

## How this course wants answers

Almost every written question says **justify**. A correct label with no reason loses points. Several environment questions explicitly accept more than one label if the assumption is stated.

Conventions used in every search trace in this course:

1. **Goal test when a node is selected for expansion** (removed from the frontier), not when it is generated. Russell & Norvig sometimes test BFS goals at generation. This class does not. The coding assignment says the same thing: (1) remove, (2) goal-test, (3) expand.
2. **Graph search.** Keep an explored set. Do not expand a state twice. If a state is still on the frontier and you find a cheaper path, update its key. Once it has been expanded, a later path is discarded.
3. **Order of visit** is the order nodes are removed from the frontier and expanded. It is not the solution path. Recover the path with parent pointers.
4. **Tie breaks** are whatever the problem states. Homework and the exam review use: lower \(x\), then lower \(y\). Graph problems use lexicographic order when the problem says so. If the problem is silent, say what you assumed.
5. **Successor order** is whatever the problem states. Recitation mazes use Up, Right, Down, Left. The coding assignment uses Up, Down, Left, Right. For DFS with a stack, push in **reverse** order so the first successor sits on top.

A node is not the same thing as a state. A **state** is a configuration of the world. A **search node** stores that state plus a parent pointer, the action that produced it, the path cost \(g\), and the depth.

---

## 0. One-page checklist

If you can do every line below without notes, you are in shape.

**Agents.** Define a rational agent. Write PEAS for a new robot and classify the environment on all six axes, with the assumption each label depends on.

**Formulation.** Given a word problem, write initial state, state representation, actions, transition model, goal test, and path cost. Say whether the path matters.

**Uninformed search.** Trace BFS, DFS, IDS, and UCS. Fill in complete / optimal / time / space / fringe for all five, including the conditions (finite \(b\), unit cost, \(\epsilon > 0\)).

**Heuristics.** Test admissibility at every node against \(h^*\). Test consistency on every edge. Know that consistent and \(h(G) = 0\) implies admissible, and that the converse is false. Know what \(\min\), \(\max\), a convex combination, and a sum do to admissibility and to consistency. Know Manhattan and misplaced-tiles, and why a relaxed problem gives an admissible heuristic.

**A\*.** \(f = g + h\). Theorem 1: tree-shaped state space + admissible \(h\) is enough. Theorem 2: consistent \(h\) is enough on any graph. Inconsistency removes the guarantee; it does not force a wrong answer. \(h = 0\) is UCS.

**Local search.** Hill climbing, sideways moves, random restart, stochastic hill climbing, simulated annealing (accept rule and the sign of \(\Delta\)), genetic algorithms (fitness-proportionate selection, crossover, mutation). State counts and successor counts for \(n\)-queens in the one-queen-per-column encoding.

**Games.** Minimax bottom-up. Alpha-beta left to right: who updates \(\alpha\), who updates \(\beta\), prune when \(\alpha \ge \beta\). A pruned node may return a bound, not its true minimax value; the root value is still correct. Expectiminimax replaces a chance node by a probability-weighted average. Evaluation functions for chance games must be cardinal, not merely a ranking.

**CSPs.** Variables, domains, constraints. Backtracking. MRV, LCV, forward checking, and why forward checking misses conflicts between unassigned variables. AC-3, including the \(O(n^2 d^3)\) bound. Tree CSPs in \(O(nd^2)\) with no backtracking. Independent subproblems. Cutset conditioning.

---

## 1. What AI is, in this course

### Definitions they quote

- Intelligence (Webster): the ability to learn and solve problems.
- McCarthy: the science and engineering of making intelligent machines.
- Russell and Norvig, and the definition this course uses: the study and design of intelligent agents. An intelligent agent perceives its environment and acts to maximize its chance of success.

### Four schools (Russell & Norvig)

| | Thinking | Acting |
|---|---|---|
| **Humanly** | Cognitive modeling. "Machines with minds" (Haugeland). "Mental faculties through computational models" (Charniak & McDermott). | The Turing test. "Make computers do things at which, at the moment, people are better" (Rich & Knight). |
| **Rationally** | Laws of thought: codify correct reasoning in logic. | **This course.** Design of rational agents (Poole et al.). |

**Thinking humanly** needs a scientific theory of how humans think, and a way to validate it. Cognitive science and AI are now separate fields. The open questions are the level of abstraction (knowledge vs. circuits) and how to check the model.

**Thinking rationally** (the "laws of thought") writes correct reasoning as logic. Two failures: not all knowledge fits logical notation, and inference can blow up computationally.

**Acting humanly** is the Turing test (Alan Turing, 1950, "Computing Machinery and Intelligence"). A machine passes if it can fool a human interrogator in the imitation game. Turing listed the capabilities this requires: language, reasoning, knowledge, learning, and understanding. Turing (1912–1954) was a British mathematician and a World War II codebreaker.

**Acting rationally** is the approach of the course. The right action is the one expected to maximize goal achievement given the information available. A rational agent acts to achieve the best outcome, or, under uncertainty, the best **expected** outcome. Rationality is about the decision, not about mimicking a human.

### Foundations, one line each

- **Philosophy:** logic, the mind as a physical system of rules, learning, language, rationality.
- **Mathematics:** formal logic, computation and algorithms, probability.
- **Economics:** rational decisions, decision theory under uncertainty, game theory, Markov decision processes.
- **Neuroscience:** how brains work, and how they differ from computers.
- **Psychology:** the brain as an information processor. Cognitive psychology plus computer models became cognitive science.
- **Computer engineering:** the machines that make AI possible (self-driving cars are the lecture's example).
- **Control theory and cybernetics:** agents that take feedback and maximize an objective over time.
- **Linguistics:** language and thought. Modern linguistics plus AI is computational linguistics / NLP.

### History, as the lecture tells it

- **1940–1950, gestation.** McCulloch and Pitts model the brain as Boolean circuits. Turing's paper.
- **1950–1970, early enthusiasm.** Early programs, Samuel's checkers, the 1956 Dartmouth meeting (the birth of the field), McCarthy's LISP, the Shakey robot (logical reasoning plus physical action), Minsky's microworlds (geometry, algebra, calculus, blocks world).
- **1970–1990, knowledge-based AI.** Expert systems become an industry, then prove hard to build and maintain. AI winter.
- **1990–present, scientific AI.** Backpropagation and the return of neural nets, probability for uncertainty, GPUs/TPUs/FPGAs, very large datasets, then large language models. The lecture calls this the AI spring.

### Applications you should be able to name with the technique

These are the lecture's examples, not a memorization list of products. The point is which AI idea each one illustrates.

- Handwriting (checks, ZIP codes).
- Machine translation. Word-for-word translation failed ("out of sight, out of mind" became "invisible imbecile"). Statistical translation uses large parallel corpora.
- Speech and conversational systems, now built on large language models rather than narrow pipelines.
- Recommendation (collaborative filtering), web search, spam, face detection (Viola–Jones), face recognition, medical imaging.
- Chess, 1997: Kasparov vs. Deep Blue. Powerful search.
- Jeopardy!, 2011: Watson. Language and information extraction.
- Go, 2016: Lee Sedol vs. AlphaGo. Deep learning, reinforcement learning, and search.
- AlphaFold, 2024 Nobel Prize in Chemistry (Hassabis and Jumper): protein structure from sequence.
- 2025 International Mathematical Olympiad: general reasoning models at gold-medal level.
- Autonomous driving: DARPA Grand Challenge 2005 (132 miles), urban challenge 2007, Google car 2009.
- The lecture's laundry list of deployed tasks: fraud detection, forecasting, logistics, VLSI layout, scheduling, route finding, sentiment, summarization, energy optimization, diagnosis.

### The course map (the "intelligence spectrum" slide)

The same figure closes Lectures 1 and 2 and opens the CSP lecture. It is the professors' picture of the whole semester.

1. **GOFAI, low-level to high-level.** Reflexes, then states (search and adversarial games), then variables (CSP and Bayes nets), then logic.
2. **Machine learning.** Classical ML (regression, \(k\)-NN), ensembles (random forests, boosting), neural nets and CNNs, attention and transformers. Hand-engineered features give way to learned representations.
3. **Modern systems.** Foundation models and LLMs, grounding (multimodal models, retrieval-augmented generation), agency (RLHF, agentic AI). Plus ethics throughout.

Exam 1 is the first block: agents, search, games, CSP.

---

## 2. Rational agents

### The agent

An agent perceives the environment through **sensors** and acts on it through **actuators**. The program runs in a cycle: perceive, think, act.

\[
\text{Agent} = \text{Architecture} + \text{Program}
\]

The **agent function** maps a percept sequence to an action. The **agent program** is a concrete implementation of that function, running on some architecture.

Examples from lecture:

- Human: sensors are eyes, ears, and other organs; actuators are hands, legs, mouth, and other body parts.
- Robot: cameras and infrared range finders; motors.
- Also agents: a thermostat, a phone, a calculator, an Alexa, a self-driving car. The course focuses on agents with real computation in environments that need nontrivial decisions.

### The vacuum world

Two squares, A and B. Percepts are location and contents, for example \([A, \text{Dirty}]\). Actions are Left, Right, Suck, NoOp.

One simple agent function:

| Percept | Action |
|---|---|
| \([A, \text{Clean}]\) | Right |
| \([A, \text{Dirty}]\) | Suck |
| \([B, \text{Clean}]\) | Left |
| \([B, \text{Dirty}]\) | Suck |

The table is infinite in principle, because the input is the whole percept **sequence**, not just the latest percept. A reflex agent that ignores history only needs the latest row.

### Rationality, verbatim

> For each possible percept sequence, a rational agent should select an action that is expected to maximize its performance measure, given the evidence provided by the percept sequence and whatever built-in knowledge the agent has.

Judge rationality against four things, and only these four:

1. The **performance measure** that defines success.
2. The agent's **prior knowledge** of the environment.
3. The **actions** the agent can actually perform.
4. The **percept sequence** so far.

Rationality is not omniscience, and it is not perfection after the fact. It is the best decision you can make with what you know at the time. A rational vacuum that has not yet seen square B is not irrational for failing to clean B. An agent that crosses a street and is hit by a meteor is not irrational. An agent that crosses without looking is irrational, even if no car comes.

The performance measure is part of the problem definition. "Clean the floor" and "clean the floor while using as little energy as possible and never falling down stairs" are different agents.

### PEAS

PEAS is the problem specification. The agent you design is the solution.

| Letter | Meaning | What to write |
|---|---|---|
| **P** | Performance measure | How the score is computed. Be concrete: safety, time, cleanliness, energy, accuracy. |
| **E** | Environment | The world the agent is in, including other objects and people that are not themselves decision-making agents. |
| **A** | Actuators | The things it can do: motors, displays, grippers, speech. |
| **S** | Sensors | The things it can perceive: cameras, bump sensors, keyboards, GPS. |

**Self-driving car (lecture).**

- Performance: safety, time, legal driving, comfort.
- Environment: roads, other cars, pedestrians, road signs.
- Actuators: steering, accelerator, brake, signal, horn.
- Sensors: camera, sonar, GPS, speedometer, odometer, accelerometer, engine sensors, keyboard.

**Vacuum / Roomba (lecture).**

- Performance: cleanness, efficiency (distance traveled), battery life, security.
- Environment: room, table, wood floor, carpet, obstacles.
- Actuators: wheels, brushes, vacuum extractor.
- Sensors: camera, dirt sensor, cliff sensor, bump sensors, infrared wall sensors.

**ChatGPT (lecture case study).** Percepts are typed prompts. Actions are displaying an answer. The goal is good answers and a conversation that continues.

- Performance: speed, accuracy, relevance, safety, hallucination rate. The lecture says this list is not exhaustive.
- Environment: computer, user, keyboard, screen.
- Actuators: the display.
- Sensors: keyboard, text.

**e-Buddy, the elder-care robot (Recitation 2).** A robot that talks, reminds, navigates, fetches, serves food, watches for falls, and can call 911.

- Applications that apply: robotics, computer vision, spoken language, planning. Games is optional; the spec is open, and the solutions accept either answer if you justify it.
- Performance: medication reminders, fall detection, food and water estimates, alerting caregivers, safety, autonomy, engagement.
- Environment: the home, family, caregivers, medical and emergency services.
- Actuators: speaker, emergency calling, arms and legs.
- Sensors: cameras, microphone, and whatever monitors the house.
- Environment class: partially observable, multi-agent (the person is a cooperating agent), stochastic (people and falls are unpredictable), continuous (position, speed, camera intensities).

**Foldy, the laundry robot (Homework 1).** It does not move around the room, it is not connected to other devices, and nobody touches it during a cycle. The bin locks when the cycle starts.

- Performance: speed, fraction folded correctly, neatness, garments damaged or dropped, energy.
- Environment: the room, the bin, the output shelf, the garments. Users load and unload but do not act during the cycle.
- Actuators: arms and grippers, a conveyor or lift.
- Sensors: bin sensor, cameras, force or slip sensors, a weight sensor.
- Observability: partially observable is the best answer, because clothes underneath and sleeves tucked inside are hidden. Fully observable is acceptable only if you explicitly assume the camera sees the complete configuration.
- Agents: single-agent. The user and the clothes are environment, not agents. This is the contrast with e-Buddy and ChatGPT.
- Deterministic vs. stochastic: stochastic is the best answer, because grasps slip and folds come out crooked. Deterministic is acceptable if you explicitly idealize grasping as perfectly reliable.
- Discrete vs. continuous: continuous in joint angles, cloth shape, and camera intensities. Discrete is acceptable if you abstract to a finite set of garment types and fold routines.
- Static vs. dynamic: static, because nothing external changes while it deliberates. Semi-dynamic is acceptable if the score depends on elapsed time.

The grading note on Foldy is the grading note for every environment question: **state the assumption**. You are graded on the justification.

### The six environment properties

Use these exact distinctions.

**Fully observable vs. partially observable.** Fully observable means the sensors give the complete state at every moment. "Complete" means the state that is relevant to the decision, not the entire physical room. Partial observability means some relevant facts are hidden, so the agent must remember or act in order to reveal them.

**Deterministic vs. stochastic.** Deterministic means the next state is fixed by the current state and the action. If the only uncertainty is what other agents will do, the lecture calls the environment **strategic**. Stochastic means outcomes are genuinely uncertain and are modeled with probabilities.

**Static vs. dynamic.** Static means the world does not change while the agent is thinking. **Semi-dynamic** means the world itself does not change with time, but the performance score does (a chess clock). Dynamic means the world changes during deliberation (other cars move).

**Discrete vs. continuous.** Discrete means a limited number of distinct percepts and actions (checkers). Continuous means quantities vary over a range (a car's steering angle, a camera image). A subtlety from the ChatGPT case study: tokens are discrete, but the lecture treats open-ended discourse as continuous. Say which level of abstraction you are using.

**Single-agent vs. multi-agent.** Multi-agent can be **cooperative** (ChatGPT and the user holding a conversation; e-Buddy and the elder) or **competitive** (games). Do not call an object an agent just because the robot touches it. Foldy's garments are environment. A person who makes decisions that affect the score is an agent.

**Known vs. unknown.** This is about the designer, and about the agent's model of the rules, not about whether the state is visible. Known means the agent knows how the environment works (the transition model). Unknown means it has to learn the rules. An environment can be fully observable and unknown (you see the whole state but do not know what actions do), or partially observable and known (you know the rules of poker but not the opponent's cards).

The textbook also distinguishes **episodic** vs. **sequential**: in an episodic environment the current decision does not affect future decisions (classifying images one by one), and in a sequential one it does (chess, driving). The Fall 2026 slides do not list this axis. Know the word if it appears; the six axes above are the ones the lectures define.

### Worked classification: ChatGPT, as the lecture classifies it

1. Fully observable if you take the conversation so far as the relevant environment. Partially observable if the user can ask about facts the model was never trained on. The lecture presents both and prefers you to argue.
2. Multi-agent, cooperative. The user and the model share the goal of a useful conversation, and the model uses earlier turns.
3. Stochastic. The model does not determine what the user types next, or whether they type at all.
4. Continuous, in the lecture's judgment, because discourse varies without a small fixed set of percepts. They note that the raw input and output are discrete.
5. Static. Nothing in the relevant environment changes while a reply is being produced.

### Four agent programs, in increasing generality

1. **Simple reflex.** Action depends only on the current percept, through condition-action rules. History is ignored. This fails when the current percept is ambiguous. A vacuum that cannot see whether the other square is dirty, and that has no memory, can loop forever.
2. **Model-based reflex.** Keeps an internal state. The model has two parts: how the world evolves on its own, and how the agent's actions change the world. The agent updates its belief about the state and then applies reflex rules to that belief.
3. **Goal-based.** Adds an explicit goal. The agent chooses actions by considering their future effects (search, planning). This is the agent of Lectures 3–5. Needed when the right action depends on where you are trying to go, not just on what the world looks like now.
4. **Utility-based.** Adds a utility function when there are many goals, or tradeoffs (faster but less safe, cleaner but more energy). The rational choice maximizes expected utility. A goal is a crude utility: 1 if satisfied, 0 otherwise.

**Learning agents** wrap any of the above. The lecture's claim: they improve performance, explore, and generate better actions. The standard structure from the textbook has four parts. The **performance element** chooses actions. The **learning element** changes the performance element using feedback. The **critic** tells the learning element how well the agent is doing against the performance measure (percepts alone do not say whether an action was good). The **problem generator** suggests exploratory actions so the agent does not only repeat what it already knows how to do.

### Three ways to represent states

Expressiveness increases down the list. The algorithm you are allowed to use depends on the representation.

| Representation | What a state is | Algorithms |
|---|---|---|
| **Atomic** | A black box with no internal structure. A city in a route. A board in a game. | Search, games, MDPs, HMMs. |
| **Factored** | A vector of attribute-value pairs. GPS location and fuel level. Colors of regions. | Constraint satisfaction, Bayesian networks. |
| **Structured** | Objects and explicit relations between them. | First-order logic, knowledge-based learning, language understanding. |

Search treats the 8-puzzle board as atomic. Formulating the same puzzle as a CSP treats each tile as a variable. That is the jump from atomic to factored, and it is why CSP algorithms can do inference that blind search cannot.

---

## 3. Problem solving as search

### Goal-based agents

A reflex agent maps states to actions. A goal-based agent considers how actions change future states and searches for a sequence that reaches a goal. The search is offline ("mental"). Then the agent executes the solution.

The 8-queens problem shows why formulation matters. If you place queens on any empty square, the number of sequences is

\[
64 \times 63 \times \cdots \times 57 = 1.8 \times 10^{14}.
\]

A better formulation (one queen per column, domain is the row) is exponentially smaller. Local search and CSP formulations are smaller still. The same problem will appear three ways on this exam: as a search problem, as a CSP, and as a local-search landscape. Know which one you are in.

### The six components of a formulation

1. **Initial state.** Where the agent starts.
2. **States.** Every configuration reachable from the initial state. This set is the **state space**.
3. **Actions.** \(\mathrm{Actions}(s)\) is the set of actions legal in \(s\).
4. **Transition model.** \(\mathrm{Result}(s, a)\) is the state that action \(a\) produces from \(s\).
5. **Goal test.** True on goal states.
6. **Path cost.** A numeric cost for a path, aligned with the performance measure. Usually the sum of step costs. A solution is a path from the initial state to a goal. An **optimal** solution has the least path cost among solutions.

### Four formulations you should be able to reproduce

**8-puzzle.** A \(3 \times 3\) board, tiles 1–8, one blank.

- States: the location of each tile.
- Initial state: whatever board you are given.
- Actions: move the blank left, right, up, or down, when that square exists.
- Transition: the resulting board.
- Goal test: the board matches the goal board.
- Path cost: 1 per move.

In the coding assignment the blank is 0 and the goal is the row-major order \(0,1,2,3,4,5,6,7,8\). Each move swaps the blank with a neighbor. Manhattan distance does **not** treat the blank as a tile.

**Route finding.**

- States: \(\mathrm{In}(\textit{city})\).
- Initial state: \(\mathrm{In}(\mathrm{Boston})\), in the lecture's example.
- Actions: \(\mathrm{Go}(\textit{city})\).
- Transition: \(\mathrm{Result}(\mathrm{In}(\mathrm{Boston}), \mathrm{Go}(\mathrm{New\ York})) = \mathrm{In}(\mathrm{New\ York})\).
- Goal test: \(\mathrm{In}(\mathrm{Denver})\).
- Path cost: kilometers, not the number of cities. That is why BFS is the wrong algorithm.

**8-queens as incremental search.**

- States: any placement of 0 to 8 queens.
- Initial state: empty board.
- Actions: add a queen to an empty square.
- Transition: the updated board.
- Goal test: 8 queens, none attacking another along a row, column, or diagonal.
- Path cost: irrelevant if you only care that a solution exists. Depth is always 8 for a complete placement.

**3-disk Tower of Hanoi (exam review).** Three pegs, three disks. Move one disk at a time. Never place a larger disk on a smaller one. Move the stack from the left peg to the right peg.

1. State: a triple of lists \((P_1, P_2, P_3)\), each list from top to bottom. Example: \(([M, L], [\ ], [S])\).
2. Initial state: \(([S, M, L], [\ ], [\ ])\).
3. There are \(3^3 = 27\) legal states. Each disk sits on one of three pegs, and the order on a peg is forced by size, so every assignment of disks to pegs is exactly one legal state. All 27 are reachable. If you allowed a larger disk on a smaller one, order would matter and there would be 60 arrangements; those extra arrangements are unreachable under the rules.
4. Actions: \(\mathrm{Move}(i, j)\) for \(i \neq j\). Legal only when peg \(i\) is nonempty and peg \(j\) is empty or its top disk is larger than the disk being moved.
5. Transition: pop the front of \(P_i\) and push it onto the front of \(P_j\). Example: \(\mathrm{Result}(([S,M,L],[\ ],[\ ]), \mathrm{Move}(1,3)) = ([M,L],[\ ],[S])\).
6. Goal test: \(([\ ], [\ ], [S, M, L])\).

**Mars rover (Recitation 2).** Leave the lander, collect rocks at three sites in any order, return to the lander. Navigation between sites is a primitive action, and the time between each pair of sites is known.

- State: \(\langle \text{location},\ \text{have rock 1?},\ \text{have rock 2?},\ \text{have rock 3?} \rangle\). Location is one of \(\{\text{lander}, \text{rock1}, \text{rock2}, \text{rock3}\}\). The flags are Boolean. This is enough and not redundant. You do not need the path history inside the state; the search tree stores the path.
- Initial state: \(\langle \text{lander}, \text{no}, \text{no}, \text{no} \rangle\).
- Actions: go to the lander or to one of the three rocks.
- Path cost: sum of travel times.
- Goal test: \(\langle \text{lander}, \text{yes}, \text{yes}, \text{yes} \rangle\).
- Among BFS, DFS, and UCS, use **UCS**, because step costs differ and you want the least time. BFS would minimize the number of legs, not the time.

The pattern: put into the state exactly the facts that future actions and the goal test depend on. Omit facts that never change.

### Real problems the lecture lists

- **Route finding.** Maps, driving directions.
- **Traveling salesperson.** Shortest tour that visits each city once and returns. There are \((n-1)!/2\) distinct tours. For \(n = 30\) that is about \(4.42 \times 10^{30}\).
- **VLSI layout.** Place millions of components so they do not overlap and wiring still fits, minimizing area and delay.
- **Robot navigation.** Route finding with no pre-built roads. The state and action spaces can be infinite (continuous 2D or 3D).
- **Automatic assembly.** Order the operations that build an object. Geometric and expensive.
- **Protein design.** A sequence of amino acids that folds into a useful 3D structure.

### State space vs. search tree

The **state space** is the graph of real configurations and legal moves. The **search tree** (or search graph) is what the algorithm builds.

- Root: initial state.
- Branches: actions.
- Nodes: the results of those actions. Each node has a parent, children, a depth, a path cost, and a state.
- **Expand** a node: generate its children.

The same state can appear many times in the tree, because many paths can reach it. That is why graph search exists.

Three regions of the search space:

1. **Explored** (closed list, visited set). Orange in the lecture figures.
2. **Frontier** (open list, the fringe). Gray.
3. **Unexplored.** White. Failures are drawn black.

Search moves nodes from unexplored to frontier to explored. The **strategy** is the order of that move.

### How to trace any of these algorithms

```
frontier ← a container holding the start node, with g = 0
explored ← empty

while frontier is not empty:
    node ← remove the next node, using this algorithm's rule
    if node is the goal: return the path to node     # test HERE
    add node.state to explored
    for each legal successor:
        if successor is in explored: skip it
        if successor is in frontier with a worse g: decrease its key
        if successor is in neither: insert it
return failure
```

Container by algorithm:

| Algorithm | Remove which node? | Container |
|---|---|---|
| BFS | shallowest (oldest) | FIFO queue |
| DFS | deepest (newest) | LIFO stack |
| DLS | deepest, and do not generate children at depth \(L\) | stack |
| IDS | DFS with limit \(0, 1, 2, \ldots\) | stack, restarted |
| UCS | smallest \(g\) | min-heap on \(g\) |
| A\* | smallest \(f = g + h\) | min-heap on \(f\) |

DFS push order: if the problem says explore Up, Right, Down, Left, push Left, then Down, then Right, then Up. Up is popped first.

When you decrease a key, show the old value and the new value. The exam review writes this as \(G_9 \to 8\).

The lecture's UCS pseudocode calls `decreaseKey` whenever the neighbor is already on the frontier. Do that only when the new path is strictly cheaper. Decreasing to a worse cost is wrong, and the traces in the solutions only update when the cost improves.

### Evaluating a strategy

- **Complete:** if a solution exists, will the algorithm find one?
- **Optimal:** will it find a least-cost solution?
- **Time:** number of nodes generated or expanded.
- **Space:** maximum number of nodes stored.

Measured with:

- \(b\): maximum branching factor (legal actions per state).
- \(d\): depth of the shallowest solution.
- \(m\): maximum depth of the state space. Can be infinite. Sometimes written \(D\).
- \(C^*\): cost of the optimal solution.
- \(\epsilon > 0\): a lower bound on every step cost.

---

## 4. Uninformed search

Uninformed search uses no domain knowledge beyond the formulation. The lecture's list is BFS, DFS, DLS, IDS, and UCS. Greedy best-first and A\* are informed and come next.

### Breadth-first search

Expand the shallowest node. Fringe is a FIFO queue.

- **Complete:** yes, if \(b\) is finite.
- **Time:** \(1 + b + b^2 + \cdots + b^d = O(b^d)\). If the goal test is applied at expansion rather than at generation, the lecture's bound is \(O(b^{d+1})\), because you may generate the next level before you dequeue the goal.
- **Space:** \(O(b^d)\). This is the practical killer. The frontier at depth \(d\) holds on the order of \(b^d\) nodes.
- **Optimal:** yes if every step costs the same. Not optimal if step costs differ. BFS returns the fewest actions, which may be the most expensive path.
- **Why use it at all, given exponential time and memory?** It is complete, it is optimal for unit costs, and it is the right tool when \(d\) is small.

The lecture's table, \(b = 10\), one million nodes per second, 1,000 bytes per node:

| Depth | Nodes | Time | Memory |
|---:|---:|---|---|
| 2 | 110 | 0.11 ms | 107 KB |
| 4 | 11,110 | 11 ms | 10.6 MB |
| 6 | \(10^6\) | 1.1 s | 1 GB |
| 8 | \(10^8\) | 2 min | 103 GB |
| 10 | \(10^{10}\) | 3 hours | 10 TB |
| 12 | \(10^{12}\) | 13 days | 1 PB |
| 14 | \(10^{14}\) | 3.5 years | 99 PB |
| 16 | \(10^{16}\) | 350 years | 10 EB |

At depth 16, DFS stores 156 KB instead of 10 EB. It keeps one path plus the unused siblings along that path, which is \(O(b \cdot m)\) nodes, not \(O(b^m)\).

### Depth-first search

Expand the deepest node. Fringe is a LIFO stack.

- **Complete:** no, in infinite-depth spaces. Yes in finite spaces if you keep an explored set so you cannot loop.
- **Time:** \(O(b^m)\). Bad when \(m \gg d\). If solutions are dense, DFS can be much faster than BFS because it may hit one quickly.
- **Space:** \(O(b \cdot m)\). One path from the root to the current node, plus the unexpanded siblings along that path. The lecture calls this linear space. It is not \(O(b^m)\). The \(m\) is a multiplier, which is why depth 16 costs 156 KB instead of 10 EB.
- **Optimal:** no. The first goal it expands can be buried at depth \(m\).

**A fact the coding FAQ wants you to understand.** With an explored set, a state is pushed at most once. Suppose a short path generates the goal early and pushes it. That goal node sits deep in the stack. DFS then wanders through almost the entire space, refusing to push the goal again because it is already on the frontier. When it finally pops that early goal node, the path it returns is the short one. On the board `1,2,5,3,4,0,6,7,8` with move order Up, Down, Left, Right, DFS returns `['Up', 'Left', 'Left']` at cost 3, after expanding 181,437 nodes. The reachable 8-puzzle has \(9!/2 = 181{,}440\) states. DFS found a 3-move solution and still touched essentially the whole space. Cost of the path and number of nodes expanded measure different things.

Also: BFS can have **max search depth = solution depth + 1**. Nodes queued before the goal at the same depth get expanded first, and expanding them generates one deeper level before the goal is dequeued. On that same board, BFS has search depth 3 and max search depth 4.

### Depth-limited search

DFS that refuses to generate children at depth \(L\). Use it when you know a solution cannot be deeper than \(L\). The lecture's example: any city is reachable from any other in at most \(L < 36\) steps, or whatever bound the map justifies.

- **Complete:** no. If the shallowest solution is deeper than \(L\), DLS returns failure even though a solution exists. The summary table marks it incomplete.
- **Time:** \(O(b^L)\).
- **Space:** \(O(b \cdot L)\). Same shape as DFS space, with the depth cap \(L\) in place of \(m\).
- **Optimal:** no.

### Iterative deepening

Run DLS with limit \(0, 1, 2, \ldots\) until a solution appears or a DLS run reports that the whole finite space was exhausted. Also called depth-first iterative deepening.

Most nodes sit on the bottom level, so regenerating the top is cheap.

**Worked count, \(b = 2\), goal at depth 3.**

| Limit | Nodes generated this iteration | Count |
|---:|---|---:|
| 0 | 1 | 1 |
| 1 | \(1 + b\) | 3 |
| 2 | \(1 + b + b^2\) | 7 |
| 3 | \(1 + b + b^2 + b^3\) | 15 |
| IDS total | | 26 |
| One BFS pass to depth 3 | | 15 |

The root is generated 4 times, the depth-1 nodes 3 times, the depth-2 nodes twice, the leaves once:

\[
4(1) + 3(2) + 2(4) + 1(8) = 26.
\]

Overhead \(26/15 \approx 1.73\). You did not need to know \(d\) in advance.

**The general sum.** The number of node generations is

\[
b^d + 2b^{d-1} + 3b^{d-2} + \cdots + (d+1)
= b^d \left(1 + \frac{2}{b} + \frac{3}{b^2} + \cdots + (d+1)b^{-d}\right)
< b^d \left(1 - \frac{1}{b}\right)^{-2}
\]

for \(b > 1\). The infinite series \(\sum_{k=0}^{\infty} (k+1) x^k = 1/(1-x)^2\) with \(x = 1/b\).

So the overhead factor is bounded by a constant that depends only on \(b\):

\[
C = \left(1 - \frac{1}{b}\right)^{-2}.
\]

| \(b\) | \(C\) | What the \(d = 3\) example actually does |
|---:|---:|---|
| 2 | 4 | ratio 1.73, so the bound is loose but safe |
| 3 | 2.25 | |
| 10 | 1.23 | one-pass 1,111 nodes vs. IDS 1,234, ratio 1.11 |

Larger branching factor, smaller waste. IDS buys BFS's optimality at DFS's memory cost, for a constant-factor time overhead.

**Criteria.**

- **Complete:** yes (finite \(b\)).
- **Time:** \(O(b^d)\).
- **Space:** \(O(b \cdot d)\), the DFS space at the solution depth. Not \(O(b^d)\).
- **Optimal:** yes if every step costs 1. Not optimal for varying step costs. IDS optimizes depth, not path cost.

Korf (1985): among brute-force tree searches, IDS is asymptotically optimal in time, space, and solution length. The lecture's three-part argument:

1. Optimality: IDS finishes every node at depth \(L\) before going deeper, so it returns the shallowest solution. With unit step costs that is a cheapest solution.
2. Space: same as DFS stopped at depth \(d\), so \(O(b \cdot d)\).
3. Time: the series above, \(O(C\, b^d)\) with \(C\) independent of \(d\).

**Exam-review trace.** States are integers. The children of \(a\) are \(2a\) and \(2a+1\), in that order. Start at 1, goal at 11. Goal test on expansion, left child first. IDS visits:

```
(1)
(1, 2, 3)
(1, 2, 4, 5, 3, 6, 7)
(1, 2, 4, 8, 9, 5, 10, 11)
```

Limit 0 expands 1 and does not generate children. Limit 1 expands 1, then 2, then 3. Limit 2 expands 1, 2 (generating 4 and 5), 4, 5, 3 (generating 6 and 7), 6, 7. Limit 3 reaches 11 as the right child of 5. Write the parentheses. They show the iterations, and the question asks for them.

### Uniform-cost search

BFS finds the shallowest path. If edges have different costs, shallowest is not cheapest. UCS expands the frontier node with the smallest path cost \(g(n)\). The fringe is a min-heap on \(g\).

The lecture's map example: BFS from Chicago to Sault Ste. Marie returns Chicago–Duluth–Sault Ste. Marie. UCS returns Chicago–Pittsburgh–Toronto–Sault Ste. Marie, which is shorter in distance.

**How UCS differs from Dijkstra.** Dijkstra computes shortest paths from the source to every reachable node. UCS is the same expansion order but stops when the goal is selected for expansion. UCS also applies to implicitly generated graphs, not only to an explicit adjacency list.

**The lecture's pseudocode, corrected at the one place it is easy to misread.**

```
function UNIFORM-COST-SEARCH(initialState, goalTest):
    frontier = min-heap containing initialState with g = 0
    explored = empty set
    while frontier is not empty:
        state = frontier.deleteMin()      # smallest g
        explored.add(state)
        if goalTest(state): return success
        for neighbor of state:
            if neighbor is in neither frontier nor explored:
                frontier.insert(neighbor)
            else if neighbor is in frontier AND the new path is strictly cheaper:
                frontier.decreaseKey(neighbor)
    return failure
```

The goal test is after the dequeue. That is what makes UCS optimal. The first time the goal is dequeued, every still-frontier node costs at least as much, and every unexplored path would have to pass through one of them.

**Criteria.**

- **Complete:** yes, if some solution has finite cost and every action costs at least \(\epsilon > 0\). A zero-cost cycle can make UCS expand forever.
- **Time and space:** \(O(b^{\lceil C^*/\epsilon \rceil})\). The cheapest solution cannot be deeper than about \(C^*/\epsilon\), because each step costs at least \(\epsilon\). UCS may have to expand every node whose \(g\) is at most \(C^*\), in every direction.
- **Optimal:** yes, for any positive step costs, not only unit costs.
- **The weakness:** no information about where the goal is, so UCS explores uniformly in cost. Contours of equal \(g\) grow in every direction.

### Map orders from the lecture (Las Vegas to Calgary)

You will not be asked to memorize the North American map. You may be asked to produce this kind of list on a small graph. The lecture's results:

- **BFS order:** Las Vegas, Los Angeles, Salt Lake City, El Paso, Phoenix, San Francisco, Denver, Helena, Portland, Dallas, Santa Fe, Kansas City, Omaha, Calgary.
- **DFS order:** Las Vegas, Los Angeles, El Paso, Dallas, Houston, New Orleans, Atlanta, Charleston, Nashville, Saint Louis, Chicago, Duluth, Helena, Calgary.
- **UCS order:** Las Vegas, Los Angeles, Salt Lake City, San Francisco, Phoenix, Denver, Helena, El Paso, Santa Fe, Portland, Seattle, Omaha, Kansas City, Calgary.

Same start and goal, three different visit orders. DFS dives down one road. UCS prefers cheap edges and reaches Calgary without the long eastern DFS detour.

### Summary table (memorize this)

From the lecture, with the conditions written out.

| | Complete? | Optimal? | Time | Space | Fringe |
|---|---|---|---|---|---|
| **BFS** | Yes, if \(b\) is finite | Yes, if step cost is uniform | \(O(b^d)\) | \(O(b^d)\) | FIFO |
| **DFS** | No in infinite spaces; yes in finite spaces with an explored set | No | \(O(b^m)\) | \(O(b \cdot m)\) | LIFO |
| **DLS** | No | No | \(O(b^L)\) | \(O(b \cdot L)\) | LIFO |
| **IDS** | Yes | Yes, if step cost is uniform | \(O(b^d)\) | \(O(b \cdot d)\) | LIFO |
| **UCS** | Yes, if the solution cost is finite and every step costs \(\ge \epsilon > 0\) | Yes, for any step costs | \(O(b^{\lceil C^*/\epsilon \rceil})\) | \(O(b^{\lceil C^*/\epsilon \rceil})\) | heap on \(g\) |

Read the space column carefully. DFS, DLS, and IDS use **linear** space: branching factor times depth. Only BFS and UCS (and A\*) store an exponential frontier. The slides write these as \(O(bm)\) and \(O(bd)\); the dot is there so \(O(b \cdot m)\) is not mistaken for \(O(b^m)\).

The exam-review solution writes A\* time and space as exponential, and writes the same UCS bound. There is no universally best algorithm. The choice depends on the constraints: memory, uniform vs. variable cost, known depth bound, size of \(d\) vs. \(m\).

### A maze trace, done the way the homework wants it

This is the Exam 1 review maze. Cells are \((x, y) = (\text{column}, \text{row})\), row 1 at the bottom. Start \(S = (1,1)\), goal \(G = (3,3)\). Moves only through the openings. The legal neighbor relation, read off the solution, is:

| Cell | Neighbors | Cell | Neighbors | Cell | Neighbors |
|---|---|---|---|---|---|
| (1,1) | (1,2), (2,1) | (2,1) | (1,1), (2,2), (3,1) | (3,1) | (2,1), (3,2) |
| (1,2) | (1,1) only | (2,2) | (2,1), (2,3) | (3,2) | (3,1), (3,3) |
| (1,3) | (2,3) only | (2,3) | (1,3), (2,2) | (3,3) | (3,2) only |

(1,2) and (3,3) are dead ends. There are 9 cells and 8 openings, so the state space is a tree: exactly one simple path between any pair. That fact matters for A\* later.

Successor order for BFS and DFS: Up, Right, Down, Left. No state visited twice. Goal test on expansion.

**BFS** (FIFO). Expand, then the queue from front to back:

| Expand | Queue after |
|---|---|
| (1,1) | (1,2), (2,1) |
| (1,2) | (2,1) |
| (2,1) | (2,2), (3,1) |
| (2,2) | (3,1), (2,3) |
| (3,1) | (2,3), (3,2) |
| (2,3) | (3,2), (1,3) |
| (3,2) | (1,3), (3,3) |
| (1,3) | (3,3) |
| (3,3) | goal |

Visit order: (1,1), (1,2), (2,1), (2,2), (3,1), (2,3), (3,2), (1,3), (3,3).

Path: (1,1) → (2,1) → (3,1) → (3,2) → (3,3), four moves.

If you had tested the goal at generation, you would stop while expanding (3,2) and you would never visit (1,3). The problem's rule visits (1,3).

**DFS** (stack, push Left, Down, Right, Up so that Up is on top):

| Expand | Stack, top at the left |
|---|---|
| (1,1) | (1,2), (2,1) |
| (1,2) | (2,1) |
| (2,1) | (2,2), (3,1) |
| (2,2) | (2,3), (3,1) |
| (2,3) | (1,3), (3,1) |
| (1,3) | (3,1) |
| (3,1) | (3,2) |
| (3,2) | (3,3) |
| (3,3) | goal |

Visit order: (1,1), (1,2), (2,1), (2,2), (2,3), (1,3), (3,1), (3,2), (3,3). Same path, because there is only one path. Pushing Up first instead of last would put Left on top and change the order. Say which convention you used.

**UCS** on the same maze. Up costs 1, right costs 2, down costs 3, left costs 4. Ties: lower \(x\), then lower \(y\).

| Expand | \(g\) | Frontier after |
|---|---:|---|
| (1,1) | 0 | (1,2):1, (2,1):2 |
| (1,2) | 1 | (2,1):2 |
| (2,1) | 2 | (2,2):3, (3,1):4 |
| (2,2) | 3 | (2,3):4, (3,1):4 |
| (2,3) | 4 | (3,1):4, (1,3):8 |
| (3,1) | 4 | (3,2):5, (1,3):8 |
| (3,2) | 5 | (3,3):6, (1,3):8 |
| (3,3) | 6 | goal |

The tie between (2,3) and (3,1), both at \(g = 4\), goes to (2,3) because \(x = 2 < 3\).

Visit order: (1,1), (1,2), (2,1), (2,2), (2,3), (3,1), (3,2), (3,3).

Path: (1,1) right (2,1) right (3,1) up (3,2) up (3,3). Cost \(2+2+1+1 = 6\).

(1,3) is generated at \(g = 8\) and never expanded. The goal enters the frontier when (3,2) is expanded, but it is not expanded until it has the smallest \(g\). That delay is the optimality argument, not an inefficiency to "fix."

---

## 5. Informed search

Blind search is complete and, in the right cases, optimal, but the complexity is exponential because the problems are combinatorial. A **heuristic** \(h(n)\) is an estimate of the cheapest cost from \(n\) to a goal. It comes from domain knowledge. It does not have to be perfect. Human chess players do not search blindly; they use rules of thumb. Those are heuristics.

The lecture uses \(h\) in two ways:

1. \(h(n)\) alone: greedy best-first search. The lecture names it and says it is **not covered**. One sentence so you can recognize it: greedy best-first always expands the frontier node with the smallest \(h\), ignores \(g\), and is not optimal. Do not confuse it with A\*.
2. \(g(n) + h(n)\): A\* search. This is the algorithm you must be able to run and prove things about.

### A\*

A\* was developed for the Shakey robot (Hart, Nilsson, and Raphael, 1968). It expands the node that minimizes the estimated total cost of a solution through that node:

\[
f(n) = g(n) + h(n)
\]

- \(g(n)\): cost of the path found so far from the start to \(n\). This can exceed the optimal cost \(g^*(n)\) if the path is not the best one.
- \(h(n)\): estimated cost from \(n\) to a goal.
- \(f(n)\): estimated cost of the cheapest solution that goes through \(n\).
- \(h^*(n)\): the true cheapest cost from \(n\) to a goal.
- \(C^*\): the true cheapest cost from the start to a goal. \(C^* = h^*(\text{start})\) when \(g(\text{start}) = 0\).

The fringe is a min-heap on \(f\). In this course A\* is always **graph search**: it keeps an explored set and does not re-expand a state. What changes between the two optimality theorems is the shape of the problem, not the code.

**Map example from the lecture.** Start St. Louis, goal Sault Ste. Marie, \(h\) is straight-line distance to the goal. The search expands St. Louis, Chicago, Kansas City, Little Rock, Nashville, Pittsburgh, Toronto, and then Sault Ste. Marie at \(f = 355\). The path is St. Louis → Chicago → Pittsburgh → Toronto → Sault Ste. Marie, cost 355. Nodes already on the frontier with a better \(f\), or already explored, are not re-added. The lecture calls out Nashville at 375, Oklahoma City at 369, and St. Louis at 300 as examples of nodes that are not inserted again.

Straight-line distance is admissible because no road is shorter than the straight line. It is also consistent, by geometry (the triangle inequality).

### Admissibility

\(h\) is **admissible** if it never overestimates:

\[
h(n) \le h^*(n) \quad \text{for every node } n.
\]

It is optimistic. Goals have \(h(G) = 0\), because \(h^*(G) = 0\).

### Consistency (monotonicity)

\(h\) is **consistent** if for every node \(n\) and every successor \(n'\) reached by an action \(a\),

\[
h(n) \le c(n, a, n') + h(n').
\]

The estimate falls no faster than the cost rises. This is the triangle inequality: the direct estimate is no larger than the estimate via a neighbor.

**Consequences you will be asked to use.**

- Along a path, \(f\) does not decrease. If \(n'\) is a successor of \(n\),

\[
f(n') = g(n) + c(n, a, n') + h(n') \ge g(n) + h(n) = f(n).
\]

  A quick check on your own trace: read down the \(f\) column of expanded nodes. If \(h\) is consistent, those values are nondecreasing. A dip means an arithmetic error, or a heuristic that is not consistent.

- Every consistent heuristic with \(h(G) = 0\) at every goal is admissible. Proof below.
- Not every admissible heuristic is consistent. Admissible-but-inconsistent heuristics are rare in practice, but they are a standard exam question, and the lecture gives an explicit counterexample.
- Consistency without \(h(G) = 0\) does **not** imply admissibility. The constant \(h(n) = 5\) satisfies \(5 \le c + 5\) whenever costs are nonnegative, so it is consistent with itself, but \(h(G) = 5 > 0\). The condition \(h(G) = 0\) anchors the estimates to the true costs.

**Proof that consistent and \(h(G) = 0\) implies admissible.** Induction on \(k\), the number of steps on a shortest path from \(n\) to a goal. If several optimal paths exist, use the one with the fewest steps.

- Base \(k = 0\): \(n\) is a goal, so \(h(n) = 0 = h^*(n)\).
- Assume \(h(n') \le h^*(n')\) whenever an optimal path from \(n'\) has at most \(k\) steps.
- Let \(n\) have an optimal path of \(k+1\) steps, and let \(n'\) be the next node on it. The rest is an optimal \(k\)-step path, so \(h^*(n) = c(n, a, n') + h^*(n')\). Then

\[
h(n) \le c(n, a, n') + h(n') \le c(n, a, n') + h^*(n') = h^*(n).
\]

- If no goal is reachable, \(h^*(n) = \infty\) and the inequality is trivial.

### The two optimality theorems, in this course's wording

This is the point the homework solution spends a page on, because it differs from a careless reading of the textbook.

AIMA uses "tree search" for the **algorithm** that keeps no explored set. In this course, "tree" describes the **problem**:

- The state space is a **tree** when each state has exactly one path from the root. Nothing reconverges. Example: 4-queens placing one queen per column from left to right. A state records exactly which columns are filled.
- The state space is a **graph** when two action sequences can reach the same state. Examples: the 8-puzzle, road maps, and tic-tac-toe (X in the corner, O in the center, X in the opposite corner can happen in two orders).

The algorithm in both theorems keeps an explored set.

**Theorem 1.** If the state space is a tree and \(h\) is admissible, A\* is optimal.

Proof, as the lecture builds it. Let \(G_o\) be an optimal goal, \(G_s\) a suboptimal goal, and \(n\) a frontier node that lies on an optimal path to \(G_o\).

- \(f(G_s) = g(G_s)\) and \(f(G_o) = g(G_o)\), because \(h = 0\) at goals.
- \(G_s\) is suboptimal, so \(f(G_s) > f(G_o)\). Call this (1).
- \(h(n) \le h^*(n)\) by admissibility, so

\[
f(n) = g(n) + h(n) \le g(n) + h^*(n) = g(G_o) = f(G_o).
\]

  Call this (2). On a tree, the path to \(n\) is the only path, so \(g(n)\) is already optimal and \(g(n) + h^*(n)\) really is \(g(G_o)\).
- From (1) and (2), \(f(G_s) > f(n)\). A\* expands the smaller \(f\) first, so it expands \(n\) before it would ever select \(G_s\). It never returns the suboptimal goal.

**Theorem 2.** If \(h\) is consistent, A\* is optimal on any state space.

The lecture's chain:

- Consistency makes \(f\) nondecreasing along every path.
- When A\* selects a node for expansion, the path it has is an optimal path to that node. Proof by contradiction, below.
- Therefore A\* expands nodes in nondecreasing order of \(f\).
- At a goal, \(h(G) = 0\), so \(f(G) = g(G)\). Any goal selected later has \(g\) at least as large.
- The first goal selected for expansion is optimal.

**Why the explored set needs consistency.** Suppose a state is first reached by an expensive path and expanded. A cheaper path found later is discarded, because the state is already explored. If that first path was not the cheapest, A\* can return a suboptimal solution. UCS does not have this bug because it expands in order of \(g\), and \(g\) only grows. A\* expands in order of \(f = g + h\), and \(h\) can drop. Consistency is what stops \(f\) from dropping, which restores "the first time you expand a node, you have the best path to it."

**Proof that the first expansion has optimal \(g\), when \(h\) is consistent.** Suppose, for a contradiction, that \(n'\) is the first node A\* expands with \(g(n') > g^*(n')\). Walk backward along an optimal path from the start to \(n'\), and let \(n\) be the first node on that path that has not yet been explored. Its predecessor was explored at optimal cost (every earlier expansion was optimal), so \(n\) is on the frontier with \(g(n) = g^*(n)\). Also \(n \ne n'\), because \(g(n') > g^*(n')\). Consistency along the optimal path from \(n\) to \(n'\) gives \(h(n) \le \mathrm{cost}(n \to n') + h(n')\), so

\[
f(n) = g^*(n) + h(n) \le g^*(n') + h(n') < g(n') + h(n') = f(n').
\]

A\* would have selected \(n\) before \(n'\). Contradiction.

### The lecture's counterexample: admissible, inconsistent, and graph-search A\* fails

Edges and heuristic:

- \(S \xrightarrow{4} A \xrightarrow{4} G\)
- \(S \xrightarrow{2} B \xrightarrow{1} A\)
- \(h(S) = 7,\ h(A) = 1,\ h(B) = 5,\ h(G) = 0\)

Check admissibility against the true costs.

- \(h^*(G) = 0\)
- \(h^*(A) = 4\), and \(1 \le 4\)
- \(h^*(B) = 1 + 4 = 5\), and \(5 \le 5\)
- \(h^*(S) = \min(4+4,\ 2+1+4) = \min(8, 7) = 7\), and \(7 \le 7\)

Admissible. Now consistency:

\[
h(S) = 7 > 4 + h(A) = 5, \qquad h(B) = 5 > 1 + h(A) = 2.
\]

Inconsistent on both of those edges.

Run graph-search A\*. \(f(A) = 4 + 1 = 5\), \(f(B) = 2 + 5 = 7\). Expand A first, with \(g(A) = 4\). Later B reaches A with \(g = 3\), which is better, but A is already explored, so the path is discarded. A\* returns \(S \to A \to G\) at cost 8. The optimal path is \(S \to B \to A \to G\) at cost 7.

**Consistency is sufficient for optimality of graph-search A\*, not necessary.** The homework graph shows a case where the heuristic is inconsistent and A\* still returns the optimal path, because every node happened to be reached by its cheapest path before it was expanded. Losing consistency means losing the **guarantee**, not guaranteeing failure. The standard repair, if you must use an inconsistent heuristic with an explored set, is to reopen a closed node when a cheaper path appears.

### A\* complexity

Depends on the heuristic. Pearl: "There is an A\* with each particular heuristic."

- **Complete:** yes, if \(b\) is finite and every step costs at least \(\epsilon > 0\).
- **Time:** exponential in the worst case. With \(h = 0\), A\* is UCS. A good heuristic reduces \(b\) to an effective branching factor \(b^* \ll b\), but \((b^*)^d\) is still exponential. Time is polynomial only if the absolute error \(|h(n) - h^*(n)|\) grows no faster than \(O(\log h^*(n))\).
- **Space:** every node stays in memory. This is the biggest practical problem, and it is exponential.
- **Optimal:** yes, under Theorem 1 or Theorem 2.

With a consistent heuristic, A\* expands every node with \(f(n) < C^*\), and it may expand some nodes with \(f(n) = C^*\). It never expands a node with \(f(n) > C^*\). A more accurate admissible heuristic, one that is larger at every node, therefore expands no more nodes.

### Heuristics for the 8-puzzle

The lecture's start state has a solution 26 steps long.

- \(h_1(n)\) = number of misplaced tiles. \(h_1(S) = 8\).
- \(h_2(n)\) = total Manhattan distance: sum over tiles of \(|\Delta x| + |\Delta y|\). The lecture computes \(h_2(S) = 3+1+2+2+2+3+3+2 = 18\), and \(18 \le 26\), so it does not overestimate on that state. The blank is not a tile.

**Dominance.** If \(h_1\) and \(h_2\) are both admissible and \(h_1(n) \le h_2(n)\) for every \(n\), then \(h_2\) **dominates** \(h_1\), and A\* with \(h_2\) never expands more nodes than A\* with \(h_1\). Manhattan dominates misplaced tiles: a tile that is misplaced contributes at least 1 to Manhattan, and a tile that is home contributes 0 to both.

Should you always want the most accurate heuristic? Not necessarily. There is a tradeoff between how accurate \(h\) is and how expensive it is to compute. A perfect heuristic that requires solving the original problem is useless.

**Relaxed problems, the systematic way to build an admissible heuristic.** Drop constraints on the actions. The optimal cost in the easier problem is a lower bound on the optimal cost in the original problem, so it is admissible.

- Allow a tile to move to any square, occupied or not: optimal cost is exactly \(h_1\), the number of tiles not already home.
- Allow a tile to move to any adjacent square, even if it is occupied: optimal cost is exactly \(h_2\), Manhattan distance.

You underestimated the real cost. That is the right direction for the error.

### The four small-graph heuristics (recitation and exam review)

Graph: \(S \xrightarrow{1} B \xrightarrow{3} G\), and a direct edge \(S \xrightarrow{5} G\).

True costs: \(h^*(G) = 0\), \(h^*(B) = 3\), \(h^*(S) = \min(5,\ 1+3) = 4\).

| | \(h(S)\) | \(h(B)\) | \(h(G)\) | Admissible? | Consistent? |
|---|---:|---:|---:|---|---|
| \(h_1\) | 4 | 2 | 0 | Yes | No, fails \(S \to B\): \(4 \le 1+2\)? |
| \(h_2\) | 6 | 3 | 0 | No, \(6 > 4\) at \(S\) | No |
| \(h_3\) | 3 | 2 | 0 | Yes | Yes, check all three edges |
| \(h_4\) | 4 | 1 | 0 | Yes | No, fails \(S \to B\): \(4 \le 1+1\)? |

Consistency check for \(h_3\), which is the one you must not wave through:

| Edge | \(h(n)\) | \(c + h(n')\) | Holds? |
|---|---:|---:|---|
| \(S \to B\) | 3 | \(1+2 = 3\) | \(3 \le 3\) |
| \(S \to G\) | 3 | \(5+0 = 5\) | \(3 \le 5\) |
| \(B \to G\) | 2 | \(3+0 = 3\) | \(2 \le 3\) |

Check **every** edge. Do not stop at the first success, and do not stop at the first failure if the question asks for the full table. On a directed graph, check each edge in the given direction only.

Among the admissible ones:

- \(h_1\) dominates \(h_3\): \(4 \ge 3\), \(2 \ge 2\), \(0 \ge 0\).
- \(h_1\) dominates \(h_4\): \(4 \ge 4\), \(2 \ge 1\), \(0 \ge 0\).
- \(h_3\) and \(h_4\) are **incomparable**: \(h_3(S) = 3 < 4 = h_4(S)\), but \(h_3(B) = 2 > 1 = h_4(B)\). Dominance is a partial order. You cannot always rank two admissible heuristics.

\(h_1\) is exactly the pointwise maximum of \(h_3\) and \(h_4\). That is the standard way to combine heuristics, and it is why the max dominates both.

Two different axes, which the recitation solution separates on purpose:

- **Dominance** is about efficiency: how many nodes get expanded.
- **Consistency** is about correctness of graph search.

On a tree, run A\* with \(h_1\), the dominant admissible heuristic. On a graph, \(h_1\) is not consistent, so either use the consistent \(h_3\), or use \(h_1\) and reopen closed nodes. The perfect heuristic on this graph is \(h^* = (4, 3, 0)\). \(h_1\) is exact at \(S\); the only slack left is at \(B\).

### Another directed graph (recitation)

Edges: \(A \xrightarrow{2} B\), \(A \xrightarrow{5} C\), \(B \xrightarrow{2} C\), \(B \xrightarrow{6} G\), \(C \xrightarrow{2} G\). Start A, goal G.

True costs:

- \(h^*(C) = 2\)
- \(h^*(B) = \min(2+2,\ 6) = 4\)
- \(h^*(A) = \min(2+4,\ 5+2) = 6\)
- \(h^*(G) = 0\)

Given \(h(A)=5,\ h(B)=4,\ h(C)=2,\ h(G)=0\):

- Admissible: \(5 \le 6\), \(4 \le 4\), \(2 \le 2\), \(0 \le 0\).
- Consistent: all five edges hold, with equality on \(B \to C\) (\(4 \le 2+2\)) and on \(C \to G\) (\(2 \le 2+0\)).

Now set \(h(C) = 0\), leaving the others unchanged.

- Still admissible. Lowering a heuristic value cannot break admissibility. \(h^*\) does not change.
- Not consistent. The single violation is \(B \to C\): \(h(B) = 4 > 2 + 0\). The estimate drops by 4 across an edge of cost 2. The other four edges still hold. If the question says check every edge, write all five rows.

### Homework graph: admissible, inconsistent, and A\* still optimal

Edges: \(S \xrightarrow{1} A\), \(S \xrightarrow{1} B\), \(A \xrightarrow{1} C\), \(A \xrightarrow{1} B\), \(B \xrightarrow{100} G\), \(C \xrightarrow{1} G\).

Heuristic: \(h(S)=3,\ h(A)=2,\ h(B)=1,\ h(C)=1,\ h(G)=0\).

Optimal costs:

- \(h^*(S) = 3\) via \(S \to A \to C \to G\). The other routes cost 102 and 101.
- \(h^*(A) = 2\) via \(A \to C \to G\).
- \(h^*(B) = 100\).
- \(h^*(C) = 1\).
- \(h^*(G) = 0\).

Admissible at all five nodes. Tight everywhere except \(B\), where 1 is far below 100.

Consistency fails on exactly one edge: \(S \to B\), because \(3 > 1 + 1\). Equivalently, \(f\) decreases: \(f(S) = 3\) but \(f(B) = 1+1 = 2\). The other five edges hold.

A\* graph search:

- Expand \(S\) (\(f = 3\)). Generate \(A\) at \(g = 1,\ f = 3\), and \(B\) at \(g = 1,\ f = 2\).
- Expand \(B\) (\(f = 2\)). Generate \(G\) at \(g = 101,\ f = 101\).
- Expand \(A\) (\(f = 3\)). Generate \(C\) at \(g = 2,\ f = 3\). The path \(A \to B\) has \(g = 2\), which is worse than the \(g = 1\) already used, and \(B\) is explored.
- Expand \(C\) (\(f = 3\)). Generate \(G\) at \(g = 3,\ f = 3\).
- Expand \(G\) at \(f = 3\). Return \(S \to A \to C \to G\), cost 3, which is optimal.

The bug that inconsistency can cause did not happen: \(S, A, B, C\) were each expanded on an optimal path. Quote this distinction if you are asked what inconsistency implies.

### Combining heuristics (homework and exam review)

Let \(h_1\) and \(h_2\) be admissible. Let \(h^*\) be the true cost.

**(a)** \(h = \min(h_1, h_2)\) is admissible.

\[
h(n) = \min(h_1(n), h_2(n)) \le h_1(n) \le h^*(n).
\]

It is admissible and weakly informed. It is dominated by each of \(h_1\) and \(h_2\).

**(b)** \(h = \max(h_1, h_2)\) is admissible.

Both are \(\le h^*(n)\), so their maximum is \(\le h^*(n)\). This is the one to prefer. It dominates both inputs.

**(c)** \(h = w h_1 + (1-w) h_2\) for \(0 \le w \le 1\) is admissible.

\[
h(n) \le w\, h^*(n) + (1-w)\, h^*(n) = h^*(n),
\]

because both weights are nonnegative. Any convex combination of admissible heuristics is admissible.

**(d)** \(h = h_1 + h_2\) is **not** admissible in general.

Counterexample: take \(h_1 = h_2 = h^*\). Then \(h = 2h^*\), which overestimates wherever \(h^* > 0\). Concretely, an 8-puzzle state with \(h^* = 26\) and Manhattan distance 18 gives a sum of 36, and \(36 > 26\).

The sum is admissible only in special cases, for example when the two heuristics count disjoint sets of moves that both have to happen (a tile's horizontal distance plus its vertical distance, which is exactly Manhattan, not "two copies of Manhattan").

**Consistency of the max.** If \(h_1\) and \(h_2\) are consistent, then \(h = \max(h_1, h_2)\) is consistent. For any edge \(n \to n'\) of cost \(c\), and for \(i = 1, 2\),

\[
h_i(n) \le c + h_i(n') \le c + h(n'),
\]

the second step because \(h_i(n') \le \max(h_1(n'), h_2(n'))\). Both inputs are \(\le c + h(n')\), so their max is too.

**The sum of two consistent heuristics need not be consistent.** One edge \(S \to G\) of cost 1, with \(h_1 = h_2 = h^*\). Then \(h(S) = 2\) and \(c + h(G) = 1\), and \(2 > 1\).

The max of two consistent heuristics is always consistent. In the four-heuristic example, \(h_4\) is inconsistent, which is why \(\max(h_3, h_4) = h_1\) came out inconsistent.

### Weighted Manhattan, and a heuristic that is not a heuristic

**Recitation 3.** Let \(d_i\) be the Manhattan distance of tile \(i\), and let \(0 \le \alpha_i \le 1\). Is \(h = \sum_{i=1}^{8} \alpha_i d_i\) admissible?

Yes. Each move relocates exactly one tile by exactly one square, so it reduces \(\sum d_i\) by at most 1. Reaching the goal requires driving that sum to 0, so \(\sum d_i \le h^*\). Manhattan is admissible. Because \(0 \le \alpha_i \le 1\), each term \(\alpha_i d_i \le d_i\), and

\[
h = \sum \alpha_i d_i \le \sum d_i \le h^*.
\]

Shrinking an admissible heuristic keeps it admissible. You should still not use it. Manhattan dominates it for every choice of the weights, and you pay extra multiplications to get a worse estimate. The only sensible choice is \(\alpha_i = 1\) for every \(i\). Setting every \(\alpha_i = 0\) gives \(h \equiv 0\), which is admissible and turns A\* back into UCS. Admissibility is a floor, not a goal.

**Is \(h(n) = 8 - g(n)\) admissible?** No.

- If the start is one move from the goal, \(h(\text{start}) = 8\) and \(h^* = 1\).
- Once \(g > 8\), this is negative. A heuristic that estimates a cost should not be negative. (A negative admissible heuristic is still a lower bound, but this one is not admissible, and the negativity is a symptom that it is not estimating a remaining cost.)
- More fundamentally, \(g(n)\) depends on the path, not only on the state. The same board could receive two different \(h\) values. A heuristic must be a function of the state.

### 8-puzzle state space, including the parity argument

A crude upper bound: 9 positions and 9 things to place (8 tiles and a blank), so at most \(9! = 362{,}880\) boards.

Not all of them are reachable. Write the board as a list in row-major order and ignore the blank. An **inversion** is a pair of tiles that appear in the wrong order relative to the goal. The goal \(0,1,2,3,4,5,6,7,8\) has zero inversions. The board \(0,1,2,3,4,5,6,8,7\) has one inversion, the pair \((8,7)\), and is unsolvable.

Classify moves, for a board of width \(w = 3\):

- A **row move** swaps the blank with a horizontal neighbor. In the list, the blank swaps with an adjacent entry. The blank is not counted, so the order of the tiles does not change. Inversions stay the same.
- A **column move** swaps the blank with the entry \(w\) positions away. The moved tile jumps over \(w - 1 = 2\) other tiles. Each jump changes the inversion count by \(\pm 1\), so the total change is a sum of two terms of \(\pm 1\): it changes by \(+2\), by \(-2\), or by 0. The parity of the inversion count is preserved.

The goal has even parity (zero). A state is solvable only if it has even parity. The 1879 paper cited in the recitation shows the converse as well: every even-inversion board is reachable. Exactly half of the \(9!\) boards are reachable:

\[
\frac{9!}{2} = 181{,}440.
\]

**This rule is for odd width.** A column move jumps over \(w-1\) tiles, and \(w-1\) is even precisely when \(w\) is odd. For even width (the \(2 \times 2\) puzzle, the \(4 \times 4\) 15-puzzle), a column move flips the parity, and the correct test also uses the row of the blank. Do not apply the 8-puzzle rule to a 15-puzzle. The recitation gives a concrete warning: on a \(2 \times 2\) board with goal \(0\ 1\ /\ 2\ 3\), the state \(1\ 3\ /\ 2\ 0\) has one inversion and **is** solvable, while \(1\ 2\ /\ 3\ 0\) has zero inversions and is **not**.

Some references put the blank last in the goal (\(1,2,3,4,5,6,7,8,0\)). Both goals have zero inversions, so the parity rule is the same. Be consistent about which goal you count against.

### A\* on the review maze, and why the heuristic's quality shows up in the tie break

Manhattan distance to \((3,3)\), ignoring walls: \(h(x,y) = |x-3| + |y-3|\).

| | \(x=1\) | \(x=2\) | \(x=3\) |
|---|---:|---:|---:|
| \(y=3\) | 2 | 1 | 0 |
| \(y=2\) | 3 | 2 | 1 |
| \(y=1\) | 4 | 3 | 2 |

With the same move costs as UCS (up 1, right 2, down 3, left 4), A\* expands in the same order as UCS and returns the same path at cost 6. Manhattan counts moves, not costs, so it is a weak heuristic here: every early node has \(f < C^* = 6\), and A\* cannot skip them. It is still admissible, because every move costs at least 1 and you need at least the Manhattan number of moves.

A better heuristic uses the actual costs. Reaching \((3,3)\) from \((x,y)\) takes at least \(3-x\) right moves at cost 2 and at least \(3-y\) up moves at cost 1:

\[
h'(x,y) = 2(3-x) + (3-y).
\]

This is admissible: any real path costs at least that. It is also consistent. Values: \(h'(1,1)=6\), \(h'(1,2)=5\), \(h'(1,3)=4\), \(h'(2,1)=4\), \(h'(2,2)=3\), \(h'(2,3)=2\), \(h'(3,1)=2\), \(h'(3,2)=1\), \(h'(3,3)=0\).

On this maze every node except (1,3) has \(f = 6 = C^*\). Every choice is a tie.

- Break ties by smaller \(h'\), then lower \(x\): expand (1,1), (2,1), (3,1), (3,2), (3,3). **Five** expansions.
- Break ties by lower \(x\), as in the UCS rule: expand eight nodes, the same set as UCS.

With a very good heuristic, the tie-break decides how much work you save. The solution path does not change. There is only one path.

Because the state space is a tree, Theorem 1 already guarantees optimality from admissibility alone. Consistency is not required on this maze. There is no second path for the explored set to discard. The explored set is still doing something: without it, the agent can walk back and forth and the search tree is infinite even though the state space is a tree. That is exactly the course's distinction between the algorithm (which keeps an explored set) and the problem (which happens to be a tree).

### Another directed-graph trace, so you can see a key decrease

The exam review's graph is directed, start \(S\), goal \(Z\), lexicographic tie breaks, full explored set. The solution's priority queues are enough to learn the pattern even without the drawing:

**UCS**, subscripts are \(g\):

```
[S:0]
[B:2, C:3, F:4]
[C:3, F:4, E:6]
[F:4, D:5, E:6]
[D:5, E:6]
[E:6, G:9]
[G:9 → 8, Z:14]
[Z:14 → 13]
```

Visit order: \(S, B, C, F, D, E, G, Z\). Path: \(S - B - E - G - Z\), cost 13. The visit order and the path are different. \(G\) and \(Z\) have their keys decreased when a cheaper path is found while they are still on the frontier.

**A\***, subscripts are \(f = g + h\), with \(h(S)=8, h(B)=7, h(C)=6, h(D)=5, h(E)=4, h(F)=5, h(G)=2, h(Z)=0\):

```
[S:8]
[B:9, C:9, F:9]
[C:9, F:9, E:10]
[F:9, D:10, E:10]
[D:10, E:10]
[E:10, G:11]
[G:11 → 10, Z:14]
[Z:14 → 13]
```

Same visit order, same path, same cost. The question asks why A\* expanded the same nodes as UCS. The heuristic is consistent, and A\* expands every node with \(f(n) < C^* = 13\). Every non-goal node has \(f \le 11\), so none of them can be skipped.

---

## 6. Local search

### When systematic search is the wrong tool

BFS, DFS, and A\* keep a frontier and return a **path**. That is the right output when the environment is observable, deterministic, and known, and when the solution is the sequence of actions.

Local search is the alternative when:

- the state space has \(10^{10}\) to \(10^{100}\) states,
- the path does not matter, only the configuration (8-queens, a TSP tour, a schedule),
- or memory cannot hold the frontier.

The idea: keep a single current state, or a small population, and move to neighbors. Advantages, which are also the three the recitation wants:

1. No search tree.
2. Very little memory. Hill climbing and simulated annealing store \(O(1)\) states. A genetic algorithm stores a population.
3. Often finds a good enough solution in continuous or enormous spaces.

Disadvantages:

1. **Not complete.** The search stops at a local optimum or a plateau and may return a state that is not a solution. Hill climbing solves 8-queens from a random start only about 14% of the time.
2. **Not optimal.** No guarantee that the returned state is the best.
3. Sensitive to the initial state and to parameters: cooling schedule, population size, mutation rate.

### The landscape

Think of the state space as a surface whose height is an objective \(f(s)\).

- **Global optimum:** \(s^* = \arg\max_s f(s)\). The target. If you are minimizing a cost, flip the picture and talk about a global minimum.
- **Local maximum:** \(f(s) \ge f(s')\) for every neighbor \(s'\). A trap.
- **Plateau:** \(f(s) = f(s')\) for every neighbor. The search stagnates because no neighbor looks better.

The lecture's line for hill climbing, from Russell and Norvig: trying to find the top of Everest in a thick fog with amnesia.

### Hill climbing

Always move to the best neighbor. Stop when no neighbor is strictly better.

```
current ← initial state
loop:
    neighbor ← the neighbor with the largest f
    if f(neighbor) ≤ f(current): return current
    current ← neighbor
```

- Selection: \(\text{next} = \arg\max_{s' \in N(s)} f(s')\).
- Monotone: \(f(s_{t+1}) \ge f(s_t)\).
- Time: \(O(k \cdot |N(s)|)\), where \(k\) is the number of steps until a local optimum.
- Space: \(O(1)\).
- Complete: no. Optimal: no.

**8-queens, one queen per column, minimize the number of attacking pairs.**

- State space: \(8^8 \approx 1.68 \times 10^7\).
- Neighborhood: 8 queens times 7 other rows, so 56 successors.
- From a random start, \(h\) often falls from 17 to 1 in about 5 steps, then gets stuck at a local minimum with \(h = 1\).
- Success rate about 14%. Average run length about 4 steps.
- Fast, and frequently not a solution.

**Variants.**

- **Sideways moves.** Allow a move with \(f(\text{next}) = f(\text{current})\), so you can cross a plateau or a shoulder. Limit them, for example to 100, or you can loop forever on a flat region.
- **Random-restart hill climbing.** Run many times from random starts and keep the best result. If each trial succeeds with probability \(p\), the expected number of trials is \(1/p\). For 8-queens, \(p \approx 0.14\), so about 7 restarts. This is often the practical fix.
- **Stochastic hill climbing.** Among the improving moves, pick one at random, with probability proportional to how much it improves. Slower than steepest ascent, and it explores good regions more thoroughly.

### Which operator is which, on 6-queens

State: one queen per column. A successor moves a single queen to a different row of its own column. \(\mathrm{Eval}(S)\) is the number of **non-attacking** pairs, so you maximize it.

- Number of states: \(6^6 = 46{,}656\). Each of 6 columns independently chooses one of 6 rows. Without the one-per-column restriction the space would be \(\binom{36}{6} = 1{,}947{,}792\), about 40 times larger. The representation is doing work.
- Successors of a state: \(6 \times 5 = 30\). Six queens, five other squares in that column. The current square does not count.
- Total pairs: \(\binom{6}{2} = 15\). Two queens attack if they share a row or a diagonal \(|r_i - r_j| = |i - j|\). They cannot share a column in this encoding. \(\mathrm{Eval} = 15 - (\text{number of attacking pairs})\).
- The successor function changes exactly one digit of the 6-digit string. That operator is **mutation**. Selection does not modify a state. Crossover copies a block from each of two parents and changes several positions at once, so it is not this successor function.

The same encoding for 8-queens in the genetic-algorithm lecture: a string of 8 digits, each in \(\{1,\ldots,8\}\), position \(i\) is the row of the queen in column \(i\). Example: \([3,2,7,4,8,5,5,2]\). Repeats are legal. Two queens in the same row is a bad state, not an invalid chromosome. Because this is not a permutation encoding, ordinary crossover always produces a legal board.

Fitness: \(\binom{8}{2} - \text{conflicts} = 28 - \text{conflicts}\). A perfect board scores 28.

### Simulated annealing

Inspired by cooling metal. Heat gives atoms the energy to move. Slow cooling lets them settle into a strong crystal, which is a global energy minimum. Fast cooling freezes in defects, which are local minima.

- **Temperature** \(T\): how willing you are to accept a worse move. High \(T\) accepts many worse moves. Low \(T\) almost never does.
- **Energy** \(E\): the cost you are minimizing.
- **Schedule** \(T(t)\): how temperature falls.
- **Metropolis criterion** (Metropolis et al., 1953):

\[
P(\text{accept}) =
\begin{cases}
1 & \text{if } \Delta E \le 0 \\
e^{-\Delta E / T} & \text{if } \Delta E > 0
\end{cases}
\]

where \(\Delta E = E(\text{next}) - E(\text{current})\). A better or equal neighbor is always accepted. A worse neighbor is accepted with a probability that is larger when the worsening is small and when \(T\) is large.

The lecture's algorithm:

```
current ← initial
for t = 1, 2, 3, ...:
    T ← schedule(t)
    if T = 0: return current
    next ← a random successor of current
    ΔE ← E(next) - E(current)
    if ΔE ≤ 0 or random(0, 1) < exp(-ΔE / T):
        current ← next
```

**Sign convention, which the recitation deliberately flips.** The lecture minimizes energy, so \(\Delta E > 0\) means worse. If the objective is a score you maximize, such as non-attacking pairs, then a worse neighbor has \(\Delta \mathrm{Eval} = \mathrm{Eval}(\text{current}) - \mathrm{Eval}(\text{neighbor}) > 0\), and the acceptance probability is \(e^{-\Delta \mathrm{Eval}/T}\). Either way the exponent is negative for a worsening move. Read the problem to see whether \(h\) is a cost or a score.

**Schedules.**

- Logarithmic: \(T(t) \ge c / \log(1+t)\). Geman and Geman (1984): this converges to the global optimum with probability 1. Too slow to use.
- Exponential, what people actually use: \(T(t) = T_0 \alpha^t\) with \(\alpha \in [0.8,\ 0.99]\). No guarantee. It works.
- Linear: \(T(t) = T_0 - \beta t\). Simple, and it hits zero.

At high temperature the algorithm makes large, often worsening, changes. As it cools, it behaves more like hill climbing and settles.

Boltzmann distribution, for context: at temperature \(T\), \(P(s) \propto e^{-E(s)/T}\). The normalizing constant is the partition function \(Z(T) = \sum_{s'} e^{-E(s')/T}\). You do not need to compute \(Z\) to run the algorithm. The Metropolis rule samples from this distribution without computing it.

**TSP numbers from the lecture.** \((n-1)!/2\) tours, about \(4.42 \times 10^{30}\) for 30 cities. Annealing typically gets within about 2–5% of optimal on moderate instances.

### Worked annealing problem (exam review)

Cities: \(A(0,0),\ B(2,2),\ C(3,1),\ D(5,3)\). Tours start and end at A. Minimize total Euclidean length.

Distances you should be able to recompute:

| Pair | Distance |
|---|---|
| \(A\)–\(B\) | \(\sqrt{8} = 2\sqrt{2} \approx 2.828\) |
| \(A\)–\(C\) | \(\sqrt{10} \approx 3.162\) |
| \(A\)–\(D\) | \(\sqrt{34} \approx 5.831\) |
| \(B\)–\(C\) | \(\sqrt{2} \approx 1.414\) |
| \(B\)–\(D\) | \(\sqrt{10} \approx 3.162\) |
| \(C\)–\(D\) | \(2\sqrt{2} \approx 2.828\) |

All tours starting at A. Reversals are the same tour, so there are \((4-1)!/2 = 3\) distinct tours, and 6 permutations:

| Tour | Length | |
|---|---:|---|
| ABCD = A-B-C-D-A | \(2\sqrt{2}+\sqrt{2}+2\sqrt{2}+\sqrt{34}\) | 12.902 |
| ABDC = A-B-D-C-A | \(2\sqrt{2}+\sqrt{10}+2\sqrt{2}+\sqrt{10}\) | **11.981** |
| ACBD = A-C-B-D-A | \(\sqrt{10}+\sqrt{2}+\sqrt{10}+\sqrt{34}\) | 13.570 |
| ACDB = A-C-D-B-A | \(\sqrt{10}+2\sqrt{2}+\sqrt{10}+2\sqrt{2}\) | **11.981** |
| ADBC | same tour as ACBD reversed | 13.570 |
| ADCB | same tour as ABCD reversed | 12.902 |

Optimal: ABDC, or ACDB in reverse, 11.981.

Annealing setup. \(T_1 = 1\), start at ABCD, cool by \(\alpha = 0.9\) after each iteration. A neighbor swaps two of \(\{B, C, D\}\). \(\Delta h = \text{distance}_{\text{next}} - \text{distance}_{\text{current}}\). Accept if \(\Delta h \le 0\), otherwise if \(u < e^{-\Delta h / T}\).

The table they give: \(e^{-0.6} \approx 0.549\), \(e^{-0.7} \approx 0.497\), \(e^{-1.1} \approx 0.333\), \(e^{-1.2} \approx 0.301\). For \(x < 0.1\), \(e^{-x} \approx 1 - x\).

**Iteration 1.** \(T = 1\). Swap B and C: ABCD (12.902) → ACBD (13.570). \(\Delta h = 0.668 > 0\). \(e^{-0.668}\) sits between \(e^{-0.6}\) and \(e^{-0.7}\), about 0.51. The draw is \(u = 0.35 < 0.51\), so **accept**. Current is ACBD. Then \(T_2 = 0.9\).

**Iteration 2.** \(T = 0.9\). Swap B and D: ACBD (13.570) → ACDB (11.981). \(\Delta h = -1.589 \le 0\), so **accept** with no random draw. Current is ACDB. Then \(T_3 = 0.81\).

**Iteration 3.** \(T = 0.81\). Swap C and D: ACDB (11.981) → ADCB (12.902). \(\Delta h = 0.921\). \(0.921 / 0.81 = 1.137\), and \(e^{-1.137}\) sits between \(e^{-1.1}\) and \(e^{-1.2}\), about 0.32. The draw is \(u = 0.62 > 0.32\), so **reject**. Current stays ACDB.

Final tour ACDB, length 11.981, which is optimal.

**Same iteration 3 at a high temperature.** If \(T_1 = 100\), then \(T_3 = 100 \times 0.9^2 = 81\). \(e^{-0.921/81} \approx 0.989\), and \(0.62 < 0.989\), so the worse move is accepted and the search leaves the optimum. \(T\) should be on the scale of the \(\Delta h\) values you actually see (here about 0.7 to 1.6). At \(T = 100\), almost every move is accepted and annealing is a random walk.

### Genetic algorithms

A population of \(k\) candidate solutions. Inspired by selection, not a model of biology you need to defend.

- **Population:** \(k\) individuals.
- **Genotype / chromosome:** a string encoding of a solution.
- **Fitness:** \(f\) from genotypes to positive reals. Higher is better.
- **Selection:** individuals are chosen to reproduce with probability proportional to fitness,

\[
P(S_i) = \frac{f(S_i)}{\sum_j f(S_j)}.
\]

  Weak individuals still have a nonzero chance. That maintains diversity. Selection pressure is a bias, not a deletion.
- **Crossover:** cut two parents and swap the tails. The 8-queens lecture cuts after position 4:

  - Parent 1: \([2,4,7,4 \mid 8,5,5,2]\)
  - Parent 2: \([3,2,7,5 \mid 2,4,1,1]\)
  - Children: \([2,4,7,4,2,4,1,1]\) and \([3,2,7,5,8,5,5,2]\)

  Both are legal boards. Each can inherit a good block of columns from one parent.
- **Mutation:** randomly change a small part of a child, so the population cannot get stuck with every individual sharing the same gene.

Crossover helps only when good solutions are built from good pieces that can be recombined. A problem with no such structure gains nothing from crossover. Mutation is the exploration operator. Selection is the exploitation operator.

**Worked selection.** Fitnesses 9, 12, 11, 8. Total 40.

| State | Fitness | Probability |
|---|---:|---:|
| \(S_1\) | 9 | \(9/40 = 0.225\) |
| \(S_2\) | 12 | \(12/40 = 0.300\) |
| \(S_3\) | 11 | \(11/40 = 0.275\) |
| \(S_4\) | 8 | \(8/40 = 0.200\) |

They sum to 1. The worst board still has a 20% chance.

**Worked fitness on bit strings.** Length 16. Fitness is the number of positions \(i\) with \(\mathrm{bit}_i = \mathrm{bit}_{17-i}\). A palindrome scores 16. There are 8 mirror pairs, and a pair contributes 2 or 0, so fitness is always even.

- \(0000000011111111\) scores 0. Every pair is 0 facing 1.
- \(1100110110110011\) scores 16. It equals its reverse.
- \(0100000011111111\) scores 2. Only positions 2 and 15 agree.

An optimal string scores 16. The first 8 bits are free and the last 8 are determined by reflection, so there are \(2^8 = 256\) optimal strings out of \(2^{16} = 65{,}536\). One string in 256 is optimal. The state space itself is \(2^{16}\), because each bit is free before you score it.

**What to expect from a GA.** Convergence can be slow or fast depending on the problem. Crossover and mutation can escape local optima. Parameters need tuning. Applications named in lecture: VLSI, neural-net training, scheduling, game playing, hyperparameter search.

**Hill climbing vs. genetic algorithms, the comparison the recitation asks for.**

| | Hill climbing | Genetic algorithm |
|---|---|---|
| What it stores | One state | A population of \(N\) states |
| Move | Deterministic, best neighbor | Stochastic: selection, crossover, mutation |
| Memory | \(O(1)\) | \(O(N)\) |
| Local optima | Stops at the first one | Can leave, via crossover and mutation |
| Complete / optimal | Neither | Neither |

### Local beam search

Named in the lecture, not given its own slide of pseudocode. Keep \(k\) states instead of one. At each step, generate all successors of all \(k\), and keep the \(k\) best. Information is shared: a strong state contributes more survivors. The failure mode is collapse: if one state generates all \(k\) of the best successors, the beam becomes \(k\) copies of one neighborhood. Stochastic beam search picks the \(k\) survivors at random with probability proportional to value, which is close to a genetic algorithm without crossover.

### Which local search to pick

From the lecture's selection slide:

- Unimodal landscape: hill climbing. Multimodal: annealing or a genetic algorithm.
- Need a quick approximate answer: hill climbing. Need higher quality: annealing or a genetic algorithm.
- Almost no memory: hill climbing or annealing.
- The solution is naturally a population of structured strings: a genetic algorithm.

---

## 7. Adversarial search

### Why games are a different search problem

Games are multi-agent and competitive. There is an opponent you do not control, who is planning against you.

In ordinary search the solution is a sequence of actions. In a game the solution is a **strategy** (a policy): if the opponent plays \(a\), respond with \(b\); if the opponent plays \(c\), respond with \(d\). Hard-coding those rules is brittle. The good news is that games are still search problems, plus a heuristic evaluation of positions you do not expand to the end.

**Single-player search, recalled so the contrast is sharp.** Only your actions matter. Success is reaching a goal, or reaching one at optimal cost. In a one-player tic-tac-toe where Max plays three moves and nobody blocks, Max just picks a path to utility \(+1\).

**Two players.** Max wants utility \(+1\). Min wants Max's utility to be \(-1\). They alternate. Max can no longer pick any path to a win, because Min will block it. Max must choose moves that win even when Min plays optimally. Success is a strategy, not a path.

This "what will they do if I do this" reasoning is the lecture's **embedded thinking**: each player imagines the opponent imagining them.

### How hard, with the lecture's numbers

Chess: branching factor \(b \approx 35\), a game is about 80 plies (40 moves each), so the game tree has about \(35^{80} \approx 10^{123}\) move sequences. The state space is "only" about \(10^{43}\) positions, because different move orders reach the same position (transpositions). The game tree is larger than the state space.

Deep Blue searched about 200 million positions per second. Exhausting the chess tree at that rate would take about \(10^{107}\) years. The age of the universe is about \(10^{10}\) years. Exhaustive search is not a plan. This is the lecture's motivation for **bounded rationality**: be rational inside a computational budget, using pruning, evaluation functions, and a depth limit.

Checkers: Chinook, 1994, ended Marion Tinsley's 40-year reign. Alpha-beta plus an endgame database of perfect play for every position with at most 8 pieces: 443,748,401,247 positions. Databases answer the simplified game exactly; alpha-beta handles the rest.

Chess history the lecture wants: Shannon, 1949, "Programming a Computer for Playing Chess," proposed minimax plus an evaluation function. Deep Blue beat Kasparov in 1997. Deep Fritz beat Kramnik in 2006. Stockfish still uses alpha-beta, now with a neural-network evaluation.

Go: \(b > 250\), games of 150 or more moves, and no good hand-built evaluation function. Minimax and alpha-beta stayed at amateur level for decades. AlphaGo (2016) beat Lee Sedol 4–1 using Monte Carlo tree search plus deep networks, not classical minimax. AlphaGo Zero (2017) learned from self-play only.

Othello: classical minimax and alpha-beta are enough, because the branching factor is manageable and the evaluation features are good (mobility, corners and edges that cannot be flipped, parity of who moves last). Human champions no longer play the top programs.

### Types of games

The lecture restricts the main algorithms to two players, zero-sum, alternating turns. The map of the field:

| | Deterministic | Stochastic |
|---|---|---|
| **Fully observable** (perfect information) | Minimax, \(\alpha\)-\(\beta\). MCTS when \(b\) is huge. Chess, checkers, Othello, Go. | Expectiminimax. Backgammon. TD-Gammon, 1992. |
| **Partially observable** (imperfect information) | Belief states. Stratego, Battleship. | Sampling and game theory. Poker, bridge. Libratus, 2017, for poker. |

### Formal model

- Initial state.
- \(\mathrm{Player}(s)\): who moves in \(s\).
- \(\mathrm{Actions}(s)\): legal moves.
- \(\mathrm{Result}(s, a)\): the transition.
- Terminal test: true when the game is over. Those states are terminal states.
- \(\mathrm{Utility}(s, p)\): the payoff of terminal state \(s\) for player \(p\). Chess and tic-tac-toe from Max's side: win \(+1\), loss \(-1\), draw \(0\).

**Zero-sum.** One player's gain is the other's loss:

\[
\mathrm{Utility}(s, \mathrm{MAX}) + \mathrm{Utility}(s, \mathrm{MIN}) = 0,
\]

so \(\mathrm{Utility}(s, \mathrm{MAX}) = -\mathrm{Utility}(s, \mathrm{MIN})\). One utility function, written from Max's point of view, is enough. Max maximizes it. Min minimizes it, which is the same as Max's opponent maximizing their own utility.

### Minimax

Depth-first. Assume both players play optimally from every state.

\[
\mathrm{minimax}(s) =
\begin{cases}
\mathrm{Utility}(s) & \text{if } s \text{ is terminal} \\
\max_{a} \mathrm{minimax}(\mathrm{Result}(s, a)) & \text{if Player}(s) = \mathrm{Max} \\
\min_{a} \mathrm{minimax}(\mathrm{Result}(s, a)) & \text{if Player}(s) = \mathrm{Min}
\end{cases}
\]

Propagate values up from the leaves. At a Max node, take the max of the children. At a Min node, take the min. Max's move at the root is the child that achieves the root value.

The lecture's pseudocode returns both the chosen child and the value. Terminal nodes call `EVAL`, which is the utility if you searched to the end, or an evaluation function if you cut off. `DECISION` calls `MAXIMIZE` and returns the child.

```
MINIMIZE(state):
    if terminal: return (null, EVAL(state))
    best = (null, +∞)
    for child in state.children:
        (_, utility) = MAXIMIZE(child)
        if utility < best.utility: best = (child, utility)
    return best

MAXIMIZE(state):
    if terminal: return (null, EVAL(state))
    best = (null, −∞)
    for child in state.children:
        (_, utility) = MINIMIZE(child)
        if utility > best.utility: best = (child, utility)
    return best

DECISION(state):
    (child, _) = MAXIMIZE(state)
    return child
```

**Properties.**

- Complete: yes, on a finite game tree.
- Optimal: yes, against an optimal opponent. If the opponent plays badly, you may do better than the minimax value; you will not do worse if you follow the strategy.
- Time: \(O(b^m)\). Every leaf is examined.
- Space: \(O(bm)\), because the traversal is depth-first.

**Scale.**

- Tic-tac-toe: average \(b \approx 5\), at most 9 plies, so \(5^9 = 1{,}953{,}125\) as a rough node bound, and \(9! = 362{,}880\) if you count fully filled boards. Solvable exactly.
- Chess: \(35^{80} \approx 10^{123}\). Not solvable exactly.
- Go: the first move has 361 choices on a \(19 \times 19\) board.

A useful habit when you read a tree: the largest leaf in a branch is irrelevant if Min plays inside that branch. Max only ever receives the **worst** leaf of the branch he chooses. Recitation 4's tree makes this concrete. The three Min nodes are

\[
\min(-3,-1,-9) = -9, \quad \min(-10, 18, 8) = -10, \quad \min(10, -14, -17) = -17.
\]

Max takes \(\max(-9, -10, -17) = -9\) and plays the first move. The leaf 18 is the largest number in the tree, and Max never collects it.

### Alpha-beta pruning

Same DFS, same root value, fewer leaves. Keep two bounds and pass both of them **down** the tree.

- \(\alpha\): the best value Max can already guarantee on the current path. A lower bound on Max's outcome. **Updated only at Max nodes.** Starts at \(-\infty\).
- \(\beta\): the best value Min can already guarantee on the current path. An upper bound on Max's outcome. **Updated only at Min nodes.** Starts at \(+\infty\).

Prune the remaining children of a node when \(\alpha \ge \beta\).

The lecture's wording of the intuition: if \(\alpha\) is already better for Max than what this branch can offer, Max will avoid the branch. If \(\beta\) is already better for Min than what this branch can offer, Min will avoid the branch.

True/false items from the recitation, which match the lecture:

- In a two-player zero-sum game, one agent maximizes a single value and the other minimizes it. **True.**
- We cannot always search to the leaves, because time is limited. **True.**
- Both \(\alpha\) and \(\beta\) are sent down the tree. **True.**
- Min updates \(\alpha\) and Max updates \(\beta\). **False.** It is the other way around.
- \(\alpha\) is the current lower bound on Max's outcome and \(\beta\) is the current upper bound on Min's outcome (equivalently, on the value Max will be allowed to receive). **True.**

The course pseudocode prunes with `≤` at Min and `≥` at Max, which is the same rule \(\alpha \ge \beta\):

```
MINIMIZE(state, α, β):
    if terminal: return (null, EVAL(state))
    best = (null, +∞)
    for child in state.children:
        (_, utility) = MAXIMIZE(child, α, β)
        if utility < best.utility: best = (child, utility)
        if best.utility ≤ α: break          # β would be ≤ α
        if best.utility < β: β = best.utility
    return best

MAXIMIZE(state, α, β):
    if terminal: return (null, EVAL(state))
    best = (null, −∞)
    for child in state.children:
        (_, utility) = MINIMIZE(child, α, β)
        if utility > best.utility: best = (child, utility)
        if best.utility ≥ β: break
        if best.utility > α: α = best.utility
    return best

DECISION(state):
    (child, _) = MAXIMIZE(state, −∞, +∞)
    return child
```

**The classic two-ply tree from the lecture.** Max at the root. Three Min children.

- First Min node, leaves 3, 12, 8. Value \(\min(3,12,8) = 3\). After this returns, the root has \(\alpha = 3\).
- Second Min node, first leaf 2. Now \(\beta = 2 \le \alpha = 3\). The remaining leaves \(X\) and \(Y\) are pruned. Whatever they are, this node's value is \(\le 2\), and Max already has 3, so Max will not play here.
- Third Min node, leaves 14, 5, 2. \(\beta\) goes \(14\), then \(5\), then \(2\). Nothing is pruned, because \(\beta\) stays above \(\alpha = 3\) until the last leaf, and there is nothing left to prune. Value \(= 2\).

Root value:

\[
\max\big(\min(3,12,8),\ \min(2, X, Y),\ \min(14,5,2)\big)
= \max(3,\ Z,\ 2)
\]

where \(Z = \min(2, X, Y) \le 2\). The max is 3, independent of \(X\) and \(Y\). Max plays toward the first child. Two of the nine leaves were never examined.

**Move order decides how much you prune.** Same tree, two orders, same root value.

Left-to-right on this tree, nothing is pruned:

```
Max root, value 7
  Min: max(2, 9) = 9, and max(7, 4) = 7, so min(9, 7) = 7
  Min: max(8, 9) = 9, and max(3, 5) = 5, so min(9, 5) = 5
```

The second Min node looks at the 9 before the 5, so its \(\beta\) drops to 5 only after both children have been evaluated. \(\alpha\) at the root is already 7, but the prune test never fires in time.

Swap the second Min node's children so it sees \(\max(3,5) = 5\) first. Then \(\beta = 5 \le \alpha = 7\), and the entire subtree worth 9 is pruned. Same answer, \(A = 7\). Only the order changed.

**Complexity of pruning.**

- Worst order (best moves last): no pruning, still \(O(b^m)\).
- Ideal order (best moves first): \(O(b^{m/2})\), which is the same as doubling the search depth in the same time.
- How to order well: remember the best move from the previous iteration of iterative deepening; use domain knowledge (in chess: captures, threats, checks, forward moves, then quiet moves); cache transpositions so a position reached two ways is searched once.

### A full alpha-beta trace with a bound that is not the true value

Recitation 4. Max to move. Three Min nodes.

Leaves, left to right: \(-3, -1, -9\), then \(-10, 18, 8\), then \(10, -14, -17\).

1. Root starts at \(\alpha = -\infty,\ \beta = +\infty\).
2. First Min node. \(\beta\) becomes \(-3\), stays \(-3\), then becomes \(-9\). Always \(\beta > \alpha\). Nothing pruned. Returns \(-9\). Root sets \(\alpha = -9\).
3. Second Min node inherits \(\alpha = -9\). First child sets \(\beta = -10\). Now \(\beta = -10 \le \alpha = -9\), so 18 and 8 are pruned. Returns \(-10\). Root \(\alpha\) stays \(-9\).
4. Third Min node inherits \(\alpha = -9\). First child 10 sets \(\beta = 10 > -9\), so continue. Second child \(-14\) sets \(\beta = -14 \le -9\), so \(-17\) is pruned. Returns \(-14\).
5. Root value \(\max(-9, -10, -14) = -9\). Best move is the first one. Three of nine leaves are never examined.

**The value \(-14\) at the third Min node is not its true minimax value.** The true value is \(\min(10, -14, -17) = -17\). After a prune, the number you bubble up is a **bound**. That is safe here: whether the node is worth \(-14\) or \(-17\), Max, who already has \(-9\), will not choose it. Alpha-beta returns the correct value at the root and the correct move. It does not return the correct minimax value at every pruned interior node. If a question asks for "the value of this pruned node," say whether they want the backed-up bound or the true minimax value, and do not pretend they are the same.

### The exam-review game tree

Structure, bottom up. The lowest layer is Max, then Min, then Max at the root.

- Max node \(c\): \(\max(2, -17) = 2\)
- Max node \(d\): \(\max(15, -10) = 15\)
- Max node \(e\): \(\max(-12, -11) = -11\)
- Max node \(f\): \(\max(12, 0) = 12\)
- Min node \(a\): \(\min(2, 15) = 2\)
- Min node \(b\): \(\min(-11, 12) = -11\)
- Root: \(\max(2, -11) = 2\)

Alpha-beta, left to right. Prune when \(\alpha \ge \beta\).

1. Root (Max): \(\alpha = -\infty,\ \beta = +\infty\).
2. Branch \(a\) (Min), same window.
   - Branch \(c\) (Max): children 2 and \(-17\), returns 2. Min node \(a\) sets \(\beta_a = 2\).
   - Branch \(d\) (Max) inherits \(\alpha = -\infty,\ \beta = 2\). First child 15 raises its \(\alpha\) to 15. Now \(15 \ge \beta = 2\), so the other child \(j\) (the \(-10\)) is pruned. Node \(a\) returns \(\min(2, 15) = 2\).
3. Root sets \(\alpha = 2\).
4. Branch \(b\) (Min) inherits \(\alpha = 2,\ \beta = +\infty\).
   - Branch \(e\): children \(-12\) and \(-11\), returns \(-11\). Min node \(b\) sets \(\beta_b = -11\).
   - Now \(\alpha = 2 \ge \beta = -11\), so the whole branch \(f\) is pruned, including both of its leaves \(m\) and \(n\).

Pruned: \(j\), \(f\), \(m\), and \(n\). The question asks for every pruned branch, including children of a pruned parent. Justifying each:

- \(j\): at Max node \(d\), \(\alpha = 15 \ge \beta = 2\) inherited from Min parent \(a\).
- \(f\), \(m\), \(n\): at Min node \(b\), \(\beta = -11 \le \alpha = 2\) inherited from the Max root.

### Limited time: cutoff, evaluation, iterative deepening

Even with pruning you cannot reach the leaves of chess. Practical minimax:

1. Prune with alpha-beta.
2. Cut off at a depth limit and replace the utility of a non-terminal position by an **evaluation function** \(\mathrm{eval}(s)\). True terminal states still use the real utility (\(+1, -1, 0\)).
3. Use iterative deepening, so you have a move ready whenever time runs out, and so you can order the next iteration by the previous iteration's best move.

An evaluation function is a heuristic. It should rank positions by how likely they are to lead to a win. The usual classical form is a weighted linear sum of features:

\[
\mathrm{eval}(s) = w_1 f_1(s) + w_2 f_2(s) + \cdots + w_n f_n(s).
\]

Chess features from the lecture:

- Material. Queen 9, rook 5, bishop 3, knight 3, pawn 1.
- Pawn structure: doubled, isolated, passed pawns.
- King safety: pawn shield, exposure.
- Mobility: number of legal moves.

Weights can be hand-tuned or learned from databases. Deep Blue used on the order of 6,000 features. AlphaZero and Stockfish NNUE replace the linear sum by a neural network trained from self-play. The lecture's point: a learned nonlinear evaluation can find patterns experts did not write down. AlphaZero found strategies that were not in the hand-built books.

What you want from an evaluation function:

- Correlation with actual winning chances.
- Speed. It is called on millions to billions of positions. Deep Blue: 200 million positions per second.
- A tradeoff: a richer evaluation is more accurate and slower. Sometimes a cheap evaluation plus a deeper search wins; sometimes the reverse. It depends on the hardware and the clock.
- Features that come from understanding the game. Chess: material, structure, king safety. Othello: mobility, corners, stability. Backgammon: pip count, blocking, racing.

### Monte Carlo tree search

Named because Go has no good evaluation function and a huge branching factor. MCTS grows the tree selectively. Each move's value is the average outcome of many random playouts, not the output of \(\mathrm{eval}\).

Four steps, repeated:

1. **Selection.** Walk down the existing tree, preferring moves that have won before but have not been tried so often that you ignore alternatives.
2. **Expansion.** Add a new node.
3. **Simulation.** Play a random game from there to a terminal state.
4. **Backpropagation.** Update win/visit counts on the path back to the root.

AlphaGo is MCTS guided by neural networks: one network proposes moves, another evaluates positions, instead of purely random playouts.

### Stochastic games and expectiminimax

Dice, cards, any random event. Insert **chance nodes** into the tree. You cannot control them, so you take the expected utility.

Backgammon has 21 distinct dice outcomes (the six doubles, and fifteen unordered pairs \(\{1,2\}, \{1,3\}, \ldots\) — the lecture's count is 21). After a player moves, a chance node branches on the roll.

\[
\mathrm{expectiminimax}(s) =
\begin{cases}
\mathrm{Utility}(s) & \text{if terminal} \\
\max_a \mathrm{expectiminimax}(\mathrm{Result}(s,a)) & \text{if Max} \\
\min_a \mathrm{expectiminimax}(\mathrm{Result}(s,a)) & \text{if Min} \\
\sum_r P(r)\, \mathrm{expectiminimax}(\mathrm{Result}(s, r)) & \text{if chance}
\end{cases}
\]

**Coin-flip example from the lecture.** A chance node is 50/50.

- Left chance node sits over two Min nodes: \(\min(4,6) = 4\) and \(\min(9,6) = 6\). Expected value \(0.5 \times 4 + 0.5 \times 6 = 5\).
- Right chance node: \(\min(8,2) = 2\) and \(\min(7,1) = 1\). Expected value \(0.5 \times 2 + 0.5 \times 1 = 1.5\).
- Max chooses the left branch, value 5.

**Complexity.** If there are \(n\) chance outcomes at each chance layer, the time is \(O(b^m n^m)\). For backgammon \(n = 21\). In the same amount of time you search much less deeply than in a deterministic game. In practice: sample the likely outcomes instead of enumerating all 21, and lean on an evaluation function. TD-Gammon (1992) learned its evaluation by temporal-difference self-play and combined it with a shallow expectiminimax search. It reached world-class play.

**Evaluation functions must be calibrated.** In deterministic games, \(\mathrm{eval}\) only has to rank positions. In stochastic games it is averaged with probabilities, so the numbers have to mean expected utility on the same scale as the terminal utilities. An ordinal ranking is not enough. A position that is "a bit better" cannot be labeled 100 if a win is worth 1, because the average would be dominated by the label rather than by the probability. The evaluation also has to separate position quality from luck: a good position can lose to a bad roll.

### Games, the lecture's closing list

Minimax chooses the best move given optimal play by the opponent. It examines the whole tree, which is impractical under a clock. Alpha-beta, move ordering, transposition tables, evaluation functions, and iterative deepening are what make it practical. Pruning does not change the root decision.

Beyond the exam's core, the lecture points at: imperfect information and belief states, opponent modeling, MCTS, and neural-network evaluations trained by self-play (AlphaGo, AlphaZero, MuZero). Robot soccer (RoboCup) adds perception, motor control, and real-time planning to the adversarial reasoning.

---

## 8. Constraint satisfaction

### How a CSP differs from path search

Path search treats a state as atomic and looks for a path. A CSP treats a state as factored: a value for each variable. The path is usually irrelevant. You care about the goal itself, which is an assignment that satisfies every constraint.

A CSP is:

1. Variables \(X = \{X_1, \ldots, X_n\}\).
2. Domains \(D = \{D_1, \ldots, D_n\}\), with \(X_i\) taking values in \(D_i\).
3. Constraints \(C\), each specifying the allowed combinations of values for some subset of the variables.

A **solution** is a **consistent, complete assignment**: every variable has a value, and every constraint is satisfied.

CSP algorithms do two things, and they can be interleaved.

1. **Search:** assign a value to a variable.
2. **Inference / constraint propagation:** use a constraint to delete values from domains, which can delete values from other domains. Sometimes propagation solves the problem with no search. Sometimes it proves the problem is impossible before you search.

### Map coloring, the running example

Variables: \(\{WA, NT, Q, NSW, V, SA, T\}\), the Australian states and territories from the lecture.

Domains: \(\{\mathrm{red}, \mathrm{green}, \mathrm{blue}\}\) for each.

Constraints: adjacent regions differ. \(WA \ne NT\), or written as the list of allowed pairs \((WA, NT) \in \{(\mathrm{red},\mathrm{green}), (\mathrm{red},\mathrm{blue}), \ldots\}\).

One solution the lecture writes down:

\[
\{WA=\mathrm{red},\ NT=\mathrm{green},\ Q=\mathrm{red},\ NSW=\mathrm{green},\ V=\mathrm{red},\ SA=\mathrm{blue},\ T=\mathrm{green}\}.
\]

Tasmania (\(T\)) shares no border with anyone. In the constraint graph it is an isolated vertex, an independent subproblem.

**Constraint graph.** For a **binary CSP** (every constraint mentions at most two variables), draw a vertex per variable and an edge per constraint. Algorithms use this graph. Map coloring is binary. The edges are the borders: WA–NT, WA–SA, NT–SA, NT–Q, Q–SA, Q–NSW, SA–NSW, SA–V, NSW–V. Tasmania has no edges.

### Kinds of variables

- **Discrete, finite domain.** \(n\) variables, domain size at most \(d\), so there are \(O(d^n)\) complete assignments. Map coloring, \(n\)-queens.
- **Discrete, infinite domain** (integers, strings). You need a constraint language, not an enumerated list of tuples. Example: job scheduling, \(T_1 + d \le T_2\).
- **Continuous.** Common in operations research. The Hubble scheduling example: observation start and finish times, with astronomical, precedence, and power constraints. Linear equalities and inequalities are linear programming, which is not solved by backtracking.

### Kinds of constraints

- **Unary:** one variable. \(SA \ne \mathrm{green}\).
- **Binary:** two variables. \(SA \ne WA\).
- **Global:** three or more. \(\mathrm{Alldiff}(X_1,\ldots,X_k)\) says all the values are distinct. Cryptarithmetic and Sudoku are built out of these. Any \(n\)-ary constraint can be rewritten as binary constraints with extra variables. Many solvers only implement binary constraints, and that rewriting is why they can still express Sudoku.
- **Preferences (soft constraints):** red is better than green. Usually a cost on each assignment. The problem is then a constrained optimization problem, not a pure CSP. The lecture distinguishes them so you do not treat a preference as a hard constraint.

### Two formulations of 8-queens

**Formulation 1.** Variables \(Q_1, \ldots, Q_8\), each a square number in \(\{1, \ldots, 64\}\). Constraints say the eight squares are non-attacking. A solution looks like \(Q_1 = 1, Q_2 = 13, \ldots\). Domain size 64, and most assignments place two queens on the same square or the same row.

**Formulation 2, the one to use.** Variables \(Q_1, \ldots, Q_8\) are the columns, already enforcing one queen per column. Domain of each is the row, \(\{1, \ldots, 8\}\). Constraints: all rows different, and no two on the same diagonal. A solution looks like \(Q_1 = 1, Q_2 = 7, Q_3 = 5, \ldots, Q_8 = 3\).

Same idea as the local-search encoding. The formulation changes the size of the search.

### Cryptarithmetic

The lecture's puzzle is

```
  T W O
+ T W O
  F O U R
```

Variables: \(\{F, T, U, W, R, O, C_1, C_2, C_3\}\). The \(C_i\) are the carries.

Domains: digits \(\{0,1,\ldots,9\}\). Carries are naturally in \(\{0,1\}\), and the digit domain is a safe superset if the addition constraints are written correctly.

Constraints:

- \(\mathrm{Alldiff}(F, T, U, W, R, O)\). A 6-ary constraint. Carries are not letters, so they are not in the Alldiff.
- \(T \ne 0\), \(F \ne 0\). Leading digits.
- \(O + O = R + 10 \cdot C_1\)
- \(C_1 + W + W = U + 10 \cdot C_2\)
- \(C_2 + T + T = O + 10 \cdot C_3\)
- \(C_3 = F\)

One solution: \(734 + 734 = 1468\). So \(T=7, W=3, O=4, F=1, U=6, R=8\), and the carries are \(C_1 = 0\) (because \(4+4 = 8\)), \(C_2 = 0\) (because \(3+3 = 6\)), \(C_3 = 1\) (because \(7+7 = 14\)). Check Alldiff: 1, 7, 6, 3, 8, 4 are all different.

### Sudoku

81 variables, one per cell. Domain \(\{1,\ldots,9\}\). A filled cell has a domain of size 1.

27 Alldiff constraints: 9 rows, 9 columns, 9 boxes. For example

\[
\mathrm{Alldiff}(A_1,\ldots,A_9), \quad
\mathrm{Alldiff}(A_1, B_1, \ldots, I_1), \quad
\mathrm{Alldiff}(A_1, A_2, A_3, B_1, B_2, B_3, C_1, C_2, C_3).
\]

**Naked pair.** Two cells in the same row, column, or box have exactly the same two candidates, say \(\{2,6\}\), and nothing else. Those two values must occupy those two cells, in some order. Delete 2 and 6 from every other cell in that unit. Naked triples are the same idea with three cells and three candidates.

The lecture also names a hidden pair: two candidates that appear in only two cells of a unit, even if those cells have other candidates written down. Those two cells must be those two values, so the extra candidates in those two cells can be deleted. You do not need a catalog of every Sudoku trick. You need to see that these are constraint propagation, the same idea as arc consistency, applied to Alldiff.

### Backtracking search

DFS that assigns one variable at a time and never assigns a value that conflicts with the assignment so far.

- Initial state: the empty assignment \(\{\}\).
- State: a partial assignment.
- Successor: assign a value to one unassigned variable.
- Goal test: the assignment is complete and consistent.

Plain BFS on the tree of assignments is hopeless. Plain DFS that ignores constraints until the end is generate-and-test, also hopeless. Backtracking fails as soon as the partial assignment is inconsistent.

The lecture's backtracking-with-inference pseudocode:

```
BACKTRACKING-SEARCH(csp):
    return BACKTRACK({}, csp)

BACKTRACK(assignment, csp):
    if assignment is complete: return assignment
    var = SELECT-UNASSIGNED-VARIABLE(csp, assignment)
    for value in ORDER-DOMAIN-VALUES(var, assignment, csp):
        if value is consistent with assignment:
            add {var = value} to assignment
            inferences = INFERENCE(csp, var, value)
            if inferences did not fail:
                add inferences to assignment
                result = BACKTRACK(assignment, csp)
                if result did not fail: return result
            remove {var = value} and the inferences
    return failure
```

`SELECT-UNASSIGNED-VARIABLE` is where MRV goes. `ORDER-DOMAIN-VALUES` is where LCV goes. `INFERENCE` is forward checking, or AC-3, or nothing.

### The three improvements

**1. Which variable next? Minimum remaining values (MRV).** Choose the variable with the fewest legal values left. Assign the hardest variable first, so failures happen high in the tree instead of after you have filled in everything else.

The textbook's tie-break, which the slides do not name but which belongs with MRV: the **degree heuristic**. When several variables have the same number of remaining values, pick the one involved in the most constraints with still-unassigned variables. It is the right tie-break at the start, when every domain has the same size and MRV has nothing to say.

**2. Which value next? Least constraining value (LCV).** For the chosen variable, try first the value that rules out the fewest values in the remaining variables. You are hoping to succeed, so try the value most likely to leave the rest of the problem solvable. MRV is fail-first on variables. LCV is succeed-first on values.

**3. Can we notice inevitable failure early? Forward checking.** When you assign a variable, delete from each unassigned neighbor every value that conflicts with the assignment. If any variable's domain becomes empty, backtrack immediately.

Forward checking only propagates from assigned variables to unassigned ones. It does **not** look at constraints between two unassigned variables. That is the lecture's Australia picture:

- Assign \(WA = \mathrm{red}\). Delete red from \(NT\) and \(SA\).
- Assign \(Q = \mathrm{green}\). Delete green from \(NT\), \(SA\), and \(NSW\).
- Now \(NT\) has only blue left, and \(SA\) has only blue left.
- \(NT\) and \(SA\) are adjacent. They cannot both be blue. Forward checking does not notice, because neither has been assigned yet. You will discover the failure only when you try to assign one of them.

Arc consistency notices immediately.

### Consistency

**Node consistency (unary).** \(X_i\) is node-consistent if every value in its domain satisfies every unary constraint on \(X_i\). If \(SA \ne \mathrm{green}\), delete green from \(SA\) before you search. This is cheap preprocessing.

**Arc consistency (binary).** The arc \(X \to Y\) is arc-consistent if and only if for every value \(x\) in the domain of \(X\), there is some value \(y\) in the domain of \(Y\) that is allowed together with \(x\). The arc is directed. \(X \to Y\) being consistent does not mean \(Y \to X\) is consistent. You check both directions.

**Path consistency** generalizes this from one neighboring variable to a chain. The lecture defines it and moves on. Arc consistency is the one you must be able to run.

**AC-3**, exactly as on the slide:

```
function AC-3(csp):
    queue = all arcs in csp
    while queue is not empty:
        (Xi, Xj) = REMOVE-FIRST(queue)
        if REVISE(csp, Xi, Xj):
            if Di is empty: return false
            for each Xk in NEIGHBORS(Xi) minus {Xj}:
                add (Xk, Xi) to queue
    return true

function REVISE(csp, Xi, Xj):
    revised = false
    for each x in Di:
        if no y in Dj allows (x, y):
            delete x from Di
            revised = true
    return revised
```

If revising \(X_i\) deletes a value, every arc **into** \(X_i\) must be checked again, except the arc you just processed. A value of a neighbor might have been supported only by the deleted value.

**Complexity.** \(n\) variables, domain size \(d\).

- A complete graph has \(n(n-1)\) directed arcs, so \(O(n^2)\) arcs.
- Each arc \(X_k \to X_i\) is reinserted at most \(d\) times, because each insertion follows a deletion from \(X_i\), and \(X_i\) has at most \(d\) values.
- Revising an arc compares up to \(d\) values against \(d\) values, so \(O(d^2)\).
- Total: \(O(n^2 d^3)\).

**Tiny example.** \(X, Y \in \{1,2,3\}\), constraint \(X < Y\).

- Revise \(X \to Y\). \(x = 1\) is supported by 2 or 3. \(x = 2\) is supported by 3. \(x = 3\) has no support. Delete 3. Domain of \(X\) is \(\{1,2\}\).
- Because \(X\) changed, enqueue arcs into \(X\). Revise \(Y \to X\). \(y = 1\): no \(x < 1\). Delete 1. \(y = 2\) is supported by \(x = 1\). \(y = 3\) is supported by \(x = 1\) or \(2\). Domain of \(Y\) is \(\{2,3\}\).
- \(Y\) changed, so recheck \(X \to Y\). Both remaining values of \(X\) still have support. Stop.

The domains are now arc-consistent, and any assignment \(X = 1, Y = 2\), or \(X = 1, Y = 3\), or \(X = 2, Y = 3\), works. Propagation did not uniquely solve it. It reduced the search.

On the Australia failure above, AC-3 does what forward checking cannot. After \(WA = \mathrm{red}\) and \(Q = \mathrm{green}\), the domain of \(NT\) is \(\{\mathrm{blue}\}\) and the domain of \(SA\) is \(\{\mathrm{blue}\}\). Revise \(NT \to SA\): blue has no support in \(SA\), because the only candidate is also blue and they must differ. Delete blue. \(NT\)'s domain is empty. AC-3 returns failure. You backtrack without assigning \(V\) or \(NSW\).

### Using the shape of the graph

**Independent subproblems.** Solve each connected component separately and combine the solutions. Tasmania is its own component: color it anything, in \(O(d)\) time, with no effect on the mainland.

If backtracking on \(n\) variables is \(O(d^n)\), and you split into subproblems of \(c\) variables, you have \(n/c\) subproblems, each \(O(d^c)\), for a total of

\[
O\left(\frac{n}{c}\, d^c\right).
\]

The lecture's numbers: \(n = 80\), \(d = 2\), four subproblems with \(c = 20\), processing \(10^7\) nodes per second.

- Without the split: \(2^{80} = 1.2089 \times 10^{24}\) nodes. At \(10^7\) nodes per second that is \(1.21 \times 10^{17}\) seconds, which is about \(3.83 \times 10^9\) years, **3.83 billion years**.
- With the split: \(4 \times 2^{20} = 4.19 \times 10^6\) nodes, which is about **0.4 seconds**.

The slide writes "3.83 million years." Recompute it if it is on the exam. \(2^{80}/10^{7}\) seconds divided by about \(3.16 \times 10^{7}\) seconds per year is 3.83 **billion** years. The 0.4 seconds is correct: \(4 \times 2^{20} / 10^{7} = 0.42\).

**Tree-structured CSPs.** A constraint graph is a tree if it is connected and has no cycles: exactly one path between any two variables, and \(n - 1\) edges.

Directed arc consistency under an ordering \(X_1, \ldots, X_n\): every \(X_i\) is arc-consistent with each \(X_j\) for \(j > i\).

The algorithm:

1. Pick a root. Topologically order the variables so every parent comes before its children. Example: if the edge is \(A \to B\) with \(A\) the parent, \(A\) appears first.
2. Make the tree directed-arc-consistent from the leaves backward. For \(i = n, n-1, \ldots, 2\), revise the arc from \(X_i\)'s parent toward \(X_i\) (remove parent values that have no support in the child). Each edge is processed once. You do not need AC-3's requeue, because each node has one parent and you are walking leaves-to-root.
3. Assign values root-to-leaf. For whatever value the parent received, the child still has at least one supporting value. No backtracking.

Why no backtracking: after the backward pass, every edge is consistent in the parent-to-child direction, so a choice at a parent always has a legal choice at the child. Why no rechecking of other neighbors: a tree node has only one parent.

Time: \(n - 1\) edges, each revision \(O(d^2)\), so \(O(nd^2)\). Linear in the number of variables. Compare with \(O(d^n)\) for general backtracking.

**Nearly a tree: cutset conditioning.** If one variable, or a small set, is the reason the graph has cycles, assign it first and delete inconsistent values from its neighbors. In Australia, assign \(SA\) a color. Every mainland region touches \(SA\), and removing \(SA\) leaves a tree (in fact a much simpler graph). Then run the tree algorithm. If that color of \(SA\) fails, try the next color.

The cost is \(d\) tree-solves for a single cutset variable, which is far better than \(d^n\) as long as the cutset is small. If the cutset value you chose is wrong, you simply try the next one. That try-next-value step is the remaining search.

### What the lecture says to remember

- A CSP state is an assignment to a fixed set of variables. The goal test is the set of constraints.
- Backtracking is DFS with one variable assigned per node, checking consistency as it goes.
- MRV and LCV choose the variable and the value. Forward checking rejects assignments that empty a future domain.
- Arc consistency does more work and catches inconsistencies forward checking misses.
- A tree-structured CSP can be solved in linear time.
- CSPs are domain-independent. You write variables, domains, and constraints. A solver supplies backtracking and propagation. The lecture points at [AISpace](http://aispace.org/constraint/) if you want to watch a solver run.
- Local search applies to CSPs too. The lecture marks this as further exploration rather than a worked algorithm. The standard method is **min-conflicts**: start from a complete assignment, even an inconsistent one, and at each step pick a conflicted variable and reassign it to the value that violates the fewest constraints. It is hill climbing on the number of violated constraints. It is how large \(n\)-queens instances are solved in practice, and it is the right thing to say if you are asked how local search would attack a CSP.

Real CSPs named in lecture, so you can recognize one in a word problem: who teaches which class, timetabling, hardware configuration, spreadsheets, transportation scheduling, factory scheduling, floor planning. Many of these have real-valued variables, which pushes them toward linear programming rather than finite-domain backtracking.

---

## 9. Procedures for the questions they actually ask

### "Give the PEAS description and classify the environment"

Write four short blocks: performance, environment, actuators, sensors. Use nouns from the problem, not the words "good" and "fast" alone. Then six labels, each with one sentence of assumption. If two labels are defensible, pick the better one and mention the assumption under which the other would hold. That is how the homework solution is written.

Watch for the trick in the problem statement. Foldy says the bin locks and nobody interacts during the cycle, which is why it is static and single-agent. e-Buddy converses with a person, which is why it is multi-agent. If the problem says the clock is part of the score, consider semi-dynamic.

### "Formulate this as a search problem"

Six lines: state representation, initial state, actions, transition, goal test, path cost. Then one sentence on why the state is not bloated. If they ask which of BFS, DFS, UCS is appropriate: UCS when costs differ, BFS when every step costs 1 and you want the fewest steps and can afford the memory, DFS when the space is large, solutions are dense, and any solution will do. IDS when you want BFS's guarantee and DFS's memory.

### "Trace this algorithm"

1. Write the legal neighbors before you search. Most errors are illegal moves.
2. Write the container after every expansion.
3. Apply the goal test only when you pop.
4. Apply the stated tie break, and mark the step where it fires.
5. Give visit order and path separately. Give the cost as a sum of edge weights, not just a total.
6. If a key changes, write \(g_{\text{old}} \to g_{\text{new}}\).

### "Is this heuristic admissible? Consistent?"

1. Compute \(h^*(n)\) for every node, by the cheapest path, before you look at \(h\).
2. Admissibility: one row per node, \(h(n) \le h^*(n)\).
3. Consistency: one row per directed edge, \(h(n) \le c(n,n') + h(n')\).
4. A single violation means not consistent. Say which edge.
5. Lowering some \(h\) value preserves admissibility and can destroy consistency.

### "Run A\*. Is the answer guaranteed?"

Say whether the state space is a tree or a graph, and whether \(h\) is admissible, consistent, both, or neither. Then cite Theorem 1 or Theorem 2, or say the guarantee does not apply and either show the suboptimal path or show that this particular run still found the optimum.

### "Minimax and alpha-beta"

1. Fill values bottom-up for plain minimax. Circle Max's move.
2. For alpha-beta, go left to right, write \([\alpha, \beta]\) at every node, and name every pruned leaf, including children of a pruned node.
3. If a pruned node returns a bound, do not report that bound as the node's minimax value unless the question asks for the backed-up value.

### "Formulate this as a CSP"

Variables, domains, constraints, and what a solution looks like. Prefer the formulation that builds structure into the variables (one queen per column, one variable per Sudoku cell, carries as their own variables). If they ask which heuristic or inference to use, say what each one decides: MRV picks the variable, LCV picks the value, forward checking and AC-3 delete future values.

---

## 10. Traps that show up in the solutions

- **Order of visit is not the path.** Parent pointers are.
- **Goal test on generation** changes which nodes are "visited" and, for UCS and A\*, can return a suboptimal path if you stop at the first time the goal is generated. The first time the goal is **dequeued** is the safe moment.
- **DFS push order.** "Explore in order Up, Right, Down, Left" means Up is popped first, so Up is pushed last.
- **Unit cost vs. variable cost.** BFS and IDS are optimal only for uniform step cost. UCS and A\* (under the theorems) handle variable costs.
- **\(h = 0\)** is admissible and consistent, and it makes A\* identical to UCS. It is a legal heuristic and a bad one.
- **A bigger heuristic is better only if it stays admissible.** Overestimating can make A\* return a suboptimal path even on a tree.
- **Dominance does not imply consistency.** The max of an admissible inconsistent heuristic and another heuristic can be inconsistent.
- **The sum of two good heuristics is usually illegal.**
- **An admissible heuristic can depend only on the state.** If the formula contains \(g(n)\), it is not a heuristic in the sense A\* needs.
- **Manhattan on a maze with unequal move costs** is still admissible if every move costs at least 1, but it is weak, and it may be inconsistent if some move costs less than the drop in Manhattan. On a tree, A\* is still optimal. On a graph, you need consistency for the guarantee.
- **Graph search vs. the word "tree."** In this course the explored set is always there. "Tree" means each state has one path.
- **Inconsistency removes a guarantee.** It does not, by itself, mean this run is wrong.
- **Alpha and beta.** Max updates \(\alpha\). Min updates \(\beta\). Both travel down. Prune at \(\alpha \ge \beta\).
- **A pruned node's number may be a bound.** The root value is still exact.
- **Expectiminimax averages.** Do not take a min or a max at a chance node. And do not use an evaluation function whose only meaning is a ranking.
- **Forward checking misses conflicts between two unassigned variables.** That is the SA/NT both-blue example. AC-3 catches it.
- **MRV and LCV pull in opposite directions on purpose.** MRV is fail-first. LCV is succeed-first. Do not swap them.
- **8-puzzle parity** is for odd-width boards. Half of the 9! boards, 181,440, are reachable. The blank is not counted as a tile in inversions or in Manhattan distance.
- **Simulated annealing's \(\Delta\).** Positive means worse only after you decide whether you are minimizing a cost or maximizing a fitness. The probability is \(e^{-(\text{badness})/T}\), never \(e^{+(\text{badness})/T}\).
- **Cooling.** Logarithmic cooling has the convergence proof. Exponential cooling is what you run. A temperature far above the size of \(\Delta E\) accepts almost everything.
- **Local search does not return a path** and does not claim completeness or optimality. If the question asks for advantages, memory and the lack of a tree are the answers, not "it always finds the optimum."
- **CSP decomposition arithmetic.** \(O(d^n)\) versus \(O((n/c) d^c)\). Recompute the years. The slide's "million" does not match \(2^{80}\) at \(10^7\) nodes per second.

---

## 11. Python, only where it meets the exam

Recitation 1 is a Python refresher for the coding homework, not a conceptual exam topic. The pieces that clarify the algorithms:

- **Stack:** a list, `append` to push, `pop` to pop. DFS.
- **Queue:** `collections.deque`, `append` to enqueue, `popleft` to dequeue. A Python list is a bad queue because popping from the front is \(O(n)\). BFS.
- **Priority queue:** `heapq`. Push `(priority, node)`. The smallest priority comes out first. UCS uses \(g\). A\* uses \(f\). Ties need a second component in the tuple, or the comparison falls through to the node and can throw.
- **Explored set:** a `set`, membership \(O(1)\). A list makes "have I seen this state?" \(O(n)\) per successor, which is the difference between an 8-puzzle search that finishes and one that does not.
- **Do not store the whole path in every node.** Store a parent pointer. Reconstruct the path once, at the end. Copying a length-\(d\) list into every child is \(O(d)\) work per node and exhausts memory.
- **Copy a board** with `list(board)` or `board[:]`, not `copy.deepcopy`.
- **Goal test after you pop**, then expand. Move order in the coding assignment is Up, Down, Left, Right. Manhattan distance ignores the blank. The blank is 0. The goal is `0,1,2,3,4,5,6,7,8`.

---

## 12. The rest of Lecture 1, so a definition question has an answer

These topics are previewed in the first lecture and taught later. Exam 1's worked problems stop at CSP. If a short definition appears, this is the lecture's version, not a full treatment.

**Bayesian networks.** A graph of probabilistic dependence. Nodes are random variables. Edges are direct influence. The graph encodes the joint distribution compactly, using conditional independence:

\[
P(X_1, \ldots, X_n) = \prod_{i=1}^{n} P(X_i \mid \mathrm{Parents}(X_i)).
\]

Words the lecture lists: conditional independence, d-separation, inference, belief propagation.

**Machine learning, Tom Mitchell's question.** How do we make programs that improve with experience?

- **Unsupervised.** Inputs \(x_1, \ldots, x_n\) with no labels. Output is a clustering \(f: X \to \{C_1, \ldots, C_k\}\). Methods named: clustering, \(k\)-means, association rules.
- **Supervised, binary classification.** Labeled pairs \((x_i, y_i)\) with \(y_i \in \{-1, +1\}\). Output is a hypothesis \(h: X \to Y\). Examples: credit approval, spam. Methods named: \(k\)-nearest neighbors, perceptrons, neural nets, linear regression.
- **Ensembles.** Many weak models, one strong predictor. Random forests: many trees, each on a random subset of the data and the features, then a vote. Gradient boosting: trees built in sequence, each correcting the previous trees. Words: bagging, boosting, variance reduction.

**Deep learning, the lecture's timeline.** Neural nets in the 1950s–60s (Rosenblatt). Slow progress in the 1970s. Backpropagation in 1986. Convolutional nets (LeCun) and recurrent nets (Schmidhuber) in the 1990s. Deep belief nets (Hinton) and autoencoders (Bengio) in 2006. ImageNet and the 2012–2017 takeoff. Transformers. Then large language models. The ingredient the lecture emphasizes is data plus compute.

A deep net is a stack of layers. Early layers detect simple structure. Later layers combine it. The hidden layers are feature detectors, so the representation is learned rather than designed.

**Convolutional nets, for images.** A small filter slides across the image and detects local patterns (edges, textures). Pooling shrinks the representation and keeps the strong signals. Weights are shared across positions. Early layers find edges, deeper layers find shapes, then objects.

**Attention and transformers.** Recurrent nets read one word at a time, train slowly, and struggle to connect distant words. Attention lets each word relate directly to every other word, at any distance, so the sequence can be processed in parallel. The paper is "Attention Is All You Need" (Vaswani et al., 2017).

The architecture: stacked self-attention and feed-forward layers. Multi-head attention runs several attention patterns in parallel. Positional encoding puts word order back in, because attention by itself has no notion of sequence. This is the architecture behind GPT, Gemini, Claude, and vision transformers. Words to recognize: query, key, value, multi-head attention, positional encoding.

**Foundation models.** One large model, pretrained on broad data, then adapted to many tasks by fine-tuning or prompting, instead of training a new model per task. At sufficient scale, abilities show up that were not explicitly trained. Words: pretraining, fine-tuning, prompting, few-shot learning, scaling laws, emergent behavior.

**Multimodal models.** Text, images, audio, and video in one system, projected into a shared representation so they can be compared. CLIP aligns images with captions. Applications: captioning, visual question answering, text-to-image generation.

**Retrieval-augmented generation.** A language model only knows its training data, which can be stale or missing private facts. Retrieve relevant documents from an external store, then give them to the model as context. Words: embeddings, vector search, grounding. The lecture's claims: more up-to-date answers, fewer hallucinations, no retraining.

**Reinforcement learning.** An agent in a stochastic environment learns from delayed reward, modeled as a Markov decision process, and maximizes reward over the long run. **RLHF**: humans rank model outputs, a reward is learned from those rankings, and the model is optimized against it. The lecture presents this as how chat models are tuned to be helpful and safe.

**Agentic AI.** A chatbot answers one prompt. An agent pursues a goal across many steps, uses tools, and adapts. The lecture's equation: agent = LLM + tools, memory, and planning. This is the same agent idea as Lecture 2, with a language model as the program.

**Ethics, the lecture's list.** Bias and fairness (models amplify training data). Interpretability (why did it decide that?). Safety and alignment with human intent. Robustness outside the lab. Privacy. The design goals the lecture names: accuracy, safety, privacy, nondiscrimination, transparency. Methods named: fairness metrics, explainability, alignment, red-teaming.

---

## 13. Sources inside this folder

If a formula here disagrees with a memory of some other textbook, trust these, in this order:

- Lecture slides 1 through 8, especially the optimality theorems in the informed-search lecture and the AC-3 and tree-CSP slides.
- Recitation 5 solutions (the exam review): Hanoi formulation, the maze traces, IDS, the properties table, the four heuristics, the proof that the max of consistent heuristics is consistent, the annealing trace, the alpha-beta prune list.
- Homework 1 solutions: Foldy's environment, the "consistency is sufficient, not necessary" A\* trace, and the four combination proofs.
- Recitation 3 solutions: 8-puzzle parity, the weighted-Manhattan argument, and why \(8 - g(n)\) is not a heuristic.
- Recitation 4 solutions: local-search pros and cons, 6-queens counts, the sign of \(\Delta\) when you are maximizing, who updates \(\alpha\) and \(\beta\), and the fact that a pruned node returns a bound.

The coding assignment is the authority on one operational point: goal-test when the node is removed from the frontier, then expand. Do not switch to the Russell & Norvig BFS variant that tests children at generation, even though the textbook does it.
