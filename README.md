
# Firefighters SRE

Esta arquitetura é projetada para simular um sistema de gerenciamento e monitoramento de um prédio, onde diferentes microsserviços são responsáveis por monitorar e gerenciar aspectos específicos, como acesso de pessoas, mobilidade (uso de escadas e elevadores), ambiente e segurança do prédio. A ideia é usar este cenário como uma analogia para um ambiente SRE (Site Reliability Engineering), onde diferentes componentes e serviços trabalham juntos para garantir a segurança, eficiência e confiabilidade de um ambiente.
## Microsserviços
- 🛎️ Microsserviço de Acesso
- 🚶‍♂️🔝 Microsserviço de Mobilidade (Escada + Elevador)
- 🏠 Microsserviço de Prédio (Ambiente + Andar)
- 🛡️ Microsserviço de Segurança
## 🛎️ Microsserviço de Acesso

### **Responsabilidade**:
Gerencia a entrada e saída de pessoas do prédio, garantindo a segurança e organização do fluxo de pessoas.

### **Funcionalidades**:
#### 1. Registrar Entrada:
- Captura detalhes da pessoa ao entrar, como nome, hora de entrada e destino.
- Pode se integrar com sistemas de controle de acesso, como leitores de cartão ou reconhecimento facial.

#### 2. Registrar Saída:
- Registra a hora de saída da pessoa.
- Pode ser usado para calcular o tempo de permanência ou para verificar quem ainda está no prédio.

### **Estrutura de Dados**:
- **Pessoa**: { id, nome, tipo (visitante/funcionário), contato }
- **RegistroAcesso**: { registroId, pessoaId, horaEntrada, horaSaída, tipoPessoa, destino }

## 🚶‍♂️🔝 Microsserviço de Mobilidade (Escada + Elevador)

### **Responsabilidade**:
Monitora e gerencia o uso das escadas e elevadores, garantindo a segurança e eficiência na movimentação vertical dentro do prédio.

### **Funcionalidades**:
#### 1. Detecção de Movimento nas Escadas:
- Utiliza sensores para detectar movimento nas escadas.
- Registra a direção do movimento (subindo ou descendo) e a zona específica da escada.

#### 2. Alertas de Segurança nas Escadas:
- Emite alertas em situações atípicas ou perigosas, como movimento na escada durante um alarme de incêndio.

#### 3. Gerenciamento de Elevadores:
- Controla a lógica de funcionamento dos elevadores.
- Responde a solicitações de chamada de elevador e direciona elevadores para os andares solicitados.
- Monitora a saúde e manutenção dos elevadores.

### **Estrutura de Dados**:
- **LogMovimento**: { logId, hora, local (andar ou zona da escada), direção (subindo/descendo) }
- **Elevador**: { id, status, andarAtual }
- **LogElevador**: { logId, elevadorId, hora, andarInicial, andarFinal }

## 🏠 Microsserviço de Prédio (Ambiente + Andar)

### **Responsabilidade**:
Gerencia informações do prédio, como temperatura, qualidade do ar e ocupação. Também gerencia detalhes específicos de cada andar.

### **Funcionalidades**:
#### 1. Monitorar Ocupação:
- Verifica quantas pessoas estão em cada andar.
- Pode se integrar com sistemas de reserva de sala ou sistemas de segurança para obter dados em tempo real.

#### 2. Ajustar Ambiente:
- Controla a temperatura, iluminação e qualidade do ar.
- Pode se integrar com sistemas de automação predial.

#### 3. Monitorar Gases Perigosos:
- Detecta a presença de gases perigosos, como monóxido de carbono ou gás natural.
- Emissão de alertas em níveis críticos.

#### 4. Monitorar Integridade Estrutural:
- Utiliza sensores para detectar vibrações ou movimentos estruturais que podem indicar problemas.
- Emissão de alertas em situações anômalas.

#### 5. Monitorar Fumaça:
- Utiliza sensores para detectar a presença de fumaça.
- Emissão de alertas de incêndio.

### **Estrutura de Dados**:
- **Andar**: { id, númeroAndar, salas, instalações }
- **Ambiente**: { andarId, temperatura, qualidadeAr, iluminação }
- **Gases**: { andarId, tipoGás (monóxido de carbono, gás natural, etc.), concentração }
- **Integridade**: { andarId, tipoEvento (vibração, movimento), intensidade }
- **Fumaça**: { andarId, densidadeFumaça, tipoFumaça }

## 🛡️ Microsserviço de Segurança

### **Responsabilidade**:
Monitora a segurança do prédio, integrando-se com câmeras, alarmes e outros sistemas de segurança.

### **Funcionalidades**:
#### 1. Monitorar Câmeras:
- Acessa feeds em tempo real de câmeras de segurança.
- Pode detectar movimentos suspeitos ou integrar-se com sistemas de reconhecimento facial.

#### 2. Gerenciar Alarmes:
- Ativa ou desativa alarmes.
- Responde a eventos de segurança, como detecção de fumaça ou intrusão.

### **Estrutura de Dados**:
- **Câmera**: { id, localização, status }
- **Alarme**: { id, tipo (incêndio, intruso), status, localização }
## Banco de Dados

