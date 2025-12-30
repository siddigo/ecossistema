# 🌿 Ecossistema — Simulação de Vida Artificial

Simulação de um **ecossistema vivo** em JavaScript utilizando **Vue/Nuxt**, onde diferentes entidades — como **plantas, coelhos e lobos** — interagem entre si de forma autônoma, criando um ciclo de vida dinâmico que busca equilíbrio natural.

Cada indivíduo do sistema tem um conjunto de comportamentos programados que refletem aspectos básicos de sobrevivência e reprodução, resultando em um ambiente de simulação visual e interativo.

---

## 🎯 Objetivo do Projeto

O foco deste projeto é:

- Criar uma simulação que represente um ecossistema com regras simplificadas inspiradas no ambiente natural.
- Explorar a emergência de comportamentos complexos a partir de regras simples.
- Permitir observação de dinâmicas entre predadores, presas e recursos.
- Utilizar JavaScript com **Vue/Nuxt** para visualização interativa.

Este projeto explora como agentes autônomos interagem e se adaptam ao ambiente, gerando padrões interessantes de população e equilíbrio.

---

## 🧠 Conceitos Aplicados

Este projeto aborda:

- **Simulação baseada em agentes** — cada organismo possui regras próprias de comportamento.
- **Comportamento autônomo** — organismos agem independentemente em um ambiente compartilhado.
- **Interações ecológicas simplificadas** — predadores, presas e recursos interagem continuamente.
- **Visualização interativa** no navegador.

---

## 🐾 Como a Simulação Funciona

O ecossistema contém três categorias principais de entidades:

### 🌱 Plantas
- Não se movem.
- Servem como alimento inicial na cadeia.
- Podem surgir espontaneamente no ambiente.

### 🐇 Coelhos
- Herbívoros que se alimentam de plantas.
- Têm energia que diminui com o tempo.
- Podem se reproduzir sob certas condições.
- Foge de predadores quando detectados.

### 🐺 Lobos
- Predadores que caçam coelhos para sobreviver.
- Movimentam-se de forma ativa em busca de presas.
- Possuem energia que deve ser reposta.

---

## 🔄 Regras de Vida e Interações

Cada entidade segue ciclos de vida básicos:

- 🎂 Nascimento  
- 🚶 Movimentação  
- 🍽️ Busca por alimento  
- ⚡ Consumo de energia  
- ❤️ Reprodução  
- ☠️ Morte por fome ou idade

Essas regras criam um **ciclo contínuo de vida**, onde populações de organismos oscilam dependentes umas das outras.

---

## 📈 Comportamento Emergente

Devido à autonomia dos organismos:

- As populações podem crescer rapidamente ou diminuir drasticamente.
- Pode ocorrer superpopulação de presas quando há poucos predadores.
- Pode ocorrer escassez de presas se predadores forem dominantes.
- Pequenas mudanças nos parâmetros externos influenciam o equilíbrio geral.

Esse tipo de comportamento é típico em modelos ecológicos e pode ser observado em tempo real na simulação.

---

## 📌 Porque Este Projeto é Relevante

- Permite entendimento prático de sistemas multiagente.
- Demonstra como regras simples podem produzir resultados complexos.
- Pode ser utilizado como base para estudos de:
  - Dinâmica populacional
  - Comportamento em sistemas complexos
  - Jogos e simulações interativas

---

## 🧑‍💻 Autor

**Sidnei Rodrigo dos Santos**

Este projeto reflete estudo técnico de simulação de agentes, lógica de programação e aplicação de ferramentas modernas de frontend para simulação visual.



# Nuxt Minimal Starter

Look at the [Nuxt documentation](https://nuxt.com/docs/getting-started/introduction) to learn more.

## Setup

Make sure to install dependencies:

```bash
# npm
npm install

# pnpm
pnpm install

# yarn
yarn install

# bun
bun install
```

## Development Server

Start the development server on `http://localhost:3000`:

```bash
# npm
npm run dev

# pnpm
pnpm dev

# yarn
yarn dev

# bun
bun run dev
```

## Production

Build the application for production:

```bash
# npm
npm run build

# pnpm
pnpm build

# yarn
yarn build

# bun
bun run build
```

Locally preview production build:

```bash
# npm
npm run preview

# pnpm
pnpm preview

# yarn
yarn preview

# bun
bun run preview
```

Check out the [deployment documentation](https://nuxt.com/docs/getting-started/deployment) for more information.
