# Tiny ImageNet Image Classifier

A full-stack image classifier for the 200-class Tiny ImageNet dataset. The project combines a custom PyTorch convolutional network and Flask inference API with a Windows WPF desktop client.

The included model accepts RGB images, resizes them to 64×64 pixels, and returns its top five predicted classes. The packaged checkpoint records **70.12% top-1 accuracy** (*~71% with TTA*).

Install the standalone .exe file here: [Download Miru](https://github.com/stu-titor/Miru-Image-Classifier/releases/download/v1.0.0/MiruInterface.zip)

## Features

- Custom CNN with residual connections, squeeze-and-excitation blocks, stochastic depth, and global average pooling
- 200 Tiny ImageNet output classes identified through WordNet IDs
- Top-5 inference with horizontal-flip test-time augmentation
- Flask endpoints for local file uploads and remote image URLs
- WPF desktop interface with local image preview and an IBM-inspired workstation design
- Automatic CUDA inference when a compatible GPU is available, with CPU fallback

## Project Structure

```text
.
├── desktop-client/             # .NET 10 WPF desktop application
│   ├── MainWindow.xaml         # Tiny ImageNet classification console UI
│   ├── MainWindow.xaml.cs      # File selection, preview, and API calls
│   └── MiruInterface.csproj
├── python-backend/
│   ├── src/
│   │   ├── CNN.py              # PyTorch model definition
│   │   ├── app.py              # Flask inference API
│   │   ├── main.ipynb          # Tiny ImageNet training notebook
│   │   ├── trained_net_70.12.pth
│   │   └── wnids.txt           # The 200 supported WordNet class IDs
│   └── README.md               # Backend and training documentation
└── desktop-client.slnx
```

## Requirements

### Backend

- Python 3.10 or newer
- PyTorch and torchvision
- Flask
- Pillow
- Requests
- timm

### Desktop client

- Windows
- .NET 10 SDK with WPF support

## Quick Start

### 1. Start the inference API

#### The client calls the hosted API directly, so step 1 is not required.

From the repository root:

```bash
cd python-backend/src
python -m venv .venv
```

Activate the environment on Windows:

```powershell
.venv\Scripts\Activate.ps1
```

Or on Linux/macOS:

```bash
source .venv/bin/activate
```

Install the dependencies and start Flask:

```bash
pip install torch torchvision flask pillow requests timm
python app.py
```

The API listens on `http://localhost:5000`.

> **Note:** the desktop client does not call this local instance. It is hardcoded to the hosted API at `http://52.207.250.144:5000` (see `desktop-client/MainWindow.xaml.cs`). Running the backend locally is useful for direct `curl` requests, or if you change that address and rebuild the client.

### 2. Start the desktop client

From the repository root:

```bash
dotnet run --project desktop-client/MiruInterface.csproj
```

Choose an image file or enter an image URL, then select **Run Classification**. Local files appear in the image monitor as soon as they are selected.

## API Endpoints

| Method | Endpoint | Input |
|---|---|---|
| `POST` | `/classify/file` | Multipart form field named `image` |
| `POST` | `/classify/url` | JSON object containing a `url` field |

Both endpoints return a JSON array containing five prediction strings ordered from highest to lowest confidence. See [python-backend/README.md](python-backend/README.md) for examples and model details.

## Notes

- The desktop client sends requests to the hosted API at `http://52.207.250.144:5000`, not to a local instance. The address is hardcoded in `desktop-client/MainWindow.xaml.cs`; change it there and rebuild to target a different host.
- The `trained_net_70.12.pth` checkpoint is tracked with Git LFS. Run `git lfs pull` after cloning, and keep the file beside `app.py`.
- Training is documented in the Colab-oriented notebook at `python-backend/src/main.ipynb`.
- Generated folders such as `.vs/`, `bin/`, `obj/`, `__pycache__/`, and notebook checkpoints are ignored by Git.
