# Neural Network Playground Pro

A single-file, browser-based neural network playground built with **HTML**, **CSS**, **JavaScript**, **TensorFlow.js**, and **Chart.js**.

Neural Network Playground Pro lets you visually build neural network architectures with drag-and-drop layers, train models on synthetic or uploaded datasets, inspect live training metrics, save/load models, export/import architectures, and visualise predictions directly in the browser.

---

## Features

### Visual Neural Network Builder

* Drag-and-drop layer palette
* Visual architecture canvas
* Reorder layers by moving nodes left to right
* Auto-layout button
* Duplicate selected layers
* Delete selected layers
* Clear full architecture
* Visual connection wires between layers
* Editable layer settings through a modal
* Browser autosave for architecture and training settings

---

## Supported Layer Types

The app includes a broad layer palette:

* Input Layer
* Dense Hidden Layer
* Output Layer
* Dropout
* Batch Normalisation
* Flatten
* Conv2D-style Layer
* Pooling-style Layer
* LSTM-style Layer
* GRU-style Layer
* Q-Learning Head
* GNN-style Layer
* Pretrained/Frozen Layer

> Note: Some advanced layer types such as Conv2D, Pooling, LSTM, and GRU are mapped into dense-compatible blocks for normal 2D demo datasets. This keeps the playground stable while still allowing experimental architecture design.

---

## Training Features

* Train directly in the browser using TensorFlow.js
* Live loss chart
* Live accuracy chart
* Validation loss tracking
* Validation accuracy tracking
* Configurable epochs
* Configurable learning rate
* Configurable batch size
* Configurable validation split
* Early stopping support
* Multiple optimizers:

  * Adam
  * SGD
  * RMSprop
  * Adagrad
  * Adamax
* Multiple loss functions:

  * Binary Crossentropy
  * Mean Squared Error
  * Categorical Crossentropy

---

## Dataset Options

Built-in dataset presets:

* XOR classification
* Noisy circles
* Two spirals
* Linear regression

You can also upload your own dataset.

### CSV Format

CSV files should use feature columns first and the final column as the label or target.

Example:

```csv
x1,x2,label
0,0,0
0,1,1
1,0,1
1,1,0
```

### JSON Format

You can upload JSON in this format:

```json
{
  "xs": [[0, 0], [0, 1], [1, 0], [1, 1]],
  "ys": [[0], [1], [1], [0]]
}
```

Or as row objects:

```json
[
  { "features": [0, 0], "label": 0 },
  { "features": [0, 1], "label": 1 },
  { "features": [1, 0], "label": 1 },
  { "features": [1, 1], "label": 0 }
]
```

---

## Prediction Visualisation

For 2D classification datasets, the app displays a decision surface showing how the model separates classes.

For regression or uploaded datasets, the app displays a prediction preview table showing:

* Input features
* Predicted output
* Actual target

---

## Save, Load, Import, and Export

### Browser Model Storage

You can save and load a trained model using browser local storage.

Model storage path:

```text
localstorage://nn-playground-pro-model
```

### Download Model

The trained TensorFlow.js model can be downloaded as files using the **Download model** button.

### Export Architecture

Architectures can be exported as JSON.

The export includes:

* Node positions
* Layer types
* Layer settings
* Training configuration

### Import Architecture

Previously exported architecture files can be imported back into the playground.

---

## Built-In Tests

The app includes a **Run tests** button and also runs smoke tests automatically on startup.

Current tests check:

* CSV dataset parsing
* JSON dataset parsing
* Model building
* Safe async prediction
* Basic TensorFlow tensor leak detection

The app specifically fixes the TensorFlow.js error:

```text
Cannot return a Promise inside of tidy.
```

This was fixed by moving async prediction reads outside `tf.tidy()` and manually disposing tensors after `await output.data()` or `await output.array()`.

---

## How to Use

1. Open the HTML file in a modern browser.
2. Drag an **Input Layer** onto the canvas.
3. Add one or more hidden layers.
4. Add an **Output Layer** at the far right.
5. Choose a dataset preset.
6. Adjust training settings.
7. Click **Train model**.
8. Watch the live loss and accuracy charts.
9. Inspect the prediction visualisation.
10. Save, download, or export your model/architecture.

A good starter architecture is:

