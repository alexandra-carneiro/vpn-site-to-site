# VPN Site-to-Site

Trabalho da UC 00639 do CET em Gestão de Redes e Sistemas Computacionais (IEFP, Tomar, 2026).

**Responsável técnica:** Alexandra Carneiro (Técnica de Redes e Segurança)

## O projeto

Ligar a sede de uma empresa (Lisboa) à sua filial (Porto) com um túnel VPN IPsec Site-to-Site, para que a filial aceda em segurança aos serviços centrais: Active Directory, DNS, DHCP, servidor web interno, base de dados e partilhas de ficheiros.

Empresa fictícia: TechSolutions. Ambiente simulado em Cisco Packet Tracer.

| Local | Rede | Gateway |
| --- | --- | --- |
| Sede (Lisboa) | 192.168.10.0/24 | 192.168.10.1 |
| Filial (Porto) | 192.168.20.0/24 | 192.168.20.1 |
| Servidor central (AD/DNS/DHCP) | 192.168.10.10 | - |
| Servidor web | 192.168.10.20 | - |
| Servidor de base de dados | 192.168.10.30 | - |

## As 7 fases

1. **Análise técnica da infraestrutura** - equipamentos, serviços, endereçamento IP estático, dependências entre serviços e justificação da VPN.
2. **Plano de intervenção** - janela de manutenção (sexta 20h a sábado), cronograma, recursos, riscos e plano de rollback.
3. **Configuração e implementação** - túnel IPsec/IKE entre os dois gateways (pfSense), com as configurações ativas nos dois lados.
4. **Testes e validação** - matriz de 9 testes (endereçamento, ping, tracert, DNS, HTTP, SMB, inspeção ESP). Todos aprovados.
5. **Incidentes** - 5 incidentes diagnosticados e resolvidos (ver tabela abaixo).
6. **Gestão da intervenção** - métricas previsto vs. real, orçamento, riscos e aprovação formal.
7. **Relatório final consolidado** - sumário executivo, arquitetura, testes e troubleshooting.

O documento consolidado com as 7 fases é o `VPN-Site-to-Site-Lisboa-Porto.pdf`. Cada fase também está em separado na pasta `fases/`.

## Os 5 incidentes

| # | Problema | Causa | Solução |
| --- | --- | --- | --- |
| 1 | Túnel não estabelece (Fase 1 IKE) | Chave pré-partilhada diferente nos dois gateways (maiúsculas vs. minúsculas) | Uniformizar a chave nos dois lados |
| 2 | Fase 2 IPsec não estabelece | ACL crypto a apontar para a rede errada (192.168.30.0 em vez de 192.168.20.0) | Corrigir a sub-rede remota na ACL |
| 3 | Túnel ativo mas sem acesso a serviços | Firewall só permitia HTTPS (porta 443) | Permitir ICMP, DNS (53) e SMB (445/139) |
| 4 | Filial não resolve nomes internos | DHCP a distribuir o DNS público 8.8.8.8 (Option 6) | Option 6 com o DNS interno (192.168.10.10) |
| 5 | Filial sem rota de saída | DHCP a distribuir um gateway inexistente (192.168.20.254, Option 3) | Option 3 com o gateway correto (192.168.20.1) |

Cada incidente tem o seu ficheiro Packet Tracer na pasta `packet-tracer/`, com o problema reproduzido e a correção aplicada.

## Ficheiros

- `VPN-Site-to-Site-Lisboa-Porto.pdf` - documento consolidado das 7 fases
- `fases/` - cada fase em PDF separado
- `packet-tracer/` - topologia principal (`VPN-Site-to-Site.pkt`) e um ficheiro por incidente

Para abrir os ficheiros `.pkt` é preciso o Cisco Packet Tracer (gratuito com conta NetAcad).

## Notas

- Projeto académico, em ambiente simulado. A empresa, os endereços IP públicos e os dados são fictícios.
- Os parâmetros de criptografia (grupo Diffie-Hellman 5, SHA-1) são os mais fortes suportados pelo Packet Tracer; em produção real usam-se os recomendados pelo NIST (grupo 14, SHA-256). Isto está explicado na Fase 2.
- A chave pré-partilhada que aparece na documentação é um exemplo de laboratório, não uma credencial real.
- Os preços da tabela da Fase 6 foram pesquisados pela autora e incluem IVA.
