# A flor e o vento

Página única em HTML + JavaScript + Three.js (r128): um lírio 3D num jardim ao entardecer. Cada clique tira uma
pétala, que voa até o centro e mostra uma frase; ao tirar a sexta, a flor desabrocha numa margarida, cai uma chuva
de corações e "eu te amo" e abre a telinha com a mensagem. Tem música com controle de volume.

## Conteúdo da pasta

```
a-flor-e-o-vento/
├─ index.html                      a página inteira (HTML, CSS e JavaScript)
├─ README.md                       este arquivo
├─ vendor/LEIA-ME.txt              como usar sem internet (opcional)
└─ assets/
   └─ wave_to_earth_-_love___lyrics__-_onlymaruu.mp3   música original, sem alterações
```

O caminho do áudio é relativo ao `index.html` e está na constante `AUDIO_SRC`, no começo do script. Para trocar a
música, coloque o arquivo em `assets/` e mude esse nome.

## Como rodar no computador

**Opção 1 (mais simples):** dê duplo clique em `index.html`. Funciona, mas o volume é controlado direto no
elemento de áudio (funciona em computador e Android; no iPhone o volume do aparelho continua mandando).

**Opção 2 (recomendada):** abra um servidor local dentro da pasta e acesse pelo navegador.

```
cd a-flor-e-o-vento
python3 -m http.server 8080
```

Depois abra http://localhost:8080 (ou use `npx serve .`). Assim o volume do site funciona em todos os aparelhos.

**Precisa de internet na primeira abertura**, para o Three.js (cdnjs) e as fontes (Google Fonts). Para usar sem
internet, veja `vendor/LEIA-ME.txt`. Para publicar, serve qualquer hospedagem estática (GitHub Pages, Netlify etc.),
de preferência em HTTPS.

## Música e controles

- Ao abrir, a música **já entra tocando, com o volume em 30%** (o botão mostra "tocando" e a barra está em 30%). A página
  não guarda volume nem pausa de visitas anteriores: sempre abre assim.
- **Os navegadores não deixam nenhum site ligar som sem uma interação do visitante** (política de autoplay, vale para Chrome,
  Safari e Firefox, e a página não tem como contornar). Por isso há dois casos: se o navegador permitir som automático, a
  música sai sozinha desde o primeiro segundo; se bloquear, ela já fica rodando **muda** e o som entra, desde o início da
  faixa, no primeiro toque, clique ou tecla em qualquer lugar (aparece o aviso "clique para ouvir" e o alto-falante pulsa).
  No Chrome dá para liberar pelo cadeado da barra de endereço → Configurações do site → Som → Permitir.
- Canto inferior esquerdo: tocar/pausar, mudo e barra de volume (no computador a barra aparece ao passar o mouse ou focar; no
  celular fica sempre visível). Funciona com mouse, toque e teclado. O número da barra é o volume do arquivo (30% = 0,3).
- A música pausa com a aba oculta e retoma ao voltar.

## Teclas

| Tecla | O que faz |
|---|---|
| Espaço / Enter | tira uma pétala (alternativa ao clique) |
| K | tocar / pausar a música |
| M | ligar / desligar o som |
| Esc | fecha a telinha da mensagem |
| F | mostra o medidor: fps, tempo de quadro, pico, tempo de CPU, resolução, nível de qualidade, chamadas de desenho |
| Q | alterna a qualidade manualmente (alta → média → baixa → mínima → automático) |

## Desempenho: meta × o que foi de fato medido

**Meta:** 60 fps ou mais, em Full HD (1920×1080) no computador e ~1,2 MP no celular.

**Como a página tenta chegar lá**

- Na tela de carregamento (nada visível), ela renderiza 20 quadros de verdade esperando a GPU terminar e escolhe a
  combinação de resolução e quantidade de detalhe (árvores, grama, flores, chuva) que cabe em ~11,5 ms. Não há troca
  de resolução em loop: depois disso ela só **desce** um degrau se o fps ficar abaixo de ~49 por 2,5 s, e nunca sobe.
- Movimento independente da taxa de quadros (tudo usa o tempo real), funciona em telas de 90/120/144 Hz.
- Instâncias (árvores, grama, flores, chuva) em poucas chamadas de desenho, sem alocar memória a cada quadro, objetos
  estáticos sem recalcular matrizes, luzes baratas, desfoque em baixa resolução.
- Trata mudança de aba, redimensionamento, rotação da tela, perda e restauração do contexto WebGL e a preferência
  de sistema por movimento reduzido.

**O que foi verificado no ambiente de desenvolvimento (Chromium, sem GPU):** erros de JavaScript, lógica completa
(7.000 passos aleatórios), compilação real de todos os shaders, layout em 7 tamanhos de tela (1920×1080, 1366×768,
celular em pé e deitado, 360×640, tablets), alvos de toque, áudio (tocar, pausar, volume, mudo, teclas, aba oculta,
persistência, plano B quando o navegador bloqueia o `<audio>`, versão local por http e por file://), política de
autoplay real do Chromium (bloqueado e liberado), movimento reduzido e perda de contexto.

**O que NÃO foi medido:** fps real em GPU. Não há GPU nem o Three.js nesse ambiente, então qualquer número de fps seria
inventado. Use a tecla **F** no seu aparelho para ver os números reais. Se ficar abaixo de 60, aperte **Q** para baixar a
qualidade e, se puder, me diga o que o medidor mostra.

## Limitações conhecidas

- Não foi testado em Safari/iOS, Firefox nem em aparelhos reais; só em Chromium.
- No iPhone/Safari o som precisa de um toque (regra do sistema) e o volume do site só funciona via servidor (http/https).
- A versão publicada como link (claude.ai) usa a mesma música regravada a 160 kbps, porque a página inteira precisa
  caber em 16 MB; esta pasta usa o arquivo original sem alterações.
- Three.js e fontes vêm da internet (veja a seção de uso offline).
