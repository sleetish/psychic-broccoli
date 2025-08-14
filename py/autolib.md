Referenced from https://medium.com/codrift/5-obscure-python-libraries-that-solved-my-dumbest-coding-problems-6747f3e0647a

pikepdf
```python
import pikepdf
from pathlib import Path

input_folder = Path("raw_reports")
output_pdf = pikepdf.Pdf.new()

for pdf_file in input_folder.glob("*.pdf"):
    try:
        with pikepdf.open(pdf_file) as src:
            output_pdf.pages.extend(src.pages)
    except pikepdf.PdfError:
        print(f"Skipping broken file: {pdf_file}")

output_pdf.save("merged_cleaned.pdf")
```

deeplake
```python
import deeplake
import json
from pathlib import Path

dataset = deeplake.empty("my_logs", overwrite=True)
dataset.create_tensor("logs", htype="text", dtype=str)
dataset.create_tensor("source", htype="text", dtype=str)

for json_file in Path("logs").glob("*.json"):
    with open(json_file) as f:
        content = json.dumps(json.load(f))
        dataset.append({"logs": content, "source": str(json_file)})

dataset.commit("Initial import")
```

mail-parser
```python
from mailparser import parse_from_file
from pathlib import Path

email_dir = Path("emails")
for eml_file in email_dir.glob("*.eml"):
    mail = parse_from_file(eml_file)
    print("Subject:", mail.subject)
    print("From:", mail.from_)
    print("Body:", mail.body)
```

boltons
```python
from boltons.cacheutils import LRU
import requests

cache = LRU(max_size=100)

def get_data(page):
    if page in cache:
        return cache[page]
    resp = requests.get(f"https://api.example.com/data?page={page}")
    cache[page] = resp.json()
    return cache[page]
```

plumbum
```python
from plumbum import local

git = local["git"]
cp = local["cp"]
sed = local["sed"]

# Replace placeholder in all .txt files
for txt_file in local.cwd // "*.txt":
    sed["-i", "s/PLACEHOLDER/VALUE/g", txt_file]()

# Stage, commit, and push
git("add", ".")
git("commit", "-m", "Automated commit")
git("push")
```
