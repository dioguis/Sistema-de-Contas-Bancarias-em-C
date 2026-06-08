# Sistema de Manutenção de Contas Bancárias em C

Sistema de gerenciamento de contas bancárias desenvolvido em **C**, utilizando **arquivo binário com registros de tamanho fixo** e as funções `fseek()`, `fread()`, `fwrite()` e `rewind()` para manipulação de baixo nível de arquivos.

---

## Funcionalidades

| Opção | Descrição |
|-------|-----------|
| 1 | Cadastrar novo cliente em posição específica |
| 2 | Consultar cliente pelo número da conta |
| 3 | Atualizar saldo (depósito ou saque) |
| 4 | Encerrar conta (remoção lógica) |
| 5 | Listar todos os clientes ativos |
| 6 | Relistar usando `rewind()` |
| 7 | Sair do sistema |

---

## Estrutura do Registro

Cada cliente é armazenado em um `struct` de **tamanho fixo**:

```c
typedef struct {
    int    numero;        // número da conta (0 = slot vazio)
    char   nome[50];      // nome do titular
    char   cpf[15];       // CPF do cliente
    double saldo;         // saldo atual
    int    ativo;         // 1 = ativo | 0 = encerrado
} Cliente;
```

Como todos os campos têm tamanho fixo, cada registro ocupa exatamente `sizeof(Cliente)` bytes no arquivo, permitindo acesso direto por posição.

---

## ⚙️ Funções de I/O Utilizadas

### `fseek()`
Usado para posicionar o ponteiro do arquivo diretamente no registro desejado, sem leitura sequencial:

```c
// Pula para o registro na posição 'i'
fseek(fp, posicao * sizeof(Cliente), SEEK_SET);
```

### `fwrite()`
Grava um registro completo no arquivo:

```c
fwrite(&c, sizeof(Cliente), 1, fp);
```

### `fread()`
Lê um registro completo do arquivo:

```c
fread(&c, sizeof(Cliente), 1, fp);
```

### `rewind()`
Restaura o ponteiro para o início do arquivo — utilizado na opção 6 e internamente para garantir leitura correta nas listagens:

```c
rewind(fp);
```

---

## Decisões de Projeto

### Remoção Lógica
A exclusão de contas (opção 4) é feita por **remoção lógica**: o campo `ativo` é marcado como `0`, mas o registro permanece no arquivo. Isso preserva as posições dos demais registros e evita a necessidade de reorganizar o arquivo a cada exclusão.

### Acesso Direto por Posição
Ao cadastrar (opção 1), o usuário informa a **posição (índice 0-baseado)** onde deseja inserir o cliente. O sistema calcula o offset em bytes:

```
offset = posicao × sizeof(Cliente)
```

Isso garante acesso O(1) a qualquer registro, independentemente do tamanho do arquivo.

### Arquivo Criado Automaticamente
Se o arquivo `contas.bin` não existir, ele é criado automaticamente na primeira execução.

---

## Como Compilar e Executar

**Requisitos:** GCC ou qualquer compilador C padrão (C99+).

```bash
# Compilar
gcc -o contas contas.c

# Executar (Linux/macOS)
./contas

# Executar (Windows)
contas.exe
```

---

## Exemplo de Uso

```
========================================
   SISTEMA DE MANUTENÇÃO DE CONTAS
========================================
  1. Cadastrar novo cliente
  2. Consultar cliente por conta
  3. Atualizar saldo
  4. Encerrar conta
  5. Listar todos os clientes
  6. Relistar (rewind)
  7. Sair
----------------------------------------
  Opção: 1

========================================
  CADASTRAR NOVO CLIENTE
========================================
Total de posições no arquivo: 0
Informe a posição (0-indexada): 0
Número da conta : 1001
Nome do titular : João da Silva
CPF             : 123.456.789-00
Saldo inicial   : R$ 1500.00

[OK] Cliente cadastrado na posição 0 com sucesso!
```

---

## Arquivos do Projeto

```
.
├── contas.c      # Código-fonte principal
├── contas.bin    # Arquivo binário gerado em tempo de execução
└── README.md     # Este arquivo
```

---

## Conceitos Aplicados

- Manipulação de **arquivos binários** em C (`fopen`, `fclose`)
- **Registros de tamanho fixo** com `struct`
- Acesso aleatório com **`fseek()`**
- Leitura e escrita com **`fread()`** e **`fwrite()`**
- Reinício de leitura com **`rewind()`**
- **Remoção lógica** de registros
- Boas práticas: limpeza de buffer, verificação de erros, `memset` para inicialização segura

---



