# DayPlannio

Aplicativo para organizar a rotina de profissionais autônomos, reunindo agenda, clientes, serviços e finanças em uma única plataforma.


**Autores:** Alissa Gabriel e Raissa Geovana Araujo

## Como rodar

O DayPlannio tem três partes: **API**, **Web** e **App**. A API e o Web já estão publicados em uma VM no Azure, então para testar basta rodar o App.

### Portal Web (já no ar)

Acesse: **http://dayplannio.canadacentral.cloudapp.azure.com/**

- A API fica em `http://dayplannio.canadacentral.cloudapp.azure.com/api/`.
- O endereço usa HTTP (sem certificado). Use apenas senhas de teste.

### Rodar o App

**Pré-requisitos**

- Windows com **Visual Studio 2026** e a carga de trabalho **Desenvolvimento de aplicativos móveis com .NET** (MAUI) instalada
- **.NET 10 SDK**

**Passos**

1. Clone o repositório:
```bash
   git clone https://github.com/DayPlannio/DayPlannio.git
```
2. Abra o arquivo da solução do App (`.sln`) no Visual Studio:
   - Pasta: `DayPlannio.App/`
   - Arquivo: `DayPlannio.App.sln`
3. Aguarde a restauração dos pacotes NuGet.
4. Selecione o projeto do App como projeto de inicialização e escolha o destino (windows machine).
5. Clique em **Iniciar (▶)** ou pressione **F5**.

> Na primeira execução o build pode demorar alguns minutos.

---

## CONTEÚDO DESTE DOCUMENTO

Este documento reúne o material base do projeto DayPlannio para a etapa de Peer Review (Semana 1 – Submissão da Documentação), organizado conforme os itens solicitados:

- Visão do sistema
- Requisitos
- Arquitetura
- Protótipos
- Backlog
- Funcionalidades
- Estratégia inicial de testes

A documentação completa do projeto (casos de uso detalhados, modelos de banco de dados, diagrama de classes, estudo de viabilidade, modelo canvas e design) está no documento principal DayPlannio e no repositório de documentação do projeto.

**Situação do desenvolvimento:** as funcionalidades de agenda, clientes, serviços, financeiro, portal do cliente, portfólio, planos e privacidade estão implementadas. O geofencing (RF40 e RF55) está em implementação e será concluído até a data de entrega.

---

## 1 VISÃO DO SISTEMA

### 1.1 Problema

Profissionais autônomos (cabeleireiros, manicures, técnicos, diaristas, personal trainers, entre outros) costumam administrar agenda, clientes e finanças em ferramentas separadas, como caderno, agenda de papel, planilhas e aplicativos de mensagem. Essa dispersão gera conflitos de horário, esquecimento de compromissos, falta de histórico por cliente e pouca clareza sobre quanto realmente se ganha em cada serviço.

### 1.2 Solução proposta

O **DayPlannio** é uma plataforma que centraliza, em um único ambiente, a agenda de atendimentos, o cadastro de clientes e serviços, o controle financeiro e as métricas de desempenho do profissional. É composta por quatro frentes:

- **Aplicativo mobile (prestador):** gestão diária da agenda, clientes, serviços e finanças, com registro automático de presença no local do atendimento por geofencing.
- **Portal web do cliente:** o cliente acompanha o histórico e as métricas dos serviços realizados para ele.
- **Portal administrativo:** aprovação de planos e fotos, consulta de clientes, prestadores e logs, e encerramento de contas.
- **Portfólio público:** página aberta, sem login, com as fotos de atendimentos autorizadas pelo prestador e aprovadas pelo administrador.

### 1.3 Usuários

| Perfil | Plataforma | Principais necessidades |
| --- | --- | --- |
| Prestador (autônomo) | Aplicativo mobile | Organizar agenda, clientes, serviços e finanças; comprovar presença; divulgar seu trabalho. |
| Cliente | Portal web | Consultar histórico e métricas dos serviços; manter dados e senha. |
| Administrador | Portal web | Aprovar planos e fotos; auditar logs; consultar a base; encerrar contas. |
| Visitante | Portal web (público) | Conhecer o trabalho dos prestadores pelo portfólio. |

### 1.4 Objetivos

**Objetivo geral:** desenvolver uma plataforma que auxilie profissionais autônomos na organização da rotina, integrando agenda, clientes, controle financeiro, métricas e recursos de Internet das Coisas (geofencing) para automatizar registros operacionais.

- Centralizar agenda, clientes, serviços e finanças em um só ambiente.
- Registrar automaticamente a chegada e a saída do prestador por geolocalização.
- Gerar métricas de produtividade, faturamento e tempo trabalhado.
- Permitir que o cliente acompanhe o histórico dos serviços e que o prestador divulgue seu trabalho.

