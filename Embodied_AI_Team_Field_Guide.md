# Embodied AI team field guide

A practical introduction for a high-school robotics team: what robot-learning terms mean, how to turn a paper into an experiment, and how to keep the mechanical robot and learning system working together.

**Who this is for:** A team of about ten students with mixed experience: some strong coders, a math-minded prospective ML lead, sensor interest, VEX experience, and less experience designing mechanisms.

**How to use it:** Read Sections 1–7 together, then use the project-specific section for the project you choose. This is a working primer, not a claim to cover all of robotics or machine learning.

## 1. What is embodied AI?

An embodied AI system senses the physical or simulated world and acts on it. It is not just a chatbot that talks about a robot. A robot has a body with limits: its joints move at finite speeds, its motors have limited force, sensors are noisy, objects slip, and actions take time.

A useful mental model is a loop:

1. **Sense:** cameras, joint encoders, touch sensors, IMUs, or other sensors produce readings.
2. **Estimate:** software turns those readings into an estimate of what the robot and environment are doing.
3. **Choose:** a controller or learned policy decides what to do next.
4. **Act:** motor drivers and actuators try to carry out that command.
5. **Observe the result:** the next sensor readings reveal what actually happened.

Because actions change future observations, a robot must act in a feedback loop. This is why a good-looking model prediction is not enough: it has to work repeatedly on a physical robot.

## 2. What is a policy?

In robotics and reinforcement learning, a **policy** is a rule that maps information available to the robot to an action. It is often written as

`π(a | o)`

Read this as: “the policy π gives a distribution over actions `a`, given observation `o`.” A deterministic policy may instead be written `a = π(o)`.

For example:

- **Observation `o`:** a camera image, six joint-angle readings, and perhaps the estimated position of the foot.
- **Action `a`:** desired joint positions for the next control step.
- **Policy `π`:** code or a trained model that decides those joint targets from the observation.

A policy is not necessarily a neural network. A hand-written rule such as “if the foot is behind the hip, move it forward” can be a policy. A conventional controller can be a policy. A neural network trained from data can be a policy too.

### Nearby terms that are easy to mix up

| Term | Plain-language meaning |
|---|---|
| **Observation** | What the robot's software currently receives from sensors. |
| **State** | A description of the system that is sufficient, in principle, to predict what happens next. The true state is often not directly measurable. |
| **Action** | The command sent by the policy, such as a target angle, target velocity, or motor torque. |
| **Controller** | Software that turns goals or errors into actuator commands. A controller may be hand-designed, learned, or a combination. |
| **Policy** | The decision rule mapping observations (or states) to actions. Often used for the high-level behavior. |
| **Trajectory** | A time-ordered sequence of states, observations, and actions. One complete attempt is a **rollout** or **episode**. |
| **Reward** | A numerical score used by reinforcement learning to express which outcomes are desirable. It is not the same as a human demonstration. |
| **Model** | Can mean a learned neural network, or a mathematical/physics model of the robot. Ask which meaning is intended. |

### Policy versus low-level motor control

A learned policy should not have to directly solve every electrical and mechanical problem. A useful layered design is:

1. **Behavior/policy layer:** decides a task action, such as “move the foot forward” or “reach toward the object.”
2. **Motion/control layer:** converts that goal into smooth joint targets and corrects errors.
3. **Motor-driver layer:** safely handles the electrical commands and actuator limits.

For a first project, let a reliable low-level controller handle basic joint motion; investigate whether learning improves the behavior above it. This makes failures easier to diagnose.

## 3. Robot hardware: the pieces ML depends on

### Degrees of freedom and joints

A **degree of freedom (DOF)** is an independent way a mechanism can move. A simple arm with shoulder, elbow, and wrist rotation has multiple DOFs. A legged robot needs enough joint motion to place its feet and support its body. More DOFs can enable more behaviors, but also mean more cost, calibration, wiring, control work, and ways to fail.

### Actuators and torque

An **actuator** converts an electrical command into movement. A hobby servo, geared motor, and high-performance robot joint are not interchangeable. Check what command and feedback each provides.

- **Position control:** request a target joint angle.
- **Velocity control:** request a target joint speed.
- **Torque/effort control:** request a force-like output at the joint.
- **Feedback:** measured position, speed, current, or temperature returned by the actuator.

The robot must have enough torque and mechanical strength for the planned motion, including when a leg or arm is extended. A motor that can move an unloaded mechanism may stall or overheat under the real load. Use published ratings and adult/mentor engineering review; do not infer a safe payload from a marketing video.

### Sensors and calibration

