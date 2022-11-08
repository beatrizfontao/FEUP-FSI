# FSI - Segurança de Sistemas

# Principios fundamentais

- **Economia nos Mecanismos**: um sistemas deve ter apenas as funcionalidades necessárias - Keep It Simple - facilita a implementação, a usabilidade e a validação
- **Proteção por omissão**: por omissão, a configuração de um sistema deve ter o nível de proteção conservador (máximo de segurança)
- **Desenho aberto**: assumir que o adversário sabe tudo sobre o nosso sistema. O segredo são parâmetros do sistema - chaves cripttográficas, passwords. A vantagem é saber mais rapidamente da existência de vulnerabilidades e podemos mudar os parametros sem mudar o sistema
- **Defesa em Profundidade**: Nunca devemos confiar numa única linha de defesa
- **Privilégio mínimo**: conceder a cada utilizador/compartimento/programa apenas os privilégios necessários para realizarem a sua função
- **Separação de Privilégios**: O sistema deve ser compartimentado. Cada compartimento está isolado dos outros e as interações devem ser monitorizadas, já que são contextos diferentes, por exemplo em termos de permissões (não confiar no que vem de fora e não enviar nada que não tenha de ser enviado).
- **Mediação Completa**: Definir para cada recurso que operações é possível fazer nesse recurso e quem tem permissões para realizar essas operações. Sempre que alguém tenta aceder a um recurso, só dar acesso se a pessoa possuir as permissões necessárias