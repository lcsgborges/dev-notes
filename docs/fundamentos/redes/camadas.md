# Camadas de Rede

## Modelo OSI

É um modelo conceitual muito usado para estudos que separa as camadas em etapas muito bem definidas, mas normalmente essas camadas se misturam, são elas:

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
- Wi-fi
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

A camada de Enlace organiza a comunicação entre dispositivos na mesma rede local através do **MAC Address**

- Ethernet
- Mac Address
- Switch
- Frame Ethernet
- ARP
- Broadcast
- VLAN

Quando queremos enviar dados para outro dispositivo, precisamos descobrir o MAC Address dele através da tabela ARP.

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

É nessa camda que entra o IP.

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

# Visualizar o caminho feito até chegar no ip destino
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

Exemplo `192.168.22.20:8000`

Comandos úteis:

```bash
# Visualizar portas que estão sendo usadas
ss -tulpen

# Acessar um serviço numa porta específica
curl http://192.168.22.40:3000
```

### Camada 5: Sessão

> "Como manter uma sessão de comunicação aberta e organizada?"

É um controle de conversas/sessões. Geralmente é misturada com outras camadas.

### Camada 6: Apresentação

> "Como os dados são representados, codificados e protegidos?"

Cuida de formato, codificação e criptografia.

- Criptografia
- TLS/SSL
- Codificação
- Compressão
- JSON
- XML
- UTF-8
- Serialização

Exemplo: O `https://exemplo.com.br` possui `HTTPS` que usa `TLS` para criptografar os dados.

### Camada 7: Aplicação

> "Qual protocolo de aplicação está sendo usado?"

- HTTP
- HTTPS
- DNS
- SMTP
- SSH
- gRPC
- WebSocket

Cuida da parte de protocolos e dados da aplicação

### Fluxo conceitual

- Camada 7: `HTTP` monta a requisição
- Camada 4: `TCP` coloca porta origem e porta destino
- Camada 3: `IP` coloca IP origem e IP destino
- Camada 2: `Ethernet` coloca MAC origem e MAC destino
- Camada 1: bits viram sinais no cabo/wi-fi

## Modelo TCP/IP

É um modelo baseado no **OSI**, mas é mais simples e agrupa algumas camadas

1. **Acesso à rede**: camadas 1 e 2 do OSI
2. **Internet**: camada 3 do OSI
3. **Transporte**: camada 4 do OSI
4. **Aplicação**: camadas 5, 6 e 7 do OSI
