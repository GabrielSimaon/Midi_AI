---
name: unir-mids
description: Une dois ou mais arquivos MIDI (.mid) em um pot-pourri (medley) coeso para palco ao vivo, ou corrige viradas desencaixadas em medleys existentes. Use quando o usuario pedir para unir MIDIs, montar pot-pourri/medley, corrigir viradas entre musicas com BPMs ou ritmos diferentes, fazer quebra de tempo entre faixas, equilibrar brilho e peso entre musicas de um medley, ou chamar a skill unir-mids.
---
# unir-mids

Une multiplos arquivos MIDI (ou corrige transicoes de um medley pronto) com viradas musicais, quebras de tempo precisas, encaixe harmonico e equalizacao uniforme de brilho e peso, prontos para tocar no vanBasco e em teclados arranger General MIDI (como Yamaha PSR-S550).

## When to Use

- O usuario envia 2 ou mais arquivos `.mid` para juntar em um medley/pot-pourri.
- O usuario envia um medley pronto cujas viradas entre musicas estao fora do tempo, apressadas ou com mudancas de BPM instaveis.
- O usuario pede quebra de tempo (breque seco), transicao harmonica ou alinhamento de volume, brilho e peso entre musicas diferentes no mesmo arquivo MIDI.

## Steps

### 1. Inspecionar cada musica e mapear dependencias musicais

Antes de cortar ou emendar qualquer trecho, execute um script Python com `mido` para levantar o perfil completo de cada faixa (ou de cada segmento, se for um medley ja unido):

- **Andamento (BPM) e mapa de tempo**: identificar o BPM principal, variacoes internas e ritardandos finais.
- **Formula de compasso e grade real**: verificar `time_signature` (4/4, 2/4, 6/8) e o deslocamento exato do tempo 1 (downbeat), pois muitos MIDIs comerciais nao comecam exatamente no tick 0 ou possuem offset de grade.
- **Ritmo e estilo**: analisar o padrao de bateria no Canal 10 (bumbo, caixa, chimbal, percussao) para reconhecer se o groove e arrocha, sertanejo universitario, vanera, balada ou forro.
- **Harmonia e resolucao de frase**: identificar o acorde final de resolucao da musica A (junto com a letra/melodia para nunca cortar no meio de uma palavra ou frase) e o acorde de entrada da musica B.
- **Dinamica e automacao**: mapear `program_change`, `CC7` (volume), `CC74` (brilho), `CC64` (sustain), canais de voz guia mutados (`CC7=0`), fade-outs automatizados e eventuais mensagens SysEx ou Bank Select fora de GM.

### 2. Escolher a estrategia de transicao (ritmo, BPM e harmonia)

Defina o tipo de virada a partir da relacao entre a musica A e a musica B:

- **BPMs diferentes ou ritmos contrastantes (padrao recomendado - quebra de tempo direta)**: evite rampas de BPM durante viradas de bateria, pois o cerebro do musico e do cantor perde a referencia de pulso quando a virada acelera ou desacelera. Em vez disso, crie um breque seco no tempo 1 da resolucao harmonica da musica A (acorde cheio + Crash1 no Canal 10), sustente por 1.5 a 2 tempos, solte todas as notas com `note_off` + `CC64=0` e deixe um respiro curto de silencio (1 a 1.5 tempos) para zerar a memoria do andamento anterior.
- **Troca de timbres dentro do silencio**: envie o reset limpo de canais (`CC64=0`, `CC123=0`, `CC121=0`, `program_change`, `CC7`, `CC74`, `CC10`, `CC91`) e o novo `set_tempo` fixo no meio do silencio (pelo menos 0.5 a 1 tempo antes da primeira nota da virada). Isso evita engasgos de polifonia ou cortes de ataque em teclados arranger.
- **Entrada direta na virada travada no novo BPM**: por padrao, entre direto na virada ou intro de bateria da musica B ja 100% travada no BPM novo, sem contagem de baqueta (so inclua contagem `1-2-3-4` no SideStick 37 se o usuario pedir explicitamente).
- **Ancoragem da virada no pulso novo**: se a intro/virada de bateria da musica B for esparsa ou solta, reforce os tempos fortes com bumbo firme (nota 35 ou 36) e uma chamada curta de caixa (nota 38 em semicolcheia antes do tempo 1) para cravar a entrada da banda inteira no mesmo tick do downbeat da musica B.
- **Encaixe harmonico**: garanta que o corte da musica A aconteca em um acorde de repouso (tonica ou dominante resolvida) e que nenhuma nota residual ou silaba de letra da frase seguinte vaze para dentro do breque. Se a musica A terminar em crescendo de prato ou ritardando, feche o crescendo com um ataque limpo de Crash1 + bumbo suave antes do respiro de silencio.
- **BPMs iguais ou muito proximos (diferenca <= 4 BPM) e mesmo estilo**: quando a harmonia permitir (mesma tonalidade, relativa ou preparacao V-I), pode-se usar virada continua de 1 compasso sem silencio, desde que o `set_tempo` mude de forma limpa no inicio da virada e a grade de compasso bata exatamente no tempo 1.