### 1.5 Escopo

**Dentro do escopo:** agenda e status de atendimentos; clientes e tipos de serviço; entradas e saídas financeiras, lucro bruto e líquido e relatórios em PDF; credenciais e portal do cliente; fotos e portfólio com moderação; planos de assinatura com 7 dias de teste; geocodificação e geofencing; política de privacidade, consentimento e encerramento de conta; logs e notificações.

### 1.6 Planos de assinatura

| Plano | Valor mensal | O que inclui |
| --- | --- | --- |
| Básico | R$ 9,90 | Agendamentos, agenda, clientes, histórico, tipos de serviço e perfil. |
| Profissional | R$ 19,90 | Tudo do Básico, além de financeiro e métricas do prestador. |
| Full | R$ 29,90 | Tudo do Profissional, além de portfólio público, fotos, área e métricas do cliente e geofencing. |

Novos usuários recebem 7 dias gratuitos no plano Full.

### 1.7 Premissas e restrições

- O prestador usa um smartphone com GPS e acesso à internet.
- A coleta de localização depende do consentimento do usuário (RF55) e só ocorre no plano Full.
- A precisão do geofencing depende do GPS do aparelho; por isso o raio mínimo é de 50 m e há tolerância de saída de 15 m.
- Os dados pessoais seguem a Política de Privacidade do sistema (RNF4).

### 1.8 Tecnologias e método

C# em todo o sistema; .NET MAUI (aplicativo mobile), ASP.NET Web API e ASP.NET MVC (API e portais); MongoDB Atlas (banco de dados); Microsoft Azure Blob Storage (imagens); Git e GitHub (versionamento); Figma (protótipo); Kanban no Trello (processo e cronograma).

---

## 2 REQUISITOS

Os requisitos descrevem o que o sistema deve fazer (funcionais) e as qualidades que deve ter (não funcionais). Os identificadores são usados no backlog e na estratégia de testes para garantir rastreabilidade.

### 2.1 Requisitos funcionais

