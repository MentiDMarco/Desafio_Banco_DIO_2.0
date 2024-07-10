# Introdução
Estarei mostrando a vocês o banco 2.0 que fiz no Bootcamp da DIO, com base no que aprendi em Python. Espero que gostem!

### 1. Função `menu`
Essa função exibe o menu principal do sistema e retorna a opção escolhida pelo usuário.

```python
def menu():
    menu = """
    =-=-=-=-=-=-=-= MARBANK 2.0 =-=-=-=-=-=-=-=
              [1] - \tDepositar
              [2] - \tSacar
              [3] - \tExtrato
              [4] - \tNova conta
              [5] - \tListar contas
              [6] - \tNovo usuário
              [0] - \tSair

    Por favor. Selecione uma função: """
    return input(textwrap.dedent(menu))
```

### 2. Função `depositar`
Essa função realiza um depósito na conta, atualizando o saldo e o extrato.

```python
def depositar(saldo, valor, extrato, /):
    if valor > 0:
        saldo += valor
        extrato += f"Depósito:\t{valor:.2f}\n"
        print("Depósito realizado com sucesso!")
    else:
        print("Valor inserido inválido! Por favor, tente novamente.")
    
    return saldo, extrato
```

### 3. Função `sacar`
Essa função realiza um saque na conta, verificando limites de saldo, valor do saque e número de saques permitidos.

```python
def sacar(*, saldo, valor, extrato, limite, numero_saques, limite_saques):
    excedeu_saldo = valor > saldo
    excedeu_limite = valor > limite
    excedeu_saques = numero_saques >= limite_saques

    if excedeu_saldo:
        print("Você não tem saldo suficiente.")
    
    elif excedeu_limite:
        print("O valor do saque excedeu o limite.")
    
    elif excedeu_saques:
        print("O número de saques foi excedido.")
    
    elif valor > 0:
        saldo -= valor
        extrato += f"Saque:\t\tR$ {valor:.2f}\n"
        numero_saques += 1
        print("Saque realizado com sucesso!")
    
    else:
        print("Valor informado é inválido. Por favor tente novamente")
    
    return saldo, extrato
```

### 4. Função `exibir_extrato`
Essa função exibe o extrato da conta.

```python
def exibir_extrato(saldo, /, *, extrato):
    print("\n=-=-=-=-=-=-=-=- EXTRATO -=-=-=-=-=-=-=-=")
    print("Não foram realizadas movimentações." if not extrato else extrato)
    print(f"\nSaldo {saldo:.2f}")
    print("=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=")
```

### 5. Função `criar_usuario`
Essa função cria um novo usuário e o adiciona à lista de usuários.

```python
def criar_usuario(usuarios):
    cpf = input("Informe o CPF (Somente número): ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\nJá existe um usuário com esse cpf!")
        return
    
    nome = input("Informe seu nome completo: ")
    data_nascimento = input("Informe sua data de nascimento: (DD/MM/AAAA): ")
    endereco = input("Informe o seu endereço (Logradouro, Número, Bairro, Cidade - Sigla do estado): ")

    usuarios.append({"nome": nome, "data_nascimento": data_nascimento, "cpf": cpf, "endereco": endereco})

    print("Usuário criado com sucesso!")
```

### 6. Função `filtrar_usuario`
Essa função busca um usuário na lista de usuários pelo CPF.

```python
def filtrar_usuario(cpf, usuarios):
    usuarios_filtrados = [usuario for usuario in usuarios if usuario["cpf"] == cpf]
    return usuarios_filtrados[0] if usuarios_filtrados else None
```

### 7. Função `criar_conta`
Essa função cria uma nova conta para um usuário existente.

```python
def criar_conta(agencia, numero_conta, usuarios):
    cpf = input("Informe o CPF do usuário: ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("Conta criada com sucesso!")
        return {"agencia": agencia, "numero_conta": numero_conta, "usuario": usuario}
    
    print("\nUsuário não encontrado, fluxo de criação de conta encerrado!")
```

### 8. Função `listar_contas`
Essa função lista todas as contas criadas.

```python
def listar_contas(contas):
    for conta in contas:
        linha = f"""\
            Agência:\t{conta['agencia']}
            C/C:\t\t{conta['numero_conta']}
            Titular:\t{conta['usuario']['nome']}
        """
        print("=" * 100)
        print(textwrap.dedent(linha))
```

### 9. Função `main`
Essa é a função principal que gerencia o fluxo do programa. Ela inicializa as variáveis e executa um loop para receber comandos do usuário.

```python
def main():
    LIMITE_SAQUES = 1
    AGENCIA = "0001"

    saldo = 0
    limite = 500
    extrato = ""
    numero_saques = 0
    usuarios = []
    contas = []

    while True: 
        opcao = int(menu())

        if opcao == 1:
            valor = float(input("Informe o valor do depósito: "))
            saldo, extrato = depositar(saldo, valor, extrato)
        
        elif opcao == 2:
            valor = float(input("Informe o valor do saque: "))
            saldo, extrato = sacar(
                saldo=saldo,
                valor=valor,
                extrato=extrato,
                limite=limite,
                numero_saques=numero_saques,
                limite_saques=LIMITE_SAQUES,
            )
        
        elif opcao == 3:
            exibir_extrato(saldo, extrato=extrato)
        
        elif opcao == 4:
            criar_usuario(usuarios)
        
        elif opcao == 5:
            numero_conta = len(contas) + 1
            conta = criar_conta(AGENCIA, numero_conta, usuarios)
            if conta:
                contas.append(conta)
        
        elif opcao == 6:
            listar_contas(contas)
        
        elif opcao == 0:
            break
        
        else:
            print("Opção inválida. Por favor, tente novamente.")

main()
```

### Resumo:
- **Menu:** Exibe as opções e retorna a escolha do usuário.
- **Depósito:** Permite ao usuário adicionar fundos à conta.
- **Saque:** Permite ao usuário retirar fundos, verificando limites e número de saques.
- **Extrato:** Mostra o histórico de transações e o saldo atual.
- **Novo Usuário:** Cria e adiciona um novo usuário à lista de usuários.
- **Nova Conta:** Cria uma nova conta para um usuário existente.
- **Listar Contas:** Exibe todas as contas criadas.
- **Loop Principal:** Executa o menu e as funções associadas em um loop até que o usuário escolha sair.
