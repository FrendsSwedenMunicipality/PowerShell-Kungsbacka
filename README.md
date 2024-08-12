# PowerShell

Kör godtycklickliga PowerShell-skript i en subprocess, serialiserar resultatet som JSON som sedan deserialiseras till ett JObject. Skriptet körs med PowerShell 5.1. Om man vill köra med PowerShell 7 så ändrar man bara sökvägen till PowerShell i koden (psPath).

Parametrar:
* **Script**: skriptet som ska köras.
* **SuppressOutput**: Sätt till 'true' om resultatet inte ska returneras till den anropande processen. (default: false)
* **ThrowIfScriptFails**: Sätt till 'true' om subprocessen ska kasta en exception om skriptet misslyckas. (default: true)

![Subprocess](/powershell-subprocess.png?raw=true)

## Execute Script
```C#
{
    var throwIfScriptFails = bool.Parse(#trigger.data.ThrowIfScriptFails);
    var output = new System.Text.StringBuilder();
    var psPath = Environment.ExpandEnvironmentVariables(@"%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe");
    var inputScript = #trigger.data.Script;
    var scriptFailed = false;

    var command = $@"
$ErrorActionPreference = 'Stop'    
$script = @'
{inputScript}
'@
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
$scriptBlock = [ScriptBlock]::Create($script)
try {{$res = & $scriptBlock}} catch {{Write-Host $_.ToString(); exit 1}}
$res | ConvertTo-Json -Compress -Depth 10 -ErrorAction SilentlyContinue
";

    var encodedComand = Convert.ToBase64String(System.Text.Encoding.Unicode.GetBytes(command));
    using (var process = new System.Diagnostics.Process())
    {
        process.StartInfo.FileName = psPath;
        process.StartInfo.Arguments = $"-NoProfile -ExecutionPolicy Bypass -EncodedCommand {encodedComand}";
        process.StartInfo.UseShellExecute = false;
        process.StartInfo.RedirectStandardOutput = true;
        process.StartInfo.CreateNoWindow = true;
        process.StartInfo.StandardOutputEncoding = System.Text.Encoding.UTF8;
        process.Start();
        process.BeginOutputReadLine();
        process.OutputDataReceived += (sender, e) =>
        {
            if (!string.IsNullOrEmpty(e.Data))
            {
                output.AppendLine(e.Data);
            }
        };
        process.WaitForExit();
        scriptFailed = process.ExitCode != 0;
    }

    if (scriptFailed && throwIfScriptFails)
    {
        throw new Exception(output.ToString());
    }
    return output.ToString();
}
```

## Assign result
```C#
bool.Parse(#trigger.data.SuppressOutput) ? null : new JObject(new JProperty("Result", JToken.Parse(#var.output)))
```
