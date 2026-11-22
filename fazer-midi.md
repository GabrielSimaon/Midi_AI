---
name: fazer-midi
description: "Criar e revisar MIDI multipista e karaoke conforme cada musica: separar audio ou YouTube, medir BPM e origem, transcrever notas, distribuir frases entre instrumentos, revisar harmonia e cifras, ajustar dinamica por trecho, corrigir solos e trocar timbres GM preservando partes aprovadas. Usar para fazer MIDI, sincronizar faixas, dividir piano e guitarra, deixar harmonico, cravar solo, corrigir violao, mudar sample e preparar karaoke no VanBasco."
---

# fazer-midi

## Tratar cada música como um caso próprio

- Determinar instrumentos e funções, ritmo/groove, BPM e variações, compasso, tonalidade/modulações, grade/fase, articulação, timbres, efeitos, volumes e arranjo a partir da música e do pedido atual.
- Transferir o método e os cuidados aprendidos; nunca aplicar automaticamente o BPM, a cifra, a instrumentação, a mixagem ou o estilo de uma música anterior. Exemplos de canais, timbres e parâmetros não são presets obrigatórios.
- Preservar requintes, pausas, contratempos e camadas adicionadas pelo usuário. Melhorar a execução e a organização sem padronizar músicas diferentes nem inventar instrumentos ausentes.

## Respeitar o pedido atual

- Responder em português e entregar arquivos prontos. Não exigir terminal, PowerShell ou CMD do usuário.
- Usar o último MIDI enviado/aprovado como fonte de verdade, mesmo que retome timbres anteriores ou contenha instrumentos novos. Nunca substituir a mixagem do usuário por uma exportação automática antiga.
- Interpretar “o resto está certo, não mexe” como restrição de edição: conservar eventos, canais, volumes, timbres, notas, pausas e tempo fora do escopo. Comparar antes/depois.
- Identificar pista e trecho pelas mensagens MIDI e pela referência. “Piano 1” pode ser o mesmo timbre em duas pistas diferentes. Nylon base e nylon solo podem coexistir em canais distintos.
- Inspecionar arquivos e contexto antes de perguntar; perguntar só quando a ambiguidade puder causar edição errada. Prosseguir com verificações independentes enquanto aguarda.
- Não pedir confirmação repetida para ações já autorizadas. Não prometer funções de interface sem conferir.

## Escolher o fluxo mais curto

| Entrada/pedido | Ação |
| --- | --- |
| Áudio misturado ou vídeo | Conferir referência, separar instrumentos e depois transcrever. |
| Stems prontos | Conferir duração/alinhamento e aproveitar sem separar novamente. |
| MIDI aprovado com problema localizado | Editar apenas a pista/trecho pedido, sem nova transcrição geral. |
| Alternância entre piano, guitarra ou outros instrumentos | Conferir a referência por frase; redistribuir somente notas com evidência e ajustar a dinâmica por trecho. |
| “Trocar sample” com nome GM | Alterar somente Program Change da pista escolhida. |
| Sample real, SoundFont ou VST | Usar quando suportado e renderizar se preciso; não alegar que áudio foi embutido no MIDI comum. |
| Karaokê no VanBasco | Incorporar a letra sincronizada no próprio .mid/.kar quando solicitado. |

Consultar as seções complementares de áudio/transcrição, revisões e arranjo/harmonia/dinâmica incluídas ao final deste arquivo. Esta versão reúne as orientações em um único Markdown.

## Preparar entradas

1. Resolver arquivos pela origem indicada, usando anexos locais quando já disponibilizados. Seguir o fluxo de acesso/salvamento do ambiente. Não inventar IDs, nomes ou caminhos.
2. Para YouTube, confirmar título, versão e canal. Identificar o vídeo por busca não equivale a escutar ou analisar suas notas.
3. Reutilizar cópia correta acessível. Se houver bloqueio, explicar o que falta e tentar outra via autorizada; não alegar separação/transcrição de arquivo inacessível.
4. Verificar Python real, módulos, modelos, FFmpeg e banco de sons antes de processar. Não pressupor GPU, rede ou acesso à pasta MidiDream de outra máquina/conversa.
5. Preservar entrada e transcrição original antes da limpeza. Trabalhar em versão separada salvo substituição solicitada. Não sobrescrever os ajustes manuais do usuário.

