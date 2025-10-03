---
title: "Malware Analysis & Threat Intelligence Platform"
date: "2025-10-02"
draft: false
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Static Malware Analysis</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f0f2f5;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        header {
            background-color: #007bff;
            color: white;
            padding: 20px;
            text-align: center;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        header h1 {
            margin: 0;
            font-size: 2em;
        }
        main {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 30px 20px;
        }
        form {
            display: flex;
            flex-direction: column;
            width: 100%;
            max-width: 500px;
            background: white;
            padding: 25px;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            margin-bottom: 30px;
        }
        input[type="file"] {
            margin-bottom: 20px;
            font-size: 1em;
        }
        button {
            background-color: #28a745;
            color: #fff;
            border: none;
            padding: 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 1em;
            transition: background 0.3s;
        }
        button:hover {
            background-color: #218838;
        }
        #result {
            width: 100%;
            max-width: 800px;
            background: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            font-family: monospace;
            white-space: pre-wrap;
            word-wrap: break-word;
            overflow-x: auto;
        }
        .json-key {
            color: #d73a49;
            font-weight: bold;
        }
        .json-string {
            color: #032f62;
        }
        .json-number {
            color: #005cc5;
        }
        .json-boolean {
            color: #e36209;
        }
        .json-null {
            color: #6a737d;
        }
    </style>
</head>
<body>
    <header>
        <h1>Static Malware Analysis</h1>
    </header>
    <main>
        <form id="uploadForm">
            <input type="file" id="fileInput" required />
            <button type="submit">Analyze File</button>
        </form>
        <div id="result">Select a file and click "Analyze File".</div>
    </main>

    <script>
        function syntaxHighlight(json) {
            if (typeof json != 'string') {
                json = JSON.stringify(json, undefined, 2);
            }
            json = json.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
            return json.replace(/("(\\u[a-zA-Z0-9]{4}|\\[^u]|[^\\"])*"(?:\s*:)?|\b(true|false|null)\b|-?\d+\.?\d*(?:[eE][+\-]?\d+)?)/g, function (match) {
                let cls = 'json-number';
                if (/^"/.test(match)) {
                    if (/:$/.test(match)) {
                        cls = 'json-key';
                    } else {
                        cls = 'json-string';
                    }
                } else if (/true|false/.test(match)) {
                    cls = 'json-boolean';
                } else if (/null/.test(match)) {
                    cls = 'json-null';
                }
                return '<span class="' + cls + '">' + match + '</span>';
            });
        }

        const form = document.getElementById('uploadForm');
        const resultDiv = document.getElementById('result');

        form.addEventListener('submit', async (e) => {
            e.preventDefault();

            const fileInput = document.getElementById('fileInput');
            if (!fileInput.files.length) return alert("Please select a file.");

            const formData = new FormData();
            formData.append('raw_data', fileInput.files[0]);

            resultDiv.innerHTML = "<em>Analyzing...</em>";

            try {
                const response = await fetch(`${serverAddress}/api/get_static/`, {
                    method: 'POST',
                    body: formData
                });

                if (!response.ok) {
                    throw new Error(`Error: ${response.status} ${response.statusText}`);
                }

                const data = await response.json();
                resultDiv.innerHTML = syntaxHighlight(data);

            } catch (err) {
                resultDiv.innerHTML = `<strong>Failed to analyze file:</strong><br>${err}`;
            }
        });
    </script>
</body>
</html>
