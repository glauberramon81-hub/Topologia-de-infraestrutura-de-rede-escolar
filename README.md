# 🏫 Rede Escolar — Cisco Packet Tracer

Simulação de infraestrutura de rede para instituição de ensino, desenvolvida como atividade extensionista. O projeto modela um ambiente real com segmentação por VLANs, roteamento inter-VLAN, DHCP centralizado, DNS interno, acesso Wi-Fi segmentado e saída para internet via NAT/PAT.

---

## 🔍 Visão Geral

Projeto da infraestrutura de rede de uma escola, contemplando:

- Separação lógica de usuários por VLANs (Alunos, Administrativo, Visitantes e Servidores)
- Roteamento inter-VLAN via Switch Core Layer 3 (Cisco 3560-24PS)
- Saída para internet com NAT/PAT no roteador de borda (Cisco 2911)
- Serviços centralizados de DHCP, DNS e Web em um servidor dedicado
- Acesso wireless segmentado por perfil de usuário
- Roteamento estático entre o Gateway e o ISP simulado

---

## 🗺️ Topologia da Rede
(Projeto/Topologia.PNG)
## 🖥️ Dispositivos

### Roteadores

- GATEWAY PADRÃO
  - Modelo: Cisco 2911
  - Função: Roteador de borda, NAT/PAT, gateway da rede interna
  - Interface WAN: GigabitEthernet0/0 — `200.0.1.2/30`
  - Interface LAN: GigabitEthernet0/1 — `10.0.0.1/30`

- **PROVEDOR INTERNET**
  - Modelo: Cisco 2911
  - Função: Simulação do ISP (Internet Service Provider)
  - Interface: GigabitEthernet0/0 — `200.0.1.1/30`

### Switches

- **SWITCH CORE**
  - Modelo: Cisco 3560-24PS
  - Função: Roteamento inter-VLAN Layer 3, SVIs para cada VLAN
  - Uplink para Gateway: GigabitEthernet0/1 — `10.0.0.2/30`

- **SWITCH ACESSO 1**
  - Modelo: Cisco 2960-24TT
  - Função: Acesso para PCs Administrativos (VLAN 20) e AP Visitantes (VLAN 30)

- **SWITCH ACESSO 2**
  - Modelo: Cisco 2960-24TT
  - Função: Acesso para PCs Alunos (VLAN 10) e AP Alunos (VLAN 10)

### Servidor

- **SERVIDOR**
  - Modelo: Server-PT
  - Função: DHCP, DNS e servidor Web
  - IP estático: `10.0.40.2/24`
  - Gateway: `10.0.40.1`
  - VLAN: 40 — SERVIDORES

### Pontos de Acesso Wireless

- **AP ALUNOS**
  - Modelo: Linksys WRT300N
  - SSID: `EscolaAlunos`
  - Segurança: WPA2-PSK
  - VLAN associada: 10

- **AP VISITANTES**
  - Modelo: Linksys WRT300N
  - SSID: `EscolaVisitantes`
  - Segurança: Rede aberta (sem senha)
  - VLAN associada: 30

### Hosts

- **PC ADMINISTRAÇÃO 1 e 2**
  - Modelo: PC-PT
  - VLAN: 20 — ADMINISTRATIVO
  - Endereço: via DHCP

- **PC ALUNO 1 e 2**
  - Modelo: PC-PT
  - VLAN: 10 — ALUNOS (cabeado)
  - Endereço: via DHCP

- **NOTEBOOK ALUNO**
  - Modelo: Laptop-PT (wireless)
  - VLAN: 10 — ALUNOS (Wi-Fi)
  - Endereço: via DHCP

- **SMARTPHONE VISITANTE**
  - Modelo: Smartphone-PT
  - VLAN: 30 — VISITANTES (Wi-Fi)
  - Endereço: via DHCP

---

## 🌐 VLANs e Endereçamento

### VLAN 10 — ALUNOS

- Rede: `10.0.10.0/24`
- Gateway (SVI): `10.0.10.1`
- Tipo de acesso: cabeado e Wi-Fi
- Dispositivos: PC Aluno 1, PC Aluno 2, Notebook Aluno

### VLAN 20 — ADMINISTRATIVO

- Rede: `10.0.20.0/24`
- Gateway (SVI): `10.0.20.1`
- Tipo de acesso: somente cabeado
- Dispositivos: PC Administração 1, PC Administração 2

### VLAN 30 — VISITANTES