## Compartilhar andamento e origem

- Medir BPM na bateria ao longo da música; comparar metade/dobro e conservar mudanças reais de andamento. Não fixar os 98 BPM do caso de origem em outras músicas.
- Em MIDI editado, ler primeiro PPQ, mapa de tempo e ataques das pistas aprovadas. Distinguir mudar andamento global, corrigir ataques, corrigir fase e ajustar duração/articulação.
- Inferir a fase pela grade das partes aprovadas, não presumir tick zero. Uma origem compartilhada deslocada pode ser correta. Não deslocar só o solo para zero e criar atraso/adiantamento relativo.
- Se os ataques já estiverem na grade, dizer isso. Não inventar uma correção de ataques. Tratar finais fora da grade como articulação: alinhar durações somente quando fizer parte do pedido de cravar notas; preservar caudas/legato deliberados quando exigidos.
- Usar grade comum conservando contratempos: estar no BPM não significa tocar só nos tempos fortes. Preservar fusas, tercinas, swing e ornamentos apoiados pela referência.
- Preservar anacruse e silêncio inicial. Não deslocar cada stem até a primeira nota. Mudar somente o evento de BPM não sincroniza ataques.
- Quantizar ataques preservando duração/articulação real; evitar cliques e cortes de acordes. Se alinhar finais, preservar pausas da frase e pares Note On/Off sem duração zero.

## Transcrever e organizar

- Separar baixo, bateria, voz, piano, violão/guitarra e outros quando presentes. Não apresentar “outros” como identificação exata de cada instrumento.
- Usar detector polifônico para acordes, fundamental monofônica para baixo/solo e transientes/classificação por peças para bateria.
- Conferir fundamental, registro, oitavas e harmônicos falsos. Preservar cromatismos, inversões e acordes; não forçar tudo em uma escala arbitrária.
- Usar canais melódicos distintos para partes independentes, especialmente base/solo. Reservar canal humano 10 à bateria GM.
- Se separar chimbal, indicar que já está incluído na bateria completa; não duplicar ao reunir.
- Tratar nomes de stems e classes dos modelos como hipóteses. Uma pista pode conter instrumentos alternados ou vazamentos; conferir quem executa cada frase antes de definir canais ou trocar timbres.
- Em karaokê, avaliar a energia vocal: resíduos não são canto verdadeiro. Não deixar guia residual duplicar a base; informar se ficar mudo/opcional.
- Em novas criações sem mapa aprovado, preferir baixo 1, voz 2, piano 3, violão 4, outros/segundo piano 5, solo 9, bateria 10. Em revisão, respeitar o mapa do usuário e canais novos.

## Corrigir solo, violão e pianos

- Distinguir fragmentação, reataque real, duração curta, conflito de canal, pedal e decaimento do timbre. Não mascarar tudo com sustain global.
- Reunir fragmentos da mesma altura somente se o áudio não indicar novo ataque; manter palhetadas, repetições e pausas legítimas.
- Prolongar caudas apenas onde a fonte sustenta a nota, respeitando próxima articulação e harmonia. Evitar desligamentos ambíguos da mesma altura no mesmo canal.
- Delimitar solo da introdução pela frase real. Usar pista/canal próprio e conferir fundamental, sem selecionar automaticamente o harmônico mais agudo.
- Recuperar poucas notas com evidência do áudio/transcrição original, mantendo a frase aprovada. Não reutilizar alturas nem segundos fixos de um exemplo anterior.
- Se dois pianos estiverem picotados, tratar as duas pistas autorizadas e manter timbre, volumes, ataques válidos e contratempos. Não inventar acordes para preencher vazios.
- Conservar camadas manuais de cordas, pad ou segunda base. Analisar registro/volume/pan sem reequilibrar uma mixagem aprovada sem pedido.

## Trocar timbre sem alterar execução

