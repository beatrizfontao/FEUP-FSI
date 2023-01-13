# FSI - Autenticação

---

# Autenticação

Entidade tem a certeza que está a comunicar com outra entidade específica. Contexto: tempo curto, estabelecer um canal seguro e depois é feita a autenticação já nesse contexto seguro.

Autenticação é, geralmente, seguido de autorização.

## Solução Criptográfica

Protocolo de **Desafio-Resposta**:
- é criado um desafio aleatório e enviado para a entidade
- essa entidade autentica esse desafio e devolve
- a assinatura/MAC é verificado, potencialmente dentro de um tempo limite

Garante que alguém que não conhece a chave, falsificar o autenticador para um desafio momentaneo.

# Autenticação de Utilizadores

- Utilizador humano pretende aceder a um recurso online
- Recurso disponível em servidor remoto
- Pretende-se garantir participação ativa/na hora do utilizador

Solução:
- humano fornece identidade e pede acesso
- servidor exige prova de identidade
- utilizador fornece essa prova

Provas de identidade:
- Algo que se sabe(pin, password)
- Algo que se possui(smartcard, telemóvel)
- Algo que se é intrinsecamente(biometria)
<br>
Podem ser usadas isoladamente ou combinadas(autenticação multi-fator).

## Passwords

Solução simples.

Problemas:
- necessita de um canal seguro
- necessário saber se estamos realmente a comunicar com um servidor(atacante ativo pode se fazer passar pelo servidor)

## Passwords Fortes

Ideal: passwords difíceis de adivinhar mas fáceis de memorizar.

Políticas:
- Composição heterogénea: maisculas, minuscula, numeros...
- Comprimento mínimo
- black-list de passwords banidas

## Phishing

Link enviado a alguém que o leva a pensar que é uma fonte credível. Leva essa pessoa a fornecer informações ao atacante (ex:passwords).

Sites muito semelhantes ao original.

## Endpoint

HW keyloggers: dispositivos colocados entre teclado e computador

SW keyloggers: malware que interceta keystrokes

Malware que encontra passwords em memória ou armazenadas.

## Armazenamento de passwords: server-side

O servidor não precisa de saber a password. É só necessário reconhecer a password. Isto pode ser feito ao guardar apenas o hash. Se esta função de hash for criptográfia não pode ser revertível.

## Ataque de Dicionário 

Sabendo a função de hash, tentar, com brute-force, muitas possibilidades de passwords até corresponder com o hash guardado.

Para tornar estes ataques mais dificeis:
- tentar evitar que a informação guardada num servidor para um utilizador possa ser utilizada 
- Salt:
  - gera-se um valor aleatório e guarda-se o hash
  - já não é realista pré-computar
  - uma data breach não compromete outros servidores e passwords
- utilizar funções mais lentas de hash diferentes para passwords

---

# Multi-fator

- Defesa em profundidade
- Utilizar fatores adicionais para confirmação e/ou deteção de problemas

## Algo que se possui

Dispositivo físico que apenas o utilizador possui(ex: chave, código,smartcard...).
prova de posse de token <> prova de identidade -> pode ser roubado por exemplo, mas a probabilidade do atancante ter o dispositivo e saber a password é reduzida

### Smartcards

Processador embutido em cartão de plástico. Este cartão pode armzenar e processar chaves criptográficas.

Tem mais utilizações para além da identificação, por exemplo, cartão SIM, descodificadores de satélite, multibanco... .

O próprio smartcard frequentemente inclui outro fator de autenticação: PIN.

### One-Time Tokens

Máquinas que tem um processador mais simples dada aos utilizadores pela entidade que faz a autenticação. Mostra um código que funciona como um segundo fator de autenticação.

Protocolo usado:
- criptografia simétrica
- periodicamente token gera MAC
- o servidor pede a password e o MAC recente

Vantajoso porque os códigos MAC são de uso único e os códigos MAC futuros são imprevisíveis.

Ainda é vulnerável a ataques Man-in-the-Middle e phishing.

### One-Time Passcode

Semelhante ao anterior mas aplicado a um app. É gerado um código(token virtual).

