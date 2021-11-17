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

