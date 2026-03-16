# Laboratório de Segurança: Testes de Autenticação com Kali Linux e Medusa

> Conversão do relatório em PDF para um repositório Git em Markdown, com imagens organizadas para publicação no GitHub.

## Visão geral

Este repositório documenta a implementação de um laboratório de segurança da informação utilizando **Kali Linux**, **Metasploitable 2**, **DVWA** e a ferramenta **Medusa** para simular cenários de ataques de autenticação em ambiente controlado.

O objetivo do laboratório foi analisar vulnerabilidades relacionadas a:

- senhas fracas
- ausência de bloqueio por tentativas de login
- ausência de mecanismos adicionais de proteção, como CAPTCHA e MFA

Foram realizados três cenários principais de teste:

1. força bruta em serviço FTP
2. automação de login em aplicação web vulnerável (DVWA)
3. enumeração e password spraying contra serviço SMB

---

## Autor

**Márcio Amâncio da Cruz**  
**Riachuelo | Cibersegurança**  
**Março/2026**

---

## Sumário

- [Resumo](#resumo)
- [Objetivo geral](#objetivo-geral)
- [Objetivos específicos](#objetivos-específicos)
- [Ambiente de laboratório](#ambiente-de-laboratório)
- [Metodologia](#metodologia)
- [Reconhecimento do alvo](#reconhecimento-do-alvo)
- [Wordlists utilizadas](#wordlists-utilizadas)
- [Teste 1 - Força Bruta em FTP](#teste-1---força-bruta-em-ftp)
- [Teste 2 - Automação de Login no DVWA](#teste-2---automação-de-login-no-dvwa)
- [Teste 3 - Enumeração e Password Spraying em SMB](#teste-3---enumeração-e-password-spraying-em-smb)
- [Recomendações de mitigação](#recomendações-de-mitigação)
- [Conclusão](#conclusão)
- [Referências](#referências)
- [Aviso ético](#aviso-ético)

---

## Resumo

Este laboratório apresenta a implementação de um ambiente de testes com foco em autenticação insegura, simulando cenários clássicos de exploração em redes e aplicações vulneráveis.

O estudo foi conduzido com o objetivo de observar, na prática, como serviços mal configurados ou protegidos com credenciais fracas podem ser comprometidos por meio de ataques automatizados.

---

## Objetivo geral

Implementar um ambiente de laboratório para simulação controlada de ataques de autenticação com o objetivo de estudar vulnerabilidades e medidas de mitigação.

## Objetivos específicos

- Configurar um ambiente virtual isolado utilizando VirtualBox
- Simular ataques de força bruta contra serviços de rede
- Automatizar tentativas de autenticação em aplicações web vulneráveis
- Realizar enumeração de usuários e password spraying em SMB
- Documentar os testes realizados
- Propor recomendações de segurança para mitigação das vulnerabilidades identificadas

---

## Ambiente de laboratório

### Infraestrutura utilizada

| Componente | Descrição |
|---|---|
| Virtualização | VirtualBox |
| Sistema atacante | Kali Linux |
| Sistema alvo | Metasploitable 2 |
| Aplicação vulnerável | DVWA |
| Ferramenta de ataque | Medusa |
| Rede | Host-only |

### Arquitetura do laboratório

| Máquina | Função | IP |
|---|---|---|
| Kali Linux | Atacante | - |
| Metasploitable 2 | Alvo | `192.168.56.102` |

**Rede utilizada:** `192.168.56.0/24`  
**Modo de rede:** Host-Only Network

---

## Metodologia

O laboratório foi realizado em ambiente isolado com duas máquinas virtuais conectadas por rede host-only.

### Etapas executadas

1. Configuração das máquinas virtuais
2. Identificação dos serviços ativos no alvo
3. Criação de wordlists simples
4. Execução de ataques de autenticação simulados
5. Validação dos acessos obtidos
6. Análise de vulnerabilidades
7. Proposição de medidas de mitigação

---

## Reconhecimento do alvo

Antes da execução dos testes foi realizada a identificação de serviços ativos na máquina vulnerável.

### Comando utilizado

```bash
nmap -sV 192.168.56.102
```

### Resultado resumido

| Porta | Serviço | Descrição |
|---|---|---|
| 21 | FTP | Serviço de transferência de arquivos |
| 80 | HTTP | Servidor web |
| 139 | SMB | Compartilhamento de arquivos |
| 445 | SMB | Compartilhamento de arquivos |

### Análise

Os serviços identificados indicam possíveis superfícies de ataque relacionadas a autenticação.

---

## Wordlists utilizadas

### Lista de usuários FTP - `users_ftp.txt`

```text
msfadmin
admin
user
ftp
test
```

### Lista de senhas - `passwords_lab.txt`

```text
123456
password
admin
msfadmin
test
```

### Lista de usuários SMB - `users_smb.txt`

```text
administrator
guest
msfadmin
user
test
```

---

## Teste 1 - Força Bruta em FTP

### Objetivo

Verificar a existência de credenciais fracas no serviço FTP.

### Ferramenta utilizada

**Medusa**

### Comando executado

```bash
medusa -h 192.168.56.102 -U users_ftp.txt -P passwords_lab.txt -M ftp -t 4
```

### Resultado

| Usuário | Senha | Status |
|---|---|---|
| `msfadmin` | `msfadmin` | Sucesso |

![Resultado do ataque FTP](assets/resultado_ftp.png)

### Validação do acesso

Após identificação da credencial válida, foi realizado login manual.

```bash
ftp 192.168.56.102
```

**Resultado:** login bem-sucedido.

### Evidência

![Evidência do login FTP](assets/evidencia_login_ftp.png)

### Análise

O serviço FTP utiliza senhas fracas.

---

## Teste 2 - Automação de Login no DVWA

### Objetivo

Simular tentativas automatizadas de login em formulário web vulnerável.

### Aplicação testada

**DVWA (Damn Vulnerable Web Application)**

### URL do alvo

```text
http://192.168.56.102/dvwa
```

### Nível de segurança configurado

**Low**

### Estrutura do formulário

| Campo | Nome |
|---|---|
| Usuário | `username` |
| Senha | `password` |
| Botão | `Login` |

### Método HTTP

`POST`

### Script/comando utilizado

> No relatório original, o campo foi deixado como placeholder. A imagem abaixo foi preservada como evidência visual do comando/teste executado.

![Script ou comando utilizado no DVWA](assets/script_dvwa.png)

### Resultado

- **Usuário:** `msfadmin`
- **Senha:** `password`

### Evidência

![Evidência do ataque no DVWA](assets/evidencia_dvwa.png)

### Análise

Vulnerabilidades observadas:

- ausência de bloqueio
- ausência de CAPTCHA
- ausência de MFA

---

## Teste 3 - Enumeração e Password Spraying em SMB

### Objetivo

Identificar contas válidas no serviço SMB por meio de password spraying.

### Enumeração inicial

```bash
enum4linux 192.168.56.20
```

### Execução do password spraying

```bash
medusa -h 192.168.56.20 -U users_smb.txt -p password -M smbnt -t 3
```

### Resultado

> O relatório original não apresenta a tabela textual completa deste resultado, mas a evidência visual foi preservada abaixo.

![Resultado do teste SMB](assets/resultado_smb.png)

### Validação

Acesso ao compartilhamento SMB:

```bash
smbclient -L //192.168.56.20 -U usuario
```

### Evidência

![Evidência do acesso SMB](assets/evidencia_smb.png)

### Análise

As contas utilizam senhas fracas e o sistema permite múltiplas tentativas.

---

## Recomendações de mitigação

### Para serviços FTP

- Utilizar SFTP em vez de FTP
- Implementar políticas de senha forte
- Bloquear tentativas repetidas
- Monitorar logs de autenticação

### Para aplicações web

- Implementar autenticação multifator
- Limitar tentativas de login
- Utilizar CAPTCHA adaptativo
- Monitorar padrões de acesso suspeitos

### Para serviços SMB

- Desativar contas padrão
- Aplicar políticas de complexidade de senha
- Implementar bloqueio de conta
- Monitorar logs de autenticação

---

## Conclusão

O laboratório demonstrou como serviços mal configurados podem ser explorados por meio de ataques de autenticação.

A utilização de ambientes vulneráveis permitiu compreender a importância de controles defensivos como:

- autenticação multifator
- políticas robustas de senha
- limitação de tentativas
- monitoramento contínuo de eventos de autenticação

Os testes reforçam a necessidade de boas práticas de segurança para reduzir a exposição a ataques de força bruta e password spraying.

---

## Referências

- Documentação Kali Linux
- Documentação Medusa
- Metasploitable 2 Exploitability Guide
- DVWA Documentation

---

## Estrutura sugerida do repositório

```text
lab-medusa-git/
├── README.md
└── assets/
    ├── resultado_ftp.png
    ├── evidencia_login_ftp.png
    ├── script_dvwa.png
    ├── evidencia_dvwa.png
    ├── resultado_smb.png
    └── evidencia_smb.png
```

---

## Aviso ético

Este conteúdo foi organizado para fins **educacionais**, **acadêmicos** e de **laboratório controlado**.  
Os testes descritos devem ser executados somente em ambientes autorizados, isolados e destinados a estudo.
