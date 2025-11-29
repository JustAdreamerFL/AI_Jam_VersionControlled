# ML-Agents Setup na Windows - Krok po kroku

## Problém
Po klonovaní projektu z iného PC nefunguje ML-Agents, pretože:
- Conda environment má zlú verziu Pythonu (3.11 namiesto 3.10.12)
- Chýbajú nainštalované ML-Agents balíky
- Systémové cesty smerujú na R: disk ktorý nemá write permissions

## Riešenie - Copy/Paste príkazy

### 1. Odstráň starý environment (ak existuje)
```powershell
conda deactivate
conda remove --name mlagents --all -y
```

### 2. Nastav správne cesty (obíď R: disk permissions)
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

### 4. Nainštaluj PyTorch s CUDA 11.7 support
```powershell
# Použi constraints súbor, aby pip nikdy neprepísal verzie
$env:PIP_INDEX_URL = "https://download.pytorch.org/whl/cu117"
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install -c "D:\uniza_ai_jam\AI_Jam_VersionControlled\constraints-cu117.txt" torch==2.0.1+cu117 torchvision==0.15.2+cu117 --upgrade --force-reinstall
```

### 5. Nainštaluj ML-Agents z lokálneho repo (editable mode)
```powershell
# Najprv ml-agents-envs
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install -e "D:\uniza_ai_jam\AI_Jam_VersionControlled\ml-agents\ml-agents-envs" --no-deps

# Potom hlavný ml-agents balík
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install -e "D:\uniza_ai_jam\AI_Jam_VersionControlled\ml-agents\ml-agents" --no-deps
```

### 6. Fix PyTorch verzie (ak sa nainštalovala zlá)
```powershell
$env:PIP_INDEX_URL = "https://download.pytorch.org/whl/cu117"
C:\Users\student\.conda\envs\mlagents\python.exe -m pip install -c "D:\uniza_ai_jam\AI_Jam_VersionControlled\constraints-cu117.txt" torch==2.0.1+cu117 torchvision==0.15.2+cu117 --force-reinstall --no-deps
```

### 7. Overenie inštalácie
```powershell
C:\Users\student\.conda\envs\mlagents\python.exe -m pip list | Select-String -Pattern "mlagents|torch"
```

Výstup by mal byť:
```
mlagents                1.1.0        D:\uniza_ai_jam\AI_Jam_VersionControlled\ml-agents\ml-agents
mlagents_envs           1.1.0        D:\uniza_ai_jam\AI_Jam_VersionControlled\ml-agents\ml-agents-envs
torch                   2.0.1+cu117
torchvision             0.15.2+cu117
```

## Použitie
# ak je problem s R: diskom 
- tak nasta nastav env variables
```powershell
$env:PIP_CACHE_DIR = "C:\Users\student\.cache\pip"
```

### Spustenie tréningu v anaconda prompt
```powershell

# aktivuj conda
conda activate mlagents

# Pacni sa do folderu kde mas unity assets
D:
# potom
cd D:\uniza_ai_jam\AI_Jam_VersionControlled_Unity\AI-JAM-2025-master\Assets


# Potom spusti ML-Agents
C:\Users\student\.conda\envs\mlagents\Scripts\mlagents-learn.exe konfig.yaml --run-id=NazovModelu --force 
# alebo --resume
```


## Technické detaily
- **Python verzia**: 3.10.12 (ML-Agents vyžaduje >=3.10.1, <=3.10.12)
- **PyTorch**: 2.0.1 s CUDA 11.7 support
- **Install mode**: Editable (-e) aby zmeny v kóde fungovali hneď
- **Environment location**: C:\Users\student\.conda\envs\mlagents

## Poznámky
- R: disk má read-only permissions, preto všetky cesty musia smerovať na C:
- Environment variables treba nastaviť v každej novej PowerShell session
- Conda activate nefunguje správne, používaj priamy path k python.exe
