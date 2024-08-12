# PowerShell

Kör godtycklickliga PowerShell-skript i en subprocess, serialiserar resultatet som JSON som sedan deserialiseras till ett JObject.

Parametrar:
* **Script**: skriptet som ska köras.
* **SuppressOutput**: Sätt till 'true' om resultatet inte ska returneras till den anropande processen. (default: false)
* **ThrowIfScriptFails**: Sätt till 'true' om subprocessen ska kasta en exception om skriptet misslyckas. (default: true)

![Subprocess](/powershell-subprocess.png?raw=true)
