# Moat Radar - Padrões e Validação de Homepages
## Refatoração 2026-09-27: Lessons Learned

---

## 🔴 PROBLEMA CRÍTICO ENCONTRADO

### Sintoma
Lista em 2 colunas em EN homepage (diferente de PT que está em 1 coluna)

### Causa Raiz
`<div </div>` tag malformada (vazio, sem atributos) inserida entre featured card e feed-label

### Localização
Posição típica: após o segundo `</a></div>` do featured card, antes de `<div class="feed-label">`

### Padrão ERRADO ❌
```html
</a></div>
<div </div></div>    <!-- MALFORMADO: <div sem atributos -->
<div class="feed-label">All content</div>
```

### Padrão CORRETO ✅
```html
</a></div></div>     <!-- Dois fechamentos: featured + feed-grid -->
<div class="feed-label">All content</div>
```

### Fix Automático
```python
content = content.replace('</div>\n<div </div></div>', '</div></div>')
```

---

## 📋 ESTRUTURA HTML CORRETA

### Layout Completo
```html
<div class="sheet">
  <div class="topbar">...</div>
  <div class="subnav">...</div>
  <hr class="dashed"/>
  
  <div class="feed">                           <!-- ← Container principal -->
    <div class="feed-grid">                  <!-- ← Grid 2 colunas -->
      <div class="featured">...</div>        <!-- ← Featured #1 -->
      <div class="featured">...</div>        <!-- ← Featured #2 -->
    </div>                                   <!-- ← FECHA feed-grid -->
    
    <div class="feed-label">...</div>        <!-- ← Label "Todos os conteúdos" -->
    
    <!-- ⚠️ CRÍTICO: Entries direto em .feed, SEM wrapper -->
    <div class="entry">...</div>             <!-- ← Entry #1 -->
    <div class="entry">...</div>             <!-- ← Entry #2 -->
    <div class="entry">...</div>             <!-- ← Entry #3 -->
    <div class="entry">...</div>
    <div class="entry">...</div>
    <div class="entry">...</div>
    <div class="entry">...</div>
    <div class="entry">...</div>             <!-- ← Entry #8 -->
  </div>                                     <!-- ← FECHA .feed -->
  
  <footer>...</footer>
</div>                                       <!-- ← FECHA .sheet -->
```

### Pontos Críticos
1. **Entries são filhos DIRETOS de `.feed`** (não em wrapper)
2. **SEM `<div>` extras** entre `</div>` (feed-grid close) e `<div class="feed-label">`
3. **SEM `<div>` wrapper** ao redor de entries
4. **Feed-label vem IMEDIATAMENTE** após feed-grid close

---

## ✅ CHECKLIST PRÉ-PUBLICAÇÃO

### DIVs
- [ ] Count `<div ` = count `</div>` (usar regex: `<div[\s>]`)
- [ ] Nenhuma tag `<div </div>` malformada (grep: `grep '<div </div>'`)
- [ ] Nenhuma tag `<div <div` malformada
- [ ] Nenhuma tag `<div ` sem atributos

### Estrutura
- [ ] Feed-grid contém APENAS 2 featured cards
- [ ] Feed-label vem IMEDIATAMENTE após `</div></div>` da feed-grid
- [ ] Entries são filhos diretos de `.feed` (não em wrapper)
- [ ] 8 entries em homepage (padrão 2026-09-27)
- [ ] Nenhum `<div>` extra entre featured e feed-label

### CSS (Não adicionar!)
- [x] `.feed-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 40px; }`
- [x] `.entry { position: relative; display: flex; gap: 16px; padding: 18px 0; ... }`
- [x] `.feed { padding: 0 24px 60px; }`
- [x] `.feed-label { ... }`
- [ ] ❌ NÃO adicionar `.feed > .entry { ... }` - isso causa 2 colunas!

### Featured Cards - PT Homepage
- [ ] Featured #1: Artigo 11 de setembro
- [ ] Featured #2: Weekly 16-22 de setembro

### Featured Cards - EN Homepage
- [ ] Featured #1: Article - September 11
- [ ] Featured #2: Weekly - Sep 16–22

---

## 🔍 DEBUGGING RÁPIDO

### "Lista está em 2 colunas?"

**Passo 1: Procurar malformação**
```bash
grep '<div </div>' pt/index.html en/index.html
# Se encontrar: está aqui o problema
```

**Passo 2: Encontrar posição exata**
```python
with open('en/index.html', 'r') as f:
    content = f.read()
if '<div </div>' in content:
    pos = content.find('<div </div>')
    print(f"Malformação em posição {pos}")
    context = content[pos-100:pos+100]
    print(f"Contexto: {repr(context)}")
```

**Passo 3: Corrigir**
```python
content = content.replace('</div>\n<div </div></div>', '</div></div>')
```

**Passo 4: Validar**
```python
import re
o = len(re.findall(r'<div[\s>]', content))
cl = len(re.findall(r'</div>', content))
print(f"DIVs: {o} = {cl} ✅" if o==cl else f"DIVs: {o} ≠ {cl} ❌")
print("Malformação: ", '<div </div>' in content)
```

---

## 📊 DADOS DE REFERÊNCIA (2026-09-27)

