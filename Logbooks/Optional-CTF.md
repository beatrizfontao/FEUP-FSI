# Desafios Opcionais

## British Punctuality

Neste desafio apenas nos é dado um ambiente onde a pasta inicial se designa por "flag_reader" e possui os ficheiros seguintes:

![](/images/Optional-CTF/CTFOpt_BP_ls.png)

O ficheiro **main.c** verifica se o ficheiro flag.txt existe e nesse caso, envia como output uma mensagem respetiva.

![](/images/Optional-CTF/CTFOpt_BP_main.png)

O ficheiro **reader** é um programa que executa o código anterior.

O ficheiro **my_script.sh** executa um comando de export através do ficheiro **/tmp/env** caso este exista e remove-o de seguida, por fim executa o comando **printenv** e o programa anterior.

![](/images/Optional-CTF/CTFOpt_BP_my_script.png)

Para além disso o ambiente desligava automaticamente depois de 1 min, onde se podia confirmar no ficheiro **my_cron_script** da pasta **/etc/cron.d** (os asteriscos estão definidos por minuto, hora, dia, mês, ano respetivamente).
Cuja intenção é iniciar a shell no ambiente visto anteriormente e executar o ficheiro **my_script.sh**, guardando o output deste no ficheiro **/tmp/last_log**.

![](/images/Optional-CTF/CTFOpt_BP_cron.png)

Depois desta informação, pesquisamos o diretório **/tmp** e criamos o ficheiro **/tmp/env** uma vez que era usado no script **my_script.sh** anteriormente visto, sendo que este mudaria a variável de ambiente **PATH** de modo a apontar para o diretório **/tmp** e assim priorizar um comando definido aqui ao invés da pasta predefinida pelo sistema.

![](/images/Optional-CTF/CTFOpt_BP_env.png)

De seguida, criamos o ficheiro **/tmp/printenv** que será o foco para explorar a vulnerabilidade, sendo o próposito deste dar overwrite ao comando **printenv** já estabelecido no sistema de modo a que este nos imprima o conteúdo de **flag.txt**, alterando as permissões do mesmo.

![](/images/Optional-CTF/CTFOpt_BP_printenv.png)

Assim executamos o ficheiro **my_script.sh** para alterar a **PATH** e uma segunda vez para imprimir no **/tmp/last_log** o resultado da execução do script.

![](/images/Optional-CTF/CTFOpt_BP_final_exec.png)

Finalmente chegamos à flag!

![](/images/Optional-CTF/CTFOpt_BP_flag.png)
