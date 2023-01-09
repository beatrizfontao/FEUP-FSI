# FSI - PKI

---

# Public-Key Infrastructure(PKI)

A criptografia de chave pública assume que as chaves públicas são autênticas. Abre portas para ataques Man-in-the-Middle.

Pode ser resolvido manualente, confirmar que a chave pública pertence à entidade correta. Ou utilizar sistemas de autenticação de chaves públicas(GPG/PGP) - não tem o suporte legal e regulamentar que a PKI tem.

Quando é que a PKI é necessária:
- Normas: qualquer pessoa pode utilizar
- Regulamentação: como devem ser utilizadas essas técnicas
- Responsabilidades e direitos dos participantes
- Leis: garantias formais e pelanizações em caso de violação das regras

# Certificados de Chave Pública

Unidades de informação que as PKI gerem.

Objetivo:
- emissor consegue enviar uma chave pública ao recetor através de um canal seguro
- recetor tem a garantia que o emissor tem a chave secreta respetiva

**Trusted-Third-Party(TTP)**: entidade em que toda a gente confiava que garante a autenticidade de uma chave pública. Esta é uma solução trivial mas não é realista porque não é viável ter apenas uma TTP em que toda a gente confia.

Problemas:
1. como contruir um canl seguro entre o recetor e a TTP
2. o que acontece se a TTP estiver off-line
3. como garantir que o recetor e o emissor confiam na mesma TTP
4. o que é que significa confiança na TTP

Para resolver o problema 1 e 2:
- os certificados de chave pública usam assinaturas digitais
- **Autoridade de Certificação(CA):**
  - conceito de TTP off-line
  - entidade tem de provar que provar à CA que possui a chave secreta correspondente à chave pública
  - Ou, a CA gera o par de chaves e fornece estas chaves à entidade
- a CA gera um certificado com a identidade do emissor junto com a sua chave pública, um número de série e a validade desta informação
- o emissor pode enviar este certificado diretamente ao recetor (não tendo este de aceder à TTP)

## Verificação de certificados

O que é que o recetor deve fazer:
- Verificar a correção da identidade da outra pessoa(ex:DNS de um servidor)
- Verificar a validade dos certificados
- Verificar meta-informação
- Verificar que a CA é de confiança
- Obter a chave pública da CA e verificar a assinatura do certificado

Os dois últimos pontos apresentados resolvem os problemas 3 e 4 apresentados anteriormente.

## Ficha técnica de certificados de chave pública

Object Identifier(OI): código que identifica qual o algoritmo usado para assinar o certificado.

Extensões(attachments):
- contêm um OI
- tem uma flag que indica se a extensão é crítica. Se esta flag estiver ativa mas se não for conhecido, o certificado fica inválido.

Extensões importantes:
- Subject/authority key identifier: Informação auxiliar para facilitar a pesquisa de certificados(valçores de hash)
- Basic constraints: flag que indica quer o certificado pertente a uma CA
- Key usage: Ca pode restringir as utilizações do certificado

---

# Public Key Infrastructure

Gestão de certificados.

Uma PKI é tudo o que é necessário para garantir que:
- um utilizador final de um certificado de uma CA, recebe a garantia que a chave pública pertence a uma determinada pessoa e que pode ser utilizada para um determinado fim
- existem responsabilidade e obrigações claras para toda a gente

## Transações operacionais/gestão

Como é que os certificados circulas na rede:
- podem ser armazenado em repositórios, ou simplesmente nas aplicações/SOs
- podem ser transferidos entre aplicações em protocolos específicos(ex:HTTP)
- têm de ser codificados em formatos que permitam a fácil comunicação entre sistemas

# Funcionamento PKI

Para o utilizador ter conhecimento de uma CA e conseguir verificar a assinatura produzida por uma CA num certificado:
- Numa PKI todas as chaves públicas estão codificadas dentro dos certificados(X.509)
- quando alguém vai verificar um certificado, vai buscar a chave da autoridade a outro certificado diferente
- Ou seja, vai usar a chave pública do segundo certificado para verificar a chave do certificado inicial

Para que se confie no certificado obtido é necessário:
- conhecer previamente a CA e já ter o certificado dessa CA cnfigurado na sua máquina

# Gestão de PKI: Inicialização

Os sistemas operativos trazem um grande número de certificados de CAs pré-instalados.

## Cadeias de Certificação

Para certificados pré-programados no sistema nem é necessário verificar a assinatura - porque são implicitamente confiáveis. Estes certificados são auto-assinados.

Validar um certificado auto-assinado implica:
- acreditar que a chave secreta associada pertence, de facto, à CA
- acreditar que a CA é de confiança

Na prática, as Root CAs, não emitem certificados finais (dos utilizadores), mas sim para outras CAs. Iste leva a uma hierarquia de CAs, quando uma CA A assina o certificado da CA B, então a confiança em B é menor ou igual à confiança em A.

# Gestão PKI: Registo

**Autoridade de Registo(RA)**:
- Contacta diretamente com os utilizadores
- Responsáveis por verificar os dados colocados nos certificados
- Responsåveis por verificar posse de chave privada correspondente à chave pública presente no certificado

# Gestão PKI: Certificação

**Autoridade de Certificação(CA)**:
- não contactam diretamente com os utilizadores
- Infrastrutura que produz chaves e certificados
- Alta segurança: incluem segurança física, air gaps, ...

# Gestão PKI: Revogação

O que acontece se o certificado ficar inválido antes do período de validade(ex: chave corrompida):
- **Certificate Revokation Lists(CRL)**:
  - CA publica uma lista(black-list) de certificados que mesmo estando dentro da data de validade não devem ser utilizados
  - Sistema não muito utilizado

No mundo real:
- **Trusted Service Provider Lists (TSL):**
  - Utilizado em comunidades pequenas/fechadas(ex: na banca)
  - lista(white-list) de certificados válidos a ser utilizados
- **On-line Certificate Status Protocol (OCSP)):**
  - servidor seguro verifica o estado de revogação de um certificado
  - traz problemas de privacidade
- **Certificate pinning:**
  - web servers, browsers e aplicações geram white-lists próprias
  - identificam os certificados mais importantes para entidade críticas(ex: já foi utilizado no google)

# Políticas de Certificação

Extensão que os certificados podem ter(Certificate Poicy) e colocar lá um código (OI) que pode aparecer num decreto-lei. Estabelece uma ligação entre a tecnologia e a legislação.