- **Joint encoders** measure joint position; calibration relates their readings to the robot's physical pose.
- **IMU** measures acceleration and rotation; it helps estimate body motion and tilt.
- **Camera** gives visual information but may be affected by lighting, blur, occlusion, and viewpoint.
- **Force/touch sensing** can reveal contact, but adds integration and calibration work.
- **Time synchronization** matters: an image paired with the wrong joint reading can teach the wrong relationship.

Record units, coordinate conventions, sensor rate, actuator limits, and timestamp behavior. A dataset with unclear units or stale sensor data can look like an ML problem when it is actually a measurement problem.

### Mechanics is part of the intelligence

The body determines which actions are possible and how forgiving the robot is. Geometry, mass distribution, friction, compliance, backlash, and structural flex all affect control. A robust, well-calibrated robot with a simple controller is a better research platform than a complicated robot whose joints behave unpredictably.

That is why mechanical design and ML are partners: the ML cannot learn a stable gait if the actuator command is unreliable, and the mechanism can be improved using evidence from repeated failures.

## 4. Three ways to get robot behavior

### A. Hand-designed control

An engineer writes a rule or controller. For example, a **gait oscillator** can move legs in a repeating pattern, with a chosen timing and phase difference between legs. This is a useful first baseline because it is understandable and does not need a training dataset.

### B. Imitation learning

The robot learns from examples of desired behavior. A person teleoperates the robot or demonstrates actions; the system records observations and actions. A simple approach called **behavior cloning** trains a model to predict the demonstrated action from the observation:

`observation → learned action`

In supervised-learning language, each example is an input and a target label. Behavior cloning is often a sensible first learning project because the team can collect its own data and see the whole pipeline.

**Common trap: distribution shift.** During training, the robot may see perfect demonstrations. During deployment it makes a small error, arrives in a state absent from the demonstrations, and then makes a larger error. Collect examples that cover variation, test on held-out conditions, and consider carefully supervised corrections. More data is not automatically better if it is mislabeled or inconsistent.

### C. Reinforcement learning (RL)

In RL, an agent tries actions and receives rewards. A training loop may look like:

1. Observe the current situation.
2. Choose an action.
3. Apply it in simulation or on a robot.
4. Measure the next observation and reward.
5. Update the policy to improve expected future reward.

RL can discover behavior that was not directly demonstrated, but it usually needs many trials, a carefully designed reward, reliable resets, and a safe environment. It can exploit a poorly designed reward in unintended ways. It is not simply “add AI and let the robot figure out walking.”

For a high-school first project, begin with a conventional baseline or imitation learning. If RL is the goal, train and debug in simulation first and keep physical tests bounded, supervised, and slow. The physical robot should not be used for uncontrolled trial-and-error.

### D. Hybrid systems

Real systems often mix the approaches: a learned policy proposes actions, while a conventional controller tracks them and safety limits prevent dangerous commands. A hybrid baseline can be more reliable and easier to explain than a neural network controlling every motor directly.

## 5. Gait learning: what the exciting problem contains

A **gait** is a repeating pattern of leg motion and ground contact. Walking is not just moving four legs in a pattern: the body must remain supported while feet alternately push against the ground, and the controller must respond when timing or contact differs from the plan.

Useful concepts:

- **Stance phase:** a foot is on the ground and helps support or propel the body.
- **Swing phase:** a foot is lifted and moved to a new location.
- **Gait period/frequency:** how long one repeating cycle takes / how many cycles happen per second.
- **Phase offset:** where each leg is in its cycle relative to the others.
- **Contact:** whether a foot is actually touching the ground; the estimate may be noisy or unavailable.
- **Stability:** whether the robot can keep its body from falling, including as support changes between feet.
- **Sim-to-real gap:** differences between a simulated robot and the real one: friction, delays, mass, compliance, actuator behavior, and sensor noise.

### A responsible gait-learning ladder

1. **Verify the hardware by itself:** inspect joints, check limits and feedback, calibrate, and test one joint at a time under supervision.
2. **Measure the body:** record mass, joint geometry, center of mass estimate, and actuator behavior. Check for overheating, looseness, and repeatability.
3. **Build a safe conventional baseline:** test supported, low-speed movements and a simple gait only after the hardware and control are reliable.
4. **Define the experiment:** for example, compare repeatability of a fixed hand-designed gait with a learned policy at one low walking speed.
5. **Use simulation if useful:** first match the robot's geometry and masses approximately, then compare simulated and real motion. Simulation is a tool, not proof of real-world success.
6. **Introduce learning gradually:** a policy might adjust a stable gait's timing or foot targets, rather than inventing arbitrary motor torques from scratch.
7. **Evaluate and report:** use a safe surface and support appropriate to the robot. Record completed steps, falls or interventions, speed, and repeatability; show failures too.

Do not begin with jumps, running, stairs, or a promise to reproduce a research lab's quadruped. A small project can make a real contribution by testing one carefully bounded question.

## 6. Simulation and computers

