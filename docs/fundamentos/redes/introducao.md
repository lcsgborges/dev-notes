# Fundamentos de rede

Uma rede de computadores é formada por vários dispositivos conectados entre si por cabos ou redes sem fio.

Na comunicação digital, os bits são representados por sinais físicos, como variações de tensão elétrica, pulsos de luz ou ondas de rádio. Os níveis usados para representar 0 e 1 dependem da tecnologia empregada.

Entretanto, apenas esse envio não basta, pois uma sequência aleatória de bits enviada de um computador A para um computador B não transmite uma informação compreensível. Por isso, foram criados os **protocolos**, que definem contratos e regras de comunicação.

Um exemplo simples seria:

```text
Velocidade: transmitir 9.600 bits por segundo.
Tamanho: representar cada caractere com 8 bits.
Início: usar um bit especial para marcar o início.
Fim: usar um bit especial para marcar o fim.
Erros: definir como o receptor informa uma falha.
```

## Comunicação serial e rede local

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

Uma forma de interligar esses dispositivos é criar uma rede local, ou LAN (*local area network*). Nas primeiras redes Ethernet, os computadores podiam compartilhar um mesmo meio físico, como um cabo em barramento:

```text
A <-> B <-> C <-> D
```

Entretanto, todos os computadores participavam da comunicação, mesmo quando a mensagem não era destinada a eles. No exemplo acima, se A enviasse uma mensagem para D, tanto B quanto C receberiam o sinal durante a transmissão.

Se dois computadores transmitissem ao mesmo tempo nesse meio compartilhado, poderia ocorrer uma colisão. O mecanismo **CSMA/CD** permitia detectar a colisão, interromper a transmissão, aguardar um intervalo e tentar novamente. Redes Ethernet modernas, com *switches* e enlaces *full-duplex*, não usam CSMA/CD.

Um ***switch*** recebe quadros Ethernet e decide por quais portas encaminhá-los. Essa decisão usa os **endereços MAC** (*media access control*) de origem e destino presentes no quadro.

O *switch* aprende a associação entre endereços MAC de origem e portas. Para verificar a integridade, um quadro Ethernet possui um **FCS** (*frame check sequence*), calculado com CRC. Se o valor calculado pelo receptor for diferente do recebido, o quadro é descartado.

Os **switches** resolvem o problema da comunicação local, mas não o da comunicação com computadores que estão em outras redes. Para isso, existem os **roteadores**, responsáveis pela comunicação entre redes.

- ***Switch***: conecta dispositivos em uma rede local e encaminha quadros na camada de enlace.
- **Roteador**: interliga redes e encaminha pacotes na camada de rede.

O principal identificador da interface de rede na camada de enlace é o **endereço MAC**, enquanto o principal identificador lógico na camada de rede é o **endereço IP**.

Em uma rede IP, o identificador lógico é o **endereço IP**, que permite a comunicação entre redes e a criação de uma organização lógica. Por exemplo, uma empresa pode ter várias redes separadas por setores, como RH e vendas.

```text
192.168.1.11
192.168.1.12
192.168.1.13
```

Percebe-se que há certa lógica entre os IPs acima, mas o IP `10.0.0.10` dificilmente faz parte da mesma rede.

Um endereço IP sem o tamanho do prefixo não informa quais bits identificam a rede e quais identificam a interface dentro dela. No IPv4, podemos representar esse limite com uma **máscara de rede**. Exemplo:

```text
IP: 192.168.1.10
Máscara de rede: 255.255.255.0
```

Essa rede também pode ser escrita assim: `192.168.1.0/24`, indicando que os primeiros 24 bits identificam a rede e os bits restantes identificam o host. Um endereço IPv4 é formado por quatro grupos com valores de 0 a 255, totalizando 8 bits por grupo.

A rede `192.168.1.0/24` abrange os endereços de `192.168.1.0` a `192.168.1.255`. Em uma sub-rede IPv4 tradicional desse tipo, o primeiro identifica a rede e o último é o endereço de *broadcast*. Assim, os endereços normalmente atribuíveis a interfaces vão de `192.168.1.1` a `192.168.1.254`. Prefixos especiais, como `/31` em enlaces ponto a ponto, seguem regras diferentes.

## Rede remota, IP interno e IP externo

Suponhamos que nosso IP seja `192.168.1.10` e queiramos enviar dados para o IP `192.168.1.20`. Esse computador está na mesma rede que o nosso; portanto, podemos enviar os dados diretamente ao endereço MAC correspondente. Agora, suponhamos que queiramos enviar algo para um computador que não está na nossa rede, como o IP `8.8.8.8`.

Para isso, usamos uma rota que normalmente aponta para um *gateway*, como o roteador em `192.168.1.1`. O roteador encaminha o pacote por outra interface e por redes intermediárias até o destino. Um endereço como `203.0.113.25`, reservado para documentação, pode representar o endereço externo nos exemplos.

Quando um IP está fora da nossa rede local, o tráfego é encaminhado ao roteador, que usa sua interface WAN (_Wide Area Network_) para se comunicar com a rede externa.

Se o destino estiver na mesma rede, não precisamos usar o **gateway**. O dispositivo de origem usa o protocolo **ARP** (_Address Resolution Protocol_) para descobrir o endereço MAC associado ao IP de destino. Para visualizar a tabela ARP:

```bash
arp -a
```

Os blocos de endereços IPv4 privados definidos para redes internas são:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Ao acessar a Internet, um roteador doméstico normalmente troca o endereço IP e a porta de origem por seu endereço externo e outra porta. Essa combinação de tradução de endereços e portas é frequentemente chamada de **NAT/PAT**. Exemplo:

```text
Computador A -> 192.168.1.10:50100 torna-se 203.0.113.25:60001.
```

Quando a resposta chega, o roteador consulta a tradução registrada e a encaminha ao dispositivo e à porta de origem corretos.

Nosso roteador geralmente precisa conhecer apenas algumas rotas, não todas:

- A rota da rede local.
- Rotas para outras redes diretamente conectadas, quando houver.
- Uma rota padrão, geralmente apontando para o provedor.

Na Internet, **sistemas autônomos** (AS) anunciam prefixos IP e trocam informações de alcançabilidade, principalmente por meio do BGP.

## Broadcast

É uma forma de enviar uma mensagem a todos os dispositivos do mesmo domínio de broadcast. É usado com frequência na descoberta de dispositivos, principalmente com **ARP** e **DHCP**. Em uma rede Ethernet, um quadro destinado a `FF:FF:FF:FF:FF:FF` é distribuído pelo switch a todas as portas relevantes. Esse processo é chamado de **flooding**. No caso do ARP, ele permite descobrir o endereço MAC associado a um IP e salvar essa associação na tabela ARP.