### 1. Banco de Dados da Área Externa

Este banco de dados armazenará informações relacionadas à área externa do prédio.

**Tabelas:**

- **AccessRecord:**
  - `recordId` (primary key)
  - `personId`
  - `entryTime`
  - `exitTime`
  - `personType` (visitor/employee)
  - `destination` (floor or department)

- **Person:**
  - `id` (primary key)
  - `name`
  - `type` (visitor/employee)
  - `contact`

- **Floor:**
  - `id` (primary key)
  - `floorNumber`
  - `rooms`
  - `facilities`

- **Environment:**
  - `floorId` (foreign key)
  - `temperature`
  - `airQuality`
  - `lighting`

- **StairMovementLog:**
  - `logId` (primary key)
  - `time`
  - `location` (floor or stairwell area)
  - `direction` (up/down)

- **Camera:**
  - `id` (primary key)
  - `location`
  - `status`

- **Alarm:**
  - `id` (primary key)
  - `type` (fire, intruder)
  - `status`
  - `location`

- **SecurityLog:**
  - `logId` (primary key)
  - `eventType`
  - `time`
  - `location`
  - `description`

## Tópicos Kafka

### 1. Lobby
Este tópico coleta eventos relacionados às atividades no saguão do prédio, como a entrada e saída de pessoas.
### 2. Elevador
Captura eventos associados às operações do elevador, incluindo movimentação entre andares, abertura/fechamento de portas e quaisquer anomalias.
### 3. Escada
Reúne dados sobre o uso da escada, monitorando os movimentos dos indivíduos que usam as escadas, quaisquer obstruções e alertas de segurança.
### 4. Prédio
Centraliza eventos sobre a saúde e atividades gerais do prédio, englobando métricas ambientais, alertas de segurança e verificações de saúde do sistema.

## Eventos
### 1. Evento de Entrada
- **Payload**:
    ```json
    {
      "pessoaId": "12345",
      "horaEntrada": "2023-09-27T09:00:00Z",
      "destino": "Andar 5"
    }
    ```
- **Tópico**: `lobby`
- **Microsserviço Leitor**: Microsserviço de Acesso

### 2. Evento de Saída
- **Payload**:
    ```json
    {
      "pessoaId": "12345",
      "horaSaída": "2023-09-27T17:00:00Z"
    }
    ```
- **Tópico**: `lobby`
- **Microsserviço Leitor**: Microsserviço de Acesso

### 3. Evento de Uso de Elevador
- **Payload**:
    ```json
    {
      "elevadorId": "E1",
      "horaChamada": "2023-09-27T09:05:00Z",
      "andarChamada": 1,
      "destino": 5
    }
    ```
- **Tópico**: `elevador`
- **Microsserviço Leitor**: Microsserviço de Mobilidade

### 4. Evento de Uso de Escada
- **Payload**:
    ```json
    {
      "zonaEscada": "Z1",
      "horaMovimento": "2023-09-27T09:06:00Z",
      "direção": "subindo"
    }
    ```
- **Tópico**: `escada`
- **Microsserviço Leitor**: Microsserviço de Mobilidade

### 5. Evento de Alteração Ambiental
- **Payload**:
    ```json
    {
      "andarId": 5,
      "temperatura": 23,
      "qualidadeAr": "boa",
      "iluminação": "normal"
    }
    ```
- **Tópico**: `predio`
- **Microsserviço Leitor**: Microsserviço de Prédio

### 6. Evento de Gás Perigoso
- **Payload**:
    ```json
    {
      "andarId": 5,
      "tipoGás": "monóxido de carbono",
      "concentração": "alto"
    }
    ```
- **Tópico**: `predio`
- **Microsserviço Leitor**: Microsserviço de Prédio

### 7. Evento de Integridade Estrutural
- **Payload**:
    ```json
    {
      "andarId": 5,
      "tipoEvento": "vibração",
      "intensidade": "alta"
    }
    ```
- **Tópico**: `predio`
- **Microsserviço Leitor**: Microsserviço de Prédio

### 8. Evento de Fumaça
- **Payload**:
    ```json
    {
      "andarId": 5,
      "densidadeFumaça": "alta",
      "tipoFumaça": "madeira queimada"
    }
    ```
- **Tópico**: `predio`
- **Microsserviço Leitor**: Microsserviço de Prédio e Microsserviço de Segurança

### 9. Evento de Alerta de Segurança
- **Payload**:
    ```json
    {
      "localização": "Andar 5, Sala 501",
      "horaEvento": "2023-09-27T14:15:00Z",
      "tipoAlerta": "intruso"
    }
    ```
- **Tópico**: `predio`
- **Microsserviço Leitor**: Microsserviço de Segurança

### 10. Evento de Falha de Sistema
- **Payload**:
    ```json
    {
      "sistema": "Microsserviço de Acesso",
      "horaFalha": "2023-09-27T15:30:00Z",
      "descrição": "Falha na leitura do cartão de acesso"
    }
    ```
- **Tópico**: `predio`
- **Microsserviço Leitor**: Equipe de TI ou manutenção