| ID | Requisito | Descrição |
| --- | --- | --- |
| RF1 | Criar Agendamento | Permitir que o usuário cadastrado registre novos serviços em sua agenda, informando cliente, tipo de serviço, data, hora, observações, valor cobrado e custo material. |
| RF2 | Editar Agendamento | Permitir que o usuário altere detalhes de um serviço previamente agendado. |
| RF3 | Cancelar Agendamento | Permitir que o usuário cancele um serviço previamente agendado. |
| RF4 | Concluir Agendamento | Permitir que o usuário conclua um serviço. |
| RF5 | Visualizar Agenda | Permitir que o usuário visualize sua agenda. |
| RF6 | Cadastrar Usuário | Permitir que novos usuários se cadastrem fornecendo nome completo, e-mail, senha, confirmação de senha e telefone. |
| RF7 | Realizar Login | Permitir que usuários acessem o sistema informando e-mail e senha. |
| RF8 | Recuperar Senha | Permitir que usuários solicitem a redefinição de senha através do e-mail cadastrado. |
| RF9 | Exibir Informações do Perfil | Permitir que o usuário visualize seus dados pessoais cadastrados. |
| RF10 | Editar Informações do Perfil | Permitir que o usuário atualize seus dados pessoais, garantindo que as informações estejam sempre corretas. |
| RF11 | Cadastrar Cliente | Permitir que o usuário adicione clientes com informações básicas: nome, telefone, endereço e observações. |
| RF12 | Listar Clientes | Permitir que usuários visualizem seus clientes cadastrados. |
| RF13 | Editar Cliente | Permitir que usuários editem seus clientes cadastrados. |
| RF14 | Histórico de Cliente | Permitir que usuários visualizem os históricos de serviço por cliente. |
| RF15 | Cadastrar Tipo do Serviço | Permitir que o usuário registre serviços oferecidos, informando tipo, descrição e tempo estimado. |
| RF16 | Listar Tipo de Serviço | Permitir que o usuário visualize os serviços cadastrados. |
| RF17 | Editar Tipo de Serviço | Permitir que usuários editem seus serviços cadastrados. |
| RF18 | Excluir Tipo de Serviço | Permitir que usuários excluam seus serviços cadastrados. |
| RF19 | Registrar Entradas e Saídas | Permitir que o usuário registre tipo (entrada/saída), descrição, valor, data e categoria. |
| RF20 | Editar Registro Financeiro | Permitir que usuários editem seus registros financeiros cadastrados. |
| RF21 | Excluir Registro Financeiro | Permitir que usuários excluam seus registros financeiros cadastrados. |
| RF22 | Calcular Lucros | Calcular automaticamente lucro bruto e lucro líquido com base nos agendamentos concluídos em modo diário, semanal ou mensal. |
| RF23 | Calcular Lucros Geral | Calcular automaticamente lucro bruto e lucro líquido com base nos agendamentos concluídos e registros financeiros avulsos em modo diário, semanal ou mensal. |
| RF24 | Gerar Relatórios Financeiros | Gerar relatórios diários, semanais e mensais, com filtro por período, podendo exportar em PDF. |
| RF25 | Realizar Login Administrativo | Permitir que um administrador do sistema faça login pelo portal Web, separado dos usuários/prestadores comuns. |
| RF26 | Visualizar Logs do Sistema | Permitir que o administrador visualize, pelo portal Web, um histórico paginado de logs/ações realizadas no sistema. |
| RF27 | Listar Clientes (Admin) | Permitir que o administrador visualize, pelo portal Web, todos os clientes cadastrados na base, de todos os prestadores. |
| RF28 | Listar Prestadores (Admin) | Permitir que o administrador visualize, pelo portal Web, todos os prestadores (usuários) cadastrados no sistema. |
| RF29 | Gerar Credenciais do Cliente | Permitir que o prestador, ao cadastrar um cliente no app, gere automaticamente um e-mail de acesso e uma senha provisória para esse cliente. |
| RF30 | Realizar Login do Cliente | Permitir que o cliente faça login pelo portal Web utilizando o e-mail e a senha provisória gerados pelo prestador. |
| RF31 | Trocar Senha no Primeiro Acesso | Obrigar que o cliente troque a senha provisória por uma senha própria no primeiro acesso ao portal Web. |
| RF32 | Definir E-mail Secundário do Cliente | Permitir que o cliente cadastre um e-mail secundário associado ao seu perfil, pelo portal Web. |
| RF33 | Recuperar Senha do Cliente | Permitir que o cliente solicite e realize redefinição de senha pelo portal Web. |
| RF34 | Exibir Perfil do Cliente | Permitir que o cliente visualize os próprios dados de perfil pelo portal Web. |
| RF35 | Fazer Upload de Fotos de Serviço | Permitir que o prestador anexe fotos a um agendamento realizado, podendo marcá-las como públicas ou privadas. |
| RF36 | Excluir Foto | Permitir que o prestador exclua fotos cadastradas. |
| RF37 | Exibir Portfólio Público | Disponibilizar, no portal Web, uma página pública de portfólio (marketing/divulgação) com fotos marcadas como públicas, visível sem necessidade de login. |
| RF38 | Exibir Métricas de Serviço para o Cliente | Permitir que o cliente tenha acesso a métricas sobre os serviços realizados pelo prestador, como últimos serviços, média de tempo por serviço, entre outras. |
| RF39 | Exibir Métricas de Serviço para o Prestador | Permitir que o prestador tenha acesso a métricas dos próprios serviços, como ganho por tipo de serviço, média de valor por serviço, entre outras. |
| RF40 | Monitorar Presença por Geocodificação e Geofencing (Status) | Permitir converter o endereço do agendamento em latitude/longitude e monitorar a presença do prestador por geofencing. O prestador define um raio de 50m, 100m, 200m ou 500m, e o aplicativo envia a localização a cada 30 segundos. O sistema atualiza automaticamente os status: Agendado (atendimento criado), Em Atendimento (prestador dentro do raio), Em Pausa (prestador fora do raio por até 30 minutos) e Concluído (prestador fora do raio por 30 minutos, sem retorno ou outro atendimento autorizado). |
| RF41 | Definir Visibilidade do Telefone | Permitir que o prestador cadastre seu telefone de contato e defina se ele será exibido publicamente (ex.: no portfólio público) ou mantido privado, visível apenas para o próprio prestador. |
| RF42 | Autorizar Foto no Portfólio | Permitir que o administrador aprove ou recuse a foto de atendimento enviada pelo prestador. |
| RF43 | Definir Visibilidade da Cidade | Permitir que o prestador cadastre sua cidade e defina se ela será exibida publicamente (ex.: no portfólio público) ou mantida privada, visível apenas para o próprio prestador. |
| RF44 | Exibir Planos Disponíveis | Permitir que o prestador visualize os planos de assinatura (Básico, Profissional e Full), com o valor mensal e as funcionalidades incluídas em cada um. |
| RF45 | Assinar Plano | Permitir que o prestador escolha e solicite a assinatura de um dos planos disponíveis: Básico (R$ 9,90), Profissional (R$ 19,90) ou Full (R$ 29,90), ficando a ativação sujeita à aprovação do administrador. |
| RF46 | Alterar Plano | Permitir que o prestador solicite upgrade ou downgrade do plano contratado, ficando a alteração sujeita à aprovação do administrador. |
| RF47 | Cancelar Assinatura | Permitir que o prestador cancele a assinatura do plano contratado. |
| RF48 | Controlar Acesso às Funcionalidades por Plano | Liberar ou bloquear automaticamente as funcionalidades conforme o plano aprovado do prestador: Básico (agendamentos, agenda, clientes, histórico, tipos de serviço e perfil), Profissional (tudo do Básico, além de financeiro e métricas do prestador) e Full (tudo do Profissional, além de portfólio público, fotos, área e métricas do cliente e geofencing). |
| RF49 | Gerenciar Período de Teste Gratuito | Conceder a novos usuários 7 dias gratuitos no plano Full e, ao término do período, solicitar a escolha de um plano. |
| RF50 | Aprovar ou Recusar Assinatura de Plano | Permitir que o administrador visualize, pelo portal Web, as solicitações de assinatura ou alteração de plano enviadas pelos prestadores e aprove ou recuse cada uma delas. O plano só é ativado após a aprovação. |
| RF51 | Exibir Política de Privacidade | Exibir a Política de Privacidade no aplicativo mobile do prestador e no portal Web do cliente, com as seções: sobre a política, dados que coletamos, uso da localização, como usamos os dados, compartilhamento de dados, armazenamento e segurança, direitos do usuário e contato, informando a data da última atualização. |
| RF52 | Exigir Concordância no Cadastro | Exigir que o usuário leia e concorde com a Política de Privacidade para concluir o cadastro. As opções "Concordar" e "Não concordar" só são liberadas depois que o usuário rola a política até o final. Sem a concordância, a conta não é criada. |
| RF53 | Reler a Política de Privacidade | Permitir que o usuário acesse e releia a Política de Privacidade a qualquer momento, por meio da área "Privacidade" do Meu Perfil, tanto no aplicativo mobile do prestador quanto no portal Web do cliente, podendo confirmar que continua de acordo com os termos apresentados. |
| RF54 | Encerrar Conta ao Revogar a Concordância | Permitir que o usuário que deixar de concordar com a política encerre a conta. Após a confirmação, a conta é encerrada e os dados do usuário são removidos: fotos, clientes, tipos de serviço, agendamentos, registros financeiros, notificações e logs. |
| RF55 | Controlar a Coleta de Localização | Permitir que o usuário ative ou desative a coleta de localização no Meu Perfil. A localização só é coletada com o consentimento do usuário, para o geofencing do plano Full e enquanto o recurso estiver ativo. Ao desativar, a coleta é interrompida imediatamente. |
| RF56 | Disponibilizar Contato de Privacidade | Exibir, na política e no Meu Perfil, o e-mail de suporte para dúvidas sobre privacidade e tratamento de dados: suporte.dayplannio@gmail.com. |
| RF57 | Encerrar Conta do Prestador | Permitir que o prestador encerre sua conta pelo aplicativo mobile, por meio da área "Meu Perfil". Antes da confirmação, o sistema deve exibir um aviso informando que o encerramento da conta é permanente e que os dados associados à conta serão removidos. Após a confirmação, a conta deve ser encerrada e o prestador deve ser desconectado do aplicativo. |
| RF58 | Encerrar Conta do Cliente | Permitir que o cliente encerre sua conta pelo portal Web, por meio da área "Meu Perfil". Antes da confirmação, o sistema deve exibir um aviso informando que o encerramento da conta é permanente e que os dados associados à conta serão removidos. Após a confirmação, a conta deve ser encerrada e o cliente deve ser desconectado do portal. |
| RF59 | Encerrar Conta por Solicitação ao Administrador | Permitir que o usuário solicite o encerramento de sua conta por meio do e-mail de suporte do sistema. Após receber e verificar a solicitação, o administrador poderá realizar o encerramento da conta, removendo os dados associados conforme as regras de privacidade e proteção de dados. |