- Converter GM humano 1–128 para Program Change 0–127: Nylon-str.Gt = GM 25/programa 24; Steel-str.Gt = GM 26/programa 25; Piano 1 = GM 1/programa 0; E.Piano 1 = GM 5/programa 4.
- Para troca simples, alterar só Program Change da pista solicitada; preservar notas, durações, velocities, controles, canais e metadados.
- Captura do nome GM não é sample de áudio. O som exato depende do banco do sintetizador.
- Escolher timbre coerente quando a escolha estiver aberta e informar qual. Respeitar a preferência mais recente, inclusive se o usuário voltar de nylon para steel.
- Em pedido de timbre com efeito, alterar só Program Change e o controle de efeito autorizado. Comparar os demais eventos exatamente; conservar a execução e a mixagem aprovada. Não transformar uma preferência desta música em regra para as próximas.

## Validar e entregar

1. Reabrir a saída; conferir PPQ, tempo, canais, timbres, duração e pares Note On/Off sem deltas negativos.
2. Procurar notas presas, desligamentos órfãos, duplicações e conflitos de altura; não tornar acordes monofônicos para esconder erros.
3. Conferir origem e duração dos arquivos separados. Em nova exportação para importação sincronizada, usar fim comum se necessário. Não padronizar finais de todas as pistas numa revisão restrita.
4. Comparar eventos das partes não autorizadas, incluindo efeitos, controles e novos instrumentos. Para ajuste de duração do solo, comprovar que ataques, alturas, velocities, timbre e demais pistas são idênticos.
5. Renderizar prévia quando útil e possível. Conferir trechos alterados, clipping e silêncio. Validação estrutural não prova fidelidade auditiva; distinguir transcrição automática e revisão manual.
6. Entregar MIDI completo e partes/pacote quando solicitados, guardando conforme regras do ambiente. Confirmar salvamento antes de anunciar disponibilidade.

Auditar eventos em ticks absolutos por pista, preservando metadados e controles. Comparar exatamente as partes protegidas e verificar pares Note On/Off, canais, programas GM, ataques, durações e ausência de notas presas ou deltas negativos. Usar ferramentas locais disponíveis; não depender de um script externo a este arquivo.

Fechar com resumo curto do ajuste e arquivo. Explicar achados reais em vez de alegar perfeição. Se algum recurso faltar, distinguir o que foi concluído do que continua pendente.


## Áudio e transcrição

### Entrada e separação

Descobrir arquivos e ferramentas nesta conversa; não presumir caminhos Windows de outra sessão. Reutilizar MidiDream/pipeline disponível. Para editar MIDI pronto, pular separação e transcrição.

Conferir stems por duração, taxa, canais e conteúdo. Se houver mix original, comparar sua soma e investigar deslocamentos. Duração igual não prova alinhamento. Preservar silêncio inicial e evitar normalização destrutiva.

Preferir Demucs 6-stems quando disponível para voz, baixo, bateria, piano, guitarra e outros. Avaliar vazamentos: nenhum modelo garante isolamento perfeito. Filtros simples não equivalem a separação de fontes. Aproveitar stems fornecidos se faltar modelo; não inventar resultados.

### Medir pulso e grade

Extrair envelope de ataques da bateria por bandas; usar autocorrelação/beat tracking e comparar metade/dobro do BPM. Ajustar período e fase em região estável, validando introdução, meio e fim. Picos de fluxo podem atrasar em relação ao ataque acústico; calibrar, sem aplicar constante universal.

Guardar BPM, fase, duração e confiança. Para tempo constante: ticks = segundos × PPQ × BPM / 60. Para mapa variável, integrar segmentos. Compartilhar a mesma conversão entre instrumentos. Manter anacruse; mudanças de origem devem ser comuns e explícitas.

960 PPQ costuma servir para novas criações; preservar PPQ em revisão. Conservar tercinas/swing e medir erro antes/depois. Para MIDI existente, examinar resíduos dos ataques módulo PPQ/4 e PPQ/8. Uma fase dominante não nula compartilhada pode estar correta.

### Detectores

