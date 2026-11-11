# Atividade Prática / Avaliação 07
Configuração de VLANs tipo Trunk em uma rede hierárquica
1. Objetivo
Configurar enlaces do tipo trunk nas portas que interligam os switches das camadas de Acesso, Distribuição e Núcleo (Core), permitindo o transporte das VLANs 10 e 20 por um mesmo enlace físico.

A atividade dá continuidade à Avaliação 06, na qual foram criadas as seguintes VLANs:

| ID da VLAN | Nome | Dispositivos associados |
|---|---|---|
| 10 | ALUNOS | Computadores (PCs) |
| 20 | VISITANTES | Notebooks |

2. Topologia utilizada

<img width="446" height="342" alt="image" src="https://github.com/user-attachments/assets/8039ba65-5db3-42a9-9bbb-56510a60fd79" />

A topologia também possui o roteador Router3 e um servidor. As portas conectadas aos computadores e notebooks permanecem no modo access. O modo trunk é utilizado nas portas que interligam switches.

3. Fundamentação: trunk e IEEE 802.1Q

Uma porta trunk permite transportar tráfego de várias VLANs por um único enlace físico entre equipamentos de rede. O padrão IEEE 802.1Q identifica a VLAN de cada quadro Ethernet por meio de uma etiqueta (tag), permitindo que o switch de destino reconheça a qual VLAN o quadro pertence.

| Conceito | Descrição |
|---|---|
| VLAN | Segmentação lógica da rede em grupos separados. |
| Porta access | Porta normalmente utilizada para conectar um dispositivo final e associada a uma VLAN. |
| Porta trunk | Porta que transporta tráfego de uma ou mais VLANs entre equipamentos de rede. |
| IEEE 802.1Q | Padrão que identifica a VLAN por meio de uma etiqueta no quadro Ethernet. |
| VLAN nativa | VLAN cujo tráfego é transmitido sem etiqueta em um enlace 802.1Q. |


As duas extremidades de um mesmo enlace devem apresentar configurações compatíveis. Uma diferença na VLAN nativa pode gerar o aviso NATIVE VLAN MISMATCH.

4. Mapeamento dos enlaces entre switches

O mapeamento foi realizado com show cdp neighbors, que identifica os equipamentos vizinhos e as interfaces utilizadas nas conexões.

4.1 Camada Core

| Switch de origem | Porta local | Switch conectado | Porta remota |
|---|---|---|---|
| SW_CORE0 | Gi1/0/2 | SW_CORE1 | Gi1/0/2 |
| SW_CORE0 | Gi1/0/3 | SW_CORE1 | Gi1/0/3 |
| SW_CORE0 | Gi1/0/4 | SW_CORE1 | Gi1/0/4 |
| SW_CORE0 | Gi1/0/5 | SW_CORE1 | Gi1/0/5 |
| SW_CORE0 | Gi1/1/1 | SW_DISTRIBUICAO0 | Gi1/1/1 |
| SW_CORE0 | Gi1/1/2 | SW_DISTRIBUICAO0 | Gi1/1/2 |
| SW_CORE1 | Gi1/1/1 | SW_DISTRIBUICAO1 | Gi1/1/1 |
| SW_CORE1 | Gi1/1/2 | SW_DISTRIBUICAO1 | Gi1/1/2 |

As quatro conexões entre SW_CORE0 e SW_CORE1 também devem operar em trunk. As portas Gi1/0/1 dos switches Core, identificadas como conexões com o roteador, não fazem parte da configuração de trunk entre switches descrita aqui.

4.2 Distribuição e Acesso

| Switch de distribuição | Porta local | Switch de acesso | Porta remota |
|---|---|---|---|
| SW_DISTRIBUICAO0 | Gi1/0/1 | SW_ACESSO_1 | Fa0/4 |
| SW_DISTRIBUICAO0 | Gi1/0/2 | SW_ACESSO_0 | Fa0/4 |
| SW_DISTRIBUICAO1 | Gi1/0/1 | SW_ACESSO_2 | Fa0/4 |
| SW_DISTRIBUICAO1 | Gi1/0/2 | SW_ACESSO_3 | Fa0/1 |

As duas extremidades de cada enlace devem ser configuradas como trunk. Por exemplo, a conexão entre SW_DISTRIBUICAO1 e SW_ACESSO_3 utiliza Gi1/0/2 na distribuição e Fa0/1 no acesso.

5. Comandos utilizados


Os comandos abaixo devem ser executados em cada switch, utilizando as interfaces identificadas no mapeamento.

5.1 Comandos básicos

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface <interface>` | Selecionar a porta. |
| 4 | `switchport mode trunk` | Definir a porta como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir as VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar a interface. |
| 7 | `end` | Sair da configuração. |

5.2 SW_CORE0

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface range gigabitEthernet 1/0/2-5` | Selecionar as portas que interligam o SW_CORE0 ao SW_CORE1. |
| 4 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar as interfaces. |
| 7 | `exit` | Sair da configuração da faixa de portas. |
| 8 | `interface range gigabitEthernet 1/1/1-2` | Selecionar as portas que interligam o SW_CORE0 ao SW_DISTRIBUICAO0. |
| 9 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 10 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 11 | `no shutdown` | Habilitar as interfaces. |
| 12 | `end` | Finalizar a configuração. |