### 2.2 Requisitos não funcionais

| ID | Requisito | Descrição |
| --- | --- | --- |
| RNF1 | Desempenho | A aplicação deve ter carregamento rápido, mesmo em conexões de internet mais lentas, para garantir uma boa experiência de usuário. |
| RNF2 | Usabilidade | A interface da aplicação deve ser intuitiva e fácil de usar, com navegação clara e design responsivo, para que os usuários possam encontrar facilmente as informações de interesse. |
| RNF3 | Manutenibilidade | O código-fonte da aplicação deve ser bem documentado, seguindo padrões de codificação e boas práticas de desenvolvimento, para facilitar a manutenção e futuras atualizações do sistema. |
| RNF4 | Privacidade e proteção de dados | Os dados pessoais devem ser tratados conforme a Política de Privacidade: não são vendidos nem alugados, ficam em serviços protegidos com acesso restrito, a senha não é exposta em texto puro, a localização só é coletada com o consentimento do usuário e o usuário pode solicitar a exclusão da conta e dos seus dados. |

### 2.3 Regras de negócio principais

- O acesso às funcionalidades depende do plano ativo do prestador (Básico, Profissional ou Full); o plano só é ativado após aprovação do administrador, exceto o teste gratuito de 7 dias no plano Full.
- Lucro bruto é a soma dos valores cobrados dos agendamentos concluídos; lucro líquido é o bruto menos o custo de material.
- Fotos só aparecem no portfólio público se forem marcadas como públicas pelo prestador e aprovadas pelo administrador.
- A localização só é coletada com consentimento e enquanto o recurso estiver ativo.
- Ao encerrar a conta, os dados do usuário (fotos, clientes, serviços, agendamentos, financeiro, notificações e logs) são removidos.

