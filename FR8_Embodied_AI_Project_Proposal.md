# FR8 funding conversation: a practical embodied-AI project

**Prepared for:** FR8, Helsinki
**Team:** High-school robotics club, about 10 core members, mostly age 17
**Funding ceiling assumed:** €10,000 total, including VAT and shipping
**Planning assumption:** Several months to build and about three hours per day are available. Vendor prices below are budget allowances, not quotations.

## The ask in one sentence

We are asking for **up to €10,000** to build a repeatable, low-cost robot-learning lab, with most of the funding going to the robot, actuators, fabrication, sensors, and spare mechanical parts—not a premium computer. The setup can support the recommended tabletop imitation-learning project or, if the team and an adult mentor agree that the mechanical risk is manageable, a small quadruped gait-learning project.

## 60-second pitch

> We want to build a real robot and answer a measurable embodied-AI question, not spend the grant on a computer alone. Our leading project is a small robot arm learning pick-and-place from demonstrations; a gait-learning project for a small quadruped is an exciting alternative if we can manage its mechanical and safety risks. We would put most of the budget into the robot, actuators, sensing, fabrication, and spares, with one shared workstation sized to the actual compute need. We will compare the learned behavior with a clear baseline, publish the results and failures, and only buy what we can justify with quotes.

## Recommended project: Demo2Sort

**Working title:** *Demo2Sort: How much demonstration data does a small robot need to learn a visual pick-and-place task?*

**Research question:** How do the amount and variety of human demonstrations affect a low-cost robot arm's success rate on object positions it has not encountered during training?

**First demonstrator:** A table-mounted arm uses a camera to pick up a light object and put it in a marked tray. Start with one object and a fixed setup; gradually vary the object's position, orientation, and appearance. Keep the task safe, slow, and physically constrained.

**Learning loop:**

1. Assemble and calibrate a leader-follower arm. A student teleoperates the leader; the follower performs the same motions.
2. Record camera images, joint positions, and actions as demonstrations. Begin with a small pilot dataset, then expand it based on observed learning curves.
3. Train a simple behavior-cloning baseline first. If the pipeline is stable, compare it with a visuomotor policy such as Diffusion Policy.
4. Compare the learned policy with a scripted baseline on held-out object positions. Report success rate, task time, human interventions, and common failure types.
5. Only after the real-robot task works, use simulation to test whether randomized simulated scenes can help with new positions or lighting.

**Evaluation:** Freeze the test layouts before the final evaluation. Run at least 20 attempts per condition, report the raw counts and success rates, and show representative failures. Compare policies trained with increasing numbers of demonstrations (for example, 10, 25, 50, then more if needed). Do not promise a particular success rate in advance.

**Why it fits this team:** The main research challenge is data, sensing, control, and measurement—not designing a complex walking mechanism. The math-minded members can lead experimental design and analysis; the coding-strong members can own the data and learning pipeline; mechanical work stays focused on a compact arm, fixtures, and reliable calibration.

**Stretch goal, not the funded deliverable:** Add a playful gorilla-inspired appearance or soft gripper for the final demonstration. Do not promise a walking gorilla or a biomimetic quadruped. A cosmetic theme is achievable; a dynamically walking robot is a separate, mechanically demanding project.

### Gait-learning alternative: a small, safe quadruped

Gait research is a genuine interest for the team, not just a visual theme. If the team chooses this direction, the project question could be: **Can a compact locomotion policy trained or tuned in simulation produce a more repeatable low-speed walk on the real robot than a hand-designed baseline?** Begin with a mechanically robust, low-speed quadruped on a flat surface; use a safety tether or support frame during early tests. First get reliable actuators, joint calibration, and a conventional baseline gait working. Then the ML work can compare a learned policy with that baseline on repeatability and recovery from small, controlled disturbances. Do not promise dynamic running, jumping, or a full gorilla robot.

One team member is particularly interested in contributing to the ML for this direction. A suitable role would be to lead the gait-learning experiment: define the baseline and metrics, help implement or tune a small policy, analyze simulation-versus-real results, and document failures. Mechanical design remains a team responsibility; the goal is to make the hardware a serious, supported part of the project rather than assume that code can compensate for an unreliable mechanism.

This is a higher-risk alternative than the arm project. Before committing, the team should confirm that a mentor can help with actuator sizing, power, structural design, and safe testing. If those supports or a credible platform quote are unavailable, keep gait as a later phase and complete Demo2Sort first.

