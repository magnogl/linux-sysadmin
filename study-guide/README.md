# Trilha de Estudos em TI

Registro público da minha trilha de 24 meses em redes, infraestrutura e segurança. Cada tema só é marcado como concluído depois do laboratório documentado neste repositório.

**Início:** outubro de 2026
**Carga semanal:** 8 a 10 h
**Última atualização:** AAAA-MM-DD

## Painel geral

| Fase | Período | Temas concluídos | Certificação | Status |
| --- | --- | --- | --- | --- |
| 1. Fundamentos | meses 1 a 6 | 0/6 | LPIC-1 | Em andamento |
| 2. Redes e ISP | meses 7 a 12 | 0/7 | MTCNA e CCNA | Não iniciada |
| 3. Segurança | meses 13 a 18 | 0/8 | Fortinet NSE 4 | Não iniciada |
| 4. Infra moderna | meses 19 a 24 | 0/7 | AZ-802 | Não iniciada |
| Camada ampla | em paralelo | 0/7 | ITIL 4 Foundation (opcional) | Não iniciada |

## Certificações

| Prova | Data-alvo | Data realizada | Resultado |
| --- | --- | --- | --- |
| LPIC-1 (101-500 e 102-500) | 2027-03 | | |
| MikroTik MTCNA | 2027-06 | | |
| Cisco CCNA 200-301 | 2027-09 | | |
| Fortinet NSE 4 (FortiGate) | 2028-03 | | |
| Microsoft AZ-802 | 2028-09 | | |

## Como marco o progresso

Cada tema passa por 5 etapas. Só marco o tema como concluído quando todas estão feitas.

1. **Diagnóstico:** simulado curto. Acima de 80% de acerto, pulo direto para o laboratório.
2. **Teoria:** estudo apenas os tópicos em que errei.
3. **Laboratório:** monto, provoco a falha e conserto.
4. **Registro:** crio a página do tema em `labs/` com o que fiz, o que quebrou e como resolvi.
5. **Ensino:** explico o tema em 5 minutos. Se não sair claro, volto para a teoria.

Legenda do diário: `[D]` diagnóstico, `[T]` teoria, `[L]` laboratório, `[R]` registro, `[E]` ensino.

## Fase 1: Fundamentos (meses 1 a 6)

- [ ] **Redes: teoria de camadas, TCP, congestionamento, roteamento**
  - Fonte: Kurose e Ross, *Redes de Computadores e a Internet*
  - Lab: capturar e explicar no Wireshark um handshake TCP, uma retransmissão, uma consulta DNS e uma negociação TLS
  - Registro: [labs/fase1/redes-teoria.md](labs/fase1/redes-teoria.md)
- [ ] **Sistemas operacionais: processos, memória virtual, concorrência, sistemas de arquivos**
  - Fonte: *Operating Systems: Three Easy Pieces* (OSTEP)
  - Lab: medir com `vmstat`, `iostat` e `perf` o efeito de swap e cache de disco
  - Registro: [labs/fase1/sistemas-operacionais.md](labs/fase1/sistemas-operacionais.md)
- [ ] **Linux: administração**
  - Fonte: objetivos da LPIC-1 e *UNIX and Linux System Administration Handbook*
  - Lab: servidor Debian do zero com LVM, systemd, usuários, SSH com chave, nftables e backup automatizado
  - Registro: [labs/fase1/linux-admin.md](labs/fase1/linux-admin.md)
- [ ] **Shell script e Python**
  - Fonte: CS50P (Harvard) e Curso em Vídeo
  - Lab: script de inventário de máquinas com saída em CSV
  - Registro: [labs/fase1/scripting.md](labs/fase1/scripting.md)
- [ ] **Arquitetura de computadores**
  - Fonte: CS50x (semanas 0 a 5) ou Nand2Tetris parte 1
  - Registro: [labs/fase1/arquitetura.md](labs/fase1/arquitetura.md)
- [ ] **Git**
  - Fonte: *Pro Git*
  - Lab: todos os scripts e configurações do lab versionados neste repositório
  - Registro: [labs/fase1/git.md](labs/fase1/git.md)
- [ ] **Certificação LPIC-1**