- Rede: `10.0.30.0/24`
- Gateway (SVI): `10.0.30.1`
- Tipo de acesso: somente Wi-Fi
- Dispositivos: Smartphone Visitante

### VLAN 40 — SERVIDORES

- Rede: `10.0.40.0/24`
- Gateway (SVI): `10.0.40.1`
- Tipo de acesso: interno (sem acesso direto de usuários finais)
- Dispositivos: Servidor

### Endereços Estáticos

```
PROVEDOR INTERNET      200.0.1.1/30
GATEWAY WAN            200.0.1.2/30
GATEWAY LAN            10.0.0.1/30
SWITCH CORE Gi0/1      10.0.0.2/30
SVI VLAN 10            10.0.10.1/24
SVI VLAN 20            10.0.20.1/24
SVI VLAN 30            10.0.30.1/24
SVI VLAN 40            10.0.40.1/24
SERVIDOR               10.0.40.2/24
```

---

## 📡 DHCP

O serviço DHCP é centralizado no **SERVIDOR (10.0.40.2)**. O Switch Core utiliza `ip helper-address` em cada SVI para redirecionar as requisições DHCP dos clientes ao servidor.

### Pool — Alunos Cabeado (VLAN 10)

```
Network:      10.0.10.0/24
Range:        10.0.10.10 — 10.0.10.59
Hosts:        50
Default GW:   10.0.10.1
DNS:          10.0.40.2
```

### Pool — Alunos Wi-Fi (VLAN 10)

```
Network:      10.0.10.0/24
Range:        10.0.10.100 — 10.0.10.149
Hosts:        50
Default GW:   10.0.10.1
DNS:          10.0.40.2
```

### Pool — Administrativo (VLAN 20)

```
Network:      10.0.20.0/24
Range:        10.0.20.10 — 10.0.20.29
Hosts:        20
Default GW:   10.0.20.1
DNS:          10.0.40.2
```

### Pool — Visitantes Wi-Fi (VLAN 30)

```
Network:      10.0.30.0/24
Range:        10.0.30.100 — 10.0.30.129
Hosts:        30
Default GW:   10.0.30.1
DNS:          10.0.40.2
```

### Configuração do ip helper-address (Switch Core)

```
interface Vlan10
 ip helper-address 10.0.40.2

interface Vlan20
 ip helper-address 10.0.40.2

interface Vlan30
 ip helper-address 10.0.40.2
```

---

## 📶 Wi-Fi

### AP ALUNOS — EscolaAlunos

- SSID: `EscolaAlunos`
- Segurança: WPA2-PSK
- Senha: `escola2024`
- VLAN: 10
- Clientes: Notebook Aluno e demais dispositivos wireless de alunos

### AP VISITANTES — EscolaVisitantes

- SSID: `EscolaVisitantes`
- Segurança: rede aberta (sem autenticação)
- VLAN: 30
- Clientes: Smartphone Visitante e demais dispositivos de visitantes

---

## 🔤 DNS Interno

O servidor DNS responde às seguintes entradas para a zona `escola.local`:

```
escola.local        →  10.0.40.2
www.escola.local    →  10.0.40.2
portal.escola.local →  10.0.40.2
```

Todos os clientes recebem `10.0.40.2` como DNS server via DHCP, permitindo a resolução dos nomes internos diretamente pelo servidor da escola.

---

## 📚 Conceitos Aplicados

- **VLANs 802.1Q** — segmentação lógica da rede em quatro domínios distintos
- **Trunk e Access Ports** — configuração de portas de tronco entre switches e portas de acesso para hosts finais
- **SVI (Switched Virtual Interface)** — interfaces virtuais no Switch Core para roteamento inter-VLAN Layer 3
- **DHCP Relay (ip helper-address)** — redirecionamento de broadcasts DHCP de cada VLAN ao servidor centralizado
- **NAT/PAT overload** — tradução de endereços no Gateway para permitir saída à internet com um único IP público
- **Roteamento Estático** — rota padrão no Gateway apontando para o ISP e rota de retorno no ISP
- **WPA2-PSK** — segurança da rede Wi-Fi dos alunos com chave pré-compartilhada
- **DNS** — resolução de nomes internos para o servidor da escola

---

## 🧪 Alguns dos testes realizados

### 1. Verificar configuração de IP nos hosts
### 2. Testar conectividade básica (ping)
### 3. Testar acesso Web
### 4. Verificar traduções NAT
### 5. Verificar VLANs no Switch Core
### 6. Verificar tabela de roteamento

