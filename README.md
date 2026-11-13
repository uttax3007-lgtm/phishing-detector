# PhishGuard — Phishing URL Detection

A focused web application that screens a submitted URL for phishing indicators using a pre-trained machine-learning classifier. The application extracts URL and website features, passes those features to the stored model, and presents a simple assessment in the browser.

> **Scope:** this repository is a phishing-URL detection project. It is not a general-purpose cybersecurity suite, browser extension, malware scanner, or guarantee that a website is safe.

## What the application does

1. Accepts a website URL through a small Flask web interface.
2. Uses `FeatureExtraction` to derive the 30 input features expected by the model.
3. Loads the existing classifier from `pickle/model.pkl`.
4. Runs the classifier and obtains its class probabilities.
5. Displays the model's assessment and safe-class score.

The interface is intentionally limited to this workflow. The redesign changes presentation and project documentation; it does not replace the model, feature extraction logic, framework, or dependency stack.

## Technology

The existing implementation uses:

| Area | Technology | Role |
|---|---|---|
| Language | Python | Application and feature-extraction logic |
| Web framework | Flask | HTTP routes and HTML rendering |
| Machine learning | scikit-learn | Loads and runs the trained classifier |
| Numerical processing | NumPy | Shapes extracted features for prediction |
| Data handling | pandas | Dataset and notebook analysis |
| URL / page inspection | requests, Beautiful Soup | Retrieves and parses page information |
| Domain information | python-whois | Domain registration metadata |
| Model artifact | Python pickle | Stores the trained classifier |
| Web server | Gunicorn | Production WSGI serving |

The repository also includes a Jupyter notebook (`Phishing URL Detection.ipynb`) and the dataset (`phishing.csv`) used for the project's experimentation and analysis.

## Repository layout

```text
Phishing-URL-Detection-master/
├── app.py                         # Flask application and prediction route
├── feature.py                     # Extracts the 30 model input features
├── phishing.csv                   # Dataset used for model exploration
├── requirements.txt               # Existing Python dependencies
├── Procfile                       # Gunicorn process declaration
├── Phishing URL Detection.ipynb   # Model exploration / training notebook
├── LICENSE                        # MIT License template
├── README.md
├── pickle/
│   └── model.pkl                  # Pre-trained classifier
├── static/
│   └── styles.css                 # Application styling
└── templates/
    └── index.html                 # Flask/Jinja interface
```

## Development and request flow

The project is organized as a compact, server-rendered Python application rather than a separate frontend and API service.

### 1. Model and feature contract

The trained classifier is stored in `pickle/model.pkl`. The prediction route loads this artifact when the Flask application starts. `feature.py` is responsible for generating the 30-value feature vector expected by that classifier. The order and meaning of these features are part of the model contract: changing them without retraining and validating the model can invalidate predictions.

### 2. Request handling

The `/` route in `app.py` serves the inspection page on `GET`. On `POST`, it reads the submitted URL, creates a `FeatureExtraction` instance, obtains the feature vector, and passes it to the loaded classifier.

### 3. Result presentation

The template receives the URL and the model's safe-class probability. It presents a clear result state and score. The result is a model assessment, not a promise of safety. The page does not automatically open the submitted website.

### 4. Interface design

The interface uses an off-white canvas, deep-charcoal typography, and a restrained terracotta accent. Layout, spacing, responsive behavior, form states, and result hierarchy are handled in `templates/index.html` and `static/styles.css`. No frontend framework or new runtime dependency has been introduced.

## Installation

### Requirements

- Python **3.8–3.10** is recommended for the pinned dependency versions in `requirements.txt`.
- pip
- Git (optional, if cloning the repository)

The dependency versions in this repository are older pins. A newer Python environment may not have compatible prebuilt wheels for every package.

### 1. Get the source

Clone your repository, or download and extract the project ZIP. Then open a terminal in the project directory:

```bash
cd Phishing-URL-Detection-master
```

### 2. Create a virtual environment

**Windows PowerShell**

```powershell
py -3.10 -m venv .venv
.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, use Command Prompt:

```bat
.venv\Scripts\activate.bat
```

**macOS / Linux**

```bash
python3.10 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Start the application

```bash
python app.py
```

Open the local address printed by Flask (normally `http://127.0.0.1:5000`) in your browser.

For a production-style local run with Gunicorn (available on Unix-like environments):

```bash
gunicorn -w 2 -b 127.0.0.1:8000 app:app
```

Then open `http://127.0.0.1:8000`.

## Using the checker

1. Enter a complete URL, including its scheme (for example, `https://example.com`).
2. Select **Analyse URL**.
3. Review the model assessment and safe-class score.
4. If the result is suspicious, do not enter credentials or payment details on that site.

## Important limitations and safe use

- A model score is not a verified probability that a site is safe. False positives and false negatives are possible.
- A site classified as likely legitimate may still be compromised, malicious, or unsafe for other reasons.
- The feature extraction process makes network requests and may inspect the submitted website. Only submit URLs you are permitted to test. Do not submit private, internal, authenticated, or otherwise sensitive URLs to a publicly hosted instance.
- Do not use this project as the sole basis for security decisions.
- The application loads a Python pickle model. Only use a model artifact from a source you trust; loading an untrusted pickle file can execute arbitrary code.
- The development server is not intended for public production deployment. Use a properly configured WSGI server, HTTPS, request controls, and operational monitoring before exposing an instance publicly.

## Model and evaluation notes

The notebook contains the experimentation and model-comparison work. The web application uses the classifier saved in `pickle/model.pkl`; changing the notebook does not automatically update the deployed artifact. To use a newly trained model, validate it against a held-out dataset, confirm that its expected feature order matches `feature.py`, and deliberately replace the artifact.

Any accuracy or evaluation figures should be reported with the dataset, split strategy, and evaluation procedure used to obtain them. Historical notebook results should not be interpreted as a guarantee of real-world detection performance.

## Troubleshooting

**Dependency installation fails**

Use Python 3.8–3.10 in a fresh virtual environment, then retry `pip install -r requirements.txt`. The project pins older scientific Python packages.

**The application cannot find `pickle/model.pkl`**

Run `python app.py` from the project root (the directory containing `app.py` and the `pickle` folder).

**A URL check fails or takes a long time**

Feature extraction depends on external network access and third-party domain/page responses. Try a publicly reachable URL. A failed lookup is not evidence that a URL is safe.

**Port 5000 is already in use**

Stop the other process using that port, or use Gunicorn with another available port.

MIT License

Copyright (c) 2026 Utkarsh Jadhav

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Acknowledgements

Built with the Python and open-source machine-learning ecosystem. Please retain appropriate attribution and comply with the licenses of the original project, dataset, model, and dependencies when redistributing.
#   p h i s h i n g - d e t e c t o r  
 