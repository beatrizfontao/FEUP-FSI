# FSI - Segurança de Sistemas

---

# Principios fundamentais

- **Economia nos Mecanismos**: um sistemas deve ter apenas as funcionalidades necessárias - Keep It Simple - facilita a implementação, a usabilidade e a validação
- **Proteção por omissão**: por omissão, a configuração de um sistema deve ter o nível de proteção conservador (máximo de segurança)
- **Desenho aberto**: assumir que o adversário sabe tudo sobre o nosso sistema. O segredo são parâmetros do sistema - chaves cripttográficas, passwords. A vantagem é saber mais rapidamente da existência de vulnerabilidades e podemos mudar os parametros sem mudar o sistema
- **Defesa em Profundidade**: Nunca devemos confiar numa única linha de defesa
- **Privilégio mínimo**: conceder a cada utilizador/compartimento/programa apenas os privilégios necessários para realizarem a sua função
- **Separação de Privilégios**: O sistema deve ser compartimentado. Cada compartimento está isolado dos outros e as interações devem ser monitorizadas, já que são contextos diferentes, por exemplo em termos de permissões (não confiar no que vem de fora e não enviar nada que não tenha de ser enviado).
- **Mediação Completa**: Definir para cada recurso que operações é possível fazer nesse recurso e quem tem permissões para realizar essas operações. Sempre que alguém tenta aceder a um recurso, só dar acesso se a pessoa possuir as permissões necessárias

# Controlo de Acessos

Mecanismos que visam aplicar privilégio mínimo e mediação total.

- **Ator**: entidade que realiza uma ação
- **Recurso**: sobre o qual se realiza a ação
- **Operaçãp**: tipo de ação que é realizada
- **Permissão**: combinação recurso/operação

## Matriz de acessos

Matriz em que as linhas são indexadas por atores e a as colunas indexadas por recursos. Cada célula mostra as permissões ou a ausência delas.<br>
Representação clara e eficiente mas muito grande.

## Lista de controlo de acessos (ACL)

Junto a cada recurso, guardar o que cada ator pode fazer sobre esse mesmo recurso, não sendo necessário guardar atores que não tem permissões sobre o recurso.<br>
Garante que o recurso é apenas acedido por um número limitado de atores mas torna difícil realizar queries orientadas aos atores.

## Listas de perissões
Junto a cada ator, guardar que recursos ele pode usar e com que permissões.<br>
Permite rapidamente sabes todos os recursos a que um ator tem acesso mas torna difícil realizar queries orientadas aos recursos.

## Role Based Access Control

Criar perfis (roles) - conceito intermédio - e atribuir a cada ator um perfil. Em cada recurso, definir as permissões dos utilizadores-tipo. Separa a gestão de recursos e a gestão de utilizadores.

## Attribute-based Access Control

Em vez de um atributo (perfil) a cada utilizador, atribuir vários tipos de atributos(ex: localização geográfica e temporal) aos utilizadores e políticas sobre atributos aos recursos.<br>
A gestão de controlo de acessos é feita vendo se as políticas aceitam os atributos do utilizador.

---

# Sistemas Operativos 

Interface entre o ultilizador e o hardware que realiza a gestão de acesso a recursos físicos por parte das aplicações.

## Aspetos de Segurança

Diferentes atores têm diferentes níveis de acesso. Os utilizadores são sempre vistos como potenciais ameaças e os recursos são sempre ativos a proteger.<br>
Como múltiplas aplicações a executar em simultâneo, tornando-as também em potenciais ameaças.<br>
Pretendem garantir: isolamento virtual entre utilizadores, aplicações e processos através de mecanismos de controlo de acesso.

## Kernel

Parte (software) do sistema operativo que desempenha as operações mais críticas. Não possui restrições do que pode fazer sobre o hardware. Dois modos de funcionamento: kernel mode e user mode. O código em user mode não tem acesso direto aos recurso do sistema, é o kernel que permite esse acesso.<br>
Tem um espaço de memória próprio, que gere. O processador garante que nenhum código a correr um user mode tem acesso ao hardware.

## Confinamento

Os pontos de entrada são system calls.

**Reference monitor**: Sistema de monitorização que vê se as regras que estão a ser colocadas em prática quando à gestão da fronteira estão a ser cumpridas. Ex: lançamento de execeções do processador é reference monitor

## System calls

Número limitado.

- Controlo de acessos(fork, free)
- Acesso a ficheiros(criar/ler/escrever)
- Gestão de dispositivos(obter acesso, ler/escrever)
- Configuração do sistema(data, hora)
- Comunicações(estabelecer ligação, enviar/receber mensagens)
- Proteção(alterar/obter permissões).

## Processos

