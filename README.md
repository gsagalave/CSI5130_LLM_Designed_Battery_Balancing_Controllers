# LLM-Designed Battery Balancing Controllers Using a Validated Digital Twin

CSI-4130/5130 Artificial Intelligence - Course Project (Fall 2026)
Author: Gaurav Agalave

Status: In development. This README serves as the project proposal and will be updated as the work progresses.

---

## 1. Real-World Problem

Battery packs in electric vehicles and grid storage are made of many lithium-ion cells connected in series. Over time, the cells drift apart in charge because of small differences in manufacturing, temperature and aging. The pack can only be used until its weakest cell runs out: charging stops when the fullest cell is full, and discharging stops when the emptiest cell is empty. Because of this, imbalance:

- reduces usable capacity and driving range,
- makes some cells wear out faster, which shortens pack life,
- increases safety risks such as overcharging and overheating.

Balancing controllers fix this by moving energy between cells, but designing them takes a lot of manual work and involves trade-offs. I saw this directly in my ECE 5640 project:

| What I found in my earlier project | Why it matters |
| --- | --- |
| The simple rule-based controller never finished balancing, because it kept switching on and off around its threshold | Simple rules can fail |
| The NNLS controller balanced faster and produced 67% less heat in the cells, but lost more total energy (7,288 J vs 5,759 J) | Better methods still trade one goal against another |
| Controller settings (gain, current limit, threshold, converter efficiency) were picked by hand | Tuning takes expert time |
| Only one scenario was tested (4 cells, one starting condition) | We don't know if the controllers work in other situations |
| The Simulink model was too slow to run many times | Testing many designs is not practical |

In industry, the balancing controller has to be redesigned and retuned by specialists for every new battery pack or vehicle.

## 2. Research Question

Can a large language model (LLM), working in a loop with a battery simulator, design balancing controllers that perform better than expert-designed ones in speed, energy efficiency and cell stress, and explain its designs in a way engineers can understand?

## 3. Related Work

LLMs as optimizers and code writers. OPRO [1] showed that an LLM can improve solutions by looking at a scored list of past attempts. Eureka [2] and FunSearch [3] showed that an LLM can write code, test it with an evaluator, and improve it from the results. ReAct [4] and Reflexion [5] showed how LLM agents can use tools and learn from their own mistakes.

LLMs for control design. Bosio and Mueller [6] used LLM-guided search to write and evolve controllers as Python programs, tested in simulation on classic tasks such as pendulum swing-up. ControlAgent [7] and AgenticControl [8] use teams of LLM agents to tune controllers, mostly for simpler or linear systems. Cui et al. [9] and PE-GPT [10] apply LLMs to power electronics design. Purnananda et al. [11] use an LLM in an evolutionary loop to write Python policies for EV charging and vehicle-to-grid, tested in a simulator. They argue that reinforcement learning produces black-box policies that are hard to audit, while LLM-written policies are short and readable, and their best policies discovered behaviors such as hysteresis without being told to. Their work controls whole batteries, not the balance between cells.

LLMs in battery research. A recent review of LLMs in battery research [12] found that LLM work in battery management focuses on state estimation, safety warnings and charging optimization, and does not cover cell balancing. The review also describes LLM use in battery management as still limited. The closest examples it describes are an LLM that repeatedly improved a machine learning model for battery health prediction, and an LLM connected to battery physics models through an API, which is similar to the BMS Copilot in this project.

Battery cell balancing. Classical approaches include NNLS optimization [13] and model predictive control [14], [15]. Deep reinforcement learning has been applied to passive balancing with switched shunt resistors [16] and to active balancing [17]. The closest work to this project is Tavakol-Moghaddam and Boroushaki [17], who use multi-agent reinforcement learning for active balancing, treating each cell as an agent that controls its own DC-DC converter. Neural networks have also been used to choose balancing resistors in passive balancing [18]. None of these use LLMs, and the reinforcement learning controllers are neural networks that are hard for engineers to read and check.

