# Installatie-instructies & Dataset download

## 1. Virtual Environment & Requirements

```bash
python3 -m venv fatigue_venv
source fatigue_venv/bin/activate

pip install -r requirements.txt
```

## 2. Oogdataset downloaden (Hugging Face)

```bash
git clone https://huggingface.co/datasets/MichalMlodawski/closed-open-eyes

brew install git-lfs
git lfs install

cd closed-open-eyes
git lfs pull
```

## 3. Yawn-dataset downloaden (Kaggle)

```bash
pip install kaggle

# Kaggle API key downloaden van https://www.kaggle.com/settings/account
# En in ~/.kaggle/kaggle.json plaatsen

kaggle datasets download -d davidvazquezcic/yawn-dataset
unzip yawn-dataset.zip
```
