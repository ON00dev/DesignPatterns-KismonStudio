# 🎬 Padrão de Display de Créditos

## 1. Introdução

Este documento define o **Design Pattern oficial da KismonStudio** para **implementação, organização e exibição de créditos em aplicações e jogos híbridos**, com foco especial em projetos baseados em **Apache Cordova + Web App**.

O padrão garante que a seção de créditos:

* Seja **padronizada entre projetos**
* Não dependa diretamente do Cordova
* Seja **compatível com browser, Android e futuras plataformas**
* Permita **injeção dinâmica de versão e plataforma**
* Atenda boas práticas legais, técnicas e de UX

---

## 2. Objetivo do Pattern

Resolver problemas comuns relacionados a créditos em aplicações:

* Créditos hardcoded e desatualizados
* Versão do app escrita manualmente
* Dependência direta de plugins no HTML
* Falta de padrão visual e estrutural
* Dificuldade de reaproveitamento entre projetos
* Falhas ao rodar fora do ambiente Cordova

---

## 3. Nome do Pattern

**Credits Display Pattern (CDP)**
*Padrão de Exibição de Créditos*

---

## 4. Princípios Fundamentais

1. **Créditos são parte da UI, não da lógica**
2. O layout de créditos deve funcionar **sem Cordova**
3. Informações dinâmicas devem ter **fallback seguro**
4. Nenhum plugin Cordova é chamado diretamente no HTML
5. Créditos devem ser claros, profissionais e auditáveis
6. O padrão deve ser reaproveitável entre projetos

---

## 5. Arquitetura Geral

```text
[ Credits UI ]
│
├── Estrutura HTML (estática)
├── Conteúdo humano (nomes, funções, licenças)
├── Dados dinâmicos (versão / plataforma)
└── Adapter Cordova (opcional)
```

A camada de créditos **não conhece o Cordova diretamente**.
Ela apenas **consome informações se disponíveis**.

---

## 6. Estrutura de Créditos Recomendada

```text
Credits Modal
├── Desenvolvimento
├── Design & Programação
├── Música
├── Efeitos Sonoros
├── Tecnologias
├── Testes
├── Inspiração
├── Equipe & Contato
├── Licenças
├── Versão (dinâmica)
└── Copyright
```

Cada bloco é **independente**, permitindo:

* Remoção sem impacto
* Reorganização
* Tradução futura

---

## 7. Pattern: HTML Declarativo e Estável

### 7.1 Regra de Ouro

> **O HTML de créditos nunca depende de JavaScript para existir.**

Exemplo:

```html
<div class="credit-item">
  <h3>Versão</h3>
  <p id="app-version">v—</p>
</div>
```

O conteúdo padrão (`v—`) garante funcionamento:

* No browser
* Em previews
* Em testes
* Em ambientes sem Cordova

---

## 8. Pattern: Dynamic Info Injection

### 8.1 Conceito

Informações técnicas (versão, plataforma) devem ser:

* **Injetadas dinamicamente**
* **Nunca hardcoded**
* **Opcionalmente disponíveis**

---

### 8.2 Adapter Cordova

```js
document.addEventListener('deviceready', () => {
  if (window.cordova && cordova.getAppVersion && window.device) {
    cordova.getAppVersion.getVersionNumber().then(version => {
      const platform = device.platform
      document.getElementById('app-version').textContent =
        `v${version} (${platform})`
    })
  }
})
```

---

### 8.3 Fallback Seguro

```js
if (!window.cordova) {
  document.getElementById('app-version').textContent = 'v—'
}
```

Nunca deve ocorrer erro caso:

* Plugin não exista
* Código rode no browser
* Ambiente seja diferente do esperado

---

## 9. Pattern: Cordova Is Optional

### Princípio

> **O Cordova é um provedor de dados, não um requisito.**

O sistema de créditos:

* Funciona sem Cordova
* Apenas melhora quando Cordova está presente
* Nunca falha se plugins estiverem ausentes

---

## 10. Pattern: Version Source of Truth

### Fonte da versão

A versão exibida deve sempre vir de:

* `config.xml` → `version`
* Build nativo (APK/AAB)
* Nunca de strings fixas no HTML

Isso garante:

* Sincronização com Play Store
* Zero manutenção manual
* Confiabilidade

---

## 11. Pattern: Legal & Credits Safety

### Boas práticas legais

* Evitar nomes de bibliotecas específicas sem licença clara
* Preferir termos genéricos:

  * “bibliotecas de código aberto”
  * “royalty-free”
* Referenciar repositório para licenças completas

Exemplo:

```html
<p>Este jogo utiliza bibliotecas e tecnologias de código aberto.</p>
<p>As licenças aplicáveis estão disponíveis no repositório do projeto.</p>
```

---

## 12. Pattern: UX & Presentation

Recomendações:

* Modal ou overlay
* Scroll interno
* Botão claro de retorno
* Tipografia legível
* Não poluir com informações técnicas excessivas

Créditos devem ser:

* Informativos
* Elegantes
* Discretos

---

## 13. Boas Práticas (KismonStudio Standard)

* Créditos sempre presentes no app
* Versão nunca hardcoded
* HTML funcional sem Cordova
* JavaScript apenas complementa
* Estrutura idêntica entre projetos
* Fácil manutenção e atualização

---

## 14. Casos de Uso

* Jogos mobile
* Aplicações híbridas
* Builds Android / AAB
* Web Apps empacotados
* Versões demo / beta / release

---

## 15. Vantagens do Pattern

* Consistência visual
* Zero retrabalho entre projetos
* Menos bugs de build
* Compatível com browser e mobile
* Facilmente extensível
* Preparado para migração futura (Capacitor, Tauri, etc.)

---

## 16. Conclusão

O **Padrão de Display de Créditos** estabelece uma base sólida, profissional e reutilizável para exibição de créditos em todos os projetos da KismonStudio.

Ele garante que:

1. Créditos sejam estáveis
2. Versões sejam confiáveis
3. Cordova seja opcional
4. O código seja limpo e portátil
5. O padrão escale com o projeto

---
*Este padrão é recomendado para todos os projetos novos e existentes da KismonStudio.*
