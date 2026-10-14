# PowerShell Cheat Sheet (Windows)

Guia rápido de referência para **Windows PowerShell** e **PowerShell 7+**.  
Comandos organizados por categoria para consulta rápida.

> **Dica**: PowerShell usa o padrão `Verbo-Substantivo` (ex: `Get-Process`).  
> Use `Tab` para autocompletar e `Ctrl + Espaço` para sugestões.

---

## 1. Ajuda e Descoberta de Comandos

| Comando | Descrição | Exemplo |
|---------|-----------|---------|
| `Get-Help` | Mostra ajuda de um comando | `Get-Help Get-Process -Examples` |
| `Get-Help -Online` | Abre a documentação online | `Get-Help Get-Service -Online` |
| `Update-Help` | Baixa/atualiza a ajuda local | `Update-Help` |
| `Get-Command` | Lista comandos disponíveis | `Get-Command *process*` |
| `Get-Command -Module` | Comandos de um módulo específico | `Get-Command -Module NetTCPIP` |
| `Get-Alias` | Mostra aliases | `Get-Alias ls` |
| `Get-Member` | Mostra propriedades e métodos de um objeto | `Get-Process \| Get-Member` |

---

## 2. Aliases Comuns (Atalhos)

| Alias | Cmdlet Completo | Descrição |
|-------|-----------------|-----------|
| `ls` / `dir` / `gci` | `Get-ChildItem` | Listar arquivos e pastas |
| `cd` / `sl` | `Set-Location` | Mudar de diretório |
| `pwd` / `gl` | `Get-Location` | Mostrar diretório atual |
| `cp` / `copy` / `cpi` | `Copy-Item` | Copiar |
| `mv` / `move` / `mi` | `Move-Item` | Mover / renomear |
| `rm` / `del` / `ri` | `Remove-Item` | Deletar |
| `cat` / `type` / `gc` | `Get-Content` | Ler conteúdo de arquivo |
| `echo` / `write` | `Write-Output` | Escrever no pipeline |
| `cls` / `clear` | `Clear-Host` | Limpar tela |
| `ps` | `Get-Process` | Listar processos |
| `kill` | `Stop-Process` | Matar processo |
| `man` / `help` | `Get-Help` | Ajuda |

---

## 3. Navegação e Sistema de Arquivos

```powershell
# Listar arquivos e pastas
Get-ChildItem
Get-ChildItem -Recurse -Filter *.txt          # Recursivo + filtro
Get-ChildItem -Force                          # Inclui arquivos ocultos

# Navegar
Set-Location C:\Users
cd ..
cd ~

# Criar
New-Item -ItemType Directory -Name "pasta"
New-Item -ItemType File -Name "arquivo.txt" -Value "conteúdo"
mkdir logs

# Copiar / Mover / Renomear / Deletar
Copy-Item arquivo.txt C:\Backup\
Copy-Item pasta\ -Destination C:\Backup\ -Recurse
Move-Item antigo.txt novo.txt
Rename-Item velho.txt novo.txt
Remove-Item arquivo.txt
Remove-Item pasta\ -Recurse -Force            # Cuidado! Força a exclusão

# Verificar se existe
Test-Path C:\caminho\arquivo.txt              # True / False

# Ler e escrever arquivos
Get-Content log.txt
Get-Content log.txt -Tail 20                  # Últimas 20 linhas
Get-Content log.txt -Wait                     # Monitorar em tempo real (como tail -f)
Set-Content arquivo.txt "Novo conteúdo"
Add-Content arquivo.txt "Linha adicional"
Clear-Content arquivo.txt                     # Limpa o conteúdo sem deletar o arquivo
```

---

## 4. Variáveis, Arrays e Hashtables

```powershell
# Variáveis
$nome = "Grok"
$idade = 42
[string]$texto = "tipado"
[int]$numero = 10

# Variáveis especiais
$_          # Objeto atual no pipeline
$PSItem     # Mesmo que $_
$null
$true / $false
$LASTEXITCODE

# Arrays
$array = @(1, 2, 3, "quatro")
$array += 5
$array[0]

# Hashtable (dicionário)
$hash = @{
    Nome = "Alice"
    Idade = 30
    Cidade = "São Paulo"
}
$hash.Nome
$hash["Idade"]

# Variáveis de ambiente
$env:USERNAME
$env:COMPUTERNAME
$env:PATH
Get-ChildItem Env:
```

---

## 5. Pipeline e Filtros (o poder do PowerShell)