---

## 3 ARQUITETURA

### 3.1 Visão geral

O DayPlannio adota uma **arquitetura cliente-servidor em camadas**, com uma API REST central consumida por dois clientes: o aplicativo mobile e o portal web. Toda regra de negócio, autenticação e acesso a dados fica na API; os clientes cuidam apenas da apresentação e da coleta de dados (no aplicativo, o GPS). Os dados persistem na nuvem.

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/modelo-arquitetura.png" alt="Figura 1 – Arquitetura do DayPlannio" width="60%">
  <br>
  <em>Figura 1 – Arquitetura do DayPlannio</em>
</p>

### 3.2 Componentes

| Componente | Tecnologia | Responsabilidade |
| --- | --- | --- |
| DayPlannio.App | .NET MAUI, MVVM, SQLite | Interface do prestador, sessão local, coleta de GPS a cada 30 s e serviço em primeiro plano no Android. |
| DayPlannio.Web | ASP.NET MVC (Razor) | Portal do cliente, portal do administrador e portfólio público; consome a API com chave de acesso. |
| DayPlannio.Api | ASP.NET Web API (.NET 10) | Regras de negócio, autenticação, controle de plano, geofencing, relatórios e integrações. |
| MongoDB Atlas | MongoDB | Usuários, clientes, tipos de serviço, agendamentos, financeiro, fotos, notificações e logs. |
| Azure Blob Storage | Azure.Storage.Blobs | Imagens do portfólio. |
| Serviço de e-mail | MailKit / SMTP | Códigos de recuperação de senha e avisos. |
| Geocodificação | Nominatim (OpenStreetMap) | Converte o endereço do atendimento em latitude e longitude. |

### 3.3 Estrutura interna da API

| Camada | Itens | Função |
| --- | --- | --- |
| Controllers | Account, Users, Clientes, Agendamentos, TiposServico, Financeiros, Fotos, Tracking, Admin | Expõem os endpoints REST (api/...). |
| Services | PlanoService, ExigirAssinaturaAtivaFilter, LogService, EmailService, RelatorioPdfService, GeocodingService, AzureBlobStorageService, PlanoRenovacaoService | Regras transversais, integrações e tarefa em segundo plano para renovação de planos. |
| Helpers | SenhaHelper, ContaClienteHelper, ContaPrestadorHelper | Hash de senha, senha provisória e rotinas de encerramento de conta. |
| Models / Data | Agendamento, Cliente, TipoServico, Financeiro, Foto, Notificacao, Log, ApplicationUser; ContextMongodb | Entidades persistidas e acesso às coleções. |

### 3.4 Decisões arquiteturais

