# 🛰️ Specto — Proxi (Edge Computing)

> Módulo de sensoriamento local do projeto **Specto** — laudos territoriais por sensoriamento remoto para todos.  
> Global Solution FIAP 2026 | Indústria Espacial | Engenharia de Software

---

## Sobre este repositório

Este repositório contém o componente **Edge / Arduino** do Specto, desenvolvido como parte da disciplina de Edge Computing da Global Solution FIAP 2026.

O **Proxi** é um dispositivo de campo que funciona como uma **camada de dupla checagem** dos dados orbitais. O satélite Sentinel-2 analisa o território de cima, capturando índices espectrais (NDVI, NDWI, NBR) em escala regional. O Proxi, instalado no ponto físico de interesse, coleta temperatura, luminosidade e umidade do solo localmente — permitindo cruzar o que o satélite detectou com o que está acontecendo de fato no terreno.

Essa combinação torna o laudo do Specto mais robusto: anomalias identificadas orbitalmente podem ser confirmadas (ou descartadas) com base nas leituras do sensor no campo.

---

## Hardware

| Componente | Pino | Função |
|---|---|---|
| Sensor LDR | A0 | Luminosidade (% de 0 a 100) |
| Sensor TMP36 | A1 | Temperatura (°C) |
| Sensor de umidade resistivo | A2 | Umidade do solo (% de 0 a 100) |
| LED Verde | D13 | Status: NORMAL |
| LED Amarelo | D12 | Status: ATENÇÃO |
| LED Vermelho | D11 | Status: CRÍTICO |
| LCD I2C 16x2 | SDA/SCL | Exibição dos dados em tempo real |

O circuito completo pode ser visualizado no arquivo [`SetupProxi.png`](./SetupProxi.png) e simulado no [Wokwi](https://wokwi.com) ou Tinkercad.

---

## Como funciona

### Leitura e conversão dos sensores

- **LDR**: valor analógico mapeado para 0–100% de luminosidade.
- **TMP36**: tensão convertida para graus Celsius via `map()`.
- **Umidade resistiva**: valor analógico invertido (alto = seco, baixo = úmido) e mapeado para 0–100%.

### Classificação de status

O sistema avalia os três sensores simultaneamente e classifica o estado ambiental em três níveis:

| Status | Condição | LED |
|---|---|---|
| `NORMAL` | Todos os valores dentro do esperado | 🟢 Verde |
| `ATENÇÃO` | Umidade < 60%, luminosidade < 60% ou temperatura > 30°C | 🟡 Amarelo |
| `CRÍTICO` | Umidade < 30%, luminosidade < 30% ou temperatura > 38°C | 🔴 Vermelho |

### Saídas

- **LCD 16x2**: exibe temperatura, luminosidade, umidade e classificação em tempo real.
- **Monitor Serial (9600 baud)**: loga os valores formatados a cada 2 segundos.
- **LEDs**: indicação visual imediata do status geral.

---

## Exemplo de saída serial

```
-------------------------
Temperatura : 27.50 C
Luminosidade: 72 %
Umidade     : 45 %
Status      : ATENCAO
```

---

## Estrutura do repositório

```
Specto-Proxi/
├── proxi.ino         # Código principal Arduino (C++)
├── SetupProxi.png    # Diagrama do circuito
└── README.md
```

---

## Conexão com o projeto Specto

O Specto é uma plataforma que processa imagens do satélite **Sentinel-2** (ESA/Copernicus) e entrega laudos territoriais em português, acessíveis a qualquer pessoa sem conhecimento técnico em sensoriamento remoto. Os índices calculados são:

| Índice | Fórmula | O que detecta |
|---|---|---|
| **NDVI** | (B08 − B04) / (B08 + B04) | Saúde e densidade da vegetação |
| **NDWI** | (B03 − B08) / (B03 + B08) | Umidade do solo e água superficial |
| **NBR** | (B08 − B12) / (B08 + B12) | Áreas queimadas e cicatrizes de incêndio |

O módulo **Proxi** é a camada de dupla checagem dessa cadeia: enquanto o satélite entrega a visão macro do território, o dispositivo Arduino coleta dados no ponto físico — temperatura, luminosidade e umidade — para validar e complementar o que foi identificado orbitalmente.

---

## ODS da ONU contemplados

- 🌱 **ODS 2** — Fome zero e agricultura sustentável
- 🏭 **ODS 9** — Indústria, inovação e infraestrutura
- ⚖️ **ODS 10** — Redução das desigualdades
- 🌍 **ODS 13** — Ação climática
- 🌿 **ODS 15** — Vida terrestre

---

## Autores

Projeto desenvolvido para a **Global Solution FIAP 2026** — Indústria Espacial.

| Nome | RM |
|---|---|
| Guilherme Pereira Ruiz da Silva | 573360 |
| Antonio do Nascimento Ferreira de Sousa | 573706 |
| Gustavo Leal | 569361 |
| Matheus Mendes Duarte da Silva | 569559 |
| Matheus Sato Oliveira do Prado | 569392 |

> *"O dado que estava no espaço. O laudo que chegou até você."*