Gap. I did not find any work that uses LLMs to design battery cell balancing controllers, checks them against a detailed circuit-level model, or studies the trade-offs between balancing speed, energy loss and heat. This project aims to fill that gap, producing controllers as short, readable programs.

## 4. Proposed Method

```
            +----------------------------+
  User ---> |  BMS Copilot (LLM agent)   |  <-- demo / explanation layer
            +-------------+--------------+
                          | tool calls
     +--------------------+---------------------+
     v                                          v
 Fast Python Simulator  <---- test design ----  LLM Controller Designer
 (checked against the                          (proposes and improves
  Simulink model)       ---- results back ---->  controllers)
     |
     v
 Final check of the best controllers in the full Simulink model
```

1. Fast Python simulator. I will build a simplified Python version of my Simulink battery model. It will be checked against the Simulink results (balancing time, energy loss, cell currents) so I can trust it. It ignores individual switching pulses and models only average currents, so each simulation should take well under a second instead of minutes. This makes it practical to test thousands of designs.
2. LLM controller designer (main contribution). This works in two modes:
   - Tuning mode: the LLM sees a list of settings it has tried and how well each one scored, and suggests better settings. This follows the OPRO method.
   - Code mode: the LLM writes a complete balancing controller in Python. The simulator tests it and sends back the results (balancing time, energy loss, cell heat, whether it finished, whether it broke any limits). The LLM uses this feedback to improve the next version. This follows Eureka, FunSearch, Bosio and Mueller, and Purnananda et al.
   - Safety check: every controller the LLM writes is limited to safe current values, and any controller that breaks battery limits is rejected and recorded.
3. BMS Copilot (demo). A chat assistant that can run simulations, compare results and make plots. It answers questions like "Why did the rule-based controller never finish balancing?" using real simulation results instead of guessing. This follows the ReAct method.
4. Final check. The best controllers designed by the LLM will be run in the original Simulink model to confirm the results.

## 5. Baselines (what I compare against)

- Rule-based controller (from my earlier project)
- NNLS optimal controller (from my earlier project; Turksoy and Teke, 2023)
- Random search and Bayesian optimization (Optuna), given the same number of simulations as the LLM
- Optional: a reinforcement learning agent (PPO), following prior reinforcement learning work on balancing [16], [17]

## 6. Data

- Simulation data created with the Python simulator and the Simulink model, covering:
  - different pack sizes (4, 8 and 12 cells)
  - random starting charge levels
  - cells with different capacity and internal resistance
  - with and without a load on the pack
- The scenarios used to design controllers are kept separate from the scenarios used to test them, so I can check whether the controllers work in new situations.
- A set of about 30-50 test questions with known answers, taken from simulations, to evaluate the Copilot.

## 7. Evaluation Plan

Numbers I will measure:
- Balancing time, total energy lost, heat in the cells, cell current, and how often balancing finishes successfully
- Trade-off plots comparing all methods across these goals
- How quickly each method finds a good controller (performance vs. number of simulations)
- How well the controllers work on new pack sizes and conditions
- How closely the Python simulator matches the Simulink model
- Effect of design choices: giving the LLM physics background or not, number of feedback rounds, tuning mode vs. code mode
- Comparison of several LLMs through OpenRouter (for example GPT, Claude, Gemini and an open-source model)
- Copilot: answer accuracy, how often it makes things up, response time and API cost

Things I will look at closely:
- What strategies the LLM came up with and whether they make physical sense
- Examples of how the LLM fixed failed designs (for example, the on/off switching problem)

## 8. How This Fits the Course Paths

