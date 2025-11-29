# ML-Agents Setup na Windows - Krok po kroku

Tento návod ti umožní nainštalovať ML-Agents s **GPU CUDA 11.7** podporou na školskom PC.  
Všetky príkazy sú **copy-paste** – stačí ich spustiť v PowerShell presne v tomto poradí.

---

## Požiadavky
- Windows 10/11
- Anaconda / Miniconda
- NVIDIA GPU s CUDA 11.7 kompatibilným driverom (≥ 516.xx)
- Klonovaný repozitár na `D:\uniza_ai_jam\AI_Jam_VersionControlled`

---

## Inštalácia (Copy-Paste)

### 1. Odstráň starý environment (ak existuje)
```powershell
conda deactivate
conda remove --name mlagents --all -y
```

### 2. Nastav správne cesty (obíď R: disk permissions)
> Tieto premenné treba nastaviť v **každej novej** PowerShell session.
```powershell
$env:CONDA_ENVS_PATH = "C:\Users\student\.conda\envs"
$env:CONDA_PKGS_DIRS = "C:\Users\student\.conda\pkgs"
$env:PIP_CACHE_DIR = "C:\Users\student\.cache\pip"
$env:TEMP = "C:\Users\student\AppData\Local\Temp"
$env:TMP = "C:\Users\student\AppData\Local\Temp"
```

### 3. Vytvor nový environment s Python 3.10.12
```powershell
conda create -n mlagents python=3.10.12 -y
```

### 4. Nainštaluj PyTorch s CUDA 11.7 (GPU)
> **Dôležité:** `conda activate mlagents` na školských PC neprepne PATH správne.  
> Preto vždy používaj plnú cestu `C:\Users\student\.conda\envs\mlagents\python.exe`.
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install --upgrade --force-reinstall torch==2.0.1+cu117 torchvision==0.15.2+cu117 --index-url https://download.pytorch.org/whl/cu117
```

### 5. Nainštaluj všetky ML-Agents závislosti
Tieto balíky sú potrebné pre tréning. Pinujeme `numpy==1.23.5`, aby trénery nepadali na `np.float` chybe.
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install numpy==1.23.5 attrs==25.4.0 cattrs==1.5.0 cloudpickle==3.1.2 grpcio==1.48.2 h5py==3.15.1 huggingface-hub==1.1.5 onnx==1.15.0 protobuf==3.20.3 pypiwin32==223 pyyaml==6.0.3 six==1.17.0 tensorboard==2.20.0 pettingzoo==1.15.0 gym==0.26.2 pillow==12.0.0 tqdm==4.67.1 typing-extensions==4.15.0 "setuptools<81"
```

### 6. Nainštaluj ML-Agents z lokálneho repo (editable mode)
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install -e "D:\uniza_ai_jam\AI_Jam_VersionControlled\ml-agents\ml-agents-envs" --no-deps
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install -e "D:\uniza_ai_jam\AI_Jam_VersionControlled\ml-agents\ml-agents" --no-deps
```

### 7. Overenie inštalácie
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -c "import torch,numpy,attr; print('torch',torch.__version__,'numpy',numpy.__version__,'attrs',attr.__version__)"
```
Očakávaný výstup:
```
torch 2.0.1+cu117 numpy 1.23.5 attrs 25.4.0
```

Kontrola GPU:
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -c "import torch; print('CUDA available:', torch.cuda.is_available()); print('Device:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'N/A')"
```

### Spustenie tréningu v anaconda prompt
```powershell
cd D:\uniza_ai_jam\AI_Jam_VersionControlled_Unity\AI-JAM-2025-master\Assets
C:\Users\student\.conda\envs\mlagents\Scripts\mlagents-learn.exe konfig.yaml --run-id=NazovModelu --force
```
> Použi `--resume` namiesto `--force` ak chceš pokračovať v predošlom tréningu.

---

## Technické detaily
| Parameter | Hodnota |
|-----------|---------|
| Python | 3.10.12 |
| PyTorch | 2.0.1+cu117 (CUDA 11.7) |
| NumPy | 1.23.5 |
| ML-Agents | 1.1.0 (editable) |
| Environment | `C:\Users\student\.conda\envs\mlagents` |

---

## Poznámky
- **R: disk** má read-only permissions → všetky cesty musia smerovať na `C:`.
- **Environment variables** z kroku 2 treba nastaviť v každej novej PowerShell session.
- **`conda activate`** nefunguje správne na školských PC → používaj plnú cestu k `python.exe`.
- **`setup.py`** v tomto repo má relaxovaný torch requirement (`>=2.0.1`) pre kompatibilitu s CUDA 11.7 wheels.

---

## Troubleshooting

### `ModuleNotFoundError: No module named 'attr'`
Spusti krok 5 (inštalácia závislostí).

### `np.float` / NumPy 2.x chyba
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install numpy==1.23.5 --force-reinstall
```

### `torch` not found / wrong version
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install --upgrade --force-reinstall torch==2.0.1+cu117 torchvision==0.15.2+cu117 --index-url https://download.pytorch.org/whl/cu117
```

### CUDA not available
1. Skontroluj driver: `nvidia-smi`
2. Uisti sa, že máš CUDA 11.7 kompatibilný driver (≥ 516.xx)
3. Reinstaluj torch s CUDA wheels (viď vyššie)
