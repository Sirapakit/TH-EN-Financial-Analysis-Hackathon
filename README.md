# TH-EN-Financial-Analysis-Hackathon

> Bilingual (Thai–English) financial analysis framework for MCQ (A/B/C/D) and Rise/Fall classification, powered by VLLM for fast, scalable inference.

## 🚀 Features

* **Fast Inference** with [VLLM](https://github.com/vllm-project/vllm)
* **Multiple‐Choice QA** (choose A/B/C/D)
* **Rise/Fall Classification** (detect “Rise” vs. “Fall” answers)
* **Custom Prompting** — easily tweak system/user prompts
* **Batch Processing** with progress bars via `tqdm`
* **Built-in Evaluation** using `scikit-learn`’s `classification_report`

## 🛠 Installation

```bash
git clone https://github.com/yourusername/TH-EN-Financial-Analysis-Hackathon.git
cd TH-EN-Financial-Analysis-Hackathon

# (Optional) create & activate virtual env
python3 -m venv venv
source venv/bin/activate

pip install vllm torch pandas tqdm scikit-learn
```

## 📂 Data

* **test.csv**

  * Must contain a `query` column with one question per row.
* **ground\_truth.csv** (optional)

  * Columns: `query`, `answer`
  * For local evaluation against your predictions.

## ⚙️ Configuration

1. **Model path**
   In `Inference_with_vllm.ipynb`, set:

   ```python
   MODEL_PATH = "/path/to/Qwen3-32B"
   ```

   or export as an environment variable:

   ```bash
   export MODEL_PATH=/path/to/Qwen3-32B
   ```

2. **Sampling parameters**

   ```python
   from vllm import SamplingParams

   sampling_params = SamplingParams(
       temperature=0.7,
       top_p=0.9,
       top_k=50,
       max_tokens=10000,
       min_tokens=1,
       repetition_penalty=1.05
   )
   ```

## ▶️ Usage

1. **Run the notebook**

   ```bash
   jupyter lab Inference_with_vllm.ipynb
   ```

   or execute headlessly:

   ```bash
   jupyter nbconvert --to notebook --execute Inference_with_vllm.ipynb
   ```

2. **Generate predictions**

   * Results are saved to `submission.csv`.
     

## 🔧 Answer Extraction

The notebook includes an `extract_first_answer` helper. Example:

```python
import re
import pandas as pd

def extract_first_answer(text):
    """
    Extract first valid answer: 'A','B','C','D','Rise','Fall'
    """
    if pd.isna(text):
        return None
    s = str(text)
    # look for <answer> tags
    m = re.search(r"<answer>\s*([ABCD]|Rise|Fall)\s*</answer>", s, re.IGNORECASE)
    if m:
        ans = m.group(1)
        return ans.upper() if ans.upper() in ["A","B","C","D"] else ans.capitalize()
    # Thai keywords fallback
    thai_map = {"ขึ้น": "Rise", "เพิ่มขึ้น": "Rise", "ลง": "Fall", "ลดลง": "Fall"}
    for kw, label in thai_map.items():
        if kw in s:
            return label
    return None

# Usage:
# df["answer"] = df["generated_output"].apply(extract_first_answer)
```

## 🤝 Contributing

1. Fork & clone the repo
2. Create a feature branch (`git checkout -b feature/YourFeature`)
3. Commit your changes (`git commit -m "Add amazing feature"`)
4. Push to your branch (`git push origin feature/YourFeature`)
5. Open a Pull Request — all feedback welcome!

## 📄 License

This project is released under the MIT License. See [LICENSE](LICENSE) for details.