| Course path | How this project covers it |
| --- | --- |
| Application development with LLM APIs | Yes - LLM controller designer and BMS Copilot |
| Comprehensive model evaluation | Yes - comparing several LLMs, prompt choices, and classical optimization methods |
| Novel algorithm development | Partly - LLMs have already been used to write and tune controllers for classic control tasks, power electronics and EV charging (see Related Work). To my knowledge, they have not been applied to battery cell balancing, or checked against a detailed Simulink model with trade-offs between speed, energy loss and heat. |
| Model fine-tuning | Future work |

## 9. Timeline

| Dates | Milestone |
| --- | --- |
| Oct 27 - Nov 3 | Set up repository; build Python simulator; check it against Simulink |
| Nov 4 - 10 | Build the LLM design loop (both modes); set up Optuna and random search |
| Nov 11 - 17 | Run main experiments, trade-off analysis, and LLM comparison |
| Nov 18 - 24 | Test on new scenarios, confirm best designs in Simulink, build Copilot demo |
| Nov 25 - 30 | Final report (PDF), video presentation, demo practice |

## 10. Repository Structure (planned)

```
twin/        # Python simulator and Simulink comparison
optimizer/   # LLM controller designer (both modes) and safety check
baselines/   # Rule-based, NNLS, random search, Optuna (and PPO)
agent/       # BMS Copilot tools and user interface
eval/        # Test scenarios, metrics, experiment scripts, results
report/      # Final report (PDF)
README.md
```

## 11. How to Run

To be added.

## 12. Relationship to Earlier Work

The Simulink battery model and the rule-based and NNLS controllers were built in ECE 5640 (Oakland University, 2026) by Gaurav Agalave, Martin Shoraji and Zhuocheng Han, under Dr. Jun Chen. In this project they are reused only as comparison baselines and as the detailed model for final checks.

New work for CSI-4130/5130: the Python simulator, the LLM controller designer, the safety check, the BMS Copilot, the optimization baselines, and all experiments and evaluation.

## 13. Citations and Acknowledgements

LLMs as optimizers and agents

1. C. Yang, X. Wang, Y. Lu, H. Liu, Q. V. Le, D. Zhou, and X. Chen, "Large language models as optimizers," in *Proc. Int. Conf. Learning Representations (ICLR)*, 2024. arXiv:2309.03409. https://arxiv.org/abs/2309.03409
2. Y. J. Ma, W. Liang, G. Wang, D.-A. Huang, O. Bastani, D. Jayaraman, Y. Zhu, L. Fan, and A. Anandkumar, "Eureka: Human-level reward design via coding large language models," in *Proc. Int. Conf. Learning Representations (ICLR)*, 2024. arXiv:2310.12931. https://arxiv.org/abs/2310.12931
3. B. Romera-Paredes *et al.*, "Mathematical discoveries from program search with large language models," *Nature*, vol. 625, pp. 468-475, 2024. https://doi.org/10.1038/s41586-023-06924-6
4. S. Yao, J. Zhao, D. Yu, N. Du, I. Shafran, K. Narasimhan, and Y. Cao, "ReAct: Synergizing reasoning and acting in language models," in *Proc. Int. Conf. Learning Representations (ICLR)*, 2023. arXiv:2210.03629. https://arxiv.org/abs/2210.03629
5. N. Shinn, F. Cassano, A. Gopinath, K. Narasimhan, and S. Yao, "Reflexion: Language agents with verbal reinforcement learning," in *Advances in Neural Information Processing Systems (NeurIPS)*, vol. 36, pp. 8634-8652, 2023. arXiv:2303.11366. https://arxiv.org/abs/2303.11366

LLMs for control design

