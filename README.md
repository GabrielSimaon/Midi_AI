# Midi_AI

Arquivos de instruções para criar e revisar MIDI e karaokê com ajuda de inteligência artificial.

O material principal é o **fazer-midi**: orienta a IA na separação de instrumentos, transcrição, sincronização, revisão de notas, escolha de timbres e preparação de letras para o VanBasco. Cada música deve ser analisada individualmente.

## Arquivos

| Arquivo | Para que serve |
| --- | --- |
| [fazer-midi.md](fazer-midi.md) | Orientações completas para criar e revisar MIDI multipista e karaokê. |
| [Fix-mid.md](Fix-mid.md) | Prompt geral para diagnosticar e corrigir problemas de código; apesar do nome, não contém um procedimento específico de edição musical. |

Estes arquivos são instruções em Markdown para uma IA. Não são um programa instalável nem executam a conversão sozinhos. O processamento depende das ferramentas, modelos e arquivos disponíveis no ambiente da IA.

## Como fazer um MIDI com IA

1. Abra uma conversa com uma IA que consiga ler arquivos e executar processamento de áudio/MIDI.
2. Anexe o arquivo **fazer-midi.md** e peça para seguir suas orientações. Se não houver suporte a anexos, copie o conteúdo para a conversa.
3. Envie a música em WAV ou MP3, as faixas separadas ou o MIDI que deseja revisar. Se enviar um link do YouTube, identifique a versão correta; se a IA não conseguir acessar o áudio, envie o arquivo.
4. Informe o resultado desejado: instrumentos, referência, BPM se conhecido, destino (teclado, sintetizador ou VanBasco) e se deseja letra incorporada.
5. Peça primeiro a análise da referência e das ferramentas disponíveis. Para áudio misturado, o fluxo é separar instrumentos, medir o andamento, transcrever e revisar. Com stems prontos, a separação pode ser pulada.
6. Solicite o MIDI completo e, se necessário, as pistas separadas e uma prévia em áudio.
7. Escute o resultado e peça correções por pista e trecho. Envie sempre sua versão mais recente para preservar ajustes manuais.

### Exemplo para criar

> Siga as orientações do arquivo fazer-midi.md. Analise a música anexada e crie um MIDI multipista com os instrumentos presentes na referência. Confira o BPM e o alinhamento comum das faixas, preservando pausas, contratempos e articulação. Separe base e solo quando necessário e use o canal 10 para bateria GM. Entregue o MIDI completo e, se houver recursos disponíveis, uma prévia em áudio. Informe limitações e o que foi efetivamente verificado.

### Exemplo para revisar

> Use este MIDI como a versão mais recente. Corrija apenas o solo entre [início] e [fim], comparando com o áudio de referência. Preserve todas as outras pistas, o andamento, os timbres, os volumes e a letra. Explique o que foi alterado.

### Exemplo para karaokê no VanBasco

> Incorpore a letra fornecida no próprio arquivo .mid/.kar e sincronize com a melodia vocal da referência, conferindo as sílabas e as quebras de linha para o VanBasco. Preserve os instrumentos e entregue o arquivo completo.

Para sincronizar a letra, forneça o texto e uma referência vocal acessível.

## Como usar o Fix-mid

Anexe **Fix-mid.md** ao pedir uma correção no código de uma ferramenta ou pipeline. Substitua o marcador `$ARGUMENTS` pela descrição do problema e envie os arquivos relevantes, a mensagem de erro e o comportamento esperado.

Ele complementa o trabalho de desenvolvimento; para revisar notas, timbres, ritmo ou karaokê, use as orientações do **fazer-midi.md**.

## Cuidados com o resultado

- A transcrição automática precisa de revisão: separação de áudio pode deixar vazamentos e detectores podem confundir notas, oitavas e instrumentos.
- Estar no BPM não significa eliminar contratempos, swing ou antecipações.
- Um MIDI guarda notas e controles; o som depende do teclado, sintetizador, SoundFont ou VST usado para tocar. Trocar um timbre GM não incorpora um sample de áudio ao arquivo.
- Preserve o áudio original, a transcrição inicial e as versões aprovadas.
- Peça à IA para distinguir verificações estruturais de MIDI da conferência auditiva.

## Privacidade

Repositório destinado a uso privado. As instruções podem ser usadas com seus próprios arquivos e referências; as músicas não estão incluídas neste repositório.
