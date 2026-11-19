# Gemini Interactive Chatbot

A small notebook-based chatbot that sends a conversation to Google's Gemini `generateContent` REST API and displays model replies as Markdown. The implementation is in [`chatbot.ipynb`](chatbot.ipynb).

[![Open in Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/danyl-dnl/chat-bot/blob/main/chatbot.ipynb)

## How it works

- The notebook reads `GEMINI_API_KEY` from Google Colab Secrets when running in Colab, or from the `GEMINI_API_KEY` environment variable in another Jupyter environment.
- Each user message is appended to an in-memory `chat_history` list in Gemini's `contents` format and sent in a JSON POST request.
- The response text is appended as a `model` turn, rendered with `IPython.display.Markdown`, and returned to the chat loop.
- Type `exit` to end the interactive session. Starting a new session clears the in-memory history.

Conversation history exists only in the Python kernel's memory. It is not saved between sessions, and the notebook does not include a web UI, database, retrieval system, streaming responses, or model evaluation.

## Run in Google Colab

1. Open the notebook with the Colab badge above.
2. In Colab's **Secrets** panel, add a secret named `GEMINI_API_KEY` and allow the notebook to access it.
3. Run the notebook cells and enter messages in the prompt. Type `exit` when finished.

The notebook uses `requests` and IPython, which are typically available in Colab. If needed, install them in a cell:

```python
%pip install requests ipython
```

## Run in a local Jupyter environment

Install Jupyter and the notebook dependencies:

```bash
python -m pip install jupyterlab requests ipython
```

Set `GEMINI_API_KEY` in the environment used to start Jupyter, then open `chatbot.ipynb` and run its cells. For example, on macOS/Linux:

```bash
export GEMINI_API_KEY="your_api_key"
jupyter lab chatbot.ipynb
```

Do not put a real API key in the notebook or commit it to the repository.

## Model availability

The notebook currently calls `gemini-2.0-flash` through `v1beta/models/...:generateContent`. Google's [Gemini 2.0 Flash model page](https://ai.google.dev/gemini-api/docs/models/gemini-2.0-flash) says the model was shut down on June 1, 2026 and recommends migrating to Gemini 3.5 Flash. **Update the model name in `API_URL` to a currently supported model before expecting API calls to succeed.**

## API key handling

The code keeps the key outside the notebook source, but inserts it into the request URL as a `?key=` query parameter. Avoid logging or sharing the full URL. For a safer REST request, Google documents sending the key in the `x-goog-api-key` header; see the [Gemini API key guide](https://ai.google.dev/gemini-api/docs/api-key).

## Error handling and limitations

- HTTP request failures and JSON parsing failures are caught and displayed in the notebook.
- The chat loop runs synchronously and waits for each response; there is no streaming or retry/backoff logic.
- The conversation history grows for the duration of the session and is sent with every request; there is no truncation or token-budget management.
- The response handler displays the first text part from the first candidate. It does not validate or evaluate the answer.
- The API key is read at startup. If it is missing, the notebook prints a warning, but API calls will fail until a valid key is configured.

## Repository contents

| File | Description |
| --- | --- |
| `chatbot.ipynb` | Colab/Jupyter notebook containing the interactive Gemini chatbot |
| `README.md` | Project overview, setup instructions, model status, and limitations |
