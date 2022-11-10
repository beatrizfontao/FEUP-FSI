# FSI - Segurança de software: Controlo

----

# Stack

Guarda:
- informação de controlo - endereços do código que temos de executar (inclui pontos de retorno) 
- dados da aplicação (ex: variaveis locais, argumentos das funções) 

## Usurpação de controlo

Substituir a sequência de intruções alterada por uma controlada pelo atacante. Pode ocorrer na stack, na heap e noutros sítios, tipicamente devido a falhas de como se gere a memória.

## Funcionamento da stack

A stack cresce de cima para baixo.
<br>Sempre que chamamos uma função a stack vai crescendo. É criada uma nova frame para essa chamada.

Ex:
```
int main(){
    f1();
    return 0;
}

void f1(){
    f2();
}
```

Na stack temos: 
- no topo a stack frame da main
- depois a stack frame do f1
- no fundo a stack frame f2

Uma stack frame tem a seguinte estrutura com sempre **dois extremos**: 
- **frame pointer**: endereço do frame pointer da função anterior - no frame pointer da frame da função f1 temos o endereço do frame pointer da main
- **registos**: guardados que são usados na função da stack frame
- **variáveis locais**
- **parametros de função**: caso se chame outra função dentro da que nos encontramos, são aqui uardados os parametros passados para essa função - na frame da f1 poderiam ser guardados os parametros passados a f2.
- **return address**: localda instrução seuinte à chamada de uma função - f1 ao chamar f2 necessita de voltar ao mesmo ponto onde se encontrava e não começar a execução do início

O **stack pointer** gere a parte dinâmica da função - mexe-se.

## Smashing the stack - buffer overflow

**Segmentation fault** - ocorre quando um processo está a escrever fora da sua zona de memória. Não é o que acontece.
<br> 
O que acontece é que estão a ser escritas coisas aonde não é suposto seremescritas e o sistema operativo assume que não fazemos isso - undifined behavior.
<br>
O atacante pode preencher a stack com código, substituir o endereço de retorno para um local onde exista código arbitrário escolhido pelo mesmo e executá-lo.
Trabalhar com offsets e não endereços fixos.
<br>
**ShellCode** - pedaço de código (string) que cria uma shell onde o atacante pode correr comandos.

## Overflows na Heap

Baseia-se também em alterar apontadores para código malicioso.<br>
Locais que podem permitir este ataque:
- apontadores para funções (virtual function tables - guardam apontadores para funções, bibliotecas de código carregadas dinamicamente)
- tratamento de execeções (try-catch, lista ligada de apontadores para funções)
- longjmp buffers(em tratamento de exceções permitem retornar para um ponto controlado e restaurar o contexto que tinha antes)

### Criação do exploit

Domínio de programação e debugging a baixo nível, compreender as causas do ataque e como não crashar o sistema.

### Heap spraying

Inserir código malicioso em várias partes da heap. Maior parte das vezes com um bufferpreenchido por NOPs seguido de shellcode, isto permite uma margem de erro ao atacante.

### Use after free

Após usar free, o meu código vai tentar usar a memória que já foi libertada.<br>
Pode levar a que o programa leia memória controlada pelo atacante.

## Outros ataques

### Overflow de inteiros

- atribuir um valor a um inteiro com diferente precisão (ex: atribuir a um short o valor de um int; multiplicação de duas variáveis que provoca overflow - resultado final não fidedigno), leva à perda de informação 
- Do lado do atacante: procurar manipular os valores que são utilizados para calcular o tamanho de regiões de memória de forma a que haja um erro no tamanho das coisas que são alocadas.

### Repetição de código

Consiste em não injetar código próprio mas utilizar código que já existe na máquina.<br>
Utilizar bibliotecas, são comuns, usadas por várias aplicações e por vezes tem mais permissões que as próprias aplicações. Para além disso, os endereços são fáceis de prever devido à forma como o sistema operativo fazem a gestão deste código.<br>
Ex: a linc é utilizada por quase todos os programas e contém duas funções importantes para auxiliar ataques: **system** - permite correr um comando na shell - e **mprotect** - permite alterar permissões do espaço de memória.

#### Return Oriented programming

DEpendendo do código a que temos acesso, identificar pedaços de código úteis e executá-los consequentemente (através dos endereços de retorno).<br>
Em teoria, permite-nos implementar qualquer código que queiramos.

### Strings de formatação

Num printf que tenha uma string de formatação, vai-se buscar o valor mais próximo que estiver na stack. O printf sobe na stack.<br>
Uma das causas pode ser código de debug esquecido.<br>
Outra é quando é dado print de inputs do user sem qualquer restrições. Caso o utilizador tiver intenções maliciosas, pode dar print a conteúdos que se encontram na stack incluindo ler de qualquer um endereço na memória.<br>
Para além de leitura, strings de formatação permitem alterar valores em memória (ex: com %n).

## Prevenção

**Defesa em profundidade**: tentar reduzir o número de erros que ocorrem e implementar defesas no hardware, sistemas operativos e compiladores.<br>

- **DEP** - Data Execution Prevention - uma página de memória executável não pode ser escrita e uma página que pode ser escrita não pode ser executável - ROP com gadgets da libc
- **ASLR** - Address Space Layout Randomization - a localização em memória de partes críticas é obtida de forma aleatório tornando mais difícil para os atacantes saberem onde estas estão - ROP com código em endereços previsíveis
  
## Deteção em tempo de execução

- o compilador adiciona código que permite a deteção de vulnerabilidades
- transforma execução arbitrária em ataques DoS
- **Stack Canaries** - objetivo é detetar alterações nos endereços na stack. Introduzir informação redundante na stack entre os dados que o atacante poderá manipular (variáveis locais) e os próprios endereços. Verificar essa informação quando sair da função a ver se não foi alterada. <ins>Canários aleatórios</ins>: array de bytes aleatórios; <ins>Canários de terminação</ins>: arrays com carateres dificeis de escrever num strcpy (ex: '\0', '\n', EOF -> dificultam ataques)
- **Mitigações adicionais** - garantir que buffers estão sempre junto a canários; copiar argumentos para o topo da stack; <ins>shadow stack</ins> onde colocamos apenas valores de controlo (endereços de frame e de retorno) e antes de retornar a função verifica-se consistência; ter dados separados numa stack à parte

## Memory tagging

- suporte de hardware para criar tags - um endereço está dividido em duas partes: uma que é a aplicação que controla e outra que é uma tag. As tags são utilizadas para fazer separação entre espaços de memória
- permite ligar pointers à região de memória para onde apontam
- comparar tags no acesso (overflow) e free altera tags (use after free)

## Control Flow Integrity

- Procura garantir que o programa saiba quais são os pontos de origem e os pontos de chegada de funções válidos
- Um endereço de retorno não pode apontar para a call de um função

## Program Safety

Um programa é safe se todas as suas execuções têm significado seguindo as regras de semântica da linguagem em que é escrito. Erros ocorrem quando o programa entra em zonas que não têm significado.<br>
Linguagem que são strongly typed (Java ou Haskell) ajudam a garantir program safety. O programa nem chega a compilar caso existam diferenças nos tipos.

## Memory Safety

Exemplos de operações não safe:
- Read: ler de zona inválida
- Write: escrever em zona inválida
- Execute: intrepertar dados inválidos como instruções executáveis

**Safety espacial**: validade depende da localização
**Safety temporal**: validade depende so tempo de vida do objeto ou variável