- Basic Pitch ou equivalente polifônico: piano, guitarra e acordes. Conferir reataques falsos, oitavas e pedal, sobretudo em stems fracos.
- pYIN/YIN: conferir fundamental de baixo e solo. Limitar registro e combinar evidência polifônica em áudio com acordes. Não escolher sempre a nota mais aguda, que pode ser harmônico.
- Bateria: distinguir corpo, esteira, cauda e energia por bandas em bumbo, caixa, tons/surdo, crash/ride e chimbal aberto/fechado. Nem todo pico agudo é caixa.
- Voz: distinguir canto, coro e resíduos de karaokê. Não apresentar letra reconhecida em vazamentos como canto confirmado.

Guardar transcrição original para recuperar notas sem recomeçar a conversão inteira.

### Sustentação

Medir envelope por altura e ataques reais, usando fundamental e alguns harmônicos. Janelas de cerca de 10 ms são um ponto de partida, não regra universal. Reunir pequenos fragmentos só se não houver novo ataque; comparar energia anterior/posterior e respeitar articulação do instrumento.

Parar cauda em silêncio persistente, novo ataque da mesma altura ou mudança harmônica incompatível. Não prolongar só porque há espaço livre no MIDI. Distinguir ressonância de piano, cordas dedilhadas, legato de solo e cauda de prato.

Violão decai naturalmente com Note Off longo. Se o usuário ouvir cortes, investigar timbre e articulação antes de adicionar pedal ou duplicar notas. Finais fora da grade podem ser legato intencional; não quantizar indiscriminadamente.

### Execução e modelos

Verificar executável Python real, módulos, FFmpeg, SoundFont e pesos de modelos. Um pacote Basic Pitch com TensorFlow pode fornecer `ICASSP_2022_MODEL_PATH` como diretório; ONNX Runtime precisa do arquivo `.onnx` efetivo. Conferir a instalação, sem fixar caminho de outra máquina.

Em escrita restrita, configurar `NUMBA_CACHE_DIR`, `TEMP` e `TMP` para pastas autorizadas antes dos imports. Cache travado não é processamento musical. Usar logs e diagnóstico com tempo limitado; não deixar processos órfãos ou interromper processos alheios.

Limitar threads, reutilizar sessões ONNX e não reinstalar dependências globais para revisões pequenas.

### Karaokê e verificação

Quando pedido, incorporar letra alinhada no próprio .mid/.kar com quebras/codificação compatíveis com VanBasco e acentos. Não substituir por arquivo de texto, cifras exibidas ou sílabas espaçadas uniformemente sem conferir. Não alegar sincronismo silábico sem evidência temporal.

Renderizar com banco GM quando disponível. Medidas de RMS/pico detectam silêncio e clipping, mas não provam afinação nem fidelidade melódica. Separar conferência técnica de auditiva.


## Revisões e aprendizado transferível

### Preservação

Guardar a última fonte enviada e comparar mensagens MIDI incluindo tempos, controles, metadados e timbres. Revisão nova deve partir da versão manual mais recente, não da primeira saída automática. Mudanças pontuais com mido evitam reconstrução geral e normalização desnecessária.

Ao reconstruir uma pista, conservar eventos de pedal, expressão, volume e outros controles também quando ocorrerem depois do tick zero. Ordenar Note Off antes do novo Note On da mesma altura se necessário. Preservar todas as pistas fora do escopo evento por evento.

### Diagnóstico

| Relato | Conferir | Correção |
| --- | --- | --- |
| Solo picotando | Fragmentação, duração, registro, harmônicos, timbre | Conferir fundamental e unir fragmentos reais, conservando pausas. |
| Instrumentos se cortam | Canal compartilhado e Note Off da mesma altura | Isolar canais e corrigir pares sem apagar outra parte. |
| Faltam poucas notas | Fonte e transcrição original, limiar e registro | Recuperar candidatas comprovadas, conservando a frase aprovada. |
| Dois Piano 1 | Programas iguais em canais distintos | Identificar as duas pistas e tratar só elas. |
| Trocar sample | Nome GM versus áudio real | Alterar só Program Change para GM; usar sintetizador/renderização para sample real. |
| Cravar BPM do solo | Tempo, fase e ataque versus final das notas | Não deslocar ataques que já coincidem com a base; ajustar só o componente realmente necessário. |