## Fase 2: Redes e ISP (meses 7 a 12)

- [ ] **Switching: VLAN, STP/RSTP, LACP, trunk**
  - Fonte: Cisco Networking Academy (CCNA 1 a 3) e livros oficiais do CCNA
  - Lab: 3 switches com redundância e falha provocada de link
  - Registro: [labs/fase2/switching.md](labs/fase2/switching.md)
- [ ] **Roteamento IGP: OSPF multiárea, rotas estáticas flutuantes, VRRP**
  - Lab: OSPF multiárea no GNS3 ou EVE-NG com failover medido
  - Registro: [labs/fase2/ospf-vrrp.md](labs/fase2/ospf-vrrp.md)
- [ ] **BGP: eBGP, iBGP, políticas, communities, prefix-lists**
  - Fonte: NIC.br, curso Básico de Boas Práticas Operacionais para Sistemas Autônomos, e *Internet Routing Architectures*
  - Lab: AS de provedor com dois trânsitos e um PTT, com filtros corretos
  - Registro: [labs/fase2/bgp.md](labs/fase2/bgp.md)
- [ ] **IPv6: plano de endereçamento, SLAAC, DHCPv6-PD**
  - Fonte: NIC.br, curso Básico de IPv6, e livro *IPv6 para Todos*
  - Lab: dual stack completo com prefixo delegado para cliente
  - Registro: [labs/fase2/ipv6.md](labs/fase2/ipv6.md)
- [ ] **MikroTik RouterOS: PPPoE, CGNAT, filas, BGP**
  - Fonte: treinamento MTCNA
  - Lab: concentrador PPPoE com CGNAT e controle de banda
  - Registro: [labs/fase2/mikrotik.md](labs/fase2/mikrotik.md)
- [ ] **MPLS, VPLS e L2VPN** (base ampla)
  - Lab: um túnel VPLS entre dois roteadores
  - Registro: [labs/fase2/mpls-vpls.md](labs/fase2/mpls-vpls.md)
- [ ] **Wi-Fi corporativo, cabeamento e GPON** (superfície)
- [ ] **Certificação MikroTik MTCNA**
- [ ] **Certificação Cisco CCNA 200-301**

## Fase 3: Segurança (meses 13 a 18)

- [ ] **Firewall de próxima geração: políticas, NAT, VPN IPsec e SSL, SD-WAN, inspeção**
  - Fonte: Fortinet Training Institute
  - Lab: FortiGate VM com duas filiais em SD-WAN e VPN site a site
  - Registro: [labs/fase3/fortigate.md](labs/fase3/fortigate.md)
- [ ] **Hardening de Windows e Linux**
  - Fonte: CIS Benchmarks e Microsoft Security Baselines
  - Lab: aplicar e auditar um benchmark CIS com relatório antes e depois
  - Registro: [labs/fase3/hardening.md](labs/fase3/hardening.md)
- [ ] **SIEM e detecção**
  - Fonte: documentação do Wazuh e trilha SOC Level 1 do TryHackMe
  - Lab: Wazuh coletando logs do firewall, do AD e do Linux, com 5 alertas próprios
  - Registro: [labs/fase3/wazuh.md](labs/fase3/wazuh.md)
- [ ] **Active Directory seguro**
  - Lab: atacar o AD do lab (Kerberoasting, senhas fracas) e corrigir
  - Registro: [labs/fase3/ad-seguro.md](labs/fase3/ad-seguro.md)
- [ ] **Criptografia aplicada e PKI** (base ampla)
  - Lab: CA interna emitindo certificados para os serviços do lab
  - Registro: [labs/fase3/pki.md](labs/fase3/pki.md)
- [ ] **Resposta a incidentes** (base ampla)
  - Fonte: CERT.br
  - Entregável: playbook de ransomware para escritório contábil
  - Registro: [labs/fase3/resposta-incidentes.md](labs/fase3/resposta-incidentes.md)
- [ ] **Pentest e OWASP Top 10** (base ampla)
  - Fonte: TryHackMe, trilha Jr Penetration Tester
- [ ] **MITRE ATT&CK** (base ampla)
  - Lab: mapear os alertas do Wazuh para técnicas do ATT&CK