A simulator runs a virtual robot and environment. It can help test code, repeat experiments, and generate training trials without wearing out hardware. But a simulation result is only evidence about that simulated setup. The real robot has imperfections the simulator may omit.

**Domain randomization** deliberately varies simulated properties such as friction, mass, or lighting during training, to encourage a policy that is less dependent on one exact virtual world. It can help with transfer but does not guarantee it.

**Isaac Sim is one option, not a project requirement.** Its official requirements can be demanding. Check the [live Isaac Sim requirements](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html), the precise GPU/VRAM, memory and OS requirements, and compatibility before buying. A shared workstation may be more practical than a laptop for a fixed workshop. Start with the robot and a learning baseline; only buy high-end compute when it unlocks a specific experiment that cannot reasonably be done on school computers, a lighter simulator, or occasional hosted compute.

Also distinguish:

- **Training:** computing updates to the policy from data or simulated experience.
- **Inference:** running the finished policy to choose actions. This can need much less compute than training.
- **Simulation rendering:** drawing realistic images, which may be demanding even if the learned model is small.

You do not need to train a giant language model to do embodied AI. For this team's questions, small policies, good sensors, reliable mechanics, and careful data can matter much more than model size.

## 7. How to make a credible experiment

### Write a question that can be answered

Weak: “Can we make an AI robot?”

Better: “Does a policy trained from demonstrations complete pick-and-place at unseen positions more often than our scripted baseline?”

For gait: “At a fixed low speed and on one surface, does the learned policy complete more repeatable steps than the fixed baseline without more interventions?”

### Choose the baseline first

A **baseline** is a simple reference that gives the result meaning. For manipulation, compare against a simple scripted controller or the leader-follower motion. For gait, compare against a hand-designed gait. If the learned system does not beat the baseline, the experiment still tells you something—but you need the baseline to interpret it.

### Separate training from testing

- Use training examples to fit the policy.
- Use validation examples to make development choices.
- Hold back test positions or trials until the final comparison.

If you repeatedly tune on the “test” layout, it is no longer an independent test. Keep notes on every change.

### Define success before running

Write an objective definition, such as “object fully inside the marked tray without a person touching the robot” or “the robot completes ten consecutive supported steps without a fall or human intervention.” Count partial successes and interventions too. Show the number of trials along with percentages; small samples produce uncertain estimates.

### Keep a useful experiment log

For every run record:

- date, operator, robot configuration, software/model version;
- battery state, surface, lighting, and test setup;
- training dataset version and policy settings;
- trial result, time, interventions, and failure category;
- unexpected changes, repairs, and safety stops.

Save raw data where possible. Do not silently discard bad trials; mark why a trial was invalid and report that decision.

### Diagnose failures systematically

When it fails, ask in order:

1. Did the sensor measure the right thing, at the right time, in the right units?
2. Did the controller or actuator execute the requested action?
3. Is the mechanical structure loose, flexing, slipping, or overloaded?
4. Was the situation represented in the training data or simulation?
5. Is the objective/reward or policy actually suited to the goal?

Avoid the reflex “the AI needs to be bigger.” Often the fix is better calibration, a sturdier mount, or more representative data.

## 8. Suggested work split for a ten-person club

Make small cross-functional pairs; everyone should understand the full robot loop.

- **Mechanical pair:** CAD, structure, feet/gripper, mounts, serviceability, and parts list.
- **Actuation/control pair:** motor interfaces, calibration, low-level commands, limits, and safe stop.
- **Sensors/data pair:** camera/IMU/encoder setup, timestamps, data recording, and dataset checks.
- **Learning pair:** simple baseline, behavior cloning or gait-policy experiment, reproducible training, and evaluation.
- **Experiment/safety pair:** test protocol, trial logging, risk assessment, supervision, and results.
- **Integration/documentation lead:** interfaces, version tracking, purchasing, demo, and making sure every subsystem works together.

These are responsibilities, not silos. Pair a strong coder with someone learning the code; pair the ML lead with mechanical teammates so the algorithm reflects what the body can really do.

## 9. A first-month learning path

### Week 1: vocabulary and observation

Read this guide together. Draw the robot loop. Identify which signals are observations, actions, state estimates, and measurements on the robot you might buy or build. Read the chosen hardware's API and safety documentation before choosing a model.

### Week 2: one joint, one sensor, one command

Under adult supervision, get one joint or actuator to move a small, bounded amount. Read the feedback. Check units, direction, limits, timestamps, and emergency stop. Do not begin ML yet.

### Week 3: repeatability and baseline

Run the same motion repeatedly. Plot commanded versus measured position, or for a walking platform first test supported posture and one leg/foot movement. Quantify variation and log failures.

### Week 4: data and first policy

