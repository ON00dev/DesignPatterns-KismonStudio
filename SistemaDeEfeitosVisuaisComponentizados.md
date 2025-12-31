# 📐 KismonStudio — Design Patterns  
## Sistema de Efeitos Visuais Componentizados (Web / WebGL)

---

## 1. Introdução

Este documento descreve um **Design Pattern oficial da KismonStudio** para criação, gerenciamento e execução de **efeitos visuais dinâmicos** em aplicações Web modernas, especialmente aquelas que envolvem:

- WebGL / WebGPU
- Experiências 3D interativas
- Jogos Web
- Interfaces ricas e animadas

O padrão é baseado exclusivamente em **Web Standards**, sem dependência de frameworks externos.

---

## 2. Objetivo do Pattern

Resolver os seguintes problemas recorrentes:

- Criação de efeitos visuais temporários (explosões, textos, partículas, HUDs)
- Isolamento de HTML, CSS e JS por efeito
- Execução de efeitos sob demanda (event-driven)
- Integração limpa entre lógica 3D (WebGL) e UI 2D (HTML)
- Evitar acoplamento, CSS global e dependência de frameworks

---

## 3. Nome do Pattern

**Componentized Effect Pattern (CEP)**  
_Padrão de Efeitos Componentizados_

---

## 4. Conceitos Fundamentais

### 4.1 Efeito como Componente

Cada efeito visual é tratado como uma **entidade independente**, implementada como um **Web Component (Custom Element)**.

Um efeito possui:
- Ciclo de vida próprio
- Estilo isolado
- Lógica interna
- Auto-destruição após execução

> Um efeito não é uma página, nem uma rota.  
> Um efeito é um **estado visual transitório**.

---

### 4.2 Effects Layer (Camada de Efeitos)

Todos os efeitos são renderizados dentro de uma **camada dedicada**, posicionada acima do conteúdo principal da aplicação (ex: canvas WebGL).

Essa camada atua como:
- Gerenciador visual
- Contêiner de composição
- Ponto único de inserção de efeitos

---

## 5. Arquitetura Geral

```text
[ KismonStudio App ] 
│ ├── Core / Engine 
│ ├── WebGL Canvas (3D Scene) 
│ └── Effects Layer (HTML Overlay) 
├── TextExplosionEffect 
├── HitMarkerEffect 
├── DamageNumberEffect 
└── Outros efeitos
```
---

## 6. Estrutura de Projeto Recomendada

```text
/effects 
├── effects-layer.js 
├── text-explosion.effect.js 
├── hit-marker.effect.js 
└── portal.effect.js
```
---

## 7. Pattern: Effects Layer

### 7.1 Responsabilidade

O **Effects Layer** é responsável por:

- Conter todos os efeitos ativos
- Definir contexto visual (z-index, posição, interação)
- Centralizar a criação de efeitos
- Evitar acoplamento direto com o DOM global

---

### 7.2 Implementação Base

```js
class EffectsLayer extends HTMLElement {
  connectedCallback() {
    this.style.position = 'absolute'
    this.style.inset = '0'
    this.style.pointerEvents = 'none'
    this.style.zIndex = '10'
  }

  spawn(effect) {
    this.appendChild(effect)
  }
}

customElements.define('effects-layer', EffectsLayer)
```
## 8. Pattern: Effect Component

### 8.1 Definição
Um Effect Component é um Custom Element que:
- Lê parâmetros via atributos
- Renderiza sua UI interna
- Executa uma animação ou transição
- Se remove automaticamente ao final

### 8.2 Exemplo: Text Explosion Effect
API do Componente

|Atributo|Tipo|Descrição|
|--------|----|---------|
|text|string|Texto exibido|
|x|number|Posição X (px)|
|y|number|Posição Y (px)|

**Implementação**
```js
class TextExplosionEffect extends HTMLElement {
  connectedCallback() {
    const text = this.getAttribute('text') || 'BOOM'
    const x = this.getAttribute('x') || 0
    const y = this.getAttribute('y') || 0

    const root = this.attachShadow({ mode: 'open' })

    root.innerHTML = `
      <style>
        .effect {
          position: absolute;
          font-size: 32px;
          font-weight: 800;
          color: orange;
          text-shadow: 0 0 10px red;
          animation: explode 0.8s ease-out forwards;
          will-change: transform, opacity;
        }

        @keyframes explode {
          0% { transform: scale(0.2); opacity: 1; }
          100% { transform: scale(2.8); opacity: 0; }
        }
      </style>
      <div class="effect">${text}</div>
    `

    const el = root.querySelector('.effect')
    el.style.left = `${x}px`
    el.style.top = `${y}px`

    el.addEventListener('animationend', () => this.remove())
  }
}

customElements.define('text-explosion-effect', TextExplosionEffect)
```

## 9. Pattern: Event-Driven Effects

### 9.1 Conceito
Efeitos nunca são criados diretamente pela UI.
Eles são disparados por eventos de domínio:
- Hit
- Damage
- Score
- Interaction
- State Change

### 9.2 Exemplo de Uso
```js
const effectsLayer = document.querySelector('effects-layer')

function spawnTextExplosion(text, x, y) {
  const effect = document.createElement('text-explosion-effect')
  effect.setAttribute('text', text)
  effect.setAttribute('x', x)
  effect.setAttribute('y', y)

  effectsLayer.spawn(effect)
}
```

## 10. Integração com WebGL

### 10.1 Separação de Responsabilidades
WebGL → Mundo 3D, física, lógica
HTML Effects → Feedback visual, UI, HUD

### 10.2 Conversão de Coordenadas
```js
function onHit(worldPosition) {
  const screen = worldToScreen(worldPosition)
  spawnTextExplosion('HIT', screen.x, screen.y)
}
```

## 11. Boas Práticas (KismonStudio Standard)
- Usar pointer-events: none em efeitos
- Usar will-change para animações
- Nunca manter efeitos vivos após execução
- Centralizar efeitos em uma camada única
- Evitar lógica pesada dentro do componente
- Tratar efeitos como descartáveis

## 12. Vantagens do Pattern
- Zero dependência de framework
- Alto desempenho
- Código modular e reutilizável
- Integração perfeita com WebGL
- Escalável para projetos grandes
- Base sólida para engine própria

## 13. Casos de Uso
- Jogos Web
- Experiências 3D interativas
- Dashboards animados
Visualização de dados
- Transições visuais avançadas
- HUDs e overlays

## 14. Conclusão
O Componentized Effect Pattern é um Design Pattern central da KismonStudio, alinhado com princípios de:
- Game Engines
- Arquitetura baseada em eventos
- Componentização moderna
- Separação clara de responsabilidades

*Este padrão permite criar experiências visuais ricas, performáticas e altamente organizadas, sem frameworks, usando apenas a plataforma Web nativa.*