```text
Input Layer → Dense Hidden → Dense Hidden → Output Layer
```

For XOR, circles, or spiral classification, try:

```text
Input Layer: 2 features
Dense Hidden: 24 units, relu
Dense Hidden: 12 units, relu
Output Layer: 1 unit, sigmoid
```

---

## Recommended Settings

### XOR Classification

```text
Epochs: 100
Learning rate: 0.01
Batch size: 32
Optimizer: Adam
Loss: Binary Crossentropy
Validation split: 0.2
Early stop patience: 18
```

### Noisy Circles

```text
Epochs: 150
Learning rate: 0.01
Batch size: 32
Optimizer: Adam
Loss: Binary Crossentropy
```

### Two Spirals

```text
Epochs: 300
Learning rate: 0.005
Batch size: 32
Optimizer: Adam
Loss: Binary Crossentropy
```

### Linear Regression

```text
Epochs: 80
Learning rate: 0.01
Batch size: 32
Optimizer: Adam
Loss: Mean Squared Error
Output activation: Linear
```

---

## Controls

### Architecture Controls

| Button      | Description                                 |
| ----------- | ------------------------------------------- |
| Auto layout | Automatically arranges layers left to right |
| Duplicate   | Copies the selected layer                   |
| Delete      | Deletes the selected layer                  |
| Clear       | Clears the whole architecture               |

### Training Controls

| Button      | Description                                     |
| ----------- | ----------------------------------------------- |
| Train model | Builds and trains the model                     |
| Stop        | Requests training to stop                       |
| Build only  | Builds the TensorFlow.js model without training |
| Run tests   | Runs built-in smoke tests                       |

### Storage Controls

| Button              | Description                         |
| ------------------- | ----------------------------------- |
| Save model          | Saves model to browser storage      |
| Load model          | Loads model from browser storage    |
| Download model      | Downloads TensorFlow.js model files |
| Export architecture | Downloads architecture JSON         |
| Import architecture | Loads architecture JSON             |
| Upload dataset      | Uploads CSV or JSON training data   |

---

## Technical Details

Built with:

* HTML5
* CSS3
* Vanilla JavaScript
* TensorFlow.js
* Chart.js
* Browser LocalStorage

No backend is required.

Everything runs locally in the browser.

---

## File Structure

This project is designed as a single-file app:

```text
index.html
```

The file contains:

```text
HTML structure
CSS styling
JavaScript application logic
TensorFlow.js model building
Chart.js metric visualisation
Dataset generation and parsing
Model save/load logic
Smoke tests
```

---

## Debugging Notes

### Fixed TensorFlow.js tidy error

TensorFlow.js does not allow returning promises from inside `tf.tidy()`.

Incorrect pattern:

```js
const result = tf.tidy(async () => {
  const prediction = model.predict(input);
  return await prediction.data();
});
```

Correct pattern used in this app:

```js
async function predictDataSafe(model, rows) {
  const input = tf.tensor2d(rows);
  const output = model.predict(input);

  try {
    return await output.data();
  } finally {
    input.dispose();

    if (Array.isArray(output)) {
      output.forEach(t => t.dispose());
    } else {
      output.dispose();
    }
  }
}
```

This avoids the error and prevents memory leaks.

---

## Browser Support

Recommended browsers:

* Google Chrome
* Microsoft Edge
* Firefox
* Brave

For best performance, use a Chromium-based browser with WebGL enabled.

---

## Limitations

* This is an educational playground, not a production ML training system.
* Large datasets may slow down the browser.
* Image and sequence layers are simplified for 2D demo compatibility.
* Browser local storage can be cleared by the user or browser settings.
* Downloaded TensorFlow.js models are intended for TensorFlow.js-compatible loading.

---

## Future Ideas

Possible improvements:

* True Conv2D image dataset support
* MNIST demo mode
* More activation visualisations
* Confusion matrix
* Learning-rate scheduler
* Weight histogram viewer
* Gradient flow visualiser
* Model comparison mode
* Architecture templates
* Multi-class classification visualisation
* ONNX or Python export
* WebGPU backend controls
* Shareable architecture links

---

## License

Use, modify, and expand freely.

---

## Credits

Created as an advanced browser-based neural network playground using TensorFlow.js and Chart.js.
