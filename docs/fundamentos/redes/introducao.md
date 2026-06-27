# Fundamentos de Rede

Uma rede de computadores é formada por vários dispositivos conectados entre si por cabos ou redes sem fio.

Na comunicação digital, os bits são representados por sinais físicos, como variações de tensão elétrica, pulsos de luz ou ondas de rádio. Os níveis usados para representar 0 e 1 dependem da tecnologia empregada.

Entretanto, apenas esse envio não basta, pois uma sequência aleatória de bits enviada de um computador A para um computador B não transmite uma informação compreensível. Por isso, foram criados os **protocolos**, que definem contratos e regras de comunicação.

Um exemplo simples seria:

```text
Velocidade: transmitir 9600 bits por segundo
Tamanho: cada caractere vai ter 8 bits
Início: um bit especial marca o início
Fim: um bit especial marca o fim
Se houver erros, o receptor avisa
```

## Comunicação Serial e Rede Local

A comunicação serial é uma forma de comunicação em que um computador A envia os bits em sequência e o computador B os recebe na mesma ordem. Por exemplo:

```text
A envia 01001111 e B recebe 01001111
```

Isso gera um problema quando temos vários computadores, pois cada um precisaria estar conectado aos demais. Por exemplo, com três computadores, A, B e C:

```text
A <-> B
A <-> C
B <-> C
```

Para resolver esse problema, surgiu a ideia de criar uma rede local, chamada de LAN (_Local Area Network_). Também surgiu a Ethernet, em que todos os computadores eram conectados por um único cabo compartilhado:

```text
A <-> B <-> C <-> D
```

Entretanto, todos os computadores participavam da comunicação, mesmo quando a mensagem não era destinada a eles. No exemplo acima, se A enviasse uma mensagem para D, tanto B quanto C receberiam o sinal durante a transmissão.

Além disso, se dois computadores enviassem mensagens ao mesmo tempo, ocorreria uma colisão na rede. Por isso, foi criado um método chamado **CSMA/CD**, que detectava a colisão, interrompia a transmissão, aguardava certo tempo e tentava novamente. Um exemplo simples seria imaginar várias pessoas em uma conversa: se duas começassem a falar ao mesmo tempo, elas parariam, esperariam certo tempo e tentariam novamente.

Depois, surgiu o **switch**, um equipamento responsável por receber um quadro e encaminhá-lo ao computador de destino. Essa identificação é feita pelo **endereço MAC** (_Media Access Control_). Quando um computador A quer se comunicar com um computador B, o quadro contém tanto o endereço MAC de origem quanto o endereço MAC de destino. Esse envio é chamado de **quadro Ethernet**.

O switch aprende quais computadores estão conectados a cada porta e registra seus endereços MAC. Para verificar a integridade da mensagem, usa-se o **FCS (_Frame Check Sequence_)**, um código de detecção de erros adicionado ao final de um quadro Ethernet. Se o FCS calculado pelo receptor não for igual ao recebido, o quadro será descartado.

Os **switches** resolvem o problema da comunicação local, mas não o da comunicação com computadores que estão em outras redes. Para isso, existem os **roteadores**, responsáveis pela comunicação entre redes.

- Switch: responsável por conectar dois ou mais dispositivos em uma rede local.
- Roteador: responsável por conectar duas ou mais redes.

O principal identificador da interface de rede na camada de enlace é o **endereço MAC**, enquanto o principal identificador lógico na camada de rede é o **endereço IP**.

Em uma rede IP, o identificador lógico é o **endereço IP**, que permite a comunicação entre redes e a criação de uma organização lógica. Por exemplo, uma empresa pode ter várias redes separadas por setores, como RH e vendas.

```text
192.168.1.11
192.168.1.12
192.168.1.13
```

Percebe-se que há certa lógica entre os IPs acima, mas o IP `10.0.0.10` dificilmente faz parte da mesma rede.

Entretanto, somente o IP não basta, pois ele não identifica a rede e muito menos o `host`. Para isso, temos a **Máscara de Rede**. A máscara de rede define qual parte do IP representa a **rede** e qual parte representa o **host**. Exemplo:

```text
IP: 192.168.1.10
Máscara de Rede: 255.255.255.0
```

Essa rede também pode ser escrita assim: `192.168.1.0/24`, indicando que os primeiros 24 bits identificam a rede e os bits restantes identificam o host. Um endereço IPv4 é formado por quatro grupos com valores de 0 a 255, totalizando 8 bits por grupo.

Logo, a rede `192.168.1.0/24` abrange os endereços de `192.168.1.0` a `192.168.1.255`, mas o primeiro e o último são reservados, respectivamente, para identificar a rede e o broadcast. Nesse caso, os endereços de host utilizáveis vão de `192.168.1.1` a `192.168.1.254`.

## Rede Remota, IP Interno e IP Externo

Suponhamos que nosso IP seja `192.168.1.10` e queiramos enviar dados para o IP `192.168.1.20`. Esse computador está na mesma rede que o nosso; portanto, podemos enviar os dados diretamente ao endereço MAC correspondente. Agora, suponhamos que queiramos enviar algo para um computador que não está na nossa rede, como o IP `8.8.8.8`.

Para isso, precisamos usar um gateway, geralmente o nosso roteador. O gateway é a "porta de saída" da rede local e costuma usar um endereço como `192.168.1.1`. Nesse caso, o roteador tem o IP interno `192.168.1.1` e, por meio de um IP externo, como `200.100.50.25`, comunica-se com outras redes para alcançar o IP `8.8.8.8`.

Quando um IP está fora da nossa rede local, o tráfego é encaminhado ao roteador, que usa sua interface WAN (_Wide Area Network_) para se comunicar com a rede externa.

Se o destino estiver na mesma rede, não precisamos usar o **gateway**. O dispositivo de origem usa o protocolo **ARP** (_Address Resolution Protocol_) para descobrir o endereço MAC associado ao IP de destino. Para visualizar a tabela ARP:

```bash
arp -a
```

Geralmente, os intervalos de IP usados em redes locais são os seguintes:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Ao acessar a Internet, o roteador pode trocar o IP e a porta de origem da rede local por seu IP externo e outra porta. O mecanismo responsável por essa tradução é o **NAT** (_Network Address Translation_). Exemplo:

```text
Computador A -> 192.168.1.10:50100 vira 200.100.50.25:60001
```

Quando a resposta chega, o roteador consulta a tradução registrada e a encaminha ao dispositivo e à porta de origem corretos.

Nosso roteador geralmente precisa conhecer apenas algumas rotas, não todas:

- Minha LAN
- Meu provedor
- Rota padrão (geralmente do provedor)

O encaminhamento entre as demais redes fica a cargo dos **sistemas autônomos** (AS), que anunciam os prefixos IP sob sua responsabilidade e trocam informações de roteamento entre si.

## Broadcast

É uma forma de enviar uma mensagem a todos os dispositivos do mesmo domínio de broadcast. É usado com frequência na descoberta de dispositivos, principalmente com **ARP** e **DHCP**. Em uma rede Ethernet, um quadro destinado a `FF:FF:FF:FF:FF:FF` é distribuído pelo switch a todas as portas relevantes. Esse processo é chamado de **flooding**. No caso do ARP, ele permite descobrir o endereço MAC associado a um IP e salvar essa associação na tabela ARP.
