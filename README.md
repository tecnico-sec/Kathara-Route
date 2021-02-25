Instituto Superior Técnico, Universidade de Lisboa

**Segurança Informática em Redes e Sistemas**

# Guia de Laboratório - *Network Routing*

## Objectivo

O objectivo do guia consiste em tomar contacto com o sistema Kathará através da configuração de uma rede simples.

## Instalação do Kathará

O Kathará já está instalado nos PCs do laboratório, em Ubuntu GNU/Linux.
Para aceder aos PCs do laboratório deverá usar o *username* "seed" e a *password* "dees".

Caso pretenda instalar o Kathará no seu PC, pode corrê-lo directamente sobre uma distribuição GNU/Linux ou através de uma máquina virtual com o mesmo sistema operativo. 
É necessário ter em conta que o Kathará lança vários contentores (semelhantes a máquinas virtuais leves) pelo que se o correr dentro de uma máquina virtual, deve garantir que esta tem memória suficiente.

Para instalar o Kathará deve fazer o *download* da versão *"Latest Stable Release"* de

> <https://www.kathara.org/#download>

e seguir os passos indicados em

> https://github.com/KatharaFramework/Kathara/wiki

## Exercício 1

O Kathará tem comandos para executar e terminar máquinas virtuais individualmente, mas neste trabalho vamos lançar uma rede com várias componentes, definida no ficheiro *lab.conf*.
O Kathará chama a uma configuração destas um "laboratório".
Para executar e parar um laboratório executam-se respectivamente os comandos `lstart` e `lclean` na directoria de trabalho (onde tem de estar o ficheiro *lab.conf*).
Pode-se usar comandos do [docker][6] para ou interagir com maquinas individuais.

Este laboratório usa vários comandos Linux. Caso precise de informação
adicional sobre algum deles execute 
```bash
man [comando]
```

Obtenha e descomprima o ficheiro *guia.tgz* que contém um laboratório Kathara, usando o comando

```bash
tar xzvf guia.tgz
````

Vamos considerar a seguinte topologia, com dois *routers* e dois computadores:

![Topologia de Rede][2]

 Todas as máquinas são computadores a correr Linux, mas os *routers* têm duas interfaces de rede. 
 O Linux não fornece de origem funcionalidades completas de *routing* mas contém uma tabela de
*forwarding* que pode ser usada para indicar para que interface deve ser encaminhado cada pacote IP em função do seu endereço IP de destino.

1.  Atribua um endereço IP a cada interface de rede, escrevendo-os na
    figura acima. 
    No caso dos endereços IP, note que existem 3 sub-redes e que pode usar apenas endereços IP do bloco `1.2.0.0/22`. 
    Não utilize nem o primeiro nem o último endereço de cada bloco escolhido pois, como sabe, têm um significado específico: rede e *broadcast*, respectivamente. 
    Mostre-os ao docente antes de prosseguir. 
    Use o mínimo de endereços possível.

2.  Observe o conteúdo do ficheiro *lab.conf* de modo a compreender porque é que ele gera a rede com a topologia acima.

3.  Execute o laboratório mudando para a directoria que contém o ficheiro *lab.conf* e executando `lstart` (ou `kathara.exe lstart` em Windows). 
Observe o arranque dos quatro contentores.

4.  Cada *router* tem duas interfaces,`eth0` e `eth1`. 
Execute o comando `ip addr` nos *routers* e nos computadores e observe que as interfaces de rede não estão configuradas.

5.  Configure todas as interfaces usando os comandos

```bash
ip addr add dev [interface] [endereço IP]/[comprimento máscara de rede]
ip link set dev eth0 up
```

Por exemplo:
```bash
ip addr add dev eth0 1.2.0.1/24
ip link set dev eth0 up
```

6.  Experimente em todos os computadores e *routers* fazer *ping* com os endereços IP das outras interfaces das sub-redes às quais pertencem e verifique que o *ping* tem sucesso (por exemplo, do PC1 faça *ping* à interface `eth0` do Router1).

7.  Experimente num PC fazer *ping* para a interface de rede do outro e observe que obtém como resposta "*Network unreachable*". 
O que significa essa resposta?

8.  Execute o comando *ip route* no mesmo computador e observe que a tabela de *forwarding* (também designada tabela de *routing*) não tem informação suficiente para enviar os pacotes ICMP do *ping* para o seu destino (ou seja, a tabela não tem informação sobre a sub-rede do outro computador). 
Note que a tabela só tem informação para fazer *forwarding* para a sub-rede à qual o computador está ligado directamente, que é preenchida automaticamente quando se liga a interface.

9.  Execute o comando *ip route* em cada *router* e observe que não existem entradas que permitam fazer *routing* entre os computadores.
Repare também que a tabela de *forwarding* de cada *router* só tem entradas para fazer *forwarding* para as redes a que o *router* está directamente ligado.

10. Neste guia vamos configurar manualmente as tabelas de *forwarding* (expedição). 
Nos computadores tem de indicar qual é a *default gateway*, ou seja, o endereço IP da interface do *router* à qual o PC está ligado através da LAN (ou sub-rede). 
Configure essa informação em cada computador executando:

```bash
ip route add default via [endereço IP]
```

Se alguma vez se enganar, pode remover os efeitos desse comando executando:

```bash
ip route del default via [endereço IP]
```

Execute o comando *ip route* para observar a nova rota na tabela de *forwarding*.

11. Experimente fazer *ping* de um PC para a interface de rede do outro.
    O que notou de diferente em relação ao que aconteceu no ponto 7?

12. Nos *routers* tem de indicar como estes podem fazer *forwarding* para as sub-redes às quais não estão ligados directamente (cada *router* só não está ligado a uma das três sub-redes).
    Para o efeito, em cada *router* execute o comando:

```bash
ip route add [rede destino] via [endereço gateway (próximo hop)]
```

> O endereço IP da *gateway* é o endereço da interface de rede do outro
> *router* que está ligada à sub-rede que interliga os dois *routers*.
> Execute o comando ip route para observar a nova linha da tabela. Mais
> uma vez, se se enganar pode remover a rota usando `ip route del...`

13. Execute em cada computador *ping* para cada uma das interfaces da rede e observe que consegue comunicar.

14. No servidor corra o comando `nmap [endereço IP do pc1]`. 
Que informação obteve?

15. No servidor corra o comando `nmap [endereço da sub-rede usada]`.
Que informação obteve?

## Exercício 2

Desligue o laboratório do exercício 1. 
Modifique os ficheiros necessários de modo a criar uma interface eth2 no *router* representado do lado esquerdo da figura. 
Ligue-lhe um novo terminal *pc2*. 
Arranque o laboratório e configure o *router* e o novo terminal de modo a que consigam comunicar (basta fazer *ping*).

## Referências

-   Kathará, [https://github.com/KatharaFramework/Kathara/wiki/][3]

-   Linux Set Up routing with ip command -
    [http://www.cyberciti.biz/faq/howto-linux-configuring-default-route-with-ipcommand/][4]

-   10 Useful "IP" Commands to Configure Network Interfaces -
    [http://www.tecmint.com/ip-command-examples/][5]

-   Docker documentation, [https://docs.docker.com/][6]

  [1]: media/tecnico.jpeg
  [2]: media/topologia-de-rede.png 
  [3]: https://github.com/KatharaFramework/Kathara/wiki
  [4]: http://www.cyberciti.biz/faq/howto-linux-configuring-default-route-with-ipcommand/
  [5]: http://www.tecmint.com/ip-command-examples/
  [6]: https://docs.docker.com/