Por exemplo, enviar por SMS o código e introduzido com password.

Vantagens:
- não há necessidade de mais um dispositivo físico
- chaves mais fáceis de gerir

Desvantagens:
- como o código se encontrará no telemóvel, existe uma menor independência entre fatores

## Sessão Web

Sessão:
- após o utilizador se autenticar
- manter informação para os pedidos seguintes

Sessões podem ser longas(ex:gmail) ou curta.
Sem o conceito de sessão, todos os pedidos ao servidor exigiam nova autenticação.

### HTTP Auth

Atribuir a diferentes pastas diferentes sistemas de autenticação.

Ficheiro que contém hashs de passwords (pares nome de utilizador-password).

Quando é feito um pedido a um servidor para algum sitio onde é necessário autenticação, na resposta este envia um pedido de autenticação de password. O browser mostra ao user um pop up para fazer uma autenticação http. O browser guarda essa password e os dados ligados à autenticação, e, em todos os pedidos para essa pasta no fututro manda essa informação novamente.

Não deve ser utilizado -> guarda passwords no servidor; só é possível fazer log out fechando o browser e não é o próprio site a controlar o formulário que o utilizador usa para se autenticar.

### Tokens de sessão

Alternativa usada.

Na autenticação, o servidor cria um "testemunho" que vai ser guardado do lado do cliente e/ou vai ser utilizado dinamicamente em todos os pedidos gerados para o servidor.

3 formas usadas(todas utilizadas):
- cookies
- informação embutida nos links clicáveis
- campos escondidos em formulários

Tokens devem ser imprevisíveis e invalidados no logout.

### Session Hijacking

**Roubo de token**:
- Cross-Site Scripting(XSS)
- eavesdropping (man-in-the-middle)
- não invalidar os tokens do lado do servidor - falha de logout
- mitigação: ligar token à máquina (ex: por IP)

**Token Fixation**:
- Adivinhar o token
- Atacante "convence" utilizador a fazer login com o mesmo token (ex embutido numa URL)
- mitigação: nunca elevar privilégios/fazer login sem criar token novo

---

## Biometria

Prova de identidade por caracteristica intrinseca da pessoa.
Pode ser uma caracterítica física(impressão digital), comportamental(caligrafia) ou combinação de ambas(voz).

Vantagens:
- Usabilidade
- não é transferível

Desvantagens:
- problemas de privacidade
- direito ao esquecimento - não dá ara esquecer uma característica própria

### Processo

Em autenticação remota temos várias hipóteses:
- servidor recebe amostra em bruto - Apenas o sensor está do lado do utilizador.
- servidor recebe "templates"/caracteristicas já processadas para match - Processo do lado do cliente
- servidor recebe apenas resultado de match - Tudo do lado do cliente, o servidor só recebe o resultado do match

Ordem crescente de necessidade de confiança servidor => cliente<br>
Ordem decrescente de necessidade de confiança cliente => servidor

### Desafios

Técnicos:
- precisão
- usabilidade
- estabilidade - características da pessoa podem mudar
- armazenamento de informaqäo sensivel/dados pessoais

Não técnicos:
- aceitação dos utilizadores

### Registo

Resgisto muito mais elaborado que uma password.Obrigada a um conjunto de medidas para se obter a precisão adequada. Usual existirem individuos para os quais é dificil obter a precisão desejada.

### Precisão

**taxa de falsos positivos (FAR)**: probabilidade de aceitar indevidamente<br>
**taxa de falsos negativos (FRR)**: probabilidade de rejeitar indevidamente

FAR baixo => parece desejável
- mais difícil de atacar
- sempre associada a FRR alto, prejudica a usabilidade
- calibrado para o Equal Error Rate Ratio: FAR = FRR

### Segurança

Ataques:
- **Intercepção**: perda de confidencialidade
  - permite falsificações
  - perda de privacidade
- **Usurpação(spoofing**
  - criar uma característica falsa para enganar o sensor
  - Replay attack: repetir algo que já aconteceu(não gera novas amostras)
  - para previnir: utilizar mais um fator