## How this project was selected from the README

The README is a research bibliography and resource list, not a set of ready-to-build kits. Use it as a funnel:

1. **Start from the team's interests:** imitation learning, hands-on sensing, and embodied robots.
2. **Search the relevant sections:** [Embodied Agent](./README.md#agent), [Sim-to-Real Adaptation](./README.md#sim-to-real), [Embodied Simulators](./README.md#simulators), and, when needed, [Embodied Perception](./README.md#perception).
3. **Keep works that offer a usable next step:** a clearly described task, a reproducible method or open project, and a measurable outcome. A paper that only describes a large, custom-built robot is inspiration, not a build plan.
4. **Score each candidate from 1 to 5** on five equally weighted tests: match to team interests; mechanical feasibility for a novice team; affordability; chance of a credible demo in a few months; and quality of measurable evaluation. Treat a score as a discussion aid, not proof.
5. **Reject projects whose critical path depends on expensive custom hardware, specialist fabrication, or training a frontier-scale model.** Shortlist the rest, then shrink each to a first experiment that can be attempted with one robot and one table.

| Candidate from the README | Why it is relevant | Fit for this team |
|---|---|---|
| **Teleoperated tabletop manipulation and imitation learning — recommended.** *Diffusion Policy: Visuomotor Policy Learning via Action Diffusion*; *Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware*; *Data Scaling Laws in Imitation Learning for Robotic Manipulation*. | Direct link between demonstrations, sensor observations, learned actions, and a repeatable task. Start with a single arm and a simple baseline; do not attempt to reproduce a research lab's full hardware setup. | **Best fit.** High AI and math content, modest mechanical scope, and outcomes that can be measured. |
| **Mobile whole-body imitation learning.** *Mobile ALOHA: Learning Bimanual Mobile Manipulation with Low-Cost Whole-Body Teleoperation*. | A strong example of learning from human teleoperation, but its mobile, bimanual system and task scope are much larger than this club's first build. | **Good inspiration, poor first build.** Could be a later extension after the arm works. |
| **Learned quadruped locomotion.** *Lifelike agility and play in quadrupedal robots using reinforcement learning and generative pre-trained models*; *DeepGait: Planning and Control of Quadrupedal Gaits using Deep Reinforcement Learning*. | Closest to the team's bionic-robot excitement and a genuine embodied-learning topic. | **Exciting higher-risk alternative.** The team's ML lead could study policy learning and sim-to-real transfer, while the team invests substantially in the physical robot, actuators, fixtures, and safe testing. Choose only with mechanical mentorship and a feasible platform quote. |
| **Humanoid shadowing or foundation-model control.** *HumanPlus: Humanoid Shadowing and Imitation from Humans*; *GR00T N1: An Open Foundation Model for Generalist Humanoid Robots*. | Ambitious examples of human imitation and generalist robot policies. | **Use as future inspiration only.** A student club cannot credibly reproduce the humanoid hardware and data infrastructure in this grant. |

### README references to discuss

- [Diffusion Policy](https://arxiv.org/abs/2303.04137) — an advanced visuomotor imitation-learning method; use it as an extension after simpler behavior cloning works.
- [Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware](https://openreview.net/forum?id=e8Eu1lqLaf) — supports the idea that hardware and data collection can be designed around a practical learning question.
- [Data Scaling Laws in Imitation Learning for Robotic Manipulation](https://arxiv.org/abs/2410.18647) — directly motivates measuring how performance changes with demonstration count.
- [Mobile ALOHA](https://mobile-aloha.github.io/) — a compelling teleoperation and imitation-learning project to learn from, not a realistic first hardware replica.
- [NVIDIA Isaac Sim](https://developer.nvidia.com/isaac/sim) and [Isaac Lab](https://arxiv.org/abs/2511.04831) — simulator resources for a later simulation and transfer milestone.
- [HumanPlus](https://arxiv.org/abs/2406.10454) and [GR00T N1](https://arxiv.org/abs/2503.14734) — useful context for humanoid imitation, but not requirements for this project.

## Budget request

These are **planning allowances, not current vendor quotes**. Every line includes an allowance for VAT and delivery. Before ordering, get written quotes, check the grant's purchasing rules, and confirm what the school can store and supervise. Do not spend a line just because it is budgeted.

| Item | Allowance | What it enables |
|---|---:|---|
| Core robot platform | €2,600 | Choose **one**: a documented leader-follower tabletop arm, or the chassis, frame, joints, and core actuators for a small low-speed quadruped. Do not buy both platforms. Obtain a real bill of materials before deciding. |
| Mechanical iteration: printer and materials | €1,000 | Entry-level 3D printer, starter spares, filament, and fixtures. Buy the printer only if regular access to the two remote printers is impractical and a supervised location is available. |
| Actuators, power, and replacement parts | €1,200 | Servos or actuators, power supplies, wiring, connectors, fasteners, and spares. For the gait option, prioritize reliable joints and replacements over cosmetic parts. |
| Tools, mounting, and safety | €700 | Basic tools, stable mounts, cable management, protective covers, and an appropriate physical power cut-off. |
| Cameras, sensors, and lighting | €600 | Task-appropriate cameras or sensors, lighting, and rigid mounts for repeatable observations. |
| Shared desktop workstation | €3,000 | A shared desktop sized to the selected workflow, with a strong NVIDIA GPU, at least 64 GB RAM if affordable, and suitable storage. Verify current quotes and the simulator compatibility checker before purchase; do not assume this allowance guarantees an RTX 5080 system. |
| Supervised workshop or fabrication support | €300 | Occasional expert advice or fabrication if eligible, especially useful for a gait project's actuator and structural design. If ineligible, seek approval to move funds to mechanical parts. |
| Cloud compute and data backup | €200 | Capped backup or occasional GPU access only if local resources are insufficient. Use free tools first; this is not a Claude/LLM budget. |
| Controlled contingency | €400 | A 4% buffer for shipping variation, replacement parts, or quote changes. Unused funds remain unspent or are handled under grant rules. |
| **Maximum request** | **€10,000** | **A complete, usable learning setup, not a promise to exhaust every euro.** |

**Mechanical-first allocation:** €6,400 (64%) is allocated to the robot platform, fabrication, actuators, tools/safety, sensors, and workshop support. The workstation, cloud, and contingency together are €3,600 (36%). These are maximum planning allowances; obtain quotes and adjust before ordering.

### Is an expensive computer a good use of the funding?

**Do not make the laptop the centerpiece of the request.** A shared desktop is easier to cool and upgrade, and the project happens at a fixed bench. First check whether the school or team can lend a suitable computer; buy a new workstation only if that check and real quotes justify it. A laptop is worth considering only if portability is essential and its *mobile GPU* meets the simulator requirement; laptop GPU names do not guarantee desktop-equivalent capability.

NVIDIA's current [Isaac Sim system requirements](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html) list an RTX 4080 with 16 GB VRAM as the minimum GPU tier and an RTX 5080 with 16 GB as the "Good" tier; 64 GB RAM is listed as good. Those specifications may exceed the €3,000 workstation allowance depending on local prices. Get quotes before promising Isaac Sim; if the price does not fit, use an existing school workstation, a suitable hosted option, or an alternative simulator rather than taking money away from essential robot mechanics without discussion.

Isaac Sim is **not required to answer the core research question**. Start with the physical arm, data collection, and a simple learning baseline. Add simulation only when it addresses a specific need, such as testing varied object positions without risking hardware. If no compatible workstation is available and the actual quotes exceed the allowance, ask FR8 whether a cloud workstation or revised scope is acceptable rather than silently buying unsuitable hardware.

The team's Claude subscription is not a substitute for GPU compute, and this project does not need to train a large language model. Use open-source robotics and ML tools, local computation, and free documentation first; use the cloud allowance only for a demonstrated bottleneck.

### Why request up to €10,000?

The request is for an integrated, reusable capability rather than a computer-led purchase: most funds are assigned to a robot platform, actuators, fabrication, sensing, safety, and spares, with compute kept to a shared-workstation allowance. A smaller award should first preserve a safe, mechanically reliable platform and reduce optional compute, printer, or stretch-goal spending. Be candid that the exact shopping list is provisional until quotations and equipment access are checked; **do not claim every euro is indispensable if FR8 offers a smaller grant.**

## Build plan and evidence of progress

| Phase | Approximate timing | Evidence to show |
|---|---|---|
| Define and de-risk | Weeks 1–2 | Confirm machine access; freeze the first task; obtain quotes; test the arm and camera; write the safety and data-recording plan. |
| Make the robot repeatable | Weeks 3–4 | Assemble and calibrate the arm; complete manual teleoperation; record and replay a short demonstration; establish a scripted baseline. |
| Collect and learn | Weeks 5–8 | Record a pilot dataset; train behavior cloning; track training data, held-out layouts, success, and failures; add demonstrations where data shows they are needed. |
| Test generalization | Weeks 9–11 | Run the pre-defined evaluation; compare scripted and learned policies; vary object location and, if reliable, appearance or lighting. |
| Demonstrate and hand over | Weeks 12–14 | Live demo, short video, dataset and code, results chart, failure analysis, build notes, and next-step proposal. |

**Team roles (adapt as needed):** 2 members on CAD/assembly and fixtures; 2 on cameras, sensors, and calibration; 3 on data, control, and learning code; 1 on test design and statistics; 1 on safety and integration; 1 on documentation, demo, and budget tracking. For the gait option, the member interested in ML can lead the gait-learning experiment and analysis, paired with the mechanical and safety leads. Pair people across roles so that knowledge is not trapped with one member.

**If the team selects gait learning:** agree on the baseline and measurements before training; the ML lead should not be expected to solve unreliable mechanical or electrical hardware in software.

**Success means:** the team can repeat a complete demonstration-to-policy workflow; the learned policy is compared fairly with a baseline on held-out cases; results include failures rather than only the best video; and another student can reproduce the setup from the documentation. A high success rate is a goal, not a funding promise.

## 30-minute conversation outline

1. **Minutes 0–3 — The question:** Can a small robot learn a useful manipulation skill from demonstrations, and how much data does it take?
2. **Minutes 3–8 — The demo:** Show a short clip or sketch of teleoperation, learned pick-and-place, and what counts as an unseen test position.
3. **Minutes 8–14 — The plan:** Explain the simple arm, camera, data, baseline, and staged evaluation. Be explicit that the first project is not a walking humanoid.
4. **Minutes 14–20 — The team:** Explain why the project matches the team's strengths and how mechanical work is constrained and shared.
5. **Minutes 20–25 — The budget:** Walk through the €10,000 cap, especially the workstation requirements, arm, rapid prototyping, sensors, spares, and the fact that unspent contingency is not automatically spent.
6. **Minutes 25–30 — Questions and next steps:** Ask about eligibility, deadlines, purchasing, school supervision, and whether FR8 expects a written application or follow-up demonstration.

**If asked "Why not build the gorilla robot?":** "We love that idea, but a walking animal-inspired robot would make custom mechanics and balance the critical path. We want our first grant to produce a real learning result. We can give the arm a gorilla-inspired look as a stretch, then use what we learn to scope a legged project responsibly."

**If asked "Why do you need Isaac Sim?":** "It is an optional tool for safe, repeatable simulation experiments, not a prerequisite for the first result. The computer request is based on NVIDIA's published minimum and good-tier hardware for Isaac Sim. We will verify that our own computers cannot meet the need before buying."

**If asked "What if the policy fails?":** "We will still have an evaluated dataset, a scripted baseline, repeatable hardware, and documented failure cases. Those let us distinguish a mechanical or sensing problem from a learning problem and make a better next attempt."

## Questions to ask FR8

- Is €10,000 a hard maximum, a target amount, or a discretionary award? Is VAT eligible?
- Which purchases and services are eligible, and are multiple quotations or named suppliers required?
- Can funds cover a shared computer, 3D printer, replacement parts, cloud credits, workshop access, or supervision?
- Who owns equipment bought with the award, and where must it be stored?
- What safety, insurance, adult-supervision, or school-approval requirements apply to powered robots and a 3D printer?
- What are the decision date, spending deadline, reporting requirements, and expected next check-in?

## Sources

- Team's starting point: [Embodied AI Paper List](./README.md), especially its [simulator](./README.md#simulators), [agent](./README.md#agent), and [sim-to-real](./README.md#sim-to-real) sections.
- NVIDIA, [Isaac Sim system requirements](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html) and [Isaac Sim](https://developer.nvidia.com/isaac/sim).
- Diffusion Policy authors, [project page](https://diffusion-policy.cs.columbia.edu/).
- Mobile ALOHA authors, [project page](https://mobile-aloha.github.io/).
- Hugging Face LeRobot, [SO follower robot code](https://github.com/huggingface/lerobot/tree/main/src/lerobot/robots/so_follower). Confirm the current assembly guide and bill of materials before selecting hardware.
