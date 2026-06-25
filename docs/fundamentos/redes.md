# Fundamentos de Rede

Uma rede de computadores nada mais é do que vários computadores conectados entre si através de cabos físicos ou wi-fi.

Toda comunicação é binária, ou seja, o computador envia bits convertidos em pulsos elétricos, sendo que 0 é para 0V (baixa tensão) e 1 é para 5V (alta tensão).

Entretanto, só esse envio não basta, pois o envio aleatório de bits entre um computador A para um computador B não quer dizer nada, por isso, foram criados os **protocolos** que definem contratos/regras de comunicação

Um exemplo simples seria:

```text
Velocidade: transmitir 9600 bits por segundo
Tamanho: cada caractere vai ter 8 bits
Início: um bit especial marca o início
Fim: um bit especial marca o fim
Se houver erros, o receptor avisa
```

## Comunicação Serial e Rede Local

A comunicação serial é uma comunicação física em que um computador A envia os bits em uma ordem e o computador B recebe os mesmos bits nessa mesma ordem, exemplo:

```text
A envia 01001111 e B recebe 01001111
```

Isso gera um problema quando temos vários computadores, pois nesse caso, cada computador precisaria estar comunicado entre si, exemplo, 3 computadores A, B e C:

```text
A <-> B
A <-> C
B <-> C
```

Dessa forma, surgiu a ideia de criar uma rede local, chamada de LAN (Local Area Network). Dessa forma, surgiu o Ethernet em que todos os computadores estavam conectados através de um único cabo:

```text
A <-> B <-> C <-> D
```

Entretanto, todos os computadores participavam da comunicação, mesmo que não fosse enviado nada para ele, pois no exemplo acima, se A enviar uma mensagem para D, tanto B quanto C participam dessa comunicação.

Além disso, se dois computadores enviarem mensagem ao mesmo tempo, teria um problema na rede. Dessa forma, foi criado um algoritmo chamado **CSMA/CD** que basicamente cancelava a transmissão, aguardava um certo tempo e tentava novamente. Exemplo simples: "imagine várias pessoas numa conversa e 2 começam a falar ao mesmo tempo, elas parariam de falar, esperariam um certo tempo e tentariam voltar a conversar"

Depois, surgiu o **switch**, um aparelho que fica responsável por receber uma mensagem e repassar para o computador de origem. Essa identificação é feita pelo **MAC Address** (Media Access Control). Quando um computador A quer falar com um computador B, ele envia tanto seu endereço MAC e o endereço MAC do destino. Esse envio é chamado de **Frame Ethernet**

O switch sabe todos os computadores conectados nele, dessa forma, ele consegue obter o endereço MAC de cada um e distribuir as conexões. Para que se tenha integridade da mensagem, ou seja, que não houve falha no recebimento, usa-se um verificador **FCS (Frame Check Sequence)**, um código de verificação de erro adicionado ao final de um quadro (frame) Ethernet e se o FCS calculado pelo receptor não for igual ao enviado, o quadro é descartado.

Os **switches** resolvem o problema local, mas não quando precisamos nos comunicar com computadores que não estão no mesmo local. Dessa forma surgiram os **roteadores**, responsáveis pela comunicação entre redes.

- Switch: responsável por conectar dois ou mais computadores
- Roteador: responsável por conectar duas ou mais redes

O principal identificador físico é o **MAC Address**, já o principal identificador lógico é o **IP**

Em uma rede Internet, o identificador lógico é o **IP**, responsável por resolver a comunicação entre redes e permitir criar uma organização lógica. Exemplo, uma empresa pode ter várias redes separadas por setores (RH, Vendas, etc)

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

Essa máscara de rede também pode ser escrita assim: 192.168.1.0/24, representando que os primeiros 24 bits identificam a rede e o restante dos bits identifica o host. Lembrando que o IP é formado por quatro grupos de 0 a 255 (totalizando 8 bits por grupo)

Logo, a rede de IP 192.168.1.0/24 permite os hosts de 192.168.1.0 até 192.168.1.255, mas geralmente os IPs terminados em 0 e 255 são reservados para identificar a rede e o broadcast respectivamente.

## Rede Remota, IP Interno e IP Externo

Suponhamos que nosso IP seja 192.168.1.10 e queremos enviar dados para o IP 192.168.1.20, esse computador está na mesma rede que a gente, dessa forma podemos enviar dados usando apenas o MAC Address do computador de IP 192.168.1.20. Entretanto, vamos supor que queremos enviar algo para um computador que não está na nossa rede, por exemplo, IP 8.8.8.8.

Para isso, precisamos usar um Gateway (geralmente nosso roteador). O gateway é a "porta de saída" da nossa rede, e geralmente fica no host 1, exemplo: 192.168.1.1. Então nesse caso, nosso roteador tem o IP Interno 192.168.1.1 e através de um IP Externo (exemplo: 200.100.50.25), faz a comunicação com o IP 8.8.8.8

Quando um IP está fora da nossa rede local usamos a interface WAN do roteador (Wide Area Network), ou seja, IP Externo

Se o destino é na nossa mesma rede, não precisamos usar o **Gateway**, basta usar o switch que usa o protocolo **ARP** (Address Resolution Protocol) para identificar os computadores conectados, para visualizar:

```bash
arp -a
```

Geralmente os IPs usados dentro de redes locais são os seguintes:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Quando vamos acessar a Internet Externa, nosso roteador troca o IP da rede local de quem chamou para o IP Externo dele através de uma porta e faz a requisição, o protocolo que faz isso é o **NAT** (Network Address Translation). Exemplo:

```text
Computador A -> 192.168.1.10:50100 vira 200.100.50.25:60001
```

Quando chega a resposta, ele só passa pra porta que o chamou a resposta recebida

Nosso roteador geralmente só precisa saber de algumas rotas, não precisa saber todas:

- Minha LAN
- Meu provedor
- Rota padrão (geralmente do provedor)

As demais rotas/redes fica para os **Sistemas Autônomos** (AS), empresas grandes que geralmente possuem uma vasta lista de endereços IPs que sabem onde estão

## Broadcast

É uma forma de enviar mensagem para todos os dispositivos conectados na mesma rede local. Usado com frequência para descoberta de dispositivos principalmente com **ARP** e **DHCP**. Geralmente enviamos pro switch FF:FF:FF:FF:FF:FF e ele distribui a mensagem para todos os dispositivos que estão conectados nele. Isso se chama **flooding** e é útil para descobrir os endereços e mapear os endereços MAC deles e o IP e salva na tabela ARP
