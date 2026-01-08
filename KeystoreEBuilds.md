# 🔐 Design Pattern – Keystore & Builds
Padrão de geração, preservação e reutilização de Keystore para builds Android assinados (Cordova).

### 📌 Contexto
Em aplicações Android, a chave de assinatura (Keystore) é o elemento mais crítico do ciclo de vida do app.
Sem ela:
- ❌ Não é possível atualizar o app na Play Store
- ❌ O bundle perde continuidade
- ❌ O aplicativo passa a ser considerado outro app

Este documento define um Design Pattern seguro, reproduzível e imutável para:
- Geração do Keystore
- Uso no build de release
- Rebuilds futuros após manutenção
- Prevenção de perda de chave

### 🎯 Objetivo do Pattern
Garantir que:
- A mesma chave seja usada durante toda a vida do app
O processo seja padronizado
- Builds futuros sejam determinísticos
- A chave não seja recriada acidentalmente
- O build seja automatizável

### 🧠 Princípios do Pattern

|Princípio|Descrição|
|---------|---------|
|Imutabilidade|O keystore é criado uma única vez|
|Reutilização|O mesmo keystore é usado em todos os releases|
|Separação de responsabilidades|Geração ≠ Build ≠ Rebuild|
|Automação segura|Scripts reduzem erro humano|
|Persistência|A chave deve sobreviver ao projeto|

### 🗂️ Estrutura Recomendada
```text
android-signing/
│
├── generate_keystore.bat     # Geração única do keystore
├── kismon-release-key.keystore
├── build.json                # Configuração de assinatura
└── rebuild_aab.ps1           # Build de release automatizado
```

*📌 Regra de ouro:
Se o keystore for perdido, o app está perdido.*

### 1️⃣ Pattern: Geração Única do Keystore

`📄 generate_keystore.bat`:
```batch
@echo off
setlocal

:: Nome do arquivo keystore
set KEYSTORE_NAME=kismon-release-key.keystore

:: Alias da chave
set KEY_ALIAS=kismon-key-alias

:: Algoritmo e tamanho da chave
set KEY_ALG=RSA
set KEY_SIZE=2048

:: Validade em dias (10000 = ~27 anos)
set VALIDITY=10000

echo === Gerando Chave Keystore: %KEYSTORE_NAME% ===
keytool -genkey -v ^
 -keystore %KEYSTORE_NAME% ^
 -alias %KEY_ALIAS% ^
 -keyalg %KEY_ALG% ^
 -keysize %KEY_SIZE% ^
 -validity %VALIDITY%

echo.
echo === Keystore Gerado com Sucesso ===
pause
```

### 🔍 Decisões Arquiteturais

|Decisão|Motivo|
|-------|------|
|RSA 2048|Compatibilidade total com Google Play|
|Validade longa|Evita expiração do app|
|Alias fixo|Referência estável no build|
|Script separado|Evita recriação acidental|

### ⚠️ Regra Crítica
Este script deve ser executado apenas UMA vez por aplicativo.
Nunca:
- Gere um novo keystore para o mesmo app
- Substitua um keystore existente
- Versione o keystore em repositórios públicos

### 2️⃣ Pattern: Configuração de Build Assinado

`📄 build.json`:
```json
{
  "android": {
    "release": {
      "keystore": "kismon-release-key.keystore",
      "storePassword": "kismon",
      "alias": "kismon-key-alias",
      "password": "kismon",
      "packageType": "bundle"
    }
  }
}
```

### 🔐 Responsabilidade do `build.json`
- Centraliza a configuração de assinatura
- Evita flags manuais no CLI
- Permite automação via script
- Garante consistência entre builds

*📌 Este arquivo não gera chave
Ele apenas usa uma chave existente.*

#### 3️⃣ Pattern: Rebuild Seguro Após Manutenção
Rebuild é um evento recorrente, geração de keystore não.

`📄 rebuild_aab.ps1`:
```PowerShell
function Test-CommandSuccess {
    param (
        [int]$ExitCode,
        [string]$Message
    )

    if ($ExitCode -eq 0) {
        Write-Host "$Message concluído com sucesso ✅" -ForegroundColor Green
    } else {
        Write-Host "$Message falhou ❌ (ExitCode: $ExitCode)" -ForegroundColor Red
        exit $ExitCode
    }
}

Write-Host "[1/1] Buildando aplicativo..." -ForegroundColor Cyan

cordova build android --release --buildConfig=build.json

Test-CommandSuccess $LASTEXITCODE "Build do aplicativo"
```

### 🧠 O que esse pattern resolve
- Evita builds quebrados silenciosamente
- Padroniza o processo de release
- Facilita CI/CD
- Mantém logs claros
- Reutiliza sempre a mesma assinatura

### 🚫 Anti-Patterns (O que NÃO fazer)
- ❌ Criar um novo keystore para update
- ❌ Apagar o keystore após o build
- ❌ Versionar o keystore publicamente
- ❌ Alterar alias ou senha no meio do projeto
- ❌ Misturar debug keystore com release

### 🔐 Boas Práticas de Segurança
- 📦 Backup offline do keystore
- ☁️ Backup criptografado (drive privado)
- 🔑 Senhas armazenadas fora do repo
- 🔒 CI/CD com secrets
- 🧾 Documentar quem tem acesso

### 📄 Conclusão
O Keystore não é um detalhe técnico,
é a identidade permanente do aplicativo.
Este Design Pattern garante:
- Continuidade
- Segurança
- Manutenibilidade
- Escalabilidade
