# Neural-Network-Playground

**Neural-Network-Playground** is a lightweight, browser-first scaffold for an **interactive neural network architecture builder**. The repo currently contains a single `index.html` file that outlines the intended UI/feature surface: **drag-and-drop layers**, **basic training hyperparameters**, and **model import/export** actions. :contentReference[oaicite:0]{index=0}

> Current state: **UI/spec prototype** (the `index.html` content is a minimal structured layout rather than a fully implemented HTML/CSS/JS app). :contentReference[oaicite:1]{index=1}

---

## Repository contents

- `index.html` — single-file playground scaffold/spec. :contentReference[oaicite:2]{index=2}

---

## Intended feature surface

### Layer palette (drag & drop)
The playground lists these layer “blocks” for composing a network graph: :contentReference[oaicite:3]{index=3}

- Input Layer
- Hidden Layer
- Output Layer
- Convolutional Layer
- Pooling Layer
- LSTM Layer
- GRU Layer
- Q-Learning Layer
- GNN Layer
- Pretrained Layer

### Canvas / builder
- “Drag and drop nodes here to build your network” (graph/workspace area). :contentReference[oaicite:4]{index=4}

### Training controls
- **Epochs**
- **Learning Rate**
- **Batch Size**
- **Optimizer**: Adam / SGD / RMSprop :contentReference[oaicite:5]{index=5}
- **Loss Function**: Mean Squared Error / Binary Crossentropy :contentReference[oaicite:6]{index=6}

### Actions
- Train Model
- Save Model
- Load Model
- Export Architecture
- Import Architecture
- Upload Dataset :contentReference[oaicite:7]{index=7}

### Status
- Training Status indicator (default: **Idle**). :contentReference[oaicite:8]{index=8}

---

## Quick start

### Option A — open directly
```bash
git clone https://github.com/kai9987kai/Neural-Network-Playground.git
cd Neural-Network-Playground
# open index.html in a browser
