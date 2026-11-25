# Moat Radar — Protocolo permanente de publicação do Weekly

Versão 1.0 · 25/11/2026
Repositório: jorge-rocha-weekly/moat-radar.com · branch de produção: main

## 1. Entrada e autorização
- Receber dois HTMLs finais aprovados, PT e EN, ou ZIP equivalente. A data de edição (AAAA-MM-DD) é extraída dos caminhos canônicos, não da data de upload.
- Extrair título, subtítulo e período de cada HTML. Não revisar, traduzir, reformatar ou reescrever conteúdo aprovado.
- Publicar somente após autorização explícita. Não pedir nova confirmação se ela já foi dada.
- Checklist específico da edição é insumo operacional, não autorização para alterações fora do escopo.

## 2. Escopo permitido
Para a data D:
- Criar pt/edicoes/D/index.html e en/editions/D/index.html.
- Atualizar pontualmente pt/index.html, en/index.html, pt/weekly/index.html e en/weekly/index.html.
- Adicionar as duas URLs e alternates ao sitemap.xml.
- Nenhum outro arquivo pode ser alterado sem autorização específica. Não modificar CSS, logos, menus, scripts de navegação, páginas de artigos, edições anteriores ou infraestrutura. Não publicar o checklist específico como página.
- Se a edição não for a mais recente, inserir na posição cronológica adequada sem substituir o destaque mais recente.

## 3. Pré-publicação
- Ler este protocolo e os arquivos vigentes no GitHub; registrar o SHA da ponta de main.
- Verificar se a edição já existe. Se idêntica, não duplicar; se diferente, interromper a sobrescrita e relatar a divergência.
- Conferir canonical, hreflang, links PT/EN e navegação interna dos dois HTMLs. Não corrigir silenciosamente defeitos no conteúdo aprovado.
- Preferir leitura direta dos arquivos anexados por ferramenta e transferência integral ao GitHub; evitar reconstrução manual ou cópia parcial.
- Tratar ZIPs com páginas prontas como propostas de alterações: comparar com a versão vigente, não sobrescrever cegamente.

## 4. Atualização de Home e Weekly
- Inspecionar o mecanismo atual antes de alterar. Em 25/11/2026, as quatro páginas usam carregadores JavaScript e conteúdo em _payload; funções norm e patchNewWeekly/patchLatestWeekly montam a apresentação.
- Preservar o carregador, normalização, marcação, classes, ícones e recursos existentes. Não trocar por HTML estático nem refatorar.
- Usar o mesmo padrão de destaque e de entradas anteriores. Quando D é a edição mais recente, promover D e mover o destaque anterior para o início da lista, sem remover ou duplicar entradas.
- Confirmar título, subtítulo, período e URL em ambos os idiomas; manter a mesma edição em destaque nos dois idiomas.
- Manter a ordem cronológica e todos os links históricos.
- Sitemap: preservar entradas existentes, acrescentar apenas as URLs de D e alternates PT, EN, x-default, seguindo o padrão XML atual. Usar data real de alteração em lastmod. Lacunas históricas são relatadas, não corrigidas sem autorização.

## 5. Commit e publicação
- Preferir um único commit atômico com os sete arquivos, usando árvore baseada na ponta atual ou branch temporária e integração sem sobrescrever alterações concorrentes.
- Se a ferramenta só permitir commits por arquivo, gravar primeiro as duas edições, depois as quatro páginas de índice e por último o sitemap; registrar todos os SHAs e informar que não foi atômico.
- Validar diffs e, quando possível, testar a execução dos carregadores antes de promover para main. Se a ponta mudou, reavaliar contra a nova versão; nunca forçar push.
- Mensagem sugerida: Weekly D: título curto.
- Reconsultar os arquivos gravados e conferir que os HTMLs das edições coincidem integralmente com os aprovados.

## 6. Auditoria do domínio público
- Verificar que as duas URLs de edição respondem e exibem a edição correta.
- Testar a alternância EN → PT → EN na própria edição, sem redirecionamento indevido à Home.
- Verificar Home PT/EN e Weekly PT/EN com JavaScript executado: destaque, título, subtítulo, data, link e preservação da edição anterior e do restante do histórico.
- Verificar navegação de retorno e sitemap público (ambas as URLs e alternates).
- Verificar desktop e mobile quando houver capacidade de renderização.
- Não confundir sucesso do commit com deploy ou teste visual. Se não for possível testar a produção, marcar a auditoria como pendente e explicar a limitação.
- Não alterar páginas antigas para criar links de próxima edição inexistentes.

## 7. Relatório final
Informar data e período da edição, URLs PT/EN, SHA anterior e final (ou lista de commits), sete arquivos alterados, confirmação de integridade dos HTMLs, resultados dos testes, pendências e quaisquer desvios. Não declarar publicação integralmente concluída enquanto a produção não estiver validada. Se houver erro, não fazer rollback amplo nem apagar alterações de terceiros; identificar os arquivos e propor reversão pontual dos próprios commits.

## 8. Comando reutilizável
"Publique o Weekly dos HTMLs anexados seguindo PUBLICATION-PROTOCOL.md do repositório oficial do Moat Radar. Preserve os textos e a estrutura integralmente. Atualize apenas as edições PT/EN, Home PT/EN, Weekly PT/EN e sitemap. Valide os arquivos e a navegação, e informe o commit e eventuais pendências."

## 9. Manutenção deste protocolo
Atualizar este documento somente com autorização editorial ou operacional explícita, após mudanças reais de arquitetura ou aprendizado de publicação. Não utilizar este protocolo para autorizar refatorações do site.
