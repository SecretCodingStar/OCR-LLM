
## Requirements

- Python 3.12+
- Tesseract OCR engine
- PDF2Image library
- PyTesseract
- OpenAI API (optional)
- Anthropic API (optional)
- Local LLM support (optional, requires compatible GGUF model)

## Installation

1. Install Pyenv and Python 3.12 (if needed):

```bash
# Install Pyenv and python 3.12 if needed and then use it to create venv:
if ! command -v pyenv &> /dev/null; then
    sudo apt-get update
    sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
    xz-utils tk-dev libffi-dev liblzma-dev python3-openssl git

    git clone https://github.com/pyenv/pyenv.git ~/.pyenv
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
    echo 'eval "$(pyenv init --path)"' >> ~/.zshrc
    source ~/.zshrc
fi
cd ~/.pyenv && git pull && cd -
pyenv install 3.12
```

2. Set up the project:

```bash
# Use pyenv to create virtual environment:
git clone https://github.com/Dicklesworthstone/llm_aided_ocr    
cd llm_aided_ocr          
pyenv local 3.12
python -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip
python -m pip install wheel
python -m pip install --upgrade setuptools wheel
pip install -r requirements.txt
```

3. Install Tesseract OCR engine (if not already installed):
   - For Ubuntu: `sudo apt-get install tesseract-ocr`
   - For macOS: `brew install tesseract`
   - For Windows: Download and install from [GitHub](https://github.com/UB-Mannheim/tesseract/wiki)

4. Set up your environment variables in a `.env` file:
   ```
   USE_LOCAL_LLM=False
   API_PROVIDER=OPENAI
   OPENAI_API_KEY=your_openai_api_key
   ANTHROPIC_API_KEY=your_anthropic_api_key
   ```

## Usage

1. Place your PDF file in the project directory.

2. Update the `input_pdf_file_path` variable in the `main()` function with your PDF filename.

3. Run the script:
   ```
   python llm_aided_ocr.py
   ```

4. The script will generate several output files, including the final post-processed text.

## How It Works

The LLM-Aided OCR project employs a multi-step process to transform raw OCR output into high-quality, readable text:

1. **PDF Conversion**: Converts input PDF into images using `pdf2image`.

2. **OCR**: Applies Tesseract OCR to extract text from images.

3. **Text Chunking**: Splits the raw OCR output into manageable chunks for processing.

4. **Error Correction**: Each chunk undergoes LLM-based processing to correct OCR errors and improve readability.

5. **Markdown Formatting** (Optional): Reformats the corrected text into clean, consistent Markdown.

6. **Quality Assessment**: An LLM-based evaluation compares the final output quality to the original OCR text.

## Code Optimization

- **Concurrent Processing**: When using API-based models, chunks are processed concurrently to improve speed.
- **Context Preservation**: Each chunk includes a small overlap with the previous chunk to maintain context.
- **Adaptive Token Management**: The system dynamically adjusts the number of tokens used for LLM requests based on input size and model constraints.

## Configuration

The project uses a `.env` file for configuration. Key settings include:

- `USE_LOCAL_LLM`: Set to `True` to use a local LLM, `False` for API-based LLMs.
- `API_PROVIDER`: Choose between "OPENAI" or "CLAUDE".
- `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`: API keys for respective services.
- `CLAUDE_MODEL_STRING`, `OPENAI_COMPLETION_MODEL`: Specify the model to use for each provider.
- `LOCAL_LLM_CONTEXT_SIZE_IN_TOKENS`: Set the context size for local LLMs.

## Output Files

The script generates several output files:

1. `{base_name}__raw_ocr_output.txt`: Raw OCR output from Tesseract.
2. `{base_name}_llm_corrected.md`: Final LLM-corrected and formatted text.

## Limitations and Future Improvements

- The system's performance is heavily dependent on the quality of the LLM used.
- Processing very large documents can be time-consuming and may require significant computational resources.
