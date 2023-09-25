# Canivete Suíco GNU/Linux

## Software Livre

Para um software ser considerado livre, ele precisa precisa seguir, **sem exceção**, as quatro seguintes liberdades:

- Liberdade número 0: O utilizador tem a liberdade de executar o software, para qualquer propósito/finalidade;
- Liberdade número 1: O utilizador tem a liberdade de estudar o funcionamento do software, alterando/adaptando conforme sua necessidade, sendo necessário o acesso total ao código fonte;
- Liberdade número 2: Após alterar o software, o utilizador tem a liberdade de redistribuir suas cópias para quem desejar;
- Liberdade número 3: O utilizador precisa disponibilizar o código fonte, com todas as suas alterações realizadas no software, para a comunidade.

## Como o SHELL prioriza suas tarefas

> Estou considerando o GNU Bash (Born Again SHell).

O Shell analisa a linha, na seguinte ordem:

0. Examina os comandos, opções e parâmetros recebidos (separados por espaços em branco), pesquisando na variável de ambiente **$PATH**.<br>
Ele também resolve todas as atribuições as variáveis definidas;
1. Resolve os operadores de redirecionamento: entrada (**stdin < <<**), saída (**stdout > >>**), erro (**stderr 2>**) e pipe (**|**);
2. Expande as chaves **{}** em sequência de caracteres (string);
3. Substitui as variáveis, definidas pelo o caractere cifrão **$var**, por seus valores;
4. Substitui os metacaracteres para arquivo(s):

- 4.1 Coringas padrão (**globs**):

  - Asterisco **\*** - Nenhum ou qualquer (muitos) caracteres;
  - Interrogação **?** - Nenhum ou apenas um caractere (opcional);
  - Lista **[]** - Qualquer caractere dentro dos colchetes (lista);
  - Lista Negada **[!]**- Qualquer caractere que não esteja dentro dos colchetes (lista).

- 4.2 Corinhas Extendidos (**extglobs**):

  - **?(LISTA)** - Zero ou uma ocorrência;
  - ***(LISTA)** - Zero ou mais ocorrências;
  - **+(LISTA)** - Uma ou mais ocorrências;
  - **@(LISTA)** - Exatamente uma ocorrência;
  - **!(LISTA)** - Tudo, exceto a lista.

5. Fases finais:

- 5.1 Substitui os aliases;
- 5.2 Verifica se o arquivo existe;
- 5.3 Verifica se tem permissão para execução.

6. Envia a linha de comando para o Kernel e, após encerrar o processo, devolve o **prompt**.

## Redefinindo a senha de root - Debian

> Para realizar este procedimento, precisa ter acesso físico ao: Desktop, Notebook ou Servidor.

Caso não se lembre ou, por outra razão, precise alterar a senha do usuário *root*, na tela do *GRUB*, pressione a tecla `'e'` para editar as "entradas do *GRUB*".<br>

![TelaGRUB](https://github.com/jplfalcao/cheat_sheet/blob/main/debian_recovery_password/debian-recovery-password-1.png)

Utilizando as teclas de navegação do teclado, precisamos encontrar a linha que se inicia com `linux`. Esta linha corresponde ao *kernel*.<br>
Precisamos navegar até o final da linha e substituir `ro quiet` por `rw init=/bin/bash`.<br>

![EditandoGRUB](https://github.com/jplfalcao/cheat_sheet/blob/main/debian_recovery_password/debian-recovery-password-2.png)

Esse procedimento informa para o *GRUB* carregar o *kernel*, montar a estrutura do diretório raiz (/), com permissão de escrita (**rw**), e fazer com que o primeiro processo (**init**) a ser executado seja o interpretador de comandos `bash`.<br>

Ao finalizar a edição, pressione a tecla `F10`, e o *GRUB* começará a carregar o kernel.<br>
Em seguida, redefinimos a senha de *root*:

```
# passwd
```

![AlterandoSenha](https://github.com/jplfalcao/cheat_sheet/blob/main/debian_recovery_password/debian-recovery-password-3.png)

Pressione as teclas `Ctrl+Alt+Delete` para reiniciar.<br>
Quando o sistema realizar o *boot* e apresentar o gerenciador de *login* gráfico (**caso tenha interface gráfica**), pressione `Ctrl+Alt+F1`.<br>
Realize o *login* utilizando o usuário *root* e a senha alterada.

![TelaLogin](https://github.com/jplfalcao/cheat_sheet/blob/main/debian_recovery_password/debian-recovery-password-4.png)

## Redefinindo a senha de root - Rocky Linux

Pressione a tecla `'e'` para editar o *GRUB*.<br>

![TelaGRUB](https://github.com/jplfalcao/cheat_sheet/blob/main/rockylinux_recovery_password/rockylinux-recovery-password-1.png)

Na linha que inicia com `linux`, navegue até o final e adicione a diretiva `rd.break`. Pressione `Ctrl+x` para acessar o **modo de emergência**.<br>

![EditandoGRUB](https://github.com/jplfalcao/cheat_sheet/blob/main/rockylinux_recovery_password/rockylinux-recovery-password-2.png)

Precisamos montar o diretório */sysroot* com permissão de escrita e leitura:

```
# mount -o rw,remount /sysroot
```

Em seguida, precisamos alterar o ambiente para o */sysroot*:

```
# chroot /sysroot
```

Redefinindo a senha de *root*:

```
# passwd root
```

Precisamos criar um arquivo oculto `.autorelabel`, no diretório raiz (/), para atualizar as informações do **SELinux**:

```
# touch  /.autorelabel
```

Em seguida, saímos do ambiente */sysroot*:

```
# exit
```

Por fim, finalizamos a sessão e reinicializamos o sistema:

```
# exit
```

![AlterandoSenha](https://github.com/jplfalcao/cheat_sheet/blob/main/rockylinux_recovery_password/rockylinux-recovery-password-3.png)

Realize o *login* utilizando o usuário *root* e a senha alterada.

![TelaLogin](https://github.com/jplfalcao/cheat_sheet/blob/main/rockylinux_recovery_password/rockylinux-recovery-password-4.png)
