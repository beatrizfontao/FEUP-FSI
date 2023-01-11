# FSI - Segurança de Redes

---

# A Rede

- Rede simples onde cada máquina tem um endereço IP
- O mais descentralizada possível
- Complexidade concentrada nos endpoints
- Funciona com base de entrega de pacotes

Regras:
- sintaxe: especifica o conteudo das mensagens
- semantica: intrepertar o conteudo das mensagens
- normas/especificações: todas partilham as mesmas regras

## Despositivos

Cada camada tem dispositivos:
- Camada Física: hubs, cabos, antenas
- Camada Lógica: switches, bridges
- Camada de Rede: routers
- Camada de Transporte e superior: gateways

Camadas inferiores transportam mensagens das camadas superiores dentro dos seus próprios pacotes.

## Ethernet

Link layer mais comum.

Mensagens são frames, contem informação sobre o protocolo de rede(ex:IP, ARP).

Cada nó tem um endereço MAC(Media Access Control) - 6 bytes.

Localmente broadcast - enviar mensagens para um meio comum.

# Protocolo IP

Comunicação sem ligação - não faz tentativas de recuperação de mensagens, os pacotes podem perder-se, ser permutados, repeticos, etc.

Estrutura de endereços hierárquica -> IPv4 32 bits, IPv6 128 bits. Este endereço pode ser atribuido de forma estática ou dinamica

## Comunicação na Internet

A comunicação dentro de uma rede local faz-se com base em endereços MAC. Se a mensagem for para fora da rede local, tem de ser enviada para o router.

Tabelas de endereços MAC -> construida dinamicamente com base em pedidos e anúncios ARP (Address Resolution).

## Routing de pacotes

Os routers encaminham pacotes com base em tabelas.

Internet organizada em Autonomous Systems. Estrutura quasi-hierárquica com um pequeno número de AS de topo. Configuração automática de tabelas de routing -> cada router mantém uma tabela de routing global, routers anunciam a outros routers o que podem encaminhar, o encaminhamentos são propagados pela rede.

## Protocolo TCP

Camada de transporte, ligações de longa duração, gestão de tráfego e congestão. Cada aplicação tem um identificador que é o número de uma porta.

### Handshake e Comunicaqöes TCP

É preciso ter a certeza que estamos a comunicar com alguém com o qual acordamos alguma metainformação que depois vai ser usada para o controlo das comunicações. Essa informação resume-se a sincronizar dois números de sequência. Estes números controlam a quantidade de informação transmitica de recebida.

# DNS - Domain Name System

Serviço que permite transformar nomes legíveis por humanos em endereços IP. Utiliza UDP.

Existem por volta de 13 servidores DNS de raiz com visão completa. Nós hierarquicamente inferiores têm visão parcial. Caso o servidor não conheça a resposta, irá perguntar a algum acima na hierarquia. Caching de resultados para eficiência.

---

# Segurança

## Modelo CIA

**Confidencialidade**: Identificação de quem pode aceder a dados<br>
**Integridade**: Noção clara de quem transmite e quem recebe informação que esta chega sem alterações<br>
**Disponibilidade**: Serviço pode ser utilizado

Ataques possíveis:
- A nível físico
- Dispositivos manipulados/configurados para realizar ataques
- Infraestruturas elaboradas de vigilåncia e controlo de comunicações

### Adversários

- observam comunicações(eavesdropper)
- podem inserir pacotes(off path)
- podem observar e inserir pacotes(on path)
- controlar todas as comunicações(man in the middle)

## Wiretrapping

Wiretapping é o processo de escutar um canal de comunicações onde entidades terceiras estão a trocar informação. O atacante liga um equipamento seu ao meio de comunicação para poder recolher informação.

## Eavesdropping/Packet sniffing

É uma forma de wiretapping em redes de comunicação, ondg se recolhgm e armazenam os pacotes trocados entre utilizadores legitimos de uma sub-rede. Permite a um atacante observar todas as comunicações não cifradas(extrair passwords e outra informação sensível).

## MAC flooding: melhor sniffing

Atacante vai injetando mensagens com MACs novos na rede para que as tabelas com endereços MAC substituam os endereços reais por estes. O switch passa a funcionar como um hub (equipamento menos elaborado). O ataque também afeta switches vizinhos.

## MAC spoofing: usurpar MAC address

Quando o sniff revela o MAC address de uma máquina, é possível usurpá-lo. O atacante configura a placa de rede para ter esse MAC e dessa forma a informação passa a ser encaminhada para ele.