5.3 SW_CORE1

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface range gigabitEthernet 1/0/2-5` | Selecionar as portas que interligam o SW_CORE1 ao SW_CORE0. |
| 4 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar as interfaces. |
| 7 | `exit` | Sair da configuração da faixa de portas. |
| 8 | `interface range gigabitEthernet 1/1/1-2` | Selecionar as portas que interligam o SW_CORE1 ao SW_DISTRIBUICAO1. |
| 9 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 10 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 11 | `no shutdown` | Habilitar as interfaces. |
| 12 | `end` | Finalizar a configuração. |

5.4 SW_DISTRIBUICAO0

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface range gigabitEthernet 1/1/1-2` | Selecionar as portas que interligam o switch à camada Core. |
| 4 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar as interfaces. |
| 7 | `exit` | Sair da configuração da faixa de portas. |
| 8 | `interface range gigabitEthernet 1/0/1-2` | Selecionar as portas que interligam o switch aos switches de acesso. |
| 9 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 10 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 11 | `no shutdown` | Habilitar as interfaces. |
| 12 | `end` | Finalizar a configuração. |

5.5 SW_DISTRIBUICAO1

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface range gigabitEthernet 1/1/1-2` | Selecionar as portas que interligam o switch à camada Core. |
| 4 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar as interfaces. |
| 7 | `exit` | Sair da configuração da faixa de portas. |
| 8 | `interface range gigabitEthernet 1/0/1-2` | Selecionar as portas que interligam o switch aos switches de acesso. |
| 9 | `switchport mode trunk` | Definir as portas selecionadas como trunk. |
| 10 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 11 | `no shutdown` | Habilitar as interfaces. |
| 12 | `end` | Finalizar a configuração. |

5.6 SW_ACESSO_0

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface fastEthernet 0/4` | Selecionar a porta que interliga o switch à distribuição. |
| 4 | `switchport mode trunk` | Definir a porta como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar a interface. |
| 7 | `end` | Finalizar a configuração. |

5.7 SW_ACESSO_1

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface fastEthernet 0/4` | Selecionar a porta que interliga o switch à distribuição. |
| 4 | `switchport mode trunk` | Definir a porta como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar a interface. |
| 7 | `end` | Finalizar a configuração. |

5.8 SW_ACESSO_2

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface fastEthernet 0/4` | Selecionar a porta que interliga o switch à distribuição. |
| 4 | `switchport mode trunk` | Definir a porta como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar a interface. |
| 7 | `end` | Finalizar a configuração. |

5.9 SW_ACESSO_3

| Etapa | Comando Cisco IOS | Finalidade |
|---|---|---|
| 1 | `enable` | Acessar o modo privilegiado. |
| 2 | `configure terminal` | Entrar no modo de configuração global. |
| 3 | `interface fastEthernet 0/1` | Selecionar a porta que interliga o switch à distribuição. |
| 4 | `switchport mode trunk` | Definir a porta como trunk. |
| 5 | `switchport trunk allowed vlan 10,20` | Permitir o tráfego das VLANs 10 e 20. |
| 6 | `no shutdown` | Habilitar a interface. |
| 7 | `end` | Finalizar a configuração. |

6. Verificação e salvamento
 
Após configurar as interfaces, utilize os comandos abaixo:

| Comando | Finalidade |
|---|---|
| `show interfaces status` | Exibir o estado das interfaces e indicar quais aparecem como trunk. |
| `show interfaces trunk` | Exibir as portas trunk e as VLANs permitidas. |
| `show vlan brief` | Conferir as VLANs e as portas de acesso associadas. |
| `show cdp neighbors` | Identificar os switches vizinhos e suas portas. |
| `show running-config` | Consultar a configuração em execução. |
| `show running-config startup-config` | Salvar a configuração atual do switch. |

7. Cuidados durante a configuração

- Configurar como trunk somente as interfaces que interligam switches.
- Configurar as duas extremidades de cada enlace com parâmetros compatíveis.
- Permitir as VLANs 10 e 20 nos enlaces trunk.
- Manter as portas conectadas aos computadores e notebooks no modo access, associadas às VLANs correspondentes.
- Não alterar as portas conectadas ao roteador ou ao servidor sem que isso esteja previsto na topologia.
- Conferir a VLAN nativa nas duas extremidades caso apareça a mensagem NATIVE VLAN MISMATCH.


8. Registro da execução e validação

Durante a atividade, foram utilizados show cdp neighbors e show interfaces status para identificar os equipamentos vizinhos, mapear as interfaces e conferir o modo das portas.

<img width="701" height="700" alt="image" src="https://github.com/user-attachments/assets/e14a8f6d-05b9-479a-8972-b33a1c508339" />

Foi observado um aviso NATIVE VLAN MISMATCH no enlace entre SW_DISTRIBUICAO1 e SW_ACESSO_3. A mensagem indicava que a porta do switch de distribuição estava na VLAN nativa 1, enquanto a porta remota aparecia associada à VLAN 10. Para corrigir a incompatibilidade, as duas extremidades do enlace devem ser configuradas de forma compatível como trunk.


9. Conclusão

A configuração trunk permite que diferentes VLANs compartilhem os enlaces físicos de interligação entre os switches da rede hierárquica. Com o padrão IEEE 802.1Q, os equipamentos identificam a VLAN de cada quadro e encaminham o tráfego de acordo com a segmentação lógica definida.

A verificação das interfaces e a compatibilidade das configurações nas duas extremidades dos enlaces são etapas importantes para o funcionamento da comunicação entre as camadas Core, Distribuição e Acesso.



