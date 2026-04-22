# OCTOPUS INVADERS - GUIA DE IMPLEMENTAÇÃO PASSO A PASSO (OTIMIZADO PARA 9B)

Este guia foi desenhado para implementação modular e incremental. 
**REGRAS PARA O AGENTE (9B):**
1. SEMPRE consulte o arquivo `octopus_invaders_prompt.md` para as cores, velocidades, tamanhos e lógica de cada tipo de polvo.
2. Execute apenas UMA FASE por vez. 
3. Não escreva código para fases futuras antes da hora.
4. Ao final de cada fase, forneça as instruções de teste para o usuário.
5. **PARE E PEÇA PERMISSÃO** antes de avançar para a próxima fase.

---

## 🛠 Metodologia de Teste Visual (Feedback para o Usuário)
Como não podemos ver o canvas, usaremos:
1. **Console Logging:** Imprimir mudanças de estado importantes no console (F12).
2. **Hitbox Debug:** Desenhar contornos vermelhos temporários em torno de colisores.
3. **Validação Verbal:** Perguntar ao usuário se o comportamento X está ocorrendo conforme o planejado.

---

## 🟢 FASE 1: A FUNDAÇÃO ESTÁTICA
**Objetivo:** Criar a estrutura de arquivos e garantir que o Canvas ocupe a tela toda.

- **Arquivos:** `index.html`, `css/styles.css`, `js/config.js`.
- **Tarefa 1:** No `index.html`, criar o canvas `#gameCanvas` e importar os 8 scripts na ordem correta (conforme `octopus_invaders_prompt.md`).
- **Tarefa 2:** No `styles.css`, garantir `100vw/100vh`, `overflow: hidden` e fundo preto `#0D1117`.
- **Tarefa 3:** No `js/config.js`, criar o objeto `CONFIG` com TODAS as constantes de cores, velocidades de balas (tier 1-4) e tamanhos de polvos citados no prompt original.

**🛑 PONTO DE CONTROLE:** 
*Teste:* "Abra o `index.html`. A tela está preta? O cursor do mouse sumiu?"
*Aguardar autorização.*

---

## 🔵 FASE 2: O MOTOR E O JOGADOR (MOVIMENTO)
**Objetivo:** Inicializar o loop de jogo e mover a nave com suavidade (Lerp).

- **Arquivos:** `js/game.js`, `js/player.js`.
- **Tarefa 1:** No `game.js`, implementar `init()` (redimensionar canvas) e `loop()` usando `requestAnimationFrame`.
- **Tarefa 2:** No `player.js`, criar a classe `Player`. Implementar o movimento suave: `this.x += (mouseX - this.x) * 0.35`.
- **Tarefa 3:** Desenhar a nave como um triângulo ciano simples para teste. Adicione um `console.log("Player initialized at:", this.x)` no `init`.

**🛑 PONTO DE CONTROLE:**
*Teste:* "Ao mover o mouse horizontalmente, o triângulo ciano segue o cursor de forma suave? Verifique se há erros no console (F12)."
*Aguardar autorização.*

---

## 🔴 FASE 3: ARTE PIXELADA E INIMIGOS
**Objetivo:** Renderizar os polvos usando grades de pixels (fillRect), não arcos suaves.

- **Arquivos:** `js/enemies.js`.
- **Tarefa 1:** Criar as matrizes de pixels (0s e 1s) para o `small`, `medium` e `boss` octopus.
- **Tarefa 2:** Criar o método `draw()` que itera sobre a matriz e usa `ctx.fillRect(x + col*cellW, y + row*cellH, cellW, cellH)`.
- **Tarefa 3:** Implementar a animação dos tentáculos (togglando a última linha da matriz a cada N frames).
- **Tarefa 4:** Criar a lógica de descida vertical (`y += speed`).

**🛑 PONTO DE CONTROLE:**
*Teste:* "Você vê polvos pixelados descendo na tela? Os tentáculos parecem estar se mexendo?"
*Aguardar autorização.*

---

## 🟡 FASE 4: COMBATE, TIROS E COLISÕES
**Objetivo:** Disparar balas e detectar impactos usando a matemática de distância exigida.

- **Arquivos:** `js/game.js` (update), `js/particles.js`, `js/audio.js`.
- **Tarefa 1:** Implementar o disparo no clique. As balas devem deixar rastros (particles).
- **Tarefa 2:** Implementar a colisão circular: `dx*dx + dy*dy < (r1+r2)*(r1+r2)`.
- **Tarefa 3:** No impacto: inimigo brilha branco por 3 frames, gera explosão de partículas e toca o som `hit` (Web Audio API).
- **Tarefa 4:** Implementar os 4 tiers de upgrade de tiro (conforme nível ou kill count).

**🛑 PONTO DE CONTROLE:**
*Teste:* "Ao clicar, a nave atira? O polvo explode quando atingido? Você ouve o som de laser e impacto?"
*Aguardar autorização.*

---

## 🟣 FASE 5: CHEFE E MODO UNLEASH
**Objetivo:** Implementar a batalha contra o Boss e o power-up final.

- **Arquivos:** `js/game.js`, `js/ui.js`.
- **Tarefa 1:** Criar o Boss Octopus (tamanho 150) a cada 5 níveis. Ele deve ter barra de vida.
- **Tarefa 2:** Ao derrotar o Boss, dropar o `Orb`. Ao pegar o orb, ativar `Unleash Mode` por 5 segundos.
- **Tarefa 3:** No `Unleash Mode`: Tiro em leque massivo, nave brilha branco, efeito de aberração cromática e anel de contagem regressiva ao redor da nave.

**🛑 PONTO DE CONTROLE:**
*Teste:* "O Chefe apareceu? Ao derrotá-lo, você conseguiu ativar o modo Unleash? O anel verde diminui com o tempo?"
*Aguardar autorização.*

---

## ⚪ FASE 6: UI, HUD E POLIMENTO FINAL
**Objetivo:** Adicionar Score, Combo e telas de Menu.

- **Arquivos:** `js/ui.js`, `README.md`.
- **Tarefa 1:** HUD com Score (topo esquerdo), Level (topo centro) e Combo (topo direito), fonte 20px monospace, y=40.
- **Tarefa 2:** Barra de vida no rodapé.
- **Tarefa 3:** Telas de START e GAME OVER (com estatísticas finais).
- **Tarefa 4:** Documentação final no `README.md` com instruções de execução.

**🛑 FINALIZAÇÃO:**
*Teste Final:* "O jogo está completo? O sistema de combo está funcionando?"
