# 📐 Estrutura de Projetos com Cordova (EPC)

---

## 1. Introdução

Este documento define o **Design Pattern oficial da KismonStudio** para **estruturação de projetos baseados em Apache Cordova**, tecnologia utilizada em **98% dos aplicativos e jogos da KismonStudio** para build de APKs.

O padrão tem como objetivo garantir:
- Organização consistente entre projetos
- Facilidade de manutenção
- Separação clara de responsabilidades
- Integração limpa entre Web, WebGL e build nativo
- Escalabilidade para jogos e aplicações complexas

---

## 2. Objetivo do Pattern

Resolver problemas comuns em projetos Cordova:

- Estrutura desorganizada do diretório `www`
- Mistura de lógica de engine, UI e build
- Dificuldade de reutilização entre projetos
- Acoplamento excessivo ao Cordova
- Falta de padrão para projetos de jogos WebGL

---

## 3. Nome do Pattern

**Cordova Project Structure Pattern (CPSP)**  
_Padrão de Estrutura de Projeto Cordova_

---

## 4. Princípios Fundamentais

1. **Cordova é apenas o empacotador**
2. O projeto Web deve ser independente do Cordova
3. O diretório `www` nunca contém lógica de domínio
4. Build nativo ≠ lógica da aplicação
5. O projeto deve rodar no navegador sem Cordova

---

## 5. Arquitetura Geral
```text
[ KismonStudio App ] 
│ ├── Core Web App (Framework-less) 
│ ├── Game / App Logic 
│ ├── WebGL / UI / Effects 
│ └── Cordova (Build Layer)
```
Cordova atua **apenas** como **camada de empacotamento e bridge nativa**.

---

## 6. Estrutura de Pastas Oficial
```text
/project-root
├── /app                # Aplicação Web pura
│   ├── /core            # Core da aplicação / engine
│   ├── /effects         # Efeitos visuais (Web Components)
│   ├── /scenes          # Cenas / estados
│   ├── /systems         # Sistemas (input, audio, state)
│   ├── /ui              # UI / HUD / overlays
│   ├── index.html
│   └── main.js
├── /cordova             # Projeto Cordova isolado
│   ├── /platforms
│   ├── /plugins
│   ├── /www              # Build output (gerado)
│   ├── config.xml
│   └── package.json
├── /scripts             # Scripts de build / deploy
├── /assets              # Assets brutos (imagens, áudio)
├── package.json
└── README.md
```
---

## 7. Pattern: Web App Isolado

### 7.1 Regra de Ouro

> **Nenhum código de aplicação vive dentro do `cordova/www`.**

O diretório `www`:
- É tratado como **build output**
- Pode ser apagado e regenerado a qualquer momento
- Nunca deve ser editado manualmente

---

### 7.2 Build Flow
/app  →  build  →  /cordova/www  →  cordova build apk
---

## 8. Pattern: Cordova como Build Layer

### Responsabilidades do Cordova

- Empacotar HTML, CSS e JS
- Gerar APK / AAB
- Gerenciar plugins nativos
- Expor APIs nativas via `cordova.plugins`

### Não é responsabilidade do Cordova

- Lógica de jogo
- Gerenciamento de estado
- Renderização
- UI
- Arquitetura de aplicação

---

## 9. Pattern: Bridge Pattern (Web ↔ Native)

### 9.1 Conceito

A comunicação entre Web e nativo deve ser feita via:
- Abstrações
- Adaptadores
- Nunca chamadas diretas espalhadas no código

---

### 9.2 Exemplo de Abstração

```js
export const Device = {
  vibrate(ms = 50) {
    if (window.cordova) {
      navigator.vibrate(ms)
    }
  }
}
```
Uso:
```js
Device.vibrate(100)
```
## 10. Pattern: Environment Detection

```js
export const Env = {
  isCordova: () => !!window.cordova,
  isBrowser: () => !window.cordova
}
```
Nunca acople lógica principal ao Cordova.

## 11. Pattern: Asset Management
- Assets brutos ficam fora do Cordova
- Assets finais são copiados para www
- Versionamento e cache são tratados no Web Layer

## 12. Pattern: Plugins Strategy
- Plugins são tratados como infraestrutura
- Nunca como dependência lógica
- Sempre usar wrappers / adapters

## 13. Boas Práticas (KismonStudio Standard)
- Cordova nunca contém lógica de negócio
- App Web deve rodar standalone no browser
- Build deve ser reproduzível
- Plugins sempre encapsulados
- Estrutura idêntica entre projetos
- Cordova pode ser substituído futuramente

## 14. Casos de Uso
- Jogos WebGL mobile
- Apps híbridos
- Experiências 3D
- Aplicações offline-first
- Prototipagem rápida com build nativo

## 15. Vantagens do Pattern
- Consistência entre projetos
- Facilidade de manutenção
- Redução de bugs específicos de plataforma
- Possibilidade de trocar Cordova por Capacitor futuramente
- Base sólida para escala

## 16. Conclusão
O Estrutura de Projetos com Cordova (EPC) estabelece uma base arquitetural sólida e previsível para todos os projetos da KismonStudio que utilizam Cordova como ferramenta de build.
Ele garante que o Cordova permaneça uma camada de empacotamento, enquanto a aplicação mantém:
1. Independência
2. Clareza arquitetural
3. Alta performance
4. Reutilização máxima

*Este padrão é obrigatório para novos projetos da KismonStudio.*
