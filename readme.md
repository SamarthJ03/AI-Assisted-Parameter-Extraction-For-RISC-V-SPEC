# **AI ASSISTED PARAMETER EXTRACTION**

### **📂 Repository Structure**

* **README.md**: Comprehensive Project Overview, Prompt Evolution logic, and Final Conclusion.  
* **final\_parameters.yaml**: The  output containing extracted parameters for the challenge snippets and the final text used for validation .  
* **results/**:  
  * **validation\_input.yaml**: Raw architectural text snippets from the Zicntr and Zihpm extensions used as the validation source.  
  * **prompt\_evolution\_results.yaml**: A single, transparent log containing the raw, unedited LLM outputs for all 14 iterations of prompt development.  
* **notebooks/**:  
  * **parameter\_extraction.ipynb**: The core execution logic for Prompt 14, demonstrating the reasoning-based extraction process.  
  * **zicntr\_zihpm\_validation.ipynb**: The dedicated notebook used to perform the stress test, hallucination filtering for validation.

## **Coding Challenge Report**

**Details of the LLMS Used \-:** 

| Feature | Qwen-2.5-14B-Instruct | Llama-3.1-8B-Instruct |
| :---- | :---- | :---- |
| Developer | Alibaba Cloud (Qwen Team) | Meta AI |
| Model Size | 14 Billion Parameters | 8 Billion Parameters |
| Context Length | 128k Tokens | 128k Tokens |

### **Execution Environment & Hyperparameters**

To ensure consistency across extraction rounds and minimize creative variance, both models were accessed via the HuggingFace Endpoint API using the following configuration:

* Temperature: 0.0 (Critical for ensuring deterministic, non-creative architectural output).  
* Seed: 42 (Used to ensure reproducibility of results across different runs).  
* Max New Tokens: 1024 (Sufficient for complex YAML/JSON structure generation).  
* Framework: Integration via **LangChain** and **ChatHuggingFace** for structured prompt handling.

#### 

#### 

#### 

#### **Implementation Snippet:**
<img width="800"  alt="image1" src="https://github.com/user-attachments/assets/56c6ce77-8e0d-4c7f-b835-45f125cb728d" />



**Methodology or Definition of Parameter on which I tested:**

I reviewed the feedback and comments provided by **Allen Baum** in the project's doubt and discussion sections,and narrowed down the extraction criteria. The objective shifted from identifying all "optional" features to isolating only those that represent a specific hardware designer's choice for the **RISC-V Unified DB**.

* **Hardware Property vs. Software Logic:** A parameter must be a fixed characteristic of the hardware instance. If a feature describes how an instruction behaves during execution or is a value controlled by software, it is classified as **Instruction/Runtime Logic** and excluded.  
* **Implementation Choice vs. ISA Standard:** A parameter must be a variable that differs across compliant RISC-V designs. If the specification mandates a universal rule such as the **CSR\[9:8\] privilege encoding convention** or the **12-bit CSR address space** it is an **ISA Standard** and does not qualify as a parameter.

**Prompt Evolution and Development**

I developed the final prompt through 14 iterations, moving from a basic keyword-matching approach to a reasoning-based system designed to isolate true engineering choices.

**Results Format:**  
The following format was used for results and format instructions were generated based on this format.  

<img width="800" alt="2" src="https://github.com/user-attachments/assets/0ef710b0-aca8-472e-b917-8f6e9411252b" />

### **1\.  Initial Approach: Keyword-Based Extraction**

The initial approach focused on identifying linguistic triggers that usually indicate a parameter.

* **The Result:** This worked perfectly for the first snippet (Caches) but failed on the second (CSRs).  
* **The Failure:** The model incorrectly selected standard CSR bit-setting conventions. Since these are fixed ISA standards rather than implementation choices, they did not meet the project's selection criteria.
<img width="800" alt="3" src="https://github.com/user-attachments/assets/ec82ca7f-e04e-412f-8238-a5b743be1c47" />
<img width="800" alt="4" src="https://github.com/user-attachments/assets/1dc3624e-2866-4c39-bdce-06f545efa983" />
<img width="800" alt="5" src="https://github.com/user-attachments/assets/99128e61-9931-4b2b-9668-372709e02671" />

### **2\.  Refining the Definition of Parameters**

To fix the previous errors, I added formal definitions for "Architectural Parameters" and "RISC-V."

* **The Failure:** This introduced a new hallucination. Instead of marking missing information as "unspecified," the model began referencing external constraints from sources outside the provided specification.

<img width="1002" height="418" alt="6" src="https://github.com/user-attachments/assets/fbc3f68f-d039-43ea-8951-fa04e7cbd924" />
<img width="800" alt="7" src="https://github.com/user-attachments/assets/0424e78e-26ec-4841-b46b-13c30ac5049b" />


### **3\. Addressing Model Failures on Complex Cases**

Based on Allen’s clarifications in the doubt section, I redefined the parameter criteria and added two specific examples to guide the model. I also established new rules for handling constraints to prevent the model from looking outside the spec.

* **The Failure:** The expanded definitions and examples created too much "context weight" for the Qwen model. It struggled to process the added information and failed to identify obvious parameters like Cache\_Organization.
<img width="1142" height="648" alt="8" src="https://github.com/user-attachments/assets/4ddeb002-ab7b-44aa-9b69-e4f062216fb5" />

### **4\. Transition to a Reasoning-Based Approach**

To reduce the burden on the model, I simplified the definitions and introduced a structured **reasoning process**. Research indicates that forcing an LLM to follow a reasoning path reduces hallucinations.

* **The Result:** This shift worked. All parameters in the first snippet were correctly identified, and hallucinations in the second snippet decreased. Llama-3.1-8B, in particular, showed a significant reduction in errors.
<img width="1148" height="637" alt="9" src="https://github.com/user-attachments/assets/949cad05-7df4-4dd6-b852-d16c1a2277c2" />
<img width="800" alt="10" src="https://github.com/user-attachments/assets/6b3736c0-6149-46ac-bc94-d5c599b5f854" />


### **5\. Improving Decision Strictness**

I continued refining the reasoning process by using stricter keywords. This was designed to force the model to be more critical of the text and avoid simple mistakes.

* **The Result:** The performance improved immediately. **Llama-3.1-8B** produced no parameters for the second snippet, correctly recognizing it as a fixed mandate rather than a configurable choice.
<img width="933" alt="11" src="https://github.com/user-attachments/assets/ceb901c5-174f-4795-a2c1-bc74fb7a670e" />
<img width="800" alt="12" src="https://github.com/user-attachments/assets/37f16934-7c7a-45b3-8494-61ed34b85ab4" />


### **6\. Final Prompt Configuration**

The final step involved adding specific examples to this strict reasoning prompt to assist the Qwen model's performance.

* **The Outcome:** This combination of reasoning and examples was successful. Both models achieved the goal: extracting valid hardware parameters while ignoring fixed ISA conventions.
<img width="986" alt="13" src="https://github.com/user-attachments/assets/fdaac997-65d5-445c-8bfc-478afbb7cdd3" />
<img width="800" alt="14" src="https://github.com/user-attachments/assets/a8dec383-f04c-47e2-bbf5-60efa2272beb" />
<img width="800" alt="15" src="https://github.com/user-attachments/assets/31c78b0a-a871-4f75-b961-00c87640219c" />

## **Parameters Extracted**

| Name | Description | Type | Constraints |
| :---- | :---- | :---- | :---- |
| **cache\_capacity** | The total capacity of the cache. | integer | unspecified |
| **cache\_organization** | The organization structure of the cache. | structural | unspecified |
| **cache\_block\_size** | The size of a cache block. | integer | uniform throughout the system |

## **Validation Test: Zicntr & Zihpm Extensions**

To evaluate the effectiveness of **Prompt 14**, I tested it against the **Zicntr (Base Counters)** and **Zihpm (Performance Counters)** extensions. This section of the RISC-V manual is particularly challenging because it frequently interleaves rigid architectural requirements with implementation-defined flexibility.

### **Data Normalization Results**

The models generated a high volume of raw data that required grouping into actual hardware concepts. This process of "normalization" is necessary to avoid duplicating entries in the Unified DB due to the LLM's varied naming conventions.

| Metric | Count | Description |
| :---- | :---- | :---- |
| **Total Extractions** | 56 | Total parameters listed by both models across all text snippets. |
| **Unique Names** | 49 | The number of distinct strings/labels invented by the AI models. |
| **Unique Logical Parameters** | **16** | The actual hardware concepts identified after grouping synonyms. |

### **Hallucination Test Results**

Each of the 16 logical parameters was tested against the project's selection criteria. In this test, **5** parameters were identified as hallucinations (false positives) because they describe fixed ISA mandates rather than designer choices.

| Feature Found by AI | Test Status | Reason for Failure |
| :---- | :---- | :---- |
| **64-bit Counter Width** | ❌ Hallucination | The spec mandates 64-bit width; it is not a designer choice. |
| **RDTIMEH Presence** | ❌ Hallucination | This is a fixed ISA rule for XLEN=32 systems. |
| **Hart Synchronization** | ❌ Hallucination | This is a compliance mandate, not a hardware parameter. |
| **XLEN Mapping Logic** | ❌ Hallucination | Describes how software accesses bits, not hardware configuration. |
| **Overflow Property** | ❌ Hallucination | A mathematical inevitability of the width, not a parameter. |

### **Valid Parameter Test Results**

The remaining **11 logical parameters** passed the test. These represent the genuine implementation-defined parameters discovered by the AI that are eligible for the Unified DB.

| Parameter Name | Type | Implementation Choice (Why it passed) |
| :---- | :---- | :---- |
| **access\_prohibition** | Boolean | Designers can choose to block access to prevent timing attacks. |
| **upper\_bits\_impl** | Enum | In RV32, upper bits can be hardware or a software trap-handler. |
| **cycle\_counter\_rate** | Integer | The frequency of the cycle counter varies by implementation. |
| **rdcycle\_mapping** | Enum | Choice to map RDCYCLE per-core or per-hart. |
| **sleep\_behavior** | Enum | Whether counters stop or continue during clock-gating/sleep. |
| **reset\_counting** | Boolean | Choice to count cycles spent during a power-on reset. |
| **clock\_accuracy** | Range | The maximum relative error of the real-time clock. |
| **rdtime\_source** | Enum | Can be a dedicated real-time clock or an alias of rdcycle. |
| **hpm\_counter\_count** | Range | The number of additional counters implemented (0 to 29). |
| **hpm\_counter\_width** | Integer | The bit-width of additional HPM counters is platform-specific. |
| **event\_mapping** | Structural | The hardware events (e.g., L1 misses) each counter tracks. |

## **Conclusion**

The testing of 14 prompt iterations demonstrates that **eliminating hallucinations entirely is likely impossible** using current LLMs. Because the RISC-V manual is written in natural language, models will occasionally misinterpret a mandatory "must" as a configurable "may," especially in sections with complex dependencies.

### **Final Impact on the Unified DB**

Despite the persistence of some hallucinations, the reasoning-based approach developed in this project provides a significant technical advantage:

* **Reduction of the Noise :** Earlier prompt versions (like Prompt 1\) would have filled the Unified DB with hundreds of "fake" parameters, such as fixed CSR addresses and bit-encodings. Prompt 14 successfully filtered out the majority of this noise.  
* **Discovery of Hidden Parameters:** The AI successfully identified subtle architectural choices, such as counter behavior during sleep states, which are critical for the Unified DB but easily overlooked by human readers.  
* **Operational Efficiency:** The methodology moves the human architect's role from "searching" to "validating." Instead of reading hundreds of pages to find these parameters, the architect only needs to test a pre-filtered list of 16 items.

By using this iterative prompt engineering process, we have created a scalable way to extract high-value hardware parameters while keeping the manual effort of data cleaning to a minimum.

Rough Work/Implementation of the Desired Task

•       During the coding challenge, I used LangChain and manually experimented with different prompt engineering techniques, refining prompts through iterative testing and studying prompting strategies to improve extraction quality and reduce hallucinations.

•       For the full project, I propose building an agent or graph-based workflow that automates extraction of architectural parameters from the specifications.

•       The workflow would run two LLMs in parallel using a common prompt to extract candidate parameters from the specification text.

•       Outputs from both LLMs would then be merged to remove duplicates and identify unique candidate parameters before further validation.

•       A human-in-the-loop validation stage would review extracted parameters to verify whether they satisfy the defined criteria for architectural parameters.

•       Correctly identified parameters would be added to the parameter list, while hallucinated or incorrect outputs would be used to refine prompts by adding negative examples and improving extraction behavior.

•       Additionally, a secondary LLM or evaluation stage can be used to combine or compare outputs from both models and select parameters that match the required definition.

•       Through iterative prompt refinement and comparison with known parameter lists, the extraction accuracy and coverage can be progressively improved.

<img width="878" height="884" alt="16" src="https://github.com/user-attachments/assets/9efb012f-294b-4e8d-bc4c-f3251e542c10" />




