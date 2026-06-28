# Camadas de rede

## Modelo OSI

É um modelo conceitual muito usado em estudos, que separa a comunicação em camadas bem definidas. Na prática, algumas dessas responsabilidades podem se sobrepor. As camadas são:

1. Física.
2. Enlace.
3. Rede.
4. Transporte.
5. Sessão.
6. Apresentação.
7. Aplicação.

### Camada 1: física

> "Como os bits são transmitidos fisicamente?"

- Cabos de rede.
- Fibra óptica.
- Camada física do Wi-Fi.
- Conectores.
- Sinais elétricos.
- Pulsos de luz.
- Ondas de rádio.

Exemplo: 0101010101 virando sinais elétricos.

Comandos úteis:

```bash
# Lista ou gerencia interfaces de rede.
ip link

# Consulta, monitora ou altera configurações de hardware e drivers de
# dispositivos de rede.
ethtool device
```

### Camada 2: enlace

> "Como entregar dados entre dispositivos no mesmo segmento local?"

A camada de enlace organiza a comunicação entre dispositivos no mesmo segmento de rede local por meio de **endereços MAC**.

- Ethernet.
- Endereços MAC.
- *Switches*.
- Quadros Ethernet.
- ARP no IPv4.
- *Broadcast*.
- VLANs.

Quando queremos enviar dados para outro dispositivo na mesma rede IPv4, precisamos descobrir o endereço MAC dele por meio do ARP.

Comandos úteis:

```bash
# Lista os vizinhos conhecidos na rede local.
ip neigh

# Exibe a tabela ARP com uma ferramenta legada.
arp -a

# Exibe o estado e as propriedades das interfaces associadas a uma
# ponte configurada no sistema.
bridge link show
```

### Camada 3: rede

> "Para qual rede esse pacote precisa ir?"

É nessa camada que entra o IP.

- IPv4.
- IPv6.
- Prefixos e máscaras.
- Roteadores.
- *Gateways*.
- Tabelas de roteamento.

```text
Seu IP: 192.168.22.40
Máscara: /24
Sua rede: 192.168.22.0/24
Gateway: 192.168.22.1
IP público de exemplo: 203.0.113.40
```

Quando nenhuma rota mais específica corresponde ao destino, a rota padrão normalmente encaminha o pacote a um *gateway*.

Comandos úteis:

```bash
# Verifica, adiciona, altera ou remove endereços IP e configurações
# de interfaces de rede.
ip addr show

# Visualiza ou modifica a tabela de roteamento.
ip route

# Testa a conectividade com outro dispositivo usando ICMP.
ping ip

# Estima o caminho percorrido até o destino.
traceroute ip
```

### Camada 4: transporte

> "Qual aplicação dentro da máquina deve receber esses dados?"

Conecta programas, não apenas máquinas.

- TCP.
- UDP.
- Portas.
- Conexões.
- *Sockets*.

- O **endereço IP** identifica uma interface de rede.
- A **porta** ajuda a identificar a aplicação ou o serviço de destino.

Exemplo: `192.168.22.20:8000`.

Comandos úteis:

```bash
# Visualiza *sockets* e portas em uso.
ss -tulpen

# Acessa um serviço em uma porta específica.
curl http://192.168.22.40:3000
```

### Camada 5: sessão

> "Como manter uma sessão de comunicação aberta e organizada?"

Controla conversas e sessões. Geralmente, suas responsabilidades são incorporadas a outras camadas.

### Camada 6: apresentação

> "Como os dados são representados, codificados e protegidos?"

Cuida do formato, da codificação e da criptografia dos dados.

- Criptografia.
- TLS.
- Codificação.
- Compressão.
- JSON.
- XML.
- UTF-8.
- Serialização.

Exemplo: `https://exemplo.com.br` usa `HTTPS`, que utiliza `TLS` para criptografar os dados.

### Camada 7: aplicação

> "Qual protocolo de aplicação está sendo usado?"

- HTTP.
- HTTPS.
- DNS.
- SMTP.
- SSH.
- gRPC.
- WebSocket.

Cuida dos protocolos e dos dados da aplicação.

### Fluxo conceitual

- **Camada 7**: HTTP representa a requisição da aplicação.
- **Camada 4**: TCP usa portas de origem e destino.
- **Camada 3**: IP usa endereços IP de origem e destino.
- **Camada 2**: Ethernet usa endereços MAC de origem e destino no enlace local.
- **Camada 1**: os bits são representados por sinais no meio físico.

## Modelo TCP/IP

É um modelo mais simples que o **OSI** e agrupa algumas de suas camadas:

1. **Acesso à rede**: camadas 1 e 2 do OSI.
2. **Internet**: camada 3 do OSI.
3. **Transporte**: camada 4 do OSI.
4. **Aplicação**: camadas 5, 6 e 7 do OSI.