### Solo

Delimitar a introdução pela referência, sem assumir sempre 15 segundos. Se o solo estiver na base, separar somente suas notas, sem eliminar acordes ou duplicatas legítimas de outros instrumentos.

Não selecionar cegamente o candidato mais agudo nem o primeiro numa janela temporal ampla: isso pode escolher harmônicos ou inverter ataques. Conferir ordem, duração positiva e continuidade. Recuperação de poucas notas deve apoiar-se na fonte, sem reutilizar alturas/segundos de outro caso.

### Observações da mixagem manual v9 de Eu Não Sou Terapia

Usar este caso como exemplo, nunca como preset obrigatório para outra música:

- Arquivo recebido: 12 pistas, 960 PPQ, um tempo de 612245 µs/semínima (~98 BPM) e duração de ~161,223 s. Esses valores são próprios do arquivo, não padrões globais.
- O usuário manteve solo nylon separado e base steel, acrescentando uma segunda base nylon em outro canal. Preferência manual mais recente prevalece sobre a troca anterior da base para nylon.
- O segundo piano passou a E.Piano 1, diferenciando sua cor do piano acústico. Novas camadas de Slow Strings e Warm Pad usam registros, volumes e panorâmicas distintos. Conservar esses arranjos em pedidos restritos ao solo.
- Steel e pianos têm ataques em semicolcheias numa fase compartilhada de 79 ticks módulo 240. Os 14 ataques do solo já estavam nessa mesma grade; deslocá-lo para a fase zero criaria erro de ~50 ms em relação à base.
- Os finais do solo continham pequenas caudas/legatos fora da grade. Na revisão v10 solicitada, foram ajustados apenas os 14 finais a semicolcheias da mesma fase, preservando ataques, alturas, velocities, timbre e todas as outras 11 pistas. Não transformar esse ajuste específico em regra de eliminar legato em todo solo.
- Os finais de pista não eram iguais. Isso não prejudicava a mixagem e não autorizava padronizá-los numa correção localizada.
- A guia vocal residual continuava muda; respeitar sua configuração, sem introduzir falsa melodia vocal.

Sem arquivo anterior acessível, não afirmar quais notas exatas o usuário mudou. Descrever características observadas e separar inferências de comparações comprovadas.

### Aplicações futuras

Medir novamente BPM/fase, identificar funções/canais, preservar camadas do usuário e demonstrar o diff permitido. Não fixar tonalidade, duração, índices, nomes, alturas, tempos ou limiares do exemplo. Explicar quando os ataques já estão certos, em vez de alegar uma correção inexistente. Não confundir estrutura MIDI válida com fidelidade de ouvido.


## Arranjo, harmonia e dinâmica conforme a música

### Conferir a referência

- Resolver o vídeo ou áudio exato antes de distribuir funções. Conferir versão, canal, duração, introdução, seções e final; não substituir um karaokê por gravação ao vivo ou outra edição só porque o título coincide.
- Usar as faixas separadas já disponíveis. Quando houver referência acessível, comparar sua mistura com a soma dos stems e medir o deslocamento comum. Correlação e duração ajudam a confirmar a fonte, mas não identificam por si só o instrumento de uma nota.
- Pedir o link ou arquivo que falta somente após conferir as entradas disponíveis. Continuar análises independentes enquanto aguarda. Distinguir acesso ao vídeo, análise do áudio e conferência auditiva efetivamente realizada.

### Dividir piano, guitarra e outros instrumentos

