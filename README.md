# KnowledgeEngineering

## Practical Exercise 4: Knowledge-Based Autonomous Trailer Agent

This repository contains a knowledge-based agent for an autonomous tractor and trailer that delivers cargo across a Kenyan road network. It extends the Uniform Cost Search (UCS) route finder from Practical Exercise 3 (the `PS-Agents` repository). The trailer now keeps an explicit knowledge base of facts and rules. It answers questions using that knowledge base and bases its route decisions on it.

All of the work is in one Jupyter notebook: **[`Autonomous_Trailer.ipynb`](Autonomous_Trailer.ipynb)**.

## From Practical Exercise 3 to Practical Exercise 4

| | Practical Exercise 3 | Practical Exercise 4 |
|---|---|---|
| Agent type | Problem-solving (search) agent | Knowledge-based agent |
| World model | Fixed graph built in code | Facts stored in a knowledge base |
| Road conditions | Not represented; every road assumed open | Stored as facts; rules decide if a road is usable |
| Reaction to change | Programmer edits the graph | Agent is told a new fact and replans |
| Route search | UCS on the fixed graph | The same UCS, run on the graph of roads the knowledge base says are usable |

## Notebook structure

The notebook follows the ten knowledge engineering steps from the lecturer's reference notebook (*Smart Traffic Light Navigation Agent*):

1. **Identify the task:** environment, agent, available information, objective and decisions.
2. **Assemble relevant knowledge:** the Practical Exercise 3 road network, split into facts about the environment and rules for reasoning.
3. **Define the vocabulary:** predicates and constants.
4. **Encode general knowledge:** road condition rules and the `CanUse`, `RoadOpen`, `RoadClosed` and `Reachable` rules.
5. **Add new facts:** fill the knowledge base, then retrieve and update facts.
6. **Encode the problem instance:** `At(Trailer, Nairobi)` and `Destination(Trailer, Kisumu)`.
7. **Pose queries:** answers obtained by fact lookup and by inference.
8. **Querying the knowledge base to make decisions:** UCS planning over the roads the knowledge base marks as usable.
9. **Debugging the knowledge base:** unknown towns and roads, invalid updates, unknown conditions, road closure and reopening, an unreachable destination.
10. **Final reflection:** summary, limitations and improvements.

An **optional extension** (between Steps 9 and 10) has the trailer drive one road at a time and replan when it learns of a closure partway through the journey.

## Knowledge representation

As in the reference notebook, the knowledge base is a Python dictionary keyed by predicate name:

| Predicate | Meaning |
|---|---|
| `Town(x)` | x is a town |
| `Road(x, y, d)` | a road leads from x to y, d km long (each two-way road is stored in both directions) |
| `RoadCondition(x, y, c)` | the road from x to y is in condition c |
| `At(Trailer, x)` | the trailer is in town x |
| `Destination(Trailer, x)` | the cargo must be delivered to x |

`CanUse`/`RoadOpen`, `RoadClosed` and `Reachable` are **derived** by rules rather than stored. A `roadConditionRules` dictionary (Open, Wet, Under Repair, Closed, Flooded, Weight Restricted) says whether a road in each condition is usable. Any unknown condition is treated as unusable, to be safe.

## Road network (from Practical Exercise 3)

10 towns and 12 two-way roads: Nairobi–Nakuru 161, Nairobi–Nyeri 151, Nyeri–Nyahururu 104, Nyahururu–Nakuru 64, Nakuru–Mau Summit 62, Mau Summit–Kericho 50, Kericho–Kisumu 84, Nairobi–Narok 141, Narok–Bomet 81, Bomet–Kericho 77, Bomet–Kisii 83, Kisii–Kisumu 114 (km).

## Key results

| Scenario | Agent's decision |
|---|---|
| All roads open | Nairobi → Nakuru → Mau Summit → Kericho → Kisumu (**357 km**) |
| Nakuru–Mau Summit closed | Nairobi → Narok → Bomet → Kericho → Kisumu (**383 km**) |
| Road reopened | Back to the 357 km route |
| Both roads into Kisumu flooded | Kisumu unreachable; the agent decides not to move |
| Closure discovered on reaching Nakuru (extension) | Goes back through Nairobi; 705 km driven in total |

Each result is checked with `assert` statements in the notebook. With every road open, the graph built from the knowledge base is checked to be identical to the Practical Exercise 3 graph.

## Running the notebook

The notebook uses only the Python standard library (`heapq`, `itertools`). Open it in Jupyter or VS Code with any Python 3 kernel and choose **Run All**. The interactive `input()` example is commented out, so the notebook runs from top to bottom without waiting for input.