### 3. Equilibrar brilho, peso e compatibilidade GM

Para que todas as musicas do medley tenham a mesma pressao ao vivo sem degraus de volume:

- **Uniformizar peso (baixo e bateria)**: nivele `CC7` de bateria (faixa de 106-112) e baixo (faixa de 112-118) entre todas as musicas e aplique reforco moderado de velocity (+8% a +16%, limitado a 127) em bumbo (35/36), caixa (38/40), pratos (49/57) e baixo, sem exagerar em chocalhos/maracas.
- **Abrir brilho sem estridente**: ajuste `CC74` (Brightness) entre 82 e 88 em violoes, guitarras, pianos, metais/sax e cordas, 74 a 76 na bateria e 72 a 74 no baixo.
- **Preservar automacoes reais**: se algum canal tiver fade-out ou crescendo via multiplos eventos `CC7`, escale os valores proporcionalmente com teto em vez de sobrescrever com valor fixo. Mantenha canais de voz guia em `CC7=0` quando ja estiverem mutados.
- **Limpeza para Yamaha PSR-S550 e vanBasco**: use apenas General MIDI puro (Canal 10 limpo com `PC=0`, sem `XG System On`, sem `GM System On` no meio do arquivo, sem `CC32` proprietario fora de GM e sem `CC120` All Sound Off que corte secamente a cauda de prato/reverb).

### 4. Auditar o arquivo gerado antes de entregar

Rode uma verificacao automatizada em Python no MIDI final e confirme todos os criterios antes de apresentar o arquivo ao usuario:

- Zero notas presas (todo `note_on` com velocity > 0 possui `note_off` correspondente antes da troca de musica e antes do fim do arquivo).
- Zero mensagens SysEx no meio do medley.
- Mapa de andamento limpo (nenhuma rampa de BPM dentro de viradas de bateria; cada musica e sua virada travadas no BPM exato).
- Letras de karaoke e marcadores (`marker`) preservados e sincronizados, sem silabas vazadas apos os cortes.
- Timestamps reais (mm:ss) conferidos para cada breque e entrada de musica.

## Gotchas

- **Rampas de BPM em viradas**: nunca distribua varios `set_tempo` graduais em cima de um fill de bateria; isso quebra a sensacao ritmica ao vivo. Quebre o tempo com breque + respiro e entre ja travado no novo BPM.
- **Cortes fora da grade**: muitos MIDIs tem a frase real deslocada (ex.: downbeat em `.856` de um compasso). Sempre localize o downbeat real olhando bumbo, baixo e mudanca de acorde antes de cortar.
- **Reset `GM System On` no meio do medley**: enviar SysEx `F0 7E 7F 09 01 F7` no meio de um arquivo provoca mudo momentaneo ou engasgo em teclados arranger. Substitua por resets suaves de controladores (`CC64=0`, `CC123=0`, `CC121=0`) + `program_change` durante o silencio.
- **Sobrescrever fade-outs ao subir volume**: sempre cheque a contagem de eventos `CC7` por canal antes de normalizar volumes para nao destruir fade-outs originais.
- **Notas desalinhadas na entrada da banda**: em alguns MIDIs comerciais, baixo/acorde e bateria entram com 4 a 12 ticks de diferenca no primeiro compasso; quantize o downbeat de entrada da musica B para que toda a banda ataque junta apos a virada.