- Conferir quem toca cada frase, resposta, base e solo ao longo da música. Não manter uma execução inteira como guitarra ou piano por causa do nome da pista; permitir alternâncias e trechos simultâneos quando a referência os sustentar.
- Apoiar a decisão no áudio, ataques, envelope, registro, polifonia e evidência por altura nos stems. CQT/chroma e energia ajudam a localizar candidatos, mas volume maior não prova identidade instrumental. Considerar vazamento, níveis diferentes entre stems, harmônicos e sons parecidos.
- Adaptar janelas, limiares e critérios de confiança à gravação. Não transportar razões de energia, tempos de seção ou contagens de notas de um caso anterior. Manter casos ambíguos sem transferência automática e delimitar a incerteza.
- Na revisão, partir do último MIDI do usuário; mover somente os pares Note On/Off autorizados ao canal correto. Preservar altura, ataque, velocity, frase e requintes, exceto componentes cuja correção foi pedida.
- Ajustar articulação ao instrumento e à referência: uma frase de piano não deve herdar o abafamento aplicado à base de guitarra. Não encurtar um solo sustentado só porque a base é percussiva.
- Procurar duplicações coincidentes após a redistribuição. Unir notas da mesma altura/ataque apenas quando forem duplicatas indevidas; preservar uníssonos e camadas intencionais. Resolver desligamentos conflitantes sem eliminar ataques legítimos.

### Revisar cifra e harmonia

- Distinguir indicação de tonalidade, cifras/markers e notas que realmente soam. Alterar key_signature ou texto não transpõe nem corrige o áudio MIDI.
- Conferir acordes por seção com baixo, acompanhamento e referência; considerar relativo maior/menor, inversões, extensões, notas de passagem, antecipações e modulações. Não supor uma única sequência repetida nem forçar toda nota a uma escala.
- Se as cifras já combinarem com a referência, dizer isso; investigar voicings incompletos, notas sustentadas sobre a troca de acorde, registro, duplicações, efeitos e equilíbrio antes de rearmonizar.
- Completar acordes ou alterar alturas somente com autorização no pedido de harmonia e evidência musical. Respeitar voicings abertos, acordes sem terça, pausas e tensão intencional; não acrescentar tríades ou cordas como preenchimento automático.
- Preservar letra, marcações e sincronismo do karaokê ao mexer em timbres, arranjo e harmonia. Alterar cifras/tonalidade apenas onde necessário e explicar o efeito real.

### Equilibrar por trecho

- Identificar instrumento principal e acompanhamento em cada passagem. Reduzir a parte que compete com a frase principal e recuperar seu nível na troca de função; não usar um volume único como substituto da revisão do arranjo.
- Usar CC7 para nível base e CC11 para expressão por trecho quando adequado. Adaptar curvas à execução, suavizar mudanças e evitar bombeamento, silêncio involuntário ou excesso de controles por nota.
- Comparar níveis relativos dentro de cada stem; diferenças de ganho da separação não equivalem diretamente à intenção da mixagem. Não tratar vazamento amplificado como solo.
- Conferir controles compartilhados: pistas no mesmo canal compartilham volume, expressão, timbre e efeitos. Para partes melódicas independentes, usar canais distintos quando preciso; para bateria GM no canal 10, equilibrar peças por velocity em vez de presumir volumes separados por pista.
- Na renderização, conferir atividade, picos/clipping e transições alteradas. Tratar caudas de efeitos e silêncio final sem cortar notas. Não confundir essas medições com prova de fidelidade auditiva.

### Escolher timbre e efeito sem alterar o arranjo

- Manter a escolha dependente do estilo, da função da parte, da referência, do banco de sons e da preferência atual. Guitarra abafada, clean com chorus ou baixo synth são alternativas, não padrões do fazer-midi.
- Usar corretamente as constantes GM quando escolhidas: Electric Guitar Clean = GM 28/programa 27; Muted Guitar = GM 29/programa 28; Electric Bass Finger = GM 34/programa 33. Interpretar baixo normal como baixo elétrico convencional quando o contexto indicar a oposição ao synth.
- Para chorus moderado, escolher CC93 conforme o sintetizador e o resultado; não fixar o valor usado em outra música. Conferir suporte a efeitos, reverberação e chorus no destino. MIDI comum não embute o sample original.
- Em troca simples, provar por comparação que somente Program Change e efeitos pedidos mudaram. Em redistribuição/dinâmica, relatar notas movidas, duplicatas resolvidas e controles novos; verificar exatamente as pistas protegidas, a letra, o tempo e os demais requintes.