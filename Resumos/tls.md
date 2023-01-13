# FSI - TSL

---

# Transport Layer Security(TLS)

## Modelo de Segurança

Assumir que existe um atacante na rede que controla tudo na insfra-estrutura(routers, DNS,...) incluindo escutar, bloquear e alterar pacotes e mensagens.

As sessões de TLS começam por usar criptografia de chave pública e de seguida chave simétrica.

Handshake TLS1.3 = Diffie-Hellman Autenticado.

Em maior parte das sessões só o servidor é que é autenticado. É necessário ter uma pki para ter a certeza que estamos a usar a chave pública do servidor.

## Integração TLS/HTTP

As mensagens HTTP são transmitidas como payloads TLS (escondido) depois do canal estabelecido.

Problemas/soluções:
- web proxy: proxy precisa de saber o cabeçalho HTTP para estabelecer ligação
  - informar o proxy qual o site para qual essas mensagens devem ser enviadas
- virtual hosts: mesmo IP com vários DNS => como é que o servidor sabe qual certificado devolver?
  - solução antiga: client-hello inclui nome de domínio do servidor - poe em causa o anonimato que estava a tentar ser garantido ao esconder os certificados
  - ainda não há solução mas o ideal seria existir uma forma de, nos registos DNS, haver chaves públicas que permitem ao cliente juntar no client-hello uma versão cifrada do nome do domínio.

## TLS/HTTPS nos browsers

Se não for garantido que estamos a comunicar com https com o servidor, pode estar algum atacante a escutar a comunicação(MiM) - SSL Strip Attack.

Solução: deixar na cache do browser uma flag(Strict Transport Security) que garante que o browser daí em diante exija https para aquele nome do domínio - desaparece com a limpeza da cache.

Potenciais problemas de mixed content HTTP/HTTPS. Browsers hoje em dia também avisam sobre mixed content.

## Privacidade

O TLS revela ainda muita meta informação (tamanho e número de mensagens). A análise de tráfego pode reconhecer com que o servidor está a interargir e as operações que está a fazer. Também se aplica a tráfego transferido via TOR.