Instância de um programa que está em execução.<br>
Cada processo tem um identificador(PID) e um conjunto de permissões. Idealmente, um processo vai executar num contexto em que as permissões são as necessárias para desempenhar o seu papel.<br>
O kernel garante a processos um espaço de memória (memória virtual) e concede acesso a outros recursos através de system calls.<br><br>
Cada utilizador tem um UID e um GID, o 0 é reservado ao super-user (root - processo init?) e o processo é associado a um UID e a um GID.<br><br>
Comunicação entre processos(IPC):
- através do sistema de ficheiros
- memória partilhada
- mensagens síncronas: pipes, sockets
- mensagens assíncronas: signals.
Pode dar problemas porque, se tiver um processo mal implementado a correr e outro malicioso, vai ser possível abusar destas system calls.

## Modelo de Confiança

Um sistema é **confiável** se faz exatamente e apenas aquilo que foi especificado.
- é indutiva
- BIOS e kernel são confiáveis
- o processo de boot, que utiliza este código para colocar o kernel em memória e passar-lhe controlo, cria um estado confiável
- o kernel lança processos com permissões que garantem que nenhum novo processo pode alterar este estado de confiança
- processos de hibernação preservam o estado de confiança

## Modelo de Ameaças

- BIOS corrompida
- ficheiros de hibernação corrompidos
- bootloader corrompido
- cold boot attacks

## Medidas de Mitigação

Erros de administração e falhas que ultrapassam o principio de funcionamento do sistema porque exploram erros na sua implementação.<br>
Monitorização é uma forma de mitigação. Procurar garantir o isolamento entre processos.

## Memória

Um processo não pode aceder ao espaço de memória de outro processo (inclui processos do kernel). A confidencialidade, integridade e controlo de fluxo do kernel tem de ser protegida de todos os processos em user mode.<br>
O espaço de memória gerido por um SO é muito maior que o espaço físico. Maior parte das páginas são guardadas no disco, apenas aquelas que estão a ser usadas ou foram usadas recentemente são guardadas na memória física.

## Tradução de endereços

Necessário à implementação de mecanismos de memória virtual.
- **Isolamento**
- **Eficiência**

### Kernel Mapping

No espaço de memória de cada processo em user mode existe um subespaço que é memória do kernel. Para que, quando houver uma system call, possamos passar para kernel mode sem um grande overhead.

---

# Sistema de ficheiros - unix

Cada recurso (ficheiro ou pasta) tem um owner que é um utilizador e um grupo (agrupa utilizadores). As permissões são atribuídas de forma independente: (da direita para a esquerda) todos os utilizadores, grupo ao que pertence o utilizador e ao próprio owner (ou processos que executam em nome do owner).<br>
**Superuser**: antigamente era o root, hoje em dia é um papel em que podemos executar ações em nome de root. UID = 0, utilização mínima.<br><br>
Para alterar permissões:
- sempre permitido ao super user
- se for owner usar chmod
- owner pode ser alterado pelo superuser ao usar chown
- grupo pode ser alterado pelo owner ou pelo superuser ao usar chgrp

**Discretionary Access Control**: Quem é dono de um recurso pode alterar as permissões desse recurso<br>
**Mandatory Access Control**: Apenas um superuser pode alterar permissões (ex:SELinux)<br><br>

## Permissões de processos

Cada processo tem associado um effective user id, que identifica em nome de quem é que está a executar, determina assim as permissões que o processo tem. Geralmente é o user id(UID) que lançou o processo.<br><br>

### Login

O sistema executa um processo de login como root. Quando o utilizador se autentica, esse processo altera o seu próprio UID e GID para corresponder às permissões do user. - **Drop privileges**<br>.

O reverso - **Elevate privileges** - procura obter mais permissões do que as que tem. Deveria ser impossível mas pode ser usado de forma controlada (ex: trocar o atributo de ficheiro e fazer com que, quando o user o executar, está a fazê-lo com as permissões do owner desse ficheiro).

# Privilégios de Processos

Um processo tem 3 UID:
- **Effective User ID (EUID)**: determina as permissões do processo
- **Real User ID(RUID)**: utilizador que lançou o processo - pode não ser igual ao de cima
- **Saved User ID(SUID)**: lembra o UID anterior a uma transição

O utilizador root, a partir da system call *setuid()*, pode alterar os UIDs para um valor arbitrário, ficando todos com o mesmo valor.<br>
  
Utilizadores comuns apenas podem alterar o EUID para RUID e SUID.<br><br>

A system call *seteuid()* apenas altera o EUID. Serve para, por exemplo, quando temos o objetivo de criar um processo com as permissões de um utilizador comum conseguirmos perder privilégio momentaneamente e recuperá-los restaurando o EUID para o RUID ou para o SUID que foram preservados.<br>

