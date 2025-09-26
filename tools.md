### 1️⃣ Read File

**Description**
Reads a file from disk and returns its contents.

**Python pseudocode**

```python
def read_file(file_path: str) -> str:
    try:
        with open(file_path, "r", encoding="utf-8") as f:
            return f.read()
    except Exception as e:
        raise Exception(f"Failed to read file {file_path}: {e}")
```

---

### 2️⃣ Read File with Imports

**Description**
Reads a source file and automatically fetches the content of local modules it imports, giving full context including dependencies.

**Python pseudocode**

```python
import os, re

def read_file_with_imports(file_path: str) -> dict:
    result = {"main_file": "", "imports": {}}
    try:
        with open(file_path, "r", encoding="utf-8") as f:
            result["main_file"] = f.read()

        import_paths = extract_import_paths(result["main_file"], file_path)

        for imp in import_paths:
            try:
                with open(imp, "r", encoding="utf-8") as f:
                    result["imports"][imp] = f.read()
            except:
                result["imports"][imp] = "Failed to read import"
        return result
    except Exception as e:
        result["main_file"] = f"Error reading file: {e}"
        return result

def extract_import_paths(file_content: str, base_file: str) -> list:
    import_paths = []
    lines = file_content.split("\n")
    base_dir = os.path.dirname(base_file)

    for line in lines:
        match = re.search(r"from\s+['\"](.*)['\"]", line) or re.search(r"import\s+['\"](.*)['\"]", line)
        if match:
            import_file = match.group(1)
            if import_file.startswith("."):
                import_file = os.path.join(base_dir, import_file)
                if not import_file.endswith(".py"):
                    import_file += ".py"
            import_paths.append(import_file)
    return import_paths
```

---

### 3️⃣ Regex Code Search

**Description**
Searches the codebase using regex/text pattern via Ripgrep. Quickly finds references/snippets.

**Python pseudocode**

```python
import subprocess

def regex_search(pattern: str, root_dir: str = ".") -> list:
    try:
        command = ["rg", "-n", pattern, root_dir]
        output = subprocess.check_output(command, encoding="utf-8")
        return [line for line in output.split("\n") if line.strip()]
    except Exception:
        return []
```

---

### 4️⃣ Semantic Code Search

**Description**
Searches the codebase using semantic understanding, returning snippets ranked by similarity.

**Python pseudocode**

```python
async def semantic_search(query: str, codebase: str) -> list:
    query_embedding = await get_embedding(query)
    codebase_index = await load_codebase_index(codebase)
    results = codebase_index.find_nearest(query_embedding, min_score=0.7)
    return results[:5]  # top 5
```

---

### 5️⃣ Read Semantic Search Files

**Description**
Reads content of multiple files returned by semantic search, retrieving full snippets for context.

**Python pseudocode**

```python
def read_semantic_search_files(query: str, top_k: int = 2) -> dict:
    vector = embed_query(query)
    files = semantic_search_index(vector, top_k)
    results = {}
    for file in files:
        results[file] = read_file(file)
    return results
```

---

### 6️⃣ Edit File

**Description**
Replaces the content of an existing file with new content.

**Python pseudocode**

```python
def edit_file(file_path: str, new_content: str) -> str:
    try:
        with open(file_path, "w", encoding="utf-8") as f:
            f.write(new_content)
        return "File updated successfully"
    except Exception as e:
        raise Exception(f"Failed to edit file {file_path}: {e}")
```

---

### 7️⃣ Create File

**Description**
Creates a new file with specified content, ensuring the directory exists.

**Python pseudocode**

```python
import os

def create_file(file_path: str, content: str) -> str:
    try:
        os.makedirs(os.path.dirname(file_path), exist_ok=True)
        if os.path.exists(file_path):
            raise Exception("File already exists")
        with open(file_path, "x", encoding="utf-8") as f:
            f.write(content)
        return "File created successfully"
    except Exception as e:
        raise Exception(f"Failed to create file {file_path}: {e}")
```

---

### 8️⃣ Run Terminal Command

**Description**
Executes a shell command on the host system, used for running tests/builds/utilities.

**Python pseudocode**

```python
import subprocess

def run_terminal_command(cmd: str, cwd: str = ".") -> dict:
    try:
        proc = subprocess.Popen(
            cmd, shell=True, cwd=cwd,
            stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True
        )
        stdout, stderr = proc.communicate()
        return {"stdout": stdout, "stderr": stderr, "exit_code": proc.returncode}
    except Exception as e:
        return {"stdout": "", "stderr": str(e), "exit_code": -1}
```

---

### 9️⃣ MCP Server Integration

**Description**
Connects to an external MCP server to invoke custom tools, extending functionality.

**Python pseudocode**

```python
import requests

def call_mcp_tool(server_url: str, tool_name: str, params: dict) -> dict:
    try:
        response = requests.post(
            f"{server_url}/{tool_name}",
            headers={"Content-Type": "application/json"},
            json=params
        )
        if not response.ok:
            raise Exception(f"MCP server error: {response.text}")
        return response.json()
    except Exception as e:
        raise Exception(f"Failed to call MCP tool: {e}")
```

