# Configurable Gaussian Process Emulator for Atmospheric Dispersion Modelling

A Python-based framework for emulating an idealised Gaussian plume dispersion model using **Gaussian Process Regression (GPR)**. The project investigates different sampling strategies for building accurate surrogate models while reducing the number of computationally expensive simulator evaluations.

## Overview

Atmospheric dispersion models are used to estimate pollutant concentrations under different meteorological and source conditions. However, repeatedly evaluating these models can become computationally demanding, particularly when they are used within larger simulation or uncertainty analysis workflows.

This project implements a Gaussian Process (GP) emulator as a computationally efficient surrogate for a Gaussian plume model. It compares four experimental design strategies to investigate how training sample selection affects predictive performance.

The framework also allows users to customise the physical parameter ranges, GP kernel, sampling strategies, and experimental settings through a configuration file.

## Methodology

The framework consists of two main components:

1. **Gaussian Plume Simulator:** Generates ground-level, plume-centreline concentration estimates using an idealised Briggs open-country dispersion formulation.
2. **Gaussian Process Emulator:** Learns the relationship between model inputs and simulated concentrations, providing predictions without repeatedly evaluating the original dispersion model.

The emulator predicts the logarithm of ground-level centreline concentration:

\[
y = \log_{10}(C)
\]

where \(C\) is the simulated concentration in µg/m³.

### Input Parameters

The default model uses four input parameters:

| Parameter | Description | Default Range |
|---|---|---|
| Wind speed (\(u\)) | Wind speed (m/s) | 1–10 |
| Effective stack height (\(H\)) | Effective release height (m) | 10–60 |
| Downwind distance (\(x\)) | Distance from the source (m) | 500–5000 |
| Stability index (\(s\)) | Atmospheric stability parameter | 0–4 (A–E) |

An optional fifth input, **emission rate (\(Q\))**, can be enabled through the configuration file. Its default range is 0.1–10 g/s.

## Sampling Strategies

Four sampling strategies are implemented and can be compared:

- **Random Sampling:** Selects training points randomly from the input space.
- **Latin Hypercube Sampling (LHS):** Uses stratified sampling to distribute training points across the input space.
- **Active Learning MacKay (ALM):** Selects new points based on predictive uncertainty.
- **Active Learning Cohn (ALC):** Selects points based on their expected reduction in integrated predictive variance.

These methods are evaluated at different training sample sizes to investigate their learning behaviour and predictive performance.

## Gaussian Process Configuration

The GP emulator supports three covariance kernel options:

- Matérn 5/2
- Matérn 3/2
- Radial Basis Function (RBF)

GP hyperparameters are optimised using the likelihood optimiser provided by scikit-learn.

## Key Features

- Configurable physical input ranges and experimental settings.
- Optional inclusion of emission rate as a fifth input parameter.
- Comparison of four sampling and active learning strategies.
- Support for multiple GP covariance kernels.
- Repeated experiments with configurable random seeds.
- Model evaluation using Root Mean Square Error (RMSE).
- Automatic generation of learning curves and result tables.
- Reproducible experiments through saved configuration files.

## Installation

Python 3.10 or newer is recommended.

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/GP-Plume-Emulator.git
cd GP-Plume-Emulator
```

Create and activate a virtual environment:

**Windows**

```bash
python -m venv .venv
.venv\Scripts\activate
```

**macOS / Linux**

```bash
python -m venv .venv
source .venv/bin/activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

## Running the Experiment

Run the experiment using the default configuration:

```bash
python run_experiment.py --config config.json
```

Alternatively, on Windows, you can use:

```bash
run_windows.bat
```

## Customisation

Experimental settings can be modified in `config.json` without editing the source code.

For example:

```json
{
  "bounds": [[1, 10], [10, 60], [500, 5000], [0, 4]],
  "include_emission_rate": false,
  "methods": ["random", "lhs", "alm", "alc"],
  "kernel": "matern52",
  "initial_samples": 10,
  "max_samples": 60,
  "sample_step": 5,
  "repetitions": 5,
  "test_samples": 1000,
  "seed": 42,
  "output_dir": "results_custom"
}
```

Available settings include:

- Physical parameter bounds
- Emission rate inclusion and range
- Sampling strategies
- GP covariance kernel
- Initial and maximum training sample sizes
- Number of repetitions
- Test set size
- Active learning candidate and reference pool sizes
- Random seed and output directory

The complete set of available options is provided in `config.json`.

## Outputs

Each experiment generates the following files in the selected output directory:

| File | Description |
|---|---|
| `results.csv` | RMSE values for each method, repetition, and training sample size |
| `summary.csv` | Mean and standard deviation of RMSE |
| `mean_rmse_table.csv` | Mean RMSE comparison across sampling strategies |
| `learning_curve.png` | Learning curves showing predictive performance against training sample size |
| `run_config.json` | Configuration used for the experiment |

## Testing

Basic simulator tests are included in the `tests` directory.

Run them using:

```bash
pytest -q
```

## Limitations

The current implementation retains the assumptions of the original idealised Gaussian plume formulation:

- Concentrations are evaluated at ground level and along the plume centreline.
- Atmospheric stability is represented as a continuous index from A to E, rather than through a complete meteorological stability model.
- Terrain effects, wind direction, deposition, atmospheric chemistry, and time-varying meteorology are not represented.
- The model does not estimate unknown source parameters through inverse modelling.
- Including emission rate as an input changes the emulator input space; in the current formulation, emission rate scales the predicted concentration.

The framework is therefore intended for surrogate modelling and sampling strategy evaluation under the assumptions of the implemented Gaussian plume model.

## Technologies

- Python
- NumPy
- SciPy
- scikit-learn
- Pandas
- Matplotlib
- Gaussian Process Regression
- Active Learning
- Latin Hypercube Sampling
- Atmospheric Dispersion Modelling

TNX

Amin Khademi

Developed as a computational research project on Gaussian Process emulation and atmospheric dispersion modelling.
