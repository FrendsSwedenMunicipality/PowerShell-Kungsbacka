# PowerShell

Kör godtycklickliga PowerShell-skript i en subprocess, serialiserar resultatet som JSON som sedan deserialiseras till ett JObject. Skriptet körs med PowerShell 5.1. Om man vill köra med PowerShell 7 så ändrar man bara sökvägen till PowerShell i koden (psPath).

Parametrar:
* **Script**: skriptet som ska köras.
* **SuppressOutput**: Sätt till 'true' om resultatet inte ska returneras till den anropande processen. (default: false)
* **ThrowIfScriptFails**: Sätt till 'true' om subprocessen ska kasta en exception om skriptet misslyckas. (default: true)

![Subprocess](/powershell-subprocess.png?raw=true)