For the arm, record a small number of teleoperated examples and train a simple behavior-cloning model or replay baseline. For gait, start with the safe hand-designed baseline and a simulation model if available; only move to a learned gait after the hardware/control prerequisites are met.

At the end of the month, present: the research question, a working sensor-to-action loop, the baseline, a dataset or test log, one graph, one failure analysis, and the next experiment. That is more convincing than a complicated demo with no measurements.

## 10. Safety and responsible engineering

Robots can pinch, collide, tip, drop parts, overheat, or move unexpectedly. High-school teams should have an adult supervisor approve the setup, risk assessment, test area, and operating procedure. Follow the manufacturer's instructions and school rules.

- Keep people outside the robot's movement envelope during powered tests.
- Use an accessible emergency stop or power-isolation method, appropriate to the system.
- Start with reduced speed/force where supported, a clear area, and a spotter.
- Secure the robot and test surface; control loose cables and objects.
- Inspect mounts, fasteners, wiring, and batteries before runs.
- Stop for abnormal heat, sound, vibration, damage, or unpredictable movement.
- Keep batteries and charging under the required supervision; do not improvise power wiring.
- Never rely on a software policy alone as the safety system.

## 11. Glossary

| Word | Meaning |
|---|---|
| **Action space** | The set and range of commands a policy can send. |
| **Behavior cloning** | Supervised learning from example observation-action pairs. |
| **Closed loop** | Repeatedly sense, act, and use new measurements to adjust behavior. |
| **Control frequency** | How often the controller updates its action, usually measured in hertz. Higher is not automatically better; latency and actuator capability matter. |
| **Episode** | One complete trial, from reset to success, failure, or timeout. |
| **Generalization** | Working in conditions different from the training examples. |
| **Inference** | Using a trained model to produce an output, such as a robot action. |
| **Latency** | Delay between sensing, deciding, and acting. |
| **Observation space** | The data available to a policy, such as images and joint values. |
| **Overfitting** | Performing well on training examples but poorly on new cases. |
| **Proprioception** | Sensing the robot's own body, such as joint positions or body orientation. |
| **Reward shaping** | Designing intermediate reward signals to guide RL; poor shaping can reward the wrong behavior. |
| **Sim-to-real transfer** | Applying a method or policy developed in simulation to a physical robot. |
| **Teleoperation** | A human directly controls a robot, often to collect demonstrations. |
| **Torque** | A rotational force at a joint. It is not the same as speed or payload. |

## 12. Reading path from the team's paper list

The team's [Embodied AI Paper List](./README.md) is a bibliography, not a step-by-step textbook. Read a paper to answer a focused question; do not try to read the entire list before building anything.

1. **Start with a survey:** [Dexterous Manipulation through Imitation Learning: A Survey](https://arxiv.org/abs/2504.03515) for imitation-learning concepts, or the [Embodied AI survey](https://arxiv.org/abs/2407.06886) for a broad map.
2. **Understand visuomotor imitation:** [Diffusion Policy](https://arxiv.org/abs/2303.04137). It is an advanced method; learn the task and simpler baselines before attempting to reproduce it.
3. **Think about demonstrations and data volume:** [Data Scaling Laws in Imitation Learning for Robotic Manipulation](https://arxiv.org/abs/2410.18647).
4. **For a low-cost hardware perspective:** [Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware](https://openreview.net/forum?id=e8Eu1lqLaf). The paper's system is a reference, not a parts list.
5. **For gait:** [DeepGait](https://arxiv.org/abs/1909.08399), then [MIT Cheetah 3: Design and Control of a Robust, Dynamic Quadruped Robot](https://dspace.mit.edu/bitstream/handle/1721.1/126619/iros.pdf?sequence=2). Treat these as research examples; their hardware and engineering resources exceed a beginner club's.
6. **For simulation:** check the [Isaac Sim requirements](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html) and select a simulator only after defining what experiment needs it.

### Questions to ask when reading a robotics paper

- What is the robot, and which hardware details are essential?
- What exactly does the policy observe and command?
- Is the method imitation learning, RL, conventional control, or a combination?
- What is the baseline? What counts as a success or failure?
- How much training data or simulated experience was used?
- Was it tested in simulation, on a physical robot, or both?
- What assumptions, specialist equipment, or safety infrastructure would we need to reproduce it?
- What is the smallest experiment our team can run that tests one idea from the paper?

## The one-minute summary

**Policy** means “the rule that chooses actions from observations.” Embodied AI joins that rule to a real body, sensors, actuators, and a feedback loop. Begin with dependable mechanics and a simple baseline. Learn from demonstrations before taking on complex RL. For gait, make the robot's joints and low-speed baseline reliable before training a policy. Measure performance on conditions the system did not train on, log failures, and treat safety and mechanical design as core parts of the intelligence.