6. C. Bosio and M. W. Mueller, "Synthesizing interpretable control policies through large language model guided search," in *Proc. American Control Conference (ACC)*, 2025, pp. 583-590. arXiv:2410.05406. https://arxiv.org/abs/2410.05406
7. X. Guo, D. Keivan, U. Syed, L. Qin, H. Zhang, G. Dullerud, P. Seiler, and B. Hu, "ControlAgent: Automating control system design via novel integration of LLM agents and domain expertise," arXiv:2410.19811, 2024. https://arxiv.org/abs/2410.19811
8. M. Narimani and S. A. Emami, "AgenticControl: An automated control design framework using large language models," arXiv:2506.19160, 2025. https://arxiv.org/abs/2506.19160
9. C. Cui, J. Liu, J. Feng, P. Hui, A. M. Y. M. Ghias, and C. Zhang, "Large language models based multi-agent framework for objective oriented control design in power electronics," arXiv:2406.12628, 2024. https://arxiv.org/abs/2406.12628
10. F. Lin *et al.*, "PE-GPT: A physics-informed interactive large language model for power converter modulation design," in *Proc. IEEE Energy Conversion Congress and Exposition (ECCE)*, 2024, pp. 1744-1747. arXiv:2403.14059. https://arxiv.org/abs/2403.14059
11. V. Purnananda, B. Wruck, and M. Guo, "Evolving LLM-derived control policies for residential EV charging and vehicle-to-grid energy optimization," in *Proc. Genetic and Evolutionary Computation Conference Companion (GECCO '26 Companion)*, San Jose, Costa Rica, 2026, pp. 661-664. https://doi.org/10.1145/3795101.3805325

LLMs in battery research

12. J. Chen, Y. Wang, D. Guo, *et al.*, "Advancing battery research through large language models: A review," *The Innovation*, vol. 7, no. 2, Art. no. 101091, 2026 (published online Sep. 2025). https://doi.org/10.1016/j.xinn.2025.101091

Battery balancing control

13. A. Turksoy and A. Teke, "A fast and energy-efficient nonnegative least square-based optimal active battery balancing control strategy for electric vehicle applications," *Energy*, vol. 262, Part A, Art. no. 125409, 2023. https://doi.org/10.1016/j.energy.2022.125409
14. J. Chen, A. Behal, Z. Li, and C. Li, "Active battery cell balancing by real time model predictive control for extending electric vehicle driving range," *IEEE Trans. Autom. Sci. Eng.*, vol. 21, no. 3, pp. 4003-4015, Jul. 2024. https://doi.org/10.1109/TASE.2023.3291679
15. L. McCurlie, M. Preindl, and A. Emadi, "Fast model predictive control for redistributive lithium-ion battery balancing," *IEEE Trans. Ind. Electron.*, vol. 64, no. 2, pp. 1350-1357, Feb. 2017. https://doi.org/10.1109/TIE.2016.2611488
16. Y. Tavakol-Moghaddam, M. Boroushaki, and M. Astaneh, "Reinforcement learning for battery energy management: A new balancing approach for Li-ion battery packs," *Results in Engineering*, vol. 23, Art. no. 102532, 2024. https://doi.org/10.1016/j.rineng.2024.102532
17. Y. Tavakol-Moghaddam and M. Boroushaki, "A multi-agent reinforcement learning approach for continuous battery cell-level balancing," *Results in Engineering*, vol. 26, Art. no. 104898, 2025. https://doi.org/10.1016/j.rineng.2025.104898
18. T. Duraisamy and D. Kaliyaperumal, "Machine learning-based optimal cell balancing mechanism for electric vehicle battery management system," *IEEE Access*, vol. 9, pp. 132846-132861, 2021. https://doi.org/10.1109/ACCESS.2021.3115255

Earlier work

19. G. Agalave, M. Shoraji, and Z. Han, "ECE 5640 Battery Management System Final Project," Oakland University, 2026. (Unpublished course report.)

Tools and libraries (to be updated): MATLAB/Simulink/Simscape, MATLAB Engine API for Python, NumPy, SciPy, Optuna, OpenRouter API, Gradio/Streamlit.

Acknowledgements: Thanks to Martin Shoraji and Zhuocheng Han for their work on the original battery model, and to Dr. Jun Chen for the ECE 5640 project framework.

AI use: An AI assistant was used for brainstorming and drafting; all content was reviewed and edited by the author.
