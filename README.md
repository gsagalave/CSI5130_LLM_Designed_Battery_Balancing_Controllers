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

## 3. Proposed Method

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

1. Fast Python simulator. I will build a simplified Python version of my Simulink battery model. It will be checked against the Simulink results (balancing time, energy loss, cell currents) so I can trust it. Because it runs in milliseconds, the AI can test thousands of designs.
2. LLM controller designer (main contribution). This works in two modes:
   - Tuning mode: the LLM sees a list of settings it has tried and how well each one scored, and suggests better settings. This follows the OPRO method.
   - Code mode: the LLM writes a complete balancing controller in Python. The simulator tests it and sends back the results (balancing time, energy loss, cell heat, whether it finished, whether it broke any limits). The LLM uses this feedback to improve the next version. This follows the Eureka and FunSearch methods.
   - Safety check: every controller the LLM writes is limited to safe current values, and any controller that breaks battery limits is rejected and recorded.
3. BMS Copilot (demo). A chat assistant that can run simulations, compare results and make plots. It answers questions like "Why did the rule-based controller never finish balancing?" using real simulation results instead of guessing. This follows the ReAct method.
4. Final check. The best controllers designed by the LLM will be run in the original Simulink model to confirm the results.

## 4. Baselines (what I compare against)

- Rule-based controller (from my earlier project)
- NNLS optimal controller (from my earlier project; Turksoy and Teke, 2023)
- Random search and Bayesian optimization (Optuna), given the same number of simulations as the LLM
- Optional: a reinforcement learning agent (PPO)

## 5. Data

- Simulation data created with the Python simulator and the Simulink model, covering:
  - different pack sizes (4, 8 and 12 cells)
  - random starting charge levels
  - cells with different capacity and internal resistance
  - with and without a load on the pack
- The scenarios used to design controllers are kept separate from the scenarios used to test them, so I can check whether the controllers work in new situations.
- A set of about 30-50 test questions with known answers, taken from simulations, to evaluate the Copilot.

## 6. Evaluation Plan

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

## 7. How This Fits the Course Paths

| Course path | How this project covers it |
| --- | --- |
| Application development with LLM APIs | Yes - LLM controller designer and BMS Copilot |
| Comprehensive model evaluation | Yes - comparing several LLMs, prompt choices, and classical optimization methods |
| Novel algorithm development | Partly - using LLM-driven design for battery balancing has not been done before |
| Model fine-tuning | Future work |

## 8. Timeline

| Dates | Milestone |
| --- | --- |
| Oct 27 - Nov 3 | Set up repository; build Python simulator; check it against Simulink |
| Nov 4 - 10 | Build the LLM design loop (both modes); set up Optuna and random search |
| Nov 11 - 17 | Run main experiments, trade-off analysis, and LLM comparison |
| Nov 18 - 24 | Test on new scenarios, confirm best designs in Simulink, build Copilot demo |
| Nov 25 - 30 | Final report (PDF), video presentation, demo practice |

## 9. Repository Structure (planned)

```
twin/        # Python simulator and Simulink comparison
optimizer/   # LLM controller designer (both modes) and safety check
baselines/   # Rule-based, NNLS, random search, Optuna (and PPO)
agent/       # BMS Copilot tools and user interface
eval/        # Test scenarios, metrics, experiment scripts, results
report/      # Final report (PDF)
README.md
```

## 10. How to Run

To be added.

## 11. Relationship to Earlier Work

The Simulink battery model and the rule-based and NNLS controllers were built in ECE 5640 (Oakland University, 2026) by Gaurav Agalave, Martin Shoraji and Zhuocheng Han, under Dr. Jun Chen. In this project they are reused only as comparison baselines and as the detailed model for final checks.

New work for CSI-4130/5130: the Python simulator, the LLM controller designer, the safety check, the BMS Copilot, the optimization baselines, and all experiments and evaluation.

## 12. Citations and Acknowledgements

LLMs as optimizers and agents
1. C. Yang, X. Wang, Y. Lu, H. Liu, Q. V. Le, D. Zhou, and X. Chen, "Large language models as optimizers," in *Proc. Int. Conf. Learning Representations (ICLR)*, 2024. arXiv:2309.03409.
2. Y. J. Ma, W. Liang, G. Wang, D.-A. Huang, O. Bastani, D. Jayaraman, Y. Zhu, L. Fan, and A. Anandkumar, "Eureka: Human-level reward design via coding large language models," in *Proc. ICLR*, 2024. arXiv:2310.12931.
3. B. Romera-Paredes *et al.*, "Mathematical discoveries from program search with large language models," *Nature*, vol. 625, pp. 468-475, 2024.
4. S. Yao, J. Zhao, D. Yu, N. Du, I. Shafran, K. Narasimhan, and Y. Cao, "ReAct: Synergizing reasoning and acting in language models," in *Proc. ICLR*, 2023. arXiv:2210.03629.
5. N. Shinn, F. Cassano, A. Gopinath, K. Narasimhan, and S. Yao, "Reflexion: Language agents with verbal reinforcement learning," in *Proc. NeurIPS*, 2023. arXiv:2303.11366.

Battery balancing control

6. A. Turksoy and A. Teke, "A fast and energy-efficient nonnegative least square-based optimal active battery balancing control strategy for electric vehicle applications," *Energy*, vol. 262, p. 125409, 2023.
7. J. Chen, A. Behal, Z. Li, and C. Li, "Active battery cell balancing by real time model predictive control for extending electric vehicle driving range," *IEEE Trans. Autom. Sci. Eng.*, vol. 21, no. 3, pp. 4003-4015, Jul. 2024.
8. L. McCurlie, M. Preindl, and A. Emadi, "Fast model predictive control for redistributive lithium-ion battery balancing," *IEEE Trans. Ind. Electron.*, vol. 64, no. 2, pp. 1350-1357, Feb. 2017.

Earlier work

9. G. Agalave, M. Shoraji, and Z. Han, "ECE 5640 Battery Management System Final Project," Oakland University, 2026.

Tools and libraries (to be updated): MATLAB/Simulink/Simscape, MATLAB Engine API for Python, NumPy, SciPy, Optuna, OpenRouter API, Gradio/Streamlit.

Acknowledgements: Thanks to Martin Shoraji and Zhuocheng Han for their work on the original battery model, and to Dr. Jun Chen for the ECE 5640 project framework.

AI use: An AI assistant was used for brainstorming and drafting; all content was reviewed and edited by the author.
