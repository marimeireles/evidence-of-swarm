## A Spy in the Swarm: Testing Double Agents for Human Oversight

In the reported July 2026 OpenAI/Hugging Face incident, hundreds of supposedly isolated AI agents found ways to communicate, share answers, and bypass restrictions while working on tasks that were deemed impossible. Almost none considered alerting a human, and none followed through with it.
We test whether giving one agent within a swarm a reporting duty helps to mitigate unlawful behavior among agents. This spy in the swarm works alongside ordinary agents and it's undifferentiated unless immoral behavior is recognized.
We track whether the agent reports its fellows AIs, stays silent, joins in, or faces attempts to be silenced by other agents. And whether such approaches reduce the risk of other swarm attacks to go unnoticed by humans.

Technical feature progress:

- Network containment: Blocked direct internet access, routed permitted connections through a controlled relay, and added an isolation check that blocks unsafe runs.
- Generational swarm runner: impossible tasks loop under tight deadlines.
- ExploitGym port: Made the benchmark run without administrator access using rootless Docker.
- Shared message board: Built a package similar to Artifactory from the original HF break-in that agents could abuse as a forum tool.
- Spy agent: Created a Spy agent.
- Protected inbox: Built a write-only reporting endpoint with sender identity determined so the Spy agent can communicate with the human reliably.
- Analysis + reproduction tooling: extensive logging system.

Results

Under the no-win deadline, agents turned to the illegal forum in order to try to find an answer for the task rather than solving the task themselves. Control agents, never told to cheat, probed the board for a staged flag to grab before concluding it was a decoy. The agents we explicitly instructed to cheat went further: they fabricated flags, announced themselves on the board, posted explicit requests for a flag, and waited for others to supply one. (We made the answer-sharing channel considerably easier to find because of budget constraints.) Against all of this the spy behaved as we'd hoped: in every generation it reported the coordination to us. It was also the one agent that didn't reach for the shared answers. Because its role stayed hidden, no one could single it out to silence it. The agents did discover and attack its reporting inbox, but they couldn't break it. The experiment showed both how agents reach for answer-sharing under pressure and how an embedded reporter can make that coordination visible to humans while resisting attempts to shut it down. We feel that this direction is promising as having a Spy agent seems to turn an agent into a much more reliable and moral one and we want to continue exploring this in more complex scenarios with a larger swarm.

Data

Data for our findings can be found here: https://github.com/marimeireles/evidence-of-swarm
