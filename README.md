# Laboratório de Segurança com Kali Linux e Medusa

![Status](https://img.shields.io/badge/status-concluído-brightgreen)
![Finalidade](https://img.shields.io/badge/finalidade-educacional-blue)
![Ambiente](https://img.shields.io/badge/ambiente-laboratório%20isolado-orange)

## Sobre o projeto

Este projeto apresenta uma prática de auditoria de autenticação realizada em um ambiente virtualizado, isolado e autorizado. O laboratório utiliza **Kali Linux**, **Medusa**, **Metasploitable 2** e **DVWA** para demonstrar como credenciais fracas podem ser identificadas em serviços FTP, formulários web e SMB.

Além da execução e documentação dos testes, o projeto apresenta recomendações para reduzir os riscos de ataques de força bruta, ataques de dicionário e password spraying.

> [!IMPORTANT]
> Este conteúdo foi desenvolvido exclusivamente para fins acadêmicos. Todos os procedimentos foram realizados em máquinas virtuais próprias, intencionalmente vulneráveis e conectadas a uma rede Host-Only. Não execute testes de autenticação contra sistemas, contas, redes ou equipamentos sem autorização expressa.

## Objetivos

- Compreender o funcionamento de tentativas automatizadas de autenticação;
- Utilizar o Kali Linux e o Medusa em um laboratório controlado;
- Analisar autenticação nos serviços FTP, Web e SMB;
- Demonstrar o conceito de password spraying;
- Identificar riscos relacionados a credenciais fracas;
- Propor medidas de prevenção e mitigação;
- Documentar a atividade de forma clara e reproduzível;
- Utilizar o GitHub como portfólio técnico.

## Tecnologias utilizadas

- **VirtualBox:** virtualização das máquinas do laboratório;
- **Kali Linux:** sistema utilizado para auditoria de segurança;
- **Metasploitable 2:** máquina intencionalmente vulnerável;
- **DVWA:** aplicação web vulnerável para treinamento;
- **Medusa:** ferramenta modular de auditoria de autenticação;
- **Nmap:** identificação de hosts, portas e serviços;
- **SMB:** serviço utilizado na demonstração de password spraying;
- **FTP:** serviço utilizado no teste de autenticação por wordlist;
- **Git e GitHub:** versionamento, documentação e publicação do projeto.

## Escopo e regras do laboratório

O escopo foi limitado às seguintes máquinas:

- Máquina de auditoria: **Kali Linux**;
- Máquina vulnerável: **Metasploitable 2**;
- Aplicação vulnerável: **DVWA**;
- Rede: **Host-Only**;
- Alvos externos: **nenhum**;
- Contas pessoais ou corporativas: **nenhuma**.

As wordlists foram mantidas pequenas e continham apenas valores fictícios ou credenciais fornecidas pelo próprio ambiente didático.

## Topologia do ambiente

```text
+-------------------------+       Rede Host-Only       +-------------------------+
|       Kali Linux        | <------------------------> |    Metasploitable 2     |
| Máquina de auditoria    |                            | Máquina vulnerável      |
|    |                            |       |
+-------------------------+                            +-------------------------+
                                                              |
                                                              +-- FTP
                                                              +-- DVWA / HTTP
                                                              +-- SMB
```



```

## Etapas realizadas

### 1. Preparação das máquinas virtuais

Foram utilizadas duas máquinas virtuais no VirtualBox:

1. Kali Linux, como estação de auditoria;
2. Metasploitable 2, como ambiente intencionalmente vulnerável.

Antes dos testes, foi criado um snapshot das máquinas para permitir a restauração do laboratório em caso de erro.

### 2. Configuração da rede

As duas máquinas foram conectadas a uma rede **Host-Only**, mantendo o laboratório separado de redes externas. Essa configuração permitiu comunicação entre as VMs sem expor os serviços vulneráveis diretamente à internet.

![Configuração da rede](./images/configuracao/01-rede-host-only.png)

### 3. Identificação dos endereços IP

Os endereços privados das máquinas foram identificados e registrados.

```text
Kali Linux:      192.168.56.101
Metasploitable 2: 192.168.56.101
```


### 4. Teste de conectividade

A comunicação entre as máquinas foi validada com um teste de ping. O resultado confirmou que o Kali Linux conseguia alcançar o Metasploitable 2 pela rede isolada.

```bash
ping -c 4 192.168.56.101
```



### 5. Identificação dos serviços

Foi realizada uma verificação no endereço privado do Metasploitable 2 para identificar os serviços disponíveis no laboratório. Foram observados os serviços necessários para os cenários de FTP, Web e SMB.

```bash
nmap -sV 192.168.56.101
```



> Somente o endereço privado da máquina vulnerável do laboratório foi analisado.

## Etapa 10: teste controlado no serviço FTP

### Objetivo

Demonstrar como uma credencial fraca pode ser identificada quando um serviço FTP permite sucessivas tentativas de autenticação sem controles adequados.

### Preparação

Antes do teste, foram confirmadas:

- A comunicação entre as máquinas virtuais;
- A disponibilidade do serviço FTP;
- A utilização do endereço IP privado correto;
- A criação de uma wordlist reduzida;
- A autorização e o isolamento do ambiente.

A wordlist foi criada exclusivamente para o laboratório:

```text
wordlists/senhas-ftp.txt
```

### Execução e resultado

A ferramenta Medusa foi utilizada com o módulo correspondente ao serviço FTP. O teste permaneceu restrito ao usuário, às senhas e ao endereço privado definidos para a atividade.

O resultado demonstrou que uma credencial fraca poderia ser identificada no ambiente intencionalmente vulnerável. A autenticação foi validada sem alteração, exclusão ou transferência de arquivos.

### Análise

O cenário demonstrou que uma senha previsível, combinada com a ausência de limitação de tentativas, pode permitir o comprometimento de uma conta. Também mostrou a importância de substituir protocolos sem proteção adequada por alternativas seguras.

## Etapa 11: teste controlado no DVWA

### Objetivo

Analisar o comportamento de um formulário web vulnerável diante de sucessivas tentativas de autenticação.

### Preparação

O DVWA foi acessado pelo navegador do Kali Linux por meio do endereço privado da máquina vulnerável. O nível de segurança foi configurado como **Low**, permitindo observar o comportamento vulnerável de forma didática.

Foi utilizada uma wordlist reduzida:

```text
wordlists/senhas-dvwa.txt
```

### Execução e resultado

Foram comparadas as respostas apresentadas pelo formulário após uma tentativa rejeitada e uma tentativa aceita. No nível Low, não foi observado um mecanismo efetivo de bloqueio temporário durante o teste.

A diferença entre as respostas permitiu identificar quando uma combinação de usuário e senha era aceita.

### Análise

A ausência de limitação adequada permitiu realizar sucessivas verificações de senha contra o mesmo usuário. Em uma aplicação real, esse comportamento poderia aumentar significativamente o risco de comprometimento de contas com credenciais fracas.

## Etapa 12: teste controlado no serviço SMB

### Objetivo

Demonstrar o conceito de **password spraying**, no qual uma quantidade mínima de senhas é avaliada contra uma lista reduzida de usuários.

### Preparação

Antes do teste, foram confirmadas a conectividade e a disponibilidade do serviço SMB. A enumeração foi limitada à máquina vulnerável do laboratório e resultou em uma lista pequena de usuários.

Arquivos utilizados:

```text
wordlists/usuarios-smb.txt
wordlists/senha-smb.txt
```

Nenhum usuário pessoal, corporativo ou externo foi incluído.

### Execução e resultado

O teste utilizou o módulo SMBNT do Medusa, uma única senha didática e poucos usuários do próprio laboratório. A quantidade de tentativas foi mantida baixa.


### Análise

O cenário demonstrou que tentativas distribuídas entre usuários podem ser menos evidentes do que muitas falhas concentradas em uma única conta. Por isso, os controles defensivos devem correlacionar origem, período, senha utilizada e variedade de contas afetadas.

## Comparação dos cenários

### FTP

- Tipo: teste de senha para um serviço de rede;
- Risco principal: credencial fraca e protocolo sem proteção adequada;
- Resultado: `[PREENCHER COM O RESULTADO REAL]`;
- Mitigação principal: substituir FTP por SFTP ou FTPS, restringir acesso e limitar tentativas.

### DVWA

- Tipo: tentativas em formulário web;
- Risco principal: ausência de limitação e respostas distinguíveis;
- Resultado: `[PREENCHER COM O RESULTADO REAL]`;
- Mitigação principal: throttling, MFA, mensagens genéricas e monitoramento.

### SMB

- Tipo: password spraying;
- Risco principal: reutilização da mesma senha entre contas;
- Resultado: `[PREENCHER COM O RESULTADO REAL]`;
- Mitigação principal: MFA, senhas exclusivas, segmentação e correlação de eventos.

## Medidas de mitigação

### Controles gerais de autenticação

- Utilizar senhas longas, exclusivas e resistentes;
- Bloquear senhas comuns ou conhecidas por vazamentos;
- Implementar autenticação multifator;
- Aplicar limitação de tentativas no servidor;
- Utilizar atraso progressivo entre novas tentativas;
- Configurar bloqueio temporário com cuidado;
- Monitorar falhas concentradas e distribuídas;
- Alertar sobre sucessos após várias falhas;
- Utilizar mensagens genéricas de autenticação;
- Proteger contas administrativas com controles adicionais;
- Desabilitar contas padrão e contas sem utilização;
- Aplicar o princípio do menor privilégio;
- Manter sistemas, serviços e dependências atualizados.

### Proteção do FTP

- Substituir FTP por SFTP ou FTPS;
- Impedir autenticação anônima quando desnecessária;
- Restringir o serviço por firewall e endereço IP;
- Segmentar o servidor em uma rede apropriada;
- Monitorar falhas de autenticação;
- Desabilitar o serviço se não houver necessidade operacional.

### Proteção de aplicações web

- Implementar limitação de requisições;
- Aplicar atraso progressivo;
- Utilizar HTTPS;
- Adotar autenticação multifator;
- Apresentar mensagens genéricas de erro;
- Registrar e analisar falhas de login;
- Utilizar CAPTCHA como camada complementar, não como defesa única;
- Proteger sessões e invalidar atividades suspeitas.

### Proteção do SMB

- Não expor o SMB diretamente à internet;
- Restringir as portas SMB no firewall;
- Desabilitar versões antigas do protocolo;
- Revisar contas e compartilhamentos;
- Segmentar servidores e estações;
- Aplicar o princípio do menor privilégio;
- Monitorar falhas distribuídas entre vários usuários;
- Manter o sistema e o serviço atualizados.

## Resultados de aprendizagem

Ao concluir o projeto, foi possível:

- Configurar duas máquinas virtuais em uma rede isolada;
- Validar comunicação entre os sistemas;
- Identificar serviços em uma máquina vulnerável;
- Criar e organizar pequenas wordlists;
- Compreender o uso modular do Medusa;
- Observar comportamentos vulneráveis em FTP, Web e SMB;
- Diferenciar força bruta, ataque de dicionário e password spraying;
- Organizar evidências técnicas;
- Relacionar vulnerabilidades a medidas defensivas;
- Produzir documentação apropriada para um portfólio técnico.

## Limitações do projeto

- O laboratório utiliza aplicações intencionalmente vulneráveis;
- Os resultados não representam sistemas modernos corretamente configurados;
- As wordlists foram propositalmente pequenas;
- O projeto não avaliou desempenho ou capacidade de detecção de produtos comerciais;
- Nenhum teste foi realizado contra redes ou sistemas externos;
- As conclusões são restritas ao ambiente acadêmico utilizado.

## Conclusão

Os testes demonstraram que a segurança da autenticação não depende apenas da existência de uma senha. Credenciais fracas tornam-se ainda mais perigosas quando combinadas com ausência de limitação de tentativas, mensagens excessivamente informativas, serviços antigos, falta de monitoramento e reutilização de senhas.

O cenário FTP evidenciou os riscos relacionados a credenciais previsíveis e protocolos inadequados. O teste no DVWA mostrou como um formulário sem controles efetivos pode permitir sucessivas tentativas. Já o cenário SMB demonstrou que uma única senha reutilizada pode afetar diferentes contas por meio de password spraying.

A principal conclusão é que a defesa deve utilizar várias camadas: senhas exclusivas, autenticação multifator, limitação de tentativas, atrasos progressivos, monitoramento, segmentação de rede, menor privilégio e atualização contínua dos sistemas.

## Referências

- [Kali Linux](https://www.kali.org/)
- [DVWA](https://github.com/digininja/DVWA)
- [Medusa](http://www.foofus.net/jmk/medusa/medusa.html)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [GitHub Markdown](https://docs.github.com/pt/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)