---

# Camada de rede

Como não há autenticação, podemos enviar pacotes a quem quiseremos em nome de quem quisermos.

**Scanning**: enviar mensagens a todas as máquinas da rede de modo a identificar potenciais alvos e vulnerabilidades.

**DoS**: sobrecarregar alvo com mais mensagens do que as que consegue tratar

## ARP poisoning/spoofing: usurpar IP

O atacante pode intercetar estas mensagens e reponder com a informação da sua própria máquina.

Permite um ataque Man-in-the-Middle:
- convencr que a sua máquina possui o endereço IP do interlucotor
- receber os pacotes no seu MAC address
- processar a mensagem e enviá-la para o MAC certo, ficando com o conteúdo da mensagem.

## Hijacking de routing

ICMP Router Discovery Protocol (IRDP) -> permite decobrir qual o router que se deve utilizar.

O atacante pode anuncia que ele próprio é o router fazendo com que as máquinas encaminhem pacotes para ele.

## Rogue DHCP

DHCP:
- cliente faz um pedido para ter um endereço IP
- o servidor respondecom uma proposta
- o cliente pede diretamente ao servidor DHCP o IP
- o servidor anda um acknowledgement
- quando existem várias sub-redes, um agente DHCP pode servir de intermediário para falar com o servidor DHCP

Um rogue DHCP server pode convencer um cliente de que o router/gateway está num IP controlado pelo adversário, permitindo um ataque Man in the Middle

## DNS

Nada é autenticado -> é possível sermos direcionados para máquina diferente.

### Spoofing

Ter uma máquina maliciosa a resolver DNS pode permitir ao atacante redirecionar utilizadores para, por exemplo, sites maliciosos, onde pode roubar informações aos mesmos.

### Chace poisoning

Kaminsky Attack.

Bombardear o servidor DNS legítimo com informação falsa. este servidor vai dar informação controlada pelo atacante aos clientes

---

# Camada de Transporte(TCP)

## Terminação de Ligações

No protocolo TCP não é possível saber com quem é que se está a comunicar.

Ataque possível:
- Enviar RST(flag no pacote) a um cliente termina a tentativa de ligação
- Permite controlar o acesso a endereços IP fora da zona onde há controlo

Acontece por exemplo na China - Great Firewall. Restringe o acesso a IPs fora da fronteira.

## Spoofing às cegas(off path)

O atacante não consegue ver a comunicação entre as duas máquinas.
Tentar iniciar contacto com uma das máquinas e tentar adivinhar o número de sequência. 

Mitigação: usar números de sequência aleatórios e dificeis de prever.

## TCP Session hijacking

Deixar duas máquinas comunicarem algum tempo(processos de autenticação) e depois tomar conta da ligação. O atacante faz-se passar por uma das máquinas que já se autenticou, aproveitando o estado da sessão para comunicar com a outra máquina.

Três fases:
- Tracking: ver o estado da sessão até chegar a um ponto que interesse o atacante
- Des-sincronização: fazer com que as duas máquinas não consigam comunicar uma com a outra
- Injeção: sabendo o estado das máquina, fica possível o atacante comunicar com elas

O TCP/IP é vulnerável a este ataque. Permite ao atacante fazer bypass a processos de autenticaçãoque não são efetuados.

Mais dificil que spoofing porque inclui conseguir previamente observar a ligação. 

## UDP Hijacking

Mais fácil que no TCP porque não há controlo de tráfego. 
Um atacante pode interferir na transferência e tentar responder antes do cliente.

## Conclusões

Os protocolos de rede não são seguros by design (segurança é adicionada à posteriori)

As soluções criptográficas permitem resolver o problema em parte.

---

# Defesas

Maior quantidade de náquinas expostas leva a uma maior superfície de ataque.

Chega desligar todos os serviços desnecessários? Não, porque é um número elevado de máquinas, com muita diversidade de sistemas e, para além disso, não existe uma definição clara da superfície de ataque, nem iria permitir defesa em profundidade.

## Solução Típica

Definir uma fronteira clara entre organização e internet - Firewall.

Mesmo dentro da organização:
- **zona desmilitarizada(DMZ)**: comunicam com o exterior de uma forma típica - rede diferente de onde estão os outros serviços. Comunicação entre a DMZ e os sistemas internos tem de ser monitorizada
  
## Firewalls

