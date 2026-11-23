---
name: unir-mids
description: Une dois ou mais arquivos MIDI (.mid) em um pot-pourri (medley) coeso para palco ao vivo com virada de 1 compasso grudada no ritmo, ou corrige viradas desencaixadas em medleys existentes. Use quando o usuario pedir para unir MIDIs, montar pot-pourri/medley, fazer virada grudada no ritmo, corrigir viradas entre musicas com BPMs ou ritmos diferentes, fazer quebra de tempo entre faixas, equilibrar brilho, peso e normalizar volume entre musicas de um medley, ou chamar a skill unir-mids.
---
# unir-mids

Une multiplos arquivos MIDI (ou corrige transicoes de um medley pronto) com virada de 1 compasso grudada no ritmo, encaixe harmonico preciso, brilho, peso e volume normalizado entre todas as musicas, prontos para tocar no vanBasco e em teclados arranger General MIDI (como Yamaha PSR-S550).

## When to Use

- O usuario envia 2 ou mais arquivos `.mid` para juntar em um medley/pot-pourri.
- O usuario envia um medley pronto cujas viradas entre musicas estao fora do tempo, apressadas ou com mudancas de BPM instaveis.
- O usuario pede virada grudada no ritmo, quebra de tempo, transicao harmonica ou alinhamento de brilho, peso e volume entre musicas diferentes no mesmo arquivo MIDI.

## Steps

### 1. Inspecionar cada musica e mapear dependencias musicais

Antes de cortar ou emendar qualquer trecho, execute um script Python com `mido` para levantar o perfil completo de cada faixa (ou de cada segmento, se for um medley ja unido):

- **Andamento (BPM) e mapa de tempo**: identificar o BPM principal, variacoes internas e ritardandos finais. Quando o usuario deixar a ordem livre, prefira a sequencia que sobe ou mantem a energia na virada (ex.: andamento ligeiramente menor -> maior).
- **Formula de compasso e grade real**: verificar `time_signature` (4/4, 2/4, 6/8) e o deslocamento exato do tempo 1 (downbeat), pois muitos MIDIs comerciais nao comecam exatamente no tick 0 ou possuem offset de grade.
- **Ritmo e estilo**: analisar o padrao de bateria no Canal 10 (bumbo, caixa, chimbal, percussao) para reconhecer o groove (disco/soul, pop, arrocha, sertanejo universitario, vanera, balada, forro).
- **Harmonia e resolucao de frase**: identificar o acorde final de resolucao da musica A (junto com a letra/melodia para nunca cortar no meio de uma palavra ou frase) e o acorde de entrada da musica B.
- **Dinamica e automacao**: mapear `program_change`, `CC7` (volume), `CC74` (brilho), `CC64` (sustain), `velocity` media por instrumento, canais de voz guia mutados (`CC7=0`), fade-outs automatizados e eventuais mensagens SysEx ou Bank Select (`CC0`/`CC32`) fora de GM.

### 2. Escolher a estrategia de transicao (padrao: virada grudada no ritmo)

Defina a virada a partir da relacao entre a musica A e a musica B, priorizando sempre manter o pulso firme e natural ao vivo:

- **Padrao principal — Virada de 1 compasso grudada no ritmo (sem buraco de silencio)**:
  - Use como padrao para medleys dançantes/ao vivo (mesmo estilo ou estilos compativeis, com BPMs iguais ou proximos) e sempre que o usuario pedir virada grudada no ritmo.
  - **Tempo 1.0 do compasso de virada (resolucao da musica A)**: feche a ultima frase/palavra da musica A sobre o acorde de tonica + Bumbo (`36`, vel ~122) + Crash1 (`49`, vel ~118), e troque o `set_tempo` **de forma limpa e imediata** para o BPM fixo da musica B ja neste tempo 1 (zero rampa de BPM durante a virada!).
  - **Tempo 2.0**: solte todas as notas melodicas/harmonicas da musica A com `note_off` + `CC64=0` + `CC123=0`, mantendo a bateria no Canal 10 tocando continuamente sem perder o pulso.
  - **Tempo 2.5 (inicializacao silenciosa da musica B)**: envie nos canais melodicos o reset limpo e os novos timbres (`CC121=0`, `program_change`, `CC7`, `CC11=127`, `CC74`, `CC10`, `CC91`, `CC93`). Isso da ~0,6s a 0,8s de folga antes da primeira nota da musica B, evitando engasgos de polifonia ou cortes de ataque no Yamaha PSR-S550.
  - **Construcao da virada de bateria (Canal 10, GM Standard Kit `PC=0`, 100% travada no BPM novo)**:
    - **Tempo 1.0**: Bumbo (`36`, vel ~122) + Crash1 (`49`, vel ~118)
    - **Tempo 2.0**: Bumbo (`36`, vel ~114) + Caixa (`38`, vel ~112) + Chimbal fechado (`42`, vel ~92), com resposta de Caixa (`38`, vel ~96) no contratempo **2.5**
    - **Tempos 3.0 a 3.75**: Cascata de tons em semicolcheias — Tom agudo (`48`) em **3.0** e **3.25** + Tom medio (`45`) em **3.5** e **3.75**, ancorada por Bumbo (`36`, vel ~116) em **3.0**
    - **Tempo 4.0 a 4.75**: Bumbo (`36`, vel ~118) + Caixa (`38`, vel ~118) + Surdo (`43`, vel ~114) em **4.0**, fechando com chamada firme de Caixa (`38`) em **4.5** e semicolcheia **4.75** (vel ~122) cravando a entrada!
  - **Compasso seguinte, Tempo 1.0 (entrada explosiva da musica B)**: quantize todas as notas de ataque inicial da musica B para o mesmo tick exato do downbeat e reforce o tempo 1 com Bumbo (`36`, vel ~124) + Crash duplo (`49` vel ~122 + `57` vel ~110).
  - Nao inclua contagem de baqueta (`1-2-3-4` no SideStick 37), a menos que o usuario peca explicitamente.

