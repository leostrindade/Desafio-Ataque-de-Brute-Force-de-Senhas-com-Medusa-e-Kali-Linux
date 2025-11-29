<h1># Desafio-Ataque-de-Brute-Force-de-Senhas-com-Medusa-e-Kali-Linux</h1>
Descrição: Projeto focado em praticar ataques de força bruta, entender seus impactos e documentar mitigação. Inclui configuração de VMs (Kali + Metasploitable), testes em FTP, DVWA e SMB, além de wordlists, comandos, validações e recomendações de segurança.

<h2>Resumo do desafio:</h2>

<h3>1 - Varredura de rede:</h3>
Para iniciar, realizei o procedimento de identificação dos dispositivos conectados à rede da VM com o <b>Nmap</b>, verificando o meu IP conectado na rede através do comando <b>“ifconfig”</b>;

Após essa análise, iniciei a varredura dos hosts e se haviam portas abertas para iniciar o ataque de força bruta, utilizando o comando <b>“sudo nmap -v -TS -sS --open (IP identificado).0/24”</b>;

Em seguida, busquei coletar mais informações sobre o sistema operacional do alvo em questão, a fim de facilitar o ataque, com o comando <b>“sudo nmap -O (IP do alvo)”</b>.

<h3>2 - Ataque de força bruta através de FTP:</h3>

Após a identificação do alvo, dei início ao processo de conexão dos dispositivos, inicialmente utilizando o comando <b>“ping (IP do alvo)”</b>, com uma resposta positiva, busquei realizar uma conexão FTP com o alvo, através do comando <b>“ftp (IP do alvo)”</b> que se mostrou ativa.

Então, dei início ao processo de criação das wordlists contendo usuários e senhas comuns em arquivos com a extensão .txt, utilizando dois comandos, sendo eles: <b>"echo -e "user\nmsfadmin\nadmin\nroot" > users.txt"</b> para criar a lista de possíveis usuários e <b>"echo - e "123456\npassword\nqwerty\nmsfadmin" > pass.txt"</b> para possíveis senhas.

Logo em seguida, utilizei da ferramenta Medusa para identificar os possíveis usuários e senhas que poderiam ser utilizadas, por meio do comando <b>"medusa -h (IP) -U users.txt -P pass.txt -M ftp -t 6"</b>, após a identificação, realizei a conexão através do protocolo FTP e aqui o ataque havia sido bem sucedido.

<h3>3 - Ataque de força bruta em um formulário de login em sistemas web:</h3>

Utilizei formulários disponíveis na web como o <b>“(IP do alvo)/dvwa/login.php”</b>, para realizar o ataque de força bruta. 

Inicialmente, verifiquei as informações que eram passadas na requisição de login do formulário. 

Após isso, utilizei o Medusa para verificar os possíveis usuários e senhas que poderiam ser utilizados para o ataque, com as wordlists criadas durante o ataque de força bruta por meio do protocolo FTP, com o comando <b>"medusa -h (IP) -U users.txt -P pass.txt -M http -m PAGE:'/dvwa/login.php' -m FORM:'username=^USER^&password=^PASS^&Login=Login' - m 'FAIL=Login failed' -t 6".</b>

Assim que os usuários e senhas foram identificados, realizei o acesso ao sistema web, concluindo o ataque.


<h3>4 - Ataque de força bruta em SMB + Password Spraying</h3>

Neste teste utilizei o processo de enumeração para coletar informações detalhadas sobre um sistema-alvo, como nomes de usuários, nomes de máquinas, serviços ativos e permissões, com o enum4linux.

Inicialmente utilizei o comando <b>“enum4linux -a (IP do alvo) | tee enum4_output.txt”</b>, onde o <b>“enum4linux -a (IP)”</b> traria informações sobre o alvo em questão e o comando <b>“tee enum4_output.txt”</b> seria utilizado para direcionar tais informações para um documento de texto simultaneamente.

Após a finalização do comando, utilizei do comando <b>"less enum4_output.txt"</b> para abrir o documento criado e analisar as informações que foram encontradas.

Logo em seguida criei duas wordlists, onde uma estaria com os possíveis usuários e outra com as possíveis senhas utilizadas para o ataque, com os comandos <b>"echo -e "user\nmsfadmin\nservice" > smb_users.txt"</b> para os usuários e <b>"echo - e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt"</b> para as senhas.

Então com a ferramenta Medusa, foi possível identificar as credenciais de acesso que poderiam ser utilizadas para o ataque, através do comando <b>"medusa -h (IP do alvo) -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50"</b>.

Por fim, utilizamos o comando <b>"smbclient -L //(IP do alvo) -U msfadmin"</b> para estabelecer uma conexão e ter acesso ao dispositivo do alvo, finalizando assim os ataques de força bruta.
