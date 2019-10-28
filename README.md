# ComandosUteisBash

Uma lista útil de comandos de uma linha no Bash.

## 1. Operações de busca com o comando find

```bash
$ find <diretorio> -name "<expressao>"
```

**Exemplos:**

### 1.1 Procurar arquivos com o nome "run" na pasta atual em diante

```bash
$ find . -name "run"
```

### 1.2 Procurar arquivo com a extensão ".ksh" na pasta /etc

```bash
$ find /etc -name "*.ksh"
```

### 1.3 Procurar por links simbólicos na pasta atual em diante

```bash
$ find . type -l
```

### 1.4 Procurar por links simbólicos na pasta atual em diante, com saída formatada (indicando a origem e o destino do link)

```bash
$ find . -type l -printf "%p -> %l\n"
```

### 1.5 Apagar todos os links simbólicos encontrados utilizando o comando xargs

```bash
$ find . -type l | xargs rm -rf
```

### 1.6 Procurar todos os arquivos de nome "monitor.t" em pastas com nome sequencial formatado de "001" a "040", na pasta atual

```bash
$ for pasta in $(seq -f "%03g" 1 40); do echo $pasta; cd $pasta; if test ! -e monitor.t; then echo ">>> $pasta falhou"; fi; cd -; done
```

### 1.7 Procurar todos os arquivos .ctl e testar se eles possuem o correspondente arquivo .idx. Caso os arquivos .idx não existam, o comando gribmap é invocado para criá-los a partir do arquivo .ctl

```bash
$ for arqctl in `find . -name "*.ctl"`; do arqidx=`echo $arqctl | sed 's,ctl,idx,g'`; if test ! -e $arqidx; then echo "$arqidx falta"; gribmap -i $arqctl; fi; done
```

### 1.8 Procurar por diretórios vazios

```bash
$ find . -type d -empty
```

```bash
$ find . -type d -empty -name "NOME"
```

### 1.9 Procurar por arquivos vazios

```bash
$ find . -type f -empty
```

```bash
$ find . -type f -empty -name "NOME"
```

### 1.10 Procurar por links quebrados

```bash
$ find . -xtype l
```

## 2. Operações de pesquisa em arquivos com o comando grep

```bash
$ grep <opcoes> <expressao> <arquivo(s)>
```

**Exemplos:**

### 2.1 Pesquisa recursiva pela expressão "letkf" em todos os scripts ksh na pasta atual em diante

```bash
$ grep -R "letkf" *.ksh
```

### 2.2 Pesquisa recursiva pela exporessão "letkf" ou "LETKF" (ou uma mistura de maiúsculas/minúsculas) em todos os scripts ksh na pasta atual em diante

```bash
$ grep -iR "letkf" *.ksh
```

## 3. Operações com arquivos utilizando o comando `awk`

```bash
$ awk <opcoes> <comandos> <arquivo>
```

ou

```bash
$ cat <arquivo> | awk <opcoes> <comandos>
```

**Exemplos:**

### 3.1 Filtrando um arquivo com colunas separadas por espaços, mostrando apenas a coluna "n"

```bash
$ cat <arquivo> | awk -F " " '{print $n}'
```

### 3.2 Adicionando uma string antes da coluna "n"

```bash
$ awk '{print "coloque_aqui_a_sua_string-ela_pode_incluir_barras_/"$n}' arquivo
```

## 4. Operações com arquivos utilizando o comando `sed`

```bash
$ sed <opcoes> <comandos> <arquivo(s)>
```

ou


```bash
$ cat <arqvuivo> | sed <opcoes> <comandos>
```

**Exemplos:**

### 4.1 Substituindo todas as ocorrências da string "A" pela string "B" em todos os scripts .ksh (apenas na pasta atual)


```bash
$ sed -i "s,A,B,g" *.ksh
```

### 4.2 Comentando a linha "n" em um arquivo (comentário é dados por #)


```bash
$ sed -i 'ns/^/#/' <arquivo>
```

### 4.3 Inserindo um comentário na primeira linha de um arquivo


```bash
$ sed -i "1s/^/# Comentario/" > <arquivo>
```

## 5. Criando uma sequência de números no shell com o comando `seq`


```bash
$(seq <inicio> <fim>)
```

**Exemplos:**

### 5.1 Imprimir no shell a sequencia de 1 a 20


```bash
$ echo $(seq 1 20)
```

## 6. Operações com loops no shell

*Comando `for`; `do`; `done`*

**Exemplos:**

### 6.1 Imprimir a sequência de 1 a 30 utilizando o comando de laço `for`


```bash
$ for i in $(seq 1 30); do echo $i; done
```

### 6.2 Renomear todos os arquivos com extensão .txt adicionando uma nova extensão .dat


```bash
$ for i in `find . -name *.txt`; do mv ${i} ${i}.dat; done
```

### 6.3 Alterar todos os arquivos com a extensão .TQ0213L042 para .T213L42, utilizando o `for` e o `sed`

```bash
$ for arq1 in `ls *.TQ0213L042`; do arq2=`echo ${arq1} | sed "s,TQ0213L042,T213L42,g"`; cp ${arq1} ${arq2}; done
```

### 6.4 Executar o comando `cmp` para todos os arquivos de uma lista

```bash
$ for i in `ls *unf*`; do cmp $i /diretorio/arquivos/a/serem/comparados/$i; done
```

*Comando `until`; `do`; `done`*

**Exemplos:**

### 6.5 Aguardar até que um arquivo esteja disponível

```bash
$ until [ -e <arquivo> ]; do sleep .1s; done
```

*Comando `if`; `then`; `fi`*

**Exemplos:**

### 6.6 Verifica se uma pasta existe; se não existir, cria, se existir sai do teste

```bash
$ if test ! -s <pasta>; then mkdir -p <pasta>; fi
```

### 6.7 Lê um lista chamada "lista" com os nomes das pastas que contém um script de submissão, dá permissão de execução e executa 

```bash
$ for i in `cat lista`; do cd $i; chmod +x qsub_interface.g2s.$i; ./qsub_interface.g2s.$i; cd -; done
```

*Comando `while`; `do`; `done`*

### 6.8 Faz um loop em cima de datas; verifica se a primeira data é menor ou igual do que a segunda. Quando esta condição é alcançada, sai do loop

```bash
$ while [ <data_inicio> -le <data_fim> ]; do <operacoes>; done
```

## 7. Comandos para visualização da quota do usuário

### 7.1 Verifica o espaço em disco ocupado (em Gigabytes) pelo usuário no disco scratchin

*Comando `lsf`*

**Exemplos:**

```bash
$ lfs quota -u $USER /scratchin/ | sed -n 3p | awk '{ print $2/1024/1024" Gb"}'
```

**Referências Úteis:**

- [Canivete Suíço do Shell](http://aurelio.net/shell/canivete/)
- [Só Sed](http://thobias.org/doc/sosed.html)
- [SED](http://aurelio.net/sed/)
