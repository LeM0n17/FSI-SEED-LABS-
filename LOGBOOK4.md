# LOGBOOK4 - Environment Variable and Set-UID Lab

### Task 2
>In this task, we study how a child process gets its environment variables from its parent. In
Unix, fork() creates a new process by duplicating the calling process. The new process,
referred to as the child, is an exact duplicate of the calling process, referred to as the
parent; however, several things are not inherited by the child (please see the manual of
fork() by typing the following command: man fork). In this task, we would like to know
whether the parent’s environment variables are inherited by the child process or not.
- Quando o programa foi executado sem alterações, exibiu-se as variáveis de ambiente do processo filho. Após a modificação do programa, observou-se que, desta vez, eram as variáveis de ambiente do processo pai que eram exibidas. Após se ter guardado os resultados em ficheiros separados, utilizou-se o comando 'diff' para comparar as variáveis de ambiente em ambos os processos. Qualquer diferença nas variáveis de ambiente entre os dois processos teria sido destacada pelo comando 'diff'. No entanto, não foram encontradas diferenças, confirmando que as variáveis de ambiente são, de facto, herdadas pelo processo filho.

### Task 3
>In this task, we study how environment variables are affected when a new program is
executed via execve(). The function execve() calls a system call to load a new command
and execute it; this function never returns. No new process is created; instead, the calling
process’s text, data, bss, and stack are overwritten by that of the program loaded.
Essentially, execve() runs the new program inside the calling process. We are interested
in what happens to the environment variables; are they automatically inherited by the new
program?
- Nesta tarefa, ao executar o programa sem qualquer modificação, nenhuma variável de ambiente é exibida. Isto acontece pois o terceiro argumento do comando 'execve()' é *NULL*. Podemos então verificar que usando *NULL* como terceiro argumento faz com que as variáveis de ambiente do processo atual não sejam herdadas pelo *novo programa*.


- Pelo contrário, ao modificar o terceiro argumento para 'environ', uma variável que armazena as variáveis de ambiente do processo atual, estas são passadas para o *novo programa* e consequentemente são exibidas após executar o programa.


- Em suma, utilizar 'environ' permite ao *novo programa* herdar as variáveis de ambiente do processo atual, enquanto usar *NULL*, faz com que as variáveis de ambiente não sejam herdadas.

### Task 5
>Set-UID is an important security mechanism in Unix operating systems. When a program
runs, it assumes the owner’s privileges. For example, if the program’s owner is root, when
anyone runs this program, the program gains the root’s privileges during its execution.
Set-UID allows us to do many interesting things, but since it escalates the user’s privilege,
it is quite risky. Although the behaviors of programs are decided by their program logic, not
by users, users can indeed affect the behaviors via environment variables. To understand
how programs are affected, let us first figure out whether environment variables are
inherited by the program’s process from the user’s process.
- Nesta tarefa, concluimos que de facto o processo filho herda as variáveis de ambiente que definimos com o comando 'export' antes de correr o programa da *shell*.

### Task 6
>Because of the shell program invoked, calling system() within a program is quite 
dangerous. This is because the actual behavior of the shell program can be affected by 
environment variables, such as PATH; these environment variables are provided by the 
user, who may be malicious. By changing these variables, malicious users can control the 
behavior of the Set-UID program. (...) Can 
you get this program to run your own malicious code, instead of /bin/ls? If you can, is 
your malicious code running with the root privilege? Describe and explain your 
observations.
- Após a realização desta tarefa, concluimos que alterando a variável de ambiente PATH, e criando um executável com um nome igual ao do executável desejado, é possível executar qualquer programa, mesmo malicioso. Além disso, foi possível verificar que o código estava a ser executado com previlégios de *root*.
