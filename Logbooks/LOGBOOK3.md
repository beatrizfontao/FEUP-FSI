
# Trabalho realizado na Semana #3

## Identificação

- O software ExifTool acede à meta data dos ficheiros mas possui uma vulnerabilidade em relação à neutralização de dados
- Criou-se um ficheiro DjVu com código malicioso na meta data, renomeado para a extensão JPEG, por exemplo.
- Ao criar este ficheiro, o ExifTool assume uma imagem normal e executa qualquer código presente no mesmo
- Deste modo, o programa permite remote code execution (RCE)

## Catalogação

- Esta falha foi reportada por William Bowling a 7 de abril de 2021.
- O bug bounty associado é de 20,000$
- O nível de gravidade desta falha é crítico (9-10), determinado pelo sistema CVSS V3

## Exploit

- O principal exploit presente nesta vulnerabilidade é RCE, que permite ao atacante executar qualquer comando na máquina da vítima 
- Existem também automações deste exploit como https://www.rapid7.com/db/modules/exploit/multi/misc/msfd_rce_remote/

## Ataques

- Não há ataques reportados associados a esta falha
- Possíveis danos seriam: acesso a informação confidencial como credenciais de login ou documentos privados, instalação de software malicioso, entre outros