```powershell
# Filtrar
Get-Process | Where-Object { $_.CPU -gt 10 }
Get-Service | Where-Object Status -eq "Running"

# Selecionar propriedades
Get-Process | Select-Object Name, CPU, WorkingSet
Get-Process | Select-Object -First 10
Get-Process | Select-Object -Last 5

# Ordenar
Get-Process | Sort-Object CPU -Descending

# Agrupar e medir
Get-Process | Group-Object Name
Get-ChildItem | Measure-Object -Property Length -Sum

# Iterar
Get-ChildItem *.txt | ForEach-Object { $_.Name }
1..10 | ForEach-Object { "Número $_" }

# Formatar saída
Get-Process | Format-Table Name, CPU -AutoSize
Get-Process | Format-List *
Get-Process | Out-GridView                    # Janela interativa
```

---

## 6. Controle de Fluxo

```powershell
# If / Else
if ($idade -ge 18) {
    "Maior de idade"
} elseif ($idade -ge 12) {
    "Adolescente"
} else {
    "Criança"
}

# Switch
switch ($dia) {
    "Segunda" { "Começo de semana" }
    "Sexta"   { "Quase fim de semana" }
    default   { "Dia normal" }
}

# Loops
for ($i = 0; $i -lt 5; $i++) { $i }
foreach ($item in $array) { $item }
while ($true) { Start-Sleep 1; "Rodando..." }

# Operadores de comparação
-eq  -ne  -gt  -lt  -ge  -le
-like  -notlike  -match  -notmatch
-contains  -notcontains
-and  -or  -not  !

# Tratamento de erros
try {
    1 / 0
} catch {
    Write-Error $_.Exception.Message
} finally {
    "Sempre executa"
}
```

---

## 7. Processos e Serviços

```powershell
# Processos
Get-Process
Get-Process -Name notepad
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10
Stop-Process -Name notepad -Force
Start-Process notepad
Start-Process "https://x.ai"                  # Abre no navegador

# Serviços
Get-Service
Get-Service -Name Spooler
Get-Service | Where-Object Status -eq Running
Start-Service -Name Spooler
Stop-Service -Name Spooler
Restart-Service -Name Spooler
```

---

## 8. Rede e Sistema

```powershell
# Informações do sistema
Get-ComputerInfo
$PSVersionTable                               # Versão do PowerShell
hostname
whoami

# Rede
Get-NetIPAddress
Get-NetAdapter
Test-Connection google.com                    # Ping
Test-NetConnection google.com -Port 443
Get-NetTCPConnection -State Listen

# Download
Invoke-WebRequest -Uri "https://exemplo.com/arquivo.zip" -OutFile arquivo.zip
# ou (alias)
curl -Uri "https://exemplo.com" -OutFile pagina.html
```

---

## 9. Scripts e Políticas de Execução

```powershell
# Verificar política atual
Get-ExecutionPolicy
Get-ExecutionPolicy -List

# Alterar (com cuidado)
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
Set-ExecutionPolicy Bypass -Scope Process     # Apenas na sessão atual

# Executar script ignorando política
powershell -ExecutionPolicy Bypass -File script.ps1

# Criar e executar função
function Saudacao {
    param([string]$Nome)
    "Olá, $Nome!"
}
Saudacao -Nome "Mundo"
```

---

## 10. Comandos Úteis Extras

```powershell
# Histórico
Get-History
Clear-History
Invoke-History 5                              # Executa o comando 5 do histórico

# Jobs em background
Start-Job { Get-ChildItem -Recurse C:\ }
Get-Job
Receive-Job -Id 1
Wait-Job

# Exportar / Importar dados
Get-Process | Export-Csv processos.csv -NoTypeInformation
Import-Csv processos.csv
Get-Process | ConvertTo-Json | Out-File processos.json
Get-Process | ConvertTo-Html | Out-File processos.html

# Buscar texto em arquivos (como grep)
Select-String -Path *.log -Pattern "erro"
Select-String -Path C:\Logs\*.txt -Pattern "password" -Recurse
```

---

## 11. Atalhos de Teclado Úteis

| Atalho | Ação |
|--------|------|
| `Tab` / `Shift+Tab` | Autocompletar |
| `Ctrl + C` | Interromper comando |
| `Ctrl + L` ou `cls` | Limpar tela |
| `↑` / `↓` | Histórico de comandos |
| `F7` | Lista de histórico em janela |
| `Ctrl + ←` / `→` | Pular palavras |
| `Home` / `End` | Início / fim da linha |
| `Ctrl + R` | Busca reversa no histórico (em alguns hosts) |

---

## Dicas Finais

- Prefira **cmdlets completos** quando estiver aprendendo (`Get-ChildItem` em vez de `ls`).
- Use o **pipeline** (`|`) para combinar comandos de forma poderosa.
- Sempre teste comandos destrutivos (`Remove-Item`, `Stop-Process`) com `-WhatIf` primeiro:
  ```powershell
  Remove-Item *.tmp -WhatIf
  ```
- Para PowerShell 7+ (cross-platform): muitos comandos funcionam igual no Linux/macOS.

---

**Arquivo gerado para consulta rápida**  
Compatível com Windows PowerShell 5.1 e PowerShell 7+  
Atualizado em 2026
