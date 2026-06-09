# Topologia-de-infraestrutura-de-rede-escolar
O projeto simula a infraestrutura de rede completa de uma escola, atendendo quatro perfis distintos de dispositivos com isolamento, controle de acesso e serviços centralizados.

O que foi implementado:

✅ Segmentação por VLAN para cada perfil de usuário
✅ Roteamento inter-VLAN via Switch Layer 3
✅ DHCP centralizado com relay entre VLANs
✅ DNS interno com domínio escola.local
✅ Servidor web para portal da instituição
✅ Acesso à internet com NAT/PAT
✅ Wi-Fi WPA2 para alunos e rede aberta para visitantes
✅ Visitantes isolados — acesso exclusivamente via Wi-Fi

🖧 Topologia da rede:

🔒 VLANs e Segmentação:
A rede é dividida em quatro VLANs com tráfego isolado em camada 2:

| VLAN	| Nome	| Rede	| Perfil	| Acesso |
| :--- | :---: | :---: | :---: |
| 10	| ALUNOS	| 10.0.10.0/24	A| lunos	| Cabeado + Wi-Fi |
| 20	| ADMINISTRATIVO	| 10.0.20.0/24	| Secretaria / Direção	| Somente cabeado |
| 30	| VISITANTES	| 10.0.30.0/24	| Visitantes	| Somente Wi-Fi |
| 40	| SERVIDORES	| 10.0.40.0/24	| Infraestrutura	| Interno |

OBS: Por que separar em VLANs? Cada perfil opera em um domínio de broadcast independente. Um aluno não enxerga o tráfego da administração, e um visitante não tem visibilidade de nenhuma rede interna além do próprio gateway.


