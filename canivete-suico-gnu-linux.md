# Canivete Suíco GNU/Linux

## Software Livre

Para um software ser considerado livre, ele precisa seguir, **sem exceção**, as quatro seguintes liberdades:

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

## Redefinindo a senha de root

### Debian

> Para realizar este procedimento, precisa ter acesso físico ao: Desktop, Notebook ou Servidor.

Caso não se lembre ou, por outra razão, precise redefinir a senha do usuário *root*, na tela do *GRUB*, pressione a tecla `'e'` para editar as "entradas do *GRUB*".<br>

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

### Rocky Linux

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

## Gerenciador de pacotes

### Debian

No GNU/Linux Debian e seus derivados, a ferramenta, em linha de comando (**CLI**), utilizada para realizar a administração dos pacotes é o **APT** (*Advanced Package Tool*).

Atualizando a lista de pacotes:

```
# apt update
```

Este comando consulta o arquivo */etc/apt/sources.list*, e verifica quais pacotes possuem novas versões, na base de dados do repositório oficial, e compara com todos os pacotes que estão instalados no sistema.

Atualizando todos os pacotes do sistema:

```
# apt upgrade
```
Atualiza todos os pacotes, após executar o `apt update`, que tenham novas versões disponíveis.

Instalando um pacote:

```
# apt install <nome-do-pacote>
```

Realizando apenas o download (sem instalar) de um pacote:

```
# apt install --download-only <nome-do-pacote>
```

Isso pode ser necessário caso deseje instalar o pacote em outro momento.<br>
Após o download, o pacote ficará no diretório */var/cache/apt/archives/nome-do-pacote.deb*.

Reinstalando um pacote:

```
# apt reinstall <nome-do-pacote>
```

Pesquisando por um pacote:

```
# apt search <nome-do-pacote>
```

Informações sobre um pacote:

```
# apt show <nome-do-pacote>
```

Verifica o estado de um pacote:

> Caso não seja passado o nome-do-pacote como parâmetro, todos os pacotes disponíveis serão listados.

```
# apt list <nome-do-pacote>
```

Se o pacote estiver instalado, aparecerá, ao final do nome: *\[installed\]*

Removendo um pacote:

```
# apt remove <nome-do-pacote>
```

Esse procedimento remove um pacote instalado, mantendo, apenas, os arquivos de configuração.

Caso deseje remover um pacote e seus arquivos de configuração completamente:

```
# apt purge <nome-do-pacote>
```

Limpando o *cache*:

```
# apt autoclean
```

Esse comando remove os *aquivos.deb* dos pacotes que não estão mais instalados, liberando espaço de armazenamento do diretório */var/cache/apt/archives/*.

Removendo pacotes que não são mais necessários:

```
# apt autoremove
```

Este comando remove os pacote e suas dependências.

### Rocky Linux

No GNU/Linux Rocky Linux, a ferramenta, em linha de comando, utilizada para realizar a administração dos pacotes é o **DNF** (*Dandified YUM*).<br>
A proposta do DNF, quando implementado, foi aumentar a performance e melhorar a resolução de dependências, se comparado ao *YUM*.

Verificando quais repositórios estão habilitados e desabilitados:

```
# dnf repolist all
```

Verificando atualizações do sistema:

```
# dnf check-update
```

Este comando consulta o arquivo */etc/yum.repos.d/rocky.repo*, e verifica quais pacotes possuem novas versões, na base de dados do repositório oficial, e compara com todos os pacotes que estão instalados no sistema.

Atualizando todos os pacotes do sistema:

```
# dnf update
```

Instalando um pacote:

```
# dnf install <nome-do-pacote>
```

Reinstalando um pacote:

```
# dnf reinstall <nome-do-pacote>
```

Pesquisando por um pacote:

```
# dnf search <nome-do-pacote>
```

Informações sobre um pacote:

```
# dnf info <nome-do-pacote>
```

Lista somente os pacotes instalados:

> O comando `dnf list` lista todos os pacotes instalados e os disponíveis.

```
# dnf list installed
```

Removendo um pacote:

```
# dnf remove <nome-do-pacote>
```

Removendo todos os pacotes instalados e suas dependências, que não são mais necessários.

```
# dnf autoremove
```

limpando todos os caches e arquivos temporários.

```
# dnf clean all
```

Este comando limpa o diretório */var/cache/dnf*.
