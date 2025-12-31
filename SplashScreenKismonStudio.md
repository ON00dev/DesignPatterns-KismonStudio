# 📘 KismonStudio Design Pattern  
## Splash Screen KismonStudio

**Uso obrigatório:** Sim (apps e jogos Cordova)  
**Responsável:** KismonStudio  
**Plataforma alvo:** Cordova (Android WebView)  
**Contexto:** Jogos WebGL / Apps interativos  

---

## 🎯 Objetivo do Design Pattern

Padronizar o uso de **Splash Screen cinematográfica** nos projetos da **KismonStudio**, substituindo splash estática por uma **experiência visual e sonora curta**, garantindo:

- Identidade visual forte
- Transição suave para o app/jogo
- Preload emocional do usuário
- Compatibilidade com WebView (Cordova)
- Baixo custo de performance

---

## 🧠 Conceito Central

A splash screen é tratada como um **mini-ciclo de animação independente**, responsável por:

1. Exibir a marca (logo)
2. Executar efeitos visuais leves
3. Tocar um efeito sonoro curto
4. Fazer fade-out visual e sonoro
5. Redirecionar para o app principal

📌 **Não é apenas uma tela inicial — é um ritual de entrada.**

---

## 🧩 Arquitetura do Pattern

Veja o código do splash screen em [SplashScreen](/splash.html).
copie e cole no projeto para integração e implemente como necessário.

---

## 📄 Estrutura do Arquivo

📌 Nome padrão
`splash.html`

📌 Ponto de entrada no Cordova
```xml
<content src="splash.html" />
```

## 🖼️ Camada Visual (HTML + CSS)
Princípios visuais
- Fundo sólido (preto)
- Logo centralizado
- Sem layouts complexos
- Animações por transform e opacity (GPU-friendly)

### Estados do Logo
|Classe|Função|
|------|------|
|.show|Fade-in inicial|
|.pulsar|Impacto visual sincronizado|
|.hold-red|Estado final de brilho|

### ✨ Animação Principal (CSS)
Keyframe: **pulsar**, Responsável por:
- Escala leve
- Aumento de brilho
- Glow com drop-shadow

📌 Regra:
*Todas as animações devem durar menos de 1s e rodar uma única vez.*

## 🔊 Camada Sonora (Web Audio API)

### Justificativa técnica
AudioContext é mais confiável no WebView
- Permite fade-out real
- Evita cortes abruptos
- Controla bloqueios de autoplay

### Estratégias usadas
- Lazy load do áudio
- Buffer reutilizável
- GainNode para fade-out suave

## ⏱️ Timeline de Execução (JS)
|Tempo (ms)|Evento|
|----------|------|
|0|Página carregada|
|2000|Logo aparece (fade-in)|
|2290|Pulso visual + som|
|3700|Estado final do logo|
|5500|Fade-out do áudio|
|6000|Fade-out visual|
|7000|Redirecionamento|

*📌 A splash nunca deve exceder 7 segundos.*

## 🔀 Redirecionamento Controlado
```js
window.location.href = "index.html";
```
Regra KismonStudio
- Sempre redirecionar
- Nunca carregar o app principal dentro da splash
Splash é descartável após uso

## 🔐 Considerações de Segurança
- Compatível com CSP padrão da KismonStudio
- Uso de fetch local
- Nenhuma dependência externa
- Sem bibliotecas de terceiros

## ⚡ Performance & Boas Práticas
.[x] Usa apenas opacity e transform
.[x] Sem reflow pesado
.[x] Sem canvas ou WebGL
.[x] Execução única
.[x] Zero impacto no runtime do jogo

## ❌ O que NÃO fazer
- Não usar frameworks (React, Vue)
- Não usar vídeos
- Não usar animações longas
- Não carregar assets pesados
- Não reutilizar splash como tela de menu

## 🧪 Compatibilidade
|Plataforma|Status|
|----------|------|
|Android WebView|✅ Total|
|Cordova|✅ Total|
|WebGL apps|✅ Ideal|
|iOS WKWebView|⚠️ Testar áudio|

## 🔄 Customizações Permitidas
- Trocar logo
- Trocar som
- Ajustar cores do glow
- Ajustar tempos (respeitando limite)

*📌 Estrutura e fluxo são imutáveis.*

### 🏷️ Identidade KismonStudio
Este padrão reforça:
- Marca
- Consistência visual
- Experiência premium
- Assinatura emocional da KismonStudio

### 📌 Status do Pattern
- Obrigatório em novos projetos: **Sim**
- Retroativo: **Recomendado**
- Revisão: **Anual**