- **Firewalls locais**
  - Correm nas máquinas das aplicações/utilizadores
  - Podem ter regras especificas para determinada máquina
- **Firewalls na rede**
  - Filtra o tráfego de e para muitas máquinas
  - Definem a fronteira com o exterior

Funcionamento:
- Filtragem de pacotes: analisam endereços IP, protocolos. Procuram eliminar comunicações maliciosas. Existe um nível de funcionalidade mínima que precisa de garantir - equilibrio nas comunicações
- Distinguir de proxies: barreira(intermediário) direcionada para um aplicação ou protocolo específico

## Políticas de Controlo de Acessos

Distingue tráfego recebido do que é enviado. Permite todos os acessos de dentro para fora mas não vice-versa. De fora para dentro é muito conservador: permite acesso a serviços explicitamente designados para exposição e nega todos os outros acessos a serviços internos.

Para o que não está expelícito nas políticas:
- **Default allow**: permitir todos os acessos exceto problemas específicos
- **Defualt deny**: permiir apenas alguns acessos comuns a todos os sistemas - proteção por omissão

## Filtragem de Pacotes

Após o processamento de um pacote este pode ser forward ou drop.

- **Filtragem sem estado**:
  - Olhar para cada pacote individualmente, não tem em conta o contexto em que o pacote foi enviado
- **Filtragem com estado**:
  - Mais dificil de implementar
  - Manter registo de ligações ativas
  - Tem em conta o contexto dos pacotes

## Contornar Filtragem de Pacotes

Pode ser até necessário para utilizadores legítimos, por exemplo dar acesso momentâneo a alguém.

É usado com fins maliciosos também, por exemplo, um atacante envia dados sensíveis para o exterior.

Túnel:
- encapsular um protocolo dentro de outro
- ex: outro protocolo dentro de mensagens http

# Network Address Translation (NAT)

Como não é possível todas as máquinas terem endereços IP únicos globalmente, na fronteira existe um router com um endereço visivel do exterior e localmente temos vários endereços locais. Para comunicar com o exterior, estes endereços locais passam por um processo de tradução.

## Funcionamento NAT

É mantida uma tabela com endereços IP e portas internas nos routers. O router vai associar cada um desses endereços uma porta sua para o exterior.
Quando chega um pacote a essa porta, o router sabe para onde reencaminhá-lo e vice-versa.

Vantagens:
- Reduz a exposição ao exterior
- Ligações externas são descartadas, a nãoser que tenham sido iniciadas internamente

Desvantagens:
- Pode perturbar o funcionamento de alguns protocolos
- Do ponto de vista da segurança, não garante muito
  - Slipstream: através de javascript, o atacante começa a povoar a tabela NAT com coisas que não existem.

# Proxies de aplicação

Obriga o tráfego de uma ou mais aplicações a passar por proxy, funciona como um Man-in-the-Middle.

## Network IDS/IPS

Existe mecanismo de monitorização tráfego que procuram especificamente malware.

Se for ao nível da rede:<br>
Vantagens:
 - Não é necessário estar a configurar máquinas individuais

Desvantagens:
- Exigentes em termos de processamento
- Menos preciso (para reduzir falsos negativos => muitos falsos positivos)
- Não lida com código cifradonem com outras formas de evasão

## Host-Based IDS/IPS

Nas máquinas mais críticas fazer uma deteção mais agressiva. Por exemplo, remoção da camada criptográfica ou identificação de padrões de ataques.

Não se deve generalizar por causo do custo de adotar esta aproximação em todas as máquinas.

## Análise de Logs

- Ferramentes fáceis de obter
- Corridas off-line
- Analisam os resultados do processamento das mensagens e o comportamento das máquinas
- Apenas reativo => após deteção do ataque : não permite prevenir/bloquear o ataque
- Logs podem ser alterados pelos atacantes
- Podem permitir melhorar a configuração do sistema

## Pen-Testing

Em vez de "esperar" um ataque, simulam-se situações de ataque para testar o sistema.

Vantagens:
- Proativo: uma falha não significa perda de valor e permite corrigir o sistema
- Permite optimizar os sistemas

Desvantagens:
- Custos podem ser elevados(contratação de profissionais)
- Mesmo um ataque simulado pode causar danos ao sistema

## Honey-Pots

Colocar nos sistemas máquinas desprotegidas que chamem à atenção dos atacantes. Estas máquinas são observadas de perto para verificar possíveis ataques.