# CTF-Bookstore-THM

**Dificuldade:** Média

<h3>Eai, tudo beleza? Trouxe dessa vez mais um write up de CTF pra nossa comunidade</h3>

A maquina dessa vez se chama [Bookstore](https://tryhackme.com/room/bookstoreoc) focada em web enumeration, API fuzzing, privilege escalation e binary decompiling.##

## 1:Enumeração
Com a maquina iniciada vamos iniciar a enumeração com um port scan usando o nmap.
![Captura de Tela (6)](https://user-images.githubusercontent.com/32500664/142086737-a8628150-3949-4eda-88a2-4089eebb8d2b.png)

## Porta 80
![homepage](https://user-images.githubusercontent.com/32500664/142088799-8daa92be-8c12-4c65-8029-afc9af1f90d2.png)

## Porta 5000
![Porta 5000](https://user-images.githubusercontent.com/32500664/142088821-8f8bed36-be86-481c-bc4d-6318d7c3ee10.png)

<h3>Sempre olhe o codigo fonte da pagina... As vezes tem até oração ali no meio.</h3>

![Source Login Port 80](https://user-images.githubusercontent.com/32500664/142089127-98ff3126-0b99-4c69-8201-5f780576f0d1.jpg)

Fazendo um directory brute force na pagina 80 não achei nada de interessante, porém, na 5000...

![FFuf](https://user-images.githubusercontent.com/32500664/142090080-91fd42f8-e30c-4d9e-a855-80fbdee1d640.png)

<h4>5000/robots.txt</h4>
Nada novo...

![robots txt](https://user-images.githubusercontent.com/32500664/142090467-c5257776-a43e-403c-a99c-eeba0e25e9e4.png)

<h4>5000/API</h4>

Isso aqui é bem interessante... E se com todos esses parametros fosse possivel achar um vulnerável a [LFI](https://www.infosec.com.br/local-file-inclusion-remore-file-inclusion/) ?

![5000APi](https://user-images.githubusercontent.com/32500664/142090479-8a962bcb-c2ec-4aad-88a9-ffc09ab5b9a0.png)


<h4>5000/console</h4>
Sem o PIN, por enquanto, não tem muita coisa pra fazer aqui.

![5000console](https://user-images.githubusercontent.com/32500664/142090494-2c85ab2f-1049-4731-9480-bbc0c8da5a7d.png)

## API FUZZING

Lembra do comentário que basicamente diz que o PIN está no arquivo ".bash_history" ? Basicamente se a API for vulneravel conseguiremos ler o arquivo, pra isso, vamos procurar parâmetros vulneráveis com o ffuf. O comando vai testar ser algum parametro de dentro de uma wordlist local me devolve o arquivo Passwd.

![Fuff API](https://user-images.githubusercontent.com/32500664/142096250-5d25626a-7f8a-43ae-bc17-a6da7edf2612.png)

Conseguimos com o arquivo Passwd como forma de teste

![TestCURLAPI](https://user-images.githubusercontent.com/32500664/142101829-1cab5341-e574-48fe-ab2b-c4c25b560ed3.png

Agora Como mágica 🎩 OLHA O PIN AI

![CURLPRAPEGAOPIN](https://user-images.githubusercontent.com/32500664/142102536-a50c8c56-f6e3-4f2f-a60b-6ddcb6bff1da.png)

Com o pin chegamos a pagina console desbloqueada

![5000ConsoleEntrei](https://user-images.githubusercontent.com/32500664/142102982-ae664994-3f78-4e30-9954-79a38233c2a0.png)

Como a própria descrição do console diz que executa em python. Vamos assim gerar um payload reverse shell em python utilizando o site [Reverse Shell Generator](https://www.revshells.com)

![shellmaker](https://user-images.githubusercontent.com/32500664/142106782-4fcbf543-e416-4c92-b2e3-72f8c6bcbe96.PNG)

Inserindo o comando gerado...

# Tamo dentro familia😎
Dentro com a flag user.txt

![InkedRecebendoAshell_LI](https://user-images.githubusercontent.com/32500664/142116851-b546a95f-7bb7-44c2-a705-1d863af273c6.jpg)

## Executando o comando 'ls -la' vemos um arquivo chamado "try-hard" que é propriedade do usuário root.

![imagem_2021-11-17_003513](https://user-images.githubusercontent.com/32500664/142130039-2cff99f3-e4d3-4d86-a2b9-09f3fbf4e636.png)

## Executando ele vemos que é solicitado um "numero mágico".

![Try-harderFofo](https://user-images.githubusercontent.com/32500664/142130173-dbd26b75-f53a-4a49-9eba-628c0cb3ea47.png)

## Então... Estamos em um CTF e falta a root.txt... Eu não acredito em magica, portanto, vamos enviar o arquivo pra nossa maquina usando o comando scp e descompila-lo para ver o que exatamente esse tal "numero mágico" faz.

![EnviandoTrypromeuPC1](https://user-images.githubusercontent.com/32500664/142130420-29cf0407-65c3-4be4-b7ff-7934c704868d.png)

O comando envia o arquivo para o meu diretório /home/kali. Óia o bonitão ai

![ChegoEncomenda](https://user-images.githubusercontent.com/32500664/142130515-fee6c40c-5729-4fdf-8309-7c56ad8b29fa.png)

## Com o arquivo na nossa maquina vou usar o Ghidra para descompilar e ver exatamente o que esse "numero mágico faz"

![Ghidra](https://user-images.githubusercontent.com/32500664/142130678-47f143ee-bea8-4a88-a0df-8a38adcc104d.png)

Então o Ghidra é meio complicado assim nas primeiras vezes mas o foco no caso é a janela a direita que é a "Decompile"

## Nela podemos notar como é feita a verificação do "numero mágico"

Equação feita para verificar.
![a conta po](https://user-images.githubusercontent.com/32500664/142131977-871a10f4-e075-445e-894c-32cef4e1e9f0.jpg)

Valores utilizados na equação.
![valores](https://user-images.githubusercontent.com/32500664/142131978-11588c39-d353-490e-a471-6cd0e96c5b31.jpg)

A primeira vista é possivel notar;
- O uid é definido como 0 (Raiz)
- A variavel uStack24 é definida com o valor hexadecimal 0x5db3
- O "numero mágico" inserido é armazenado na variável uStack28
- A existencia de uma função matemática que é igual a variavél uStack20
- Embaixo vemos a condição que segue a execução caso a variável uStack20 seja igual ao valor hexadeciam seja igual a 0x5dcd21f4
- Se tudo ocorrer ele executa o comando / bin / bash -p

Temos uma equação com 3 variáveis e um valor já definido. Dessas variáveis temos 2 delas(uStack24,uStack20)

Tornando mais facil: **x = y ^ a ^ b** --> 0x5dcd21f4 = y ^ 0x1116 ^ 0x5db3 **y = "numero mágico" que inserimos**
Podemos re-organizar para conseguir o que queremos **y = x ^ a ^ b** --> y = 0x5dcd21f4 ^ 0x1116 ^ 0x5db3

Depois de rasgar minha cabeça no google pra entender o que é esse operador (^)... Descobri que operador é usado em operações XOR bit a bit

'''
 O resultado desta operação é 1 se os dois bits de entrada forem diferentes, caso contrário retorna 0 : A operação exemplo é a 0101 ^ 0110 = 1100
  0 1 0 1
  0 1 1 0
  = = ≠ ≠ 
  1 1 0 0
'''

# Você pode transformar esses valores hexadecimais em decimais e fazer o calculo utilizando o python em decimal mesmo em hexadecimal mesmo (que é o utilizado por mim)

![InkedFazendoConta_LI](https://user-images.githubusercontent.com/32500664/142138015-eadba549-f63a-4139-a74b-cf54bf939f63.jpg)


### Root

## Agora com "numero mágico" em mãos vamos executar o try-harder

Sabemos que se o valor estiver correto ele irá executar uma shell em root.

![VirandoRootComMagicNumber](https://user-images.githubusercontent.com/32500664/142138995-e3798904-f815-4934-958c-d28eb3aa71ec.png)

Não é que o numero magico fazia magica mesmo.

## Flag root

![InkedScreenshot_2021-11-13_22_08_16_LI](https://user-images.githubusercontent.com/32500664/142139285-3ddbf753-8a29-43ba-afab-20c64a5cafb3.jpg)