- **Alternativa — Quebra de tempo seca com respiro curto** (usar apenas quando houver contraste radical de BPM/estilo que nao comporte virada continua, como balada lenta para forro rapido, ou quando o usuario pedir breque seco):
  - Faca um breque seco no tempo 1 da resolucao harmonica da musica A (acorde cheio + Crash1), sustente 1,5 a 2 tempos, solte todas as notas (`note_off` + `CC64=0`), deixe 1 a 1,5 tempos de silencio, envie a inicializacao silenciosa + novo `set_tempo` fixo no meio do silencio e entre direto na virada/intro da musica B travada no novo BPM.

### 3. Equilibrar brilho, peso, normalizacao de volume e compatibilidade GM

Para que todas as musicas do medley tenham exatamente a mesma pressao e volume ao vivo, sem degraus na troca de faixa:

- **Normalizacao real de volume (velocity + CC7)**: calcule a `velocity` media de cada instrumento nas duas musicas e normalize para a mesma faixa-alvo antes de unir (ex.: Piano ~96, Guitarra base ~96, Solo ~106, Sopro 1 ~102, Sopro 2 ~96, Baixo ~112, Strings/Coral ~78–84), garantindo que a media global de velocity entre as musicas fique praticamente identica.
- **Uniformizar peso (baixo e bateria)**:
  - Nivele `CC7` de bateria (`112`) e baixo (`116`) com `CC11=127` em todas as musicas.
  - Normalize as pecas da bateria no Canal 10: Bumbo (`35/36`) ~118, Caixa (`38/40`) ~112, Tons ~108, Pratos Crash (`49/57`) ~116, mantendo Chimbal (`42/44/46`) ~92 e Pandeiro/Percussao (`54`) controlado ~82 sem soar estridente.
  - Separe `Bateria (GM Kit)` e `Chimbal / Percussao` em duas faixas limpas no Canal 10 (`PC=0`).
- **Abrir brilho sem estridente (`CC74`)**: ajuste `CC74` (Brightness) entre `84` e `88` em pianos, violoes, guitarras, metais/sopros e cordas, `76` na bateria e `74` no baixo, com `CC91` (reverb) seco no baixo (`8`), moderado na bateria (`24`) e aberto na harmonia/metais (`30–42`).
- **Preservar automacoes reais**: se algum canal tiver fade-out ou crescendo via multiplos eventos `CC7`, escale os valores proporcionalmente ao novo teto em vez de sobrescrever com valor fixo. Mantenha canais de voz guia em `CC7=0` quando ja estiverem mutados.
- **Limpeza para Yamaha PSR-S550 e vanBasco**: converta para `960 PPQN`, `SMF Format 1`, General MIDI puro — Canal 10 obrigatoriamente em `PC=0` (GM Standard Kit), zero `SysEx` (`XG System On` / `GM System On`), zero Bank Select proprietario (`CC0`/`CC32`) e zero `CC120` (All Sound Off).
- **Letras sincronizadas em `.mid` e `.kar`**: preserve todas as letras existentes e recem-sincronizadas em codificacao `latin1` (`iso-8859-1`), entregando tanto `.mid` (`0xFF 0x05` com `\r`) quanto `.kar` (cabecalho `Soft Karaoke` + faixa `Words` `0xFF 0x01` + `0xFF 0x05`).

### 4. Auditar o arquivo gerado antes de entregar

Rode uma verificacao automatizada em Python nos arquivos finais (`.mid` e `.kar`) e confirme todos os criterios antes de entregar:

- Zero notas presas (`hanging == 0` antes da virada e no fim do arquivo).
- Zero mensagens `SysEx`, zero `CC0`/`CC32` e zero `CC120`; Canal 10 exclusivamente em `PC=0`.
- Mapa de andamento limpo (zero rampas de BPM; cada musica e a virada travadas no BPM exato).
- `Velocity` media normalizada e equilibrada entre todas as musicas do medley.
- Letras em `latin1` (acentos verificados em binario sem UTF-8) e marcadores (`marker`) sincronizados nos timestamps exatos (`mm:ss`).

## Gotchas

- **Rampas de BPM em viradas**: nunca distribua varios `set_tempo` graduais em cima de um fill de bateria; isso quebra a sensacao ritmica ao vivo. Troque o `set_tempo` de forma limpa ja no tempo 1 da virada para que o compasso inteiro de bateria fique travado no pulso da musica seguinte.
- **Cortes fora da grade**: muitos MIDIs tem a frase real deslocada. Sempre localize o downbeat real olhando bumbo, baixo e mudanca de acorde antes de cortar.
- **Reset `GM System On` no meio do medley**: enviar SysEx `F0 7E 7F 09 01 F7` no meio de um arquivo provoca mudo momentaneo ou engasgo em teclados arranger. Substitua por `CC64=0`, `CC123=0`, `CC121=0` + `program_change` no tempo 2.5 do compasso de virada.
- **Sobrescrever fade-outs ao subir volume**: sempre cheque a contagem de eventos `CC7` por canal antes de normalizar volumes para nao destruir crescendos ou fade-outs originais.
- **Notas desalinhadas na entrada da banda**: em alguns MIDIs comerciais, baixo/acorde e bateria entram com 4 a 16 ticks de diferenca no primeiro compasso; quantize o downbeat de entrada da musica B para o tick exato da entrada para que toda a banda ataque junta apos a virada.
