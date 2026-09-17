# Incidente 01: Brute Force SSH contra o Kali Linux

## Resumo Executivo

Ataque de força bruta contra o serviço SSH do endpoint Kali Linux foi detectado pelo Wazuh SIEM. O Wazuh correlacionou múltiplas falhas de autenticação em um curto intervalo e disparou um alerta de nível 10 (alta severidade), mapeado para a técnica T1110 (Brute Force) do MITRE ATT&CK.

## Informações do Incidente

| Campo | Valor |
|---|---|
| Data/Hora | 2026-09-16 20:44 (EDT) |
| Endpoint afetado | kali (192.168.100.108) |
| Serviço | SSH (porta 22) |
| Vetor | Brute Force / Password Guessing |
| Severidade | Alta (nível 10/15) |
| Status | Detectado |

## Linha do Tempo

| Horário | Evento | Rule ID | Nível |
|---|---|---|---|
| 20:44:03 | Primeira falha de autenticação SSH | 5760 | 5 |
| 20:44:07 | Falha de validação de senha (PAM) | 5557 | 5 |
| 20:44:09 | Segunda falha de autenticação | 5760 | 5 |
| 20:44:11 | Segunda falha PAM | 5557 | 5 |
| 20:44:13 | Terceira falha de autenticação | 5760 | 5 |
| 20:44:13 | **Correlação: brute force detectado** | **2502** | **10** |

## Análise Técnica

O atacante realizou múltiplas tentativas de autenticação SSH com credenciais inválidas em um intervalo de 10 segundos. Cada tentativa gerou dois eventos distintos:

- **Rule 5760** (sshd): falha de autenticação reportada pelo daemon SSH
- **Rule 5557** (unix_chkpwd): falha na validação de senha pelo módulo PAM

O Wazuh correlacionou os eventos através da **rule 2502**, que dispara quando o mesmo usuário acumula múltiplas falhas de senha em uma janela curta de tempo. Essa regra eleva a severidade para **nível 10**, indicando atividade maliciosa provável.

## Mapeamento MITRE ATT&CK

| Técnica | ID | Descrição |
|---|---|---|
| Brute Force | T1110 | Tentativa sistemática de adivinhar credenciais |
| Password Guessing | T1110.001 | Uso de senhas comuns contra o serviço |

## Recomendações de Mitigação

1. **Desabilitar autenticação por senha no SSH** e usar apenas chaves públicas
2. **Implementar fail2ban** para bloquear IPs após N tentativas falhas
3. **Aplicar rate limiting** no SSHd
4. **Monitorar alertas de nível >= 10** em tempo real (integração com SIEM/SOAR)
5. **Usar autenticação multifator (MFA)** para acesso remoto

## Evidências

- `evidence/06-deteccao-brute-force.png` — Lista de eventos no Wazuh
- `evidence/07-mitre-attack.png` — Gráfico de técnicas MITRE detectadas
- `evidence/08-regra-2502.png` — Detalhe da regra disparada