### PT Homepage
- Tamanho: 20,043 bytes
- DIVs: 47 open = 47 close ✅
- Featured: 2 ✅
- Entries: 8 ✅
- Minificado: ✅
- Status: 100% Correto

### EN Homepage
- Tamanho: 20,010 bytes (original: 20,015)
- DIVs: 47 open = 47 close ✅ (após fix de malformação)
- Featured: 2 ✅
- Entries: 8 ✅
- Minificado: ✅
- Status: 100% Correto (após remover `<div </div>`)

### Diferença
EN tinha `</div>\n<div </div></div>` que foi corrigido para `</div></div>\n`
Remover 7 caracteres: `<div </div>` (8 chars) - 1 newline = 7 bytes

---

## 🔄 PROCESSO DE VALIDAÇÃO COMPLETO

```bash
# Script Python de validação completa
python3 << 'VALIDATE'
import re
import os

files = ['pt/index.html', 'en/index.html']
issues = {}

for filepath in files:
    if not os.path.exists(filepath):
        continue
    
    with open(filepath, 'r', encoding='utf-8') as f:
        content = f.read()
    
    # Contar DIVs
    div_open = len(re.findall(r'<div[\s>]', content))
    div_close = len(re.findall(r'</div>', content))
    balanced = div_open == div_close
    
    # Procurar malformações
    has_malformed = '<div </div>' in content
    has_bad_grid = '<div <div' in content
    
    # Featured cards
    featured_count = content.count('<div class="featured">')
    
    # Entries
    entry_count = content.count('<div class="entry">')
    
    # Feed-label
    has_feed_label = '<div class="feed-label">' in content
    
    # Status
    all_ok = balanced and not has_malformed and not has_bad_grid and featured_count == 2
    
    status = "✅" if all_ok else "❌"
    
    print(f"\n{status} {filepath}")
    print(f"  DIVs: {div_open} open vs {div_close} close → {balanced}")
    print(f"  Featured: {featured_count}/2")
    print(f"  Entries: {entry_count}")
    print(f"  Malformed: {has_malformed}")
    print(f"  Feed-label: {has_feed_label}")
    
    if not all_ok:
        issues[filepath] = {
            'div_balance': balanced,
            'malformed': has_malformed,
            'featured': featured_count == 2
        }

if issues:
    print(f"\n❌ {len(issues)} arquivo(s) com problemas")
    for file, problems in issues.items():
        print(f"  {file}: {problems}")
else:
    print("\n✅ TODOS OS ARQUIVOS VALIDADOS COM SUCESSO")

VALIDATE
```

---

## 📝 NOTAS PARA PRÓXIMAS PUBLICAÇÕES

1. **Ao atualizar featured cards:**
   - Copiar EXATAMENTE do commit anterior
   - Não editar manualmente (risco de adicionar espaços/quebras erradas)
   - Usar `git show HEAD` ou `re.sub()` com flags `re.DOTALL`

2. **Ao editar HTML minificado:**
   - Usar regex com `re.DOTALL` para casamento multi-linha
   - Validar DIVs IMEDIATAMENTE após cada alteração
   - NUNCA adicionar DIVs extras

3. **Após qualquer mudança:**
   - Rodar validação completa acima
   - Verificar DIVs balanceados
   - Procurar `<div </div>` malformado
   - Fazer commit APENAS após validação passar

4. **Problema recorrente:**
   - Procurar `<div </div>` PRIMEIRO
   - Se encontrar: está entre featured e feed-label
   - Fix: remover com `replace('</div>\n<div </div></div>', '</div></div>')`

5. **Before push:**
   - Validação DIVs ✅
   - Validação estrutura ✅
   - Validação featured datas ✅
   - Então fazer push

---

## 🚀 COMANDOS ÚTEIS PARA TERMINAL

```bash
# Quick check DIVs (contar)
grep -o '<div ' en/index.html | wc -l
grep -o '</div>' en/index.html | wc -l

# Find malformed divs
grep '<div </div>' *.html
grep '<div <div' *.html

# Count featured cards
grep -o '<div class="featured">' pt/index.html | wc -l

# Extract featured dates
grep -oP '/\d{4}-\d{2}-\d{2}/' pt/index.html | head -3

# Validate structure after edit (one-liner)
python3 << 'END'
import re
f=open('pt/index.html').read()
o,c=len(re.findall(r'<div[\s>]',f)),len(re.findall(r'</div>',f))
m='<div </div>' in f
print(f"DIVs: {o}={c} {'✅' if o==c else '❌'} | Malformed: {m} {'❌' if m else '✅'}")
END

# Git commands
git diff pt/index.html en/index.html  # Ver diferenças
git show HEAD:pt/index.html           # Ver versão anterior
git log --oneline -5                  # Ver últimos commits
```

---

## ✨ SUMMARY

**Padrão encontrado:** `<div </div>` malformado entre featured e feed-label
**Resultado:** Lista renderiza em 2 colunas em vez de 1
**Fix:** Um-liner: `content.replace('</div>\n<div </div></div>', '</div></div>')`
**Validação:** Sempre rodar checklist antes de publicar
**Próximas vezes:** Procurar isso PRIMEIRO se lista estiver em 2 colunas

---

*Documentação consolidada em 27 de setembro de 2026. Atualizar quando encontrar novos padrões.*