| Decisão | Justificativa |
| --- | --- |
| API central única | Evita duplicar regras entre aplicativo e portal; um ponto para autenticação, logs e controle de plano. |
| .NET MAUI no aplicativo | Uma base de código para Android e iOS, na mesma linguagem (C#) da API e do portal. |
| MongoDB (NoSQL) | Esquema flexível, boa escala e hospedagem gerenciada no Atlas. |
| Controle de plano no servidor | O filtro ExigirAssinaturaAtivaFilter e o PlanoService bloqueiam recursos fora do plano, sem depender do cliente. |
| Geofencing avaliado no servidor | O aplicativo apenas envia a posição; a API decide o status do atendimento, mantendo a regra em um só lugar e facilitando testes. |
| Imagens em Blob Storage | Mantém o banco leve e separa arquivos de dados. |

### 3.5 Segurança

- **Autenticação:** ASP.NET Identity com armazenamento no MongoDB; perfil Admin; middleware de chave de API (X-Api-Key) para o portal web.
- **Senhas:** nunca em texto puro; o hash dos clientes usa PBKDF2 (SHA-256, 100 mil iterações, salt aleatório).
- **Autorização por plano:** verificada em cada recurso restrito (por exemplo, o rastreamento exige plano Full).
- **Privacidade:** consentimento obrigatório, controle da coleta de localização, encerramento de conta com remoção de dados.
- **Segredos:** strings de conexão, chaves e senhas ficam em arquivos de configuração locais, fora do repositório; apenas o modelo appsettings.example.json é versionado.
- **Auditoria:** ações relevantes são registradas pelo LogService e consultadas pelo administrador.

### 3.6 Fluxo do geofencing

- O prestador ativa a coleta de localização (consentimento) e o aplicativo inicia o serviço de rastreamento.
- A cada 30 segundos o aplicativo consulta o estado na API; havendo atendimento próximo, coleta o GPS e envia a posição (POST api/tracking/ping).
- A API calcula a distância (fórmula de Haversine) até o endereço geocodificado de cada agendamento candidato, dentro de uma janela de ±24 horas.
- Conforme a posição, a API atualiza o status do atendimento e registra o evento no log.

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/como-funciona-geofencing.png" alt="Figura 2 – Modelo Geofencing do DayPlannio" width="60%">
  <br>
  <em>Figura 2 – Modelo Geofencing do DayPlannio</em>
</p>

| Parâmetro | Valor padrão | Configuração |
| --- | --- | --- |
| Raio do geofence | 50 m (opções: 50, 100, 200 e 500 m) | Definido por agendamento |
| Tolerância de saída | 15 m | Geofencing:ToleranciaSaidaMetros |
| Tolerância de pausa | 30 minutos | Geofencing:ToleranciaPausaMinutos |
| Janela de busca | ±24 horas | Geofencing:JanelaHoras |
| Intervalo de coleta | 30 segundos | Aplicativo |

---

## 4 PROTÓTIPOS

O protótipo de interface foi elaborado no **Figma**, permitindo validar conceitos e interações antes da implementação. O protótipo navegável está disponível em:

[Protótipo DayPlannio no Figma](https://www.figma.com/design/RR434kEsmEtgwjU8zBkahQ/DayPlannio?node-id=0-1&t=JTmqGH7lg5nEMBKP-1)

### 4.1 Identidade visual

O design do DayPlannio utiliza uma paleta em tons de verde-azulado, com cor principal #006260, e um isotipo próprio, com foco em interface limpa e navegação simples (RNF2). No aplicativo mobile, a tipografia adotada é a Open Sans, escolhida pela boa legibilidade em telas pequenas. No portal web, a fonte utilizada é a Segoe UI, com a fonte padrão do sistema como alternativa, o que garante boa leitura e carregamento rápido nos navegadores.

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/isotipo.png" alt="Figura 3 – Isotipo do DayPlannio" width="60%">
  <br>
  <em>Figura 3 – Isotipo do DayPlannio</em>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/paleta-cores.png" alt="Figura 4 – Paleta de cores" width="60%">
  <br>
  <em>Figura 4 – Paleta de cores</em>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/fonte-open-sans.png" alt="Figura 5 – Fonte Open Sans (aplicativo mobile)" width="60%">
  <br>
  <em>Figura 5 – Fonte Open Sans (aplicativo mobile)</em>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/fonte.png" alt="Figura 6 – Fonte Segoe UI (site web)" width="60%">
  <br>
  <em>Figura 6 – Fonte Segoe UI (site web)</em>
</p>

### 4.2 Modelo de navegação

<p align="center">
  <img src="https://raw.githubusercontent.com/DayPlannio/Documentos/master/imagens/modelo-navegacao.png" alt="Figura 7 – Modelo de Navegação do DayPlannio" width="60%">
  <br>
  <em>Figura 7 – Modelo de Navegação do DayPlannio</em>
</p>

---

## 5 BACKLOG

O backlog está organizado em **épicos** derivados dos requisitos funcionais e priorizado pelo método **MoSCoW** (M = deve ter, S = deveria ter, C = poderia ter). O acompanhamento é feito em quadro Kanban no Trello (A Fazer, Em Andamento, Concluído). A situação corresponde ao estado do código em 03/10/2026.

| Épico | História de usuário | RFs | Prior. | Plano | Situação |
| --- | --- | --- | --- | --- | --- |
| E1 Conta e acesso | Como prestador, quero me cadastrar, entrar e recuperar minha senha para usar o app com segurança. | RF6–RF8 | M | Todos | Concluído |
| E1 Conta e acesso | Como prestador, quero ver e editar meu perfil, definindo se telefone e cidade ficam públicos. | RF9, RF10, RF41, RF43 | S | Todos | Concluído |
| E2 Agenda | Como prestador, quero criar, editar, cancelar e concluir agendamentos. | RF1–RF4 | M | Básico | Concluído |
| E2 Agenda | Como prestador, quero visualizar minha agenda para planejar a rotina. | RF5 | M | Básico | Concluído |
| E3 Clientes | Como prestador, quero cadastrar, listar e editar clientes e consultar o histórico. | RF11–RF14 | M | Básico | Concluído |
| E4 Serviços | Como prestador, quero cadastrar, listar, editar e excluir tipos de serviço. | RF15–RF18 | M | Básico | Concluído |
| E5 Financeiro | Como prestador, quero registrar entradas e saídas, editá-las e excluí-las. | RF19–RF21 | S | Profissional | Concluído |
| E5 Financeiro | Como prestador, quero ver lucro bruto e líquido por período e exportar relatório em PDF. | RF22–RF24 | S | Profissional | Concluído |
| E5 Financeiro | Como prestador, quero ver métricas dos meus serviços. | RF39 | C | Profissional | Concluído |
| E6 Portal do cliente | Como prestador, quero gerar e-mail e senha provisória ao cadastrar um cliente. | RF29 | S | Full | Concluído |
| E6 Portal do cliente | Como cliente, quero entrar, trocar a senha, cadastrar e-mail secundário e recuperar senha. | RF30–RF34 | S | Full | Concluído |
| E6 Portal do cliente | Como cliente, quero ver métricas dos serviços feitos para mim. | RF38 | C | Full | Concluído |
| E7 Portfólio | Como prestador, quero enviar e excluir fotos, públicas ou privadas. | RF35, RF36 | C | Full | Concluído |
| E7 Portfólio | Como visitante, quero ver o portfólio público sem login. | RF37 | C | Full | Concluído |
| E8 Administração | Como administrador, quero ver logs, clientes e prestadores. | RF25–RF28 | S | — | Concluído |
| E8 Administração | Como administrador, quero aprovar ou recusar fotos antes de irem ao portfólio. | RF42 | S | — | Concluído |
| E9 Planos | Como prestador, quero ver, assinar, alterar e cancelar planos, com 7 dias de teste. | RF44–RF47, RF49 | S | Todos | Concluído |
| E9 Planos | Como sistema, quero liberar ou bloquear funcionalidades conforme o plano. | RF48 | M | Todos | Concluído |
| E9 Planos | Como administrador, quero aprovar ou recusar solicitações de plano. | RF50 | S | — | Concluído |
| E10 Privacidade | Como usuário, quero ler a política, concordar no cadastro, relê-la e encerrar minha conta. | RF51–RF54, RF56–RF59 | M | Todos | Concluído |
| E11 Geofencing | Como prestador, quero que o endereço do atendimento seja convertido em coordenadas ao salvar o agendamento. | RF40 | M | Full | Em andamento |
| E11 Geofencing | Como prestador, quero escolher o raio (50, 100, 200 ou 500 m) ao criar ou editar um agendamento. | RF40 | M | Full | Em andamento |
| E11 Geofencing | Como sistema, quero atualizar automaticamente os status do atendimento conforme a posição do prestador. | RF40 | M | Full | Em andamento |
| E11 Geofencing | Como prestador, quero ativar ou desativar a coleta de localização no Meu Perfil. | RF55 | M | Full | Em andamento |
| E12 Qualidade | Como equipe, queremos testes automatizados (xUnit) das regras de negócio e do geofencing. | RNF3 | M | — | Planejado |
| E12 Qualidade | Como equipe, queremos executar os testes automaticamente a cada envio ao GitHub. | RNF3 | S | — | Planejado |
| E12 Qualidade | Como equipe, queremos um roteiro de testes manuais do aplicativo com GPS simulado. | RF40 | S | — | Planejado |

### 5.1 Próximos passos

- Concluir o épico E11 (geocodificação ao salvar, seletor de raio, status automáticos e controle de localização no perfil).
- Criar o projeto de testes DayPlannio.Tests e implementar os casos prioritários da estratégia de testes.
- Configurar a execução automática dos testes (GitHub Actions).
- Atualizar a documentação com os resultados.

### 5.2 Definição de pronto

Um item é considerado pronto quando atende ao requisito descrito, foi testado (automatizado ou por roteiro manual), compila sem erros, não expõe credenciais e teve a documentação atualizada.

---

## 6 FUNCIONALIDADES

A tabela resume as funcionalidades por módulo, com a plataforma em que estão disponíveis e o plano mínimo necessário.

| Módulo | Funcionalidade | Plataforma | Plano mínimo | RFs |
| --- | --- | --- | --- | --- |
| Acesso | Cadastro com aceite da política, login e recuperação de senha por código | App | Todos | RF6–RF8, RF52 |
| Perfil | Visualizar e editar perfil; visibilidade de telefone e cidade; releitura da política; encerrar conta | App | Todos | RF9, RF10, RF41, RF43, RF53, RF57 |
| Agenda | Criar, editar, cancelar, concluir e excluir agendamentos; visualizar agenda | App | Básico | RF1–RF5 |
| Clientes | Cadastrar, listar, editar, excluir e ver histórico | App | Básico | RF11–RF14 |
| Serviços | Cadastrar, listar, editar e excluir tipos de serviço | App | Básico | RF15–RF18 |
| Financeiro | Entradas e saídas; lucro bruto e líquido (diário, semanal e mensal); relatório em PDF | App | Profissional | RF19–RF24 |
| Métricas do prestador | Ganho por tipo de serviço, valor médio e outros indicadores | App | Profissional | RF39 |
| Planos | Ver planos, assinar, alterar, cancelar; teste gratuito de 7 dias | App | Todos | RF44–RF49 |
| Portal do cliente | Login com senha provisória, troca de senha, e-mail secundário, perfil, histórico, métricas e encerrar conta | Web | Full | RF29–RF34, RF38, RF58 |
| Fotos e portfólio | Enviar e excluir fotos (públicas ou privadas); portfólio público | App e Web | Full | RF35–RF37 |
| Geofencing | Geocodificação do endereço, raio configurável, status automáticos e coleta de localização com consentimento | App e API | Full | RF40, RF55 |
| Administração | Login, logs, clientes, prestadores, aprovação de planos e fotos, encerramento de contas | Web | — | RF25–RF28, RF42, RF50, RF59 |
| Privacidade | Política, consentimento, contato de suporte, encerramento de conta | App e Web | Todos | RF51–RF59 |
| Notificações | Avisos ao prestador (decisões sobre plano e fotos, entre outros) | App | Todos | — |

---

## 7 ESTRATÉGIA INICIAL DE TESTES

### 7.1 Objetivos

Garantir que as regras de negócio funcionem conforme os requisitos, detectar falhas cedo, evitar regressões a cada alteração e dar segurança para evoluir o sistema. A estratégia prioriza as regras de maior risco: **geofencing (RF40), controle de acesso por plano (RF48), cálculo financeiro (RF22 e RF23) e segurança de senhas**.

### 7.2 Escopo

| Dentro do escopo | Fora do escopo (nesta etapa) |
| --- | --- |
| Regras de negócio da API (planos, financeiro, geofencing, senhas) | Testes de carga e desempenho |
| Endpoints principais da API (integração) | Testes automatizados de interface do aplicativo |
| Fluxos críticos do aplicativo e do portal (manuais) | Testes de compatibilidade em muitos modelos de aparelho |
| Validação dos modelos de dados | Testes de segurança avançados |

### 7.3 Níveis de teste e ferramentas

| Nível | O que verifica | Ferramenta | Execução |
| --- | --- | --- | --- |
| Unitário | Funções e regras isoladas, sem banco nem rede | xUnit (dotnet test) com Moq para simular dependências | Automática |
| Integração | Endpoints da API com a aplicação em memória e banco de teste | xUnit + WebApplicationFactory e MongoDB de teste (Mongo2Go ou Testcontainers) | Automática |
| Sistema / Aceitação | Fluxos completos nas telas do aplicativo e do portal | Roteiros manuais; GPS simulado no emulador Android | Manual |
| Regressão | Reexecução dos testes a cada alteração | GitHub Actions executando dotnet test | Automática |

A cobertura será medida com **coverlet**. Meta inicial: **70% das linhas nas classes de regra de negócio** (planos, financeiro, geofencing e helpers), sem exigir cobertura de controllers e telas.

### 7.4 Organização dos testes

Será criado o projeto DayPlannio.Tests (xUnit) referenciando DayPlannio.Api, seguindo estes padrões:

- Estrutura **Arrange – Act – Assert**, um comportamento por teste.
- Nomes no formato Metodo_Cenario_ResultadoEsperado.
- Testes independentes, sem dependência de ordem; nenhum teste usa o banco de produção.
- Dados de exemplo fixos (coordenadas, valores e datas) para resultados reproduzíveis.
