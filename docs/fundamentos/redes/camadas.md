# Camadas de Rede

## Modelo OSI

É um modelo conceitual muito usado em estudos, que separa a comunicação em camadas bem definidas. Na prática, algumas dessas responsabilidades podem se sobrepor. As camadas são:

1. Física
2. Enlace
3. Rede
4. Transporte
5. Sessão
6. Apresentação
7. Aplicação

### Camada 1: Física

> "Como os bits são transmitidos fisicamente?"

- Cabo de rede
- Fibra ótica
- Wi-Fi
- Conectores
- Sinais elétricos
- Sinais de luz
- Ondas de rádio

Exemplo: 0101010101 virando sinais elétricos.

Comandos úteis:

```bash
# Listar/gerenciar interfaces de rede
ip link

# Consultar/monitorar/alterar configurações de hardware e driver de
# dispositivos de rede
ethtool device
```

### Camada 2: Enlace

> "Como entregar dados entre dispositivos no mesmo segmento local?"

A camada de enlace organiza a comunicação entre dispositivos no mesmo segmento de rede local por meio de **endereços MAC**.

- Ethernet
- Endereço MAC
- Switch
- Frame Ethernet
- ARP
- Broadcast
- VLAN

Quando queremos enviar dados para outro dispositivo na mesma rede IPv4, precisamos descobrir o endereço MAC dele por meio do ARP.

Comandos úteis:

```bash
# Lista os dispositivos conectados na mesma rede local
ip neigh
arp -a

# Exibir o estado e propriedades das interfaces de rede que estão
# conectadas a uma ponte (bridge) configurada no sistema
bridge link show
```

### Camada 3: Rede

> "Para qual rede esse pacote precisa ir?"

É nessa camada que entra o IP.

- IPv4
- IPv6
- Máscara
- Roteador
- Gateway
- Tabela de roteamento

```text
Seu IP: 192.168.22.40
Máscara: /24
Sua rede: 192.168.22.0/24
Gateway: 192.168.22.1
IP Público: 186.195.35.40
```

Para qualquer destino que não seja minha rede local, envie para o gateway.

Comandos úteis:

```bash
# Verificar/adicionar/alterar/remover endereços IP e configurações
# de interface de rede
ip addr show

# Visualizar e modificar a tabela de roteamento (mapa de caminhos)
ip route

# Testar conectividade entre seu dispositivo e um outro dispositivo
ping ip

# Visualizar o caminho percorrido até chegar ao IP de destino
traceroute ip
```

### Camada 4: Transporte

> "Qual aplicação dentro da máquina deve receber esses dados?"

Conecta programas, não apenas máquinas.

- TCP
- UDP
- Portas
- Conexões
- Sockets

- O **IP** identifica o dispositivo
- A **porta** identifica o serviço

Exemplo: `192.168.22.20:8000`.

Comandos úteis:

```bash
# Visualizar portas que estão sendo usadas
ss -tulpen

# Acessar um serviço numa porta específica
curl http://192.168.22.40:3000
```

### Camada 5: Sessão

> "Como manter uma sessão de comunicação aberta e organizada?"

Controla conversas e sessões. Geralmente, suas responsabilidades são incorporadas a outras camadas.

### Camada 6: Apresentação

> "Como os dados são representados, codificados e protegidos?"

Cuida do formato, da codificação e da criptografia dos dados.

- Criptografia
- TLS/SSL
- Codificação
- Compressão
- JSON
- XML
- UTF-8
- Serialização

Exemplo: `https://exemplo.com.br` usa `HTTPS`, que utiliza `TLS` para criptografar os dados.

### Camada 7: Aplicação

> "Qual protocolo de aplicação está sendo usado?"

- HTTP
- HTTPS
- DNS
- SMTP
- SSH
- gRPC
- WebSocket

Cuida dos protocolos e dos dados da aplicação.

### Fluxo conceitual

- Camada 7: `HTTP` monta a requisição
- Camada 4: `TCP` adiciona as portas de origem e de destino
- Camada 3: `IP` adiciona os endereços IP de origem e de destino
- Camada 2: `Ethernet` adiciona os endereços MAC de origem e de destino
- Camada 1: os bits são transformados em sinais no cabo ou na rede sem fio

## Modelo TCP/IP

É um modelo mais simples que o **OSI** e agrupa algumas de suas camadas:

1. **Acesso à rede**: camadas 1 e 2 do OSI
2. **Internet**: camada 3 do OSI
3. **Transporte**: camada 4 do OSI
4. **Aplicação**: camadas 5, 6 e 7 do OSI