- [ ] **Certificação Fortinet NSE 4**

## Fase 4: Infra moderna (meses 19 a 24)

- [ ] **Windows Server, AD DS e híbrido com Azure**
  - Fonte: Microsoft Learn, guia e curso da AZ-802
  - Lab: domínio do lab sincronizado com um tenant Entra ID de teste
  - Registro: [labs/fase4/windows-hibrido.md](labs/fase4/windows-hibrido.md)
- [ ] **Virtualização: Proxmox VE e Hyper-V**
  - Lab: cluster Proxmox com migração ao vivo e Proxmox Backup Server
  - Registro: [labs/fase4/proxmox.md](labs/fase4/proxmox.md)
- [ ] **Ansible**
  - Fonte: documentação oficial e *Ansible for DevOps*
  - Lab: playbook que sobe servidor Linux endurecido e configura MikroTik
  - Registro: [labs/fase4/ansible.md](labs/fase4/ansible.md)
- [ ] **Observabilidade: Zabbix, Prometheus e Grafana**
  - Lab: painel único com rede, servidores e firewall, com alertas no Telegram
  - Registro: [labs/fase4/observabilidade.md](labs/fase4/observabilidade.md)
- [ ] **Docker e noções de Kubernetes** (base ampla)
  - Fonte: LINUXtips
  - Lab: 3 containers via Docker Compose atrás de proxy reverso com TLS
  - Registro: [labs/fase4/containers.md](labs/fase4/containers.md)
- [ ] **Nuvem pública: rede, identidade e custos** (base ampla)
  - Lab: VPN site a site do lab para uma VNet no Azure
  - Registro: [labs/fase4/nuvem.md](labs/fase4/nuvem.md)
- [ ] **Terraform** (base ampla)
  - Lab: criar e destruir o ambiente Azure via Terraform
  - Registro: [labs/fase4/terraform.md](labs/fase4/terraform.md)
- [ ] **Certificação Microsoft AZ-802**

## Camada ampla (em paralelo)

- [ ] ITIL 4 (meses 3 a 5): catálogo de serviços de TI
- [ ] LGPD (meses 6 e 7): checklist de adequação técnica para escritório contábil
- [ ] SQL e PostgreSQL (meses 8 a 10): banco de inventário do lab
- [ ] APIs REST e noções de arquitetura web (meses 10 a 12): API do inventário
- [ ] ISO 27001 e 27002 (meses 13 a 15): declaração de aplicabilidade simplificada
- [ ] COBIT 2019 (meses 16 a 18): saber explicar os objetivos de governança
- [ ] PMBOK 7 e Scrum (meses 20 a 22): plano de projeto de uma migração real

## Diário de estudos

Entradas mais recentes no topo. Uma linha por sessão.

| Data | Horas | Tema | Etapa | O que fiz | Próximo passo |
| --- | --- | --- | --- | --- | --- |
| AAAA-MM-DD | 1,5 | Linux admin | [T] | Exemplo: LVM, criar e estender volume | Lab de LVM no sábado |

## Balanço mensal

### Mês 1 (AAAA-MM)

- Horas no mês:
- Temas concluídos:
- O que travou:
- Ajuste para o próximo mês:

## Estrutura do repositório

```
.
├── README.md          # este painel
├── labs/
│   ├── fase1/         # uma página por tema, com comandos e prints
│   ├── fase2/
│   ├── fase3/
│   └── fase4/
├── configs/           # configurações exportadas dos equipamentos do lab
├── scripts/           # automações criadas durante a trilha
└── diagramas/         # topologias do lab
```

## Modelo de página de laboratório

Copie para `labs/faseX/tema.md`:

```markdown
# Tema

**Data:** AAAA-MM-DD
**Tempo gasto:** X h

## Objetivo
O que o laboratório precisa provar.

## Topologia
Diagrama ou descrição dos equipamentos e endereços.

## Passo a passo
Comandos e configurações aplicados.

## Falha provocada
O que quebrei de propósito e o que aconteceu.

## Como resolvi
Diagnóstico e correção.

## O que aprendi
Três pontos em frases curtas.

## Referências
Links da documentação usada.
```
