# Vibe-Coder Optimizations & Security Patches

This document tracks the custom modifications made to the original `vibe-coder.py` from the `ochyai/vibe-local` repository. 

**Version**: `1.3.2-optimized`

## 1. Security Enhancements (Completed)
- **Path Traversal Protection**: Implemented a `_is_safe_path()` helper function that restricts file access tools (`ReadTool`, `WriteTool`, `EditTool`) to the current workspace (`os.getcwd()`). The agent can no longer read or modify files outside the project directory.
- **SSRF Validation**: Verified `WebFetchTool` includes robust protection (`_is_private_ip`) blocking access to internal network domains and loopback IPs.

## 2. Speed & Optimization (Completed)
- **Ollma Token Caching**: Added `functools.lru_cache(maxsize=1000)` to `OllamaClient.tokenize()` to prevent redundant HTTP requests to Ollama's API for token counting. Dramatically speeds up context re-calculation.
- **NumPy RAG Engine**: Rewrote the `_cosine_similarity` function in `RAGEngine` to use `numpy` vectorization (via `np.dot` and `np.linalg.norm`). Replaced the slow standard Library list comprehension. `requirements.txt` added to support this. Reverts to standard python math safely if `numpy` is not installed.
- **OOM Prevention**: Reduced the `ReadTool` size boundary from 100MB to 2MB. Exceedingly large files are blocked to prevent context limit exhaustion.

## 3. Visual Indicators
- Updated the startup banner to display `v1.3.2-optimized` instead of `v1.3.2`.
- Added dynamic indicators to the banner showing the status of the NumPy RAG acceleration and SSRF/Path Traversal protections.

## How to Update from Upstream
To pull new changes from the original repository without overwriting these optimizations:
```bash
git fetch upstream
git merge upstream/main
```
