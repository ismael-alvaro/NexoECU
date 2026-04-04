# NexoECU

PT-BR | [EN](#EN)

---

## PT-BR

Repositório de firmware para um sistema embarcado com 2 ECUs, baseado em ESP32 e PlatformIO.

## Visao Geral

Este projeto contem duas unidades principais:

- ECU Transmissora (MPU): coleta e transmite dados de sensores, GPS e LoRa, alem de enviar status por Bluetooth.
- ECU Intermediaria (SCU): recebe/organiza dados via CAN, faz logging em micro-SD e envia pacotes por GPRS/MQTT.

## Estrutura do Repositorio

- MPU_2024-Transmitter/
	- Firmware da ECU transmissora.
	- Framework: Arduino (ESP32).
	- Funcoes principais: LoRa, CAN, GPS, Bluetooth debug.
- espidf_ecu-main/
	- Firmware da ECU intermediaria.
	- Framework: Arduino (ESP32).
	- Funcoes principais: CAN logging, SD state machine, conectividade GPRS/MQTT.

## Dependencias

Ambos os projetos usam PlatformIO e ESP32 (board: esp32doit-devkit-v1).

Dependencias principais:

- MPU_2024-Transmitter
	- CircularBuffer
	- TinyGPSPlus-ESP32
	- ArduinoJson
- espidf_ecu-main
	- TinyGSM
	- PubSubClient
	- CANmsg (via GitHub)

## Como Compilar e Gravar

Pre-requisitos:

- VS Code com extensao PlatformIO
- Drivers USB da placa ESP32

Passos (exemplo com terminal):

1. Compilar ECU transmissora:
	 - `pio run -d MPU_2024-Transmitter`
2. Gravar ECU transmissora:
	 - `pio run -d MPU_2024-Transmitter -t upload`
3. Monitor serial ECU transmissora:
	 - `pio device monitor -d MPU_2024-Transmitter -b 115200`
4. Compilar ECU intermediaria:
	 - `pio run -d espidf_ecu-main`
5. Gravar ECU intermediaria:
	 - `pio run -d espidf_ecu-main -t upload`
6. Monitor serial ECU intermediaria:
	 - `pio device monitor -d espidf_ecu-main -b 115200`

Se houver mais de uma porta serial conectada, defina `upload_port` e `monitor_port` nos respectivos `platformio.ini`.

## Fluxo Funcional (Resumo)

- ECU Transmissora:
	- Inicializa LoRa, CAN, GPS e Bluetooth.
	- Publica dados periodicamente e transmite mensagens de status.
- ECU Intermediaria:
	- Inicializa CAN, SD e modem.
	- Executa maquinas de estado separadas para SD e conectividade.
	- Publica pacotes via MQTT e envia status de diagnostico via CAN.

## Observacoes

- Existe conteudo de backup em `espidf_ecu-main/backup/`.
- A configuracao atual pressupoe uso de FreeRTOS tasks no ESP32.
- Para ajustes de hardware (pinos, IDs CAN, pacotes), consulte headers em `include/` e `lib/Defines/` de cada modulo.

---

## EN

Firmware repository for a 2-ECU embedded system based on ESP32 and PlatformIO.

## Overview

This project contains two main units:

- Transmitter ECU (MPU): collects and transmits sensor, GPS, and LoRa data, and exposes status over Bluetooth.
- Intermediate ECU (SCU): handles CAN data flow, logs to micro-SD, and pushes packets through GPRS/MQTT.

## Repository Structure

- MPU_2024-Transmitter/
	- Transmitter ECU firmware.
	- Framework: Arduino (ESP32).
	- Main features: LoRa, CAN, GPS, Bluetooth debug.
- espidf_ecu-main/
	- Intermediate ECU firmware.
	- Framework: Arduino (ESP32).
	- Main features: CAN logging, SD state machine, GPRS/MQTT connectivity.

## Dependencies

Both projects use PlatformIO and ESP32 (board: esp32doit-devkit-v1).

Main dependencies found:

- MPU_2024-Transmitter
	- CircularBuffer
	- TinyGPSPlus-ESP32
	- ArduinoJson
- espidf_ecu-main
	- TinyGSM
	- PubSubClient
	- CANmsg (GitHub)

## Build and Upload

Prerequisites:

- VS Code with PlatformIO extension
- ESP32 USB drivers

Steps (terminal example):

1. Build transmitter ECU:
	 - `pio run -d MPU_2024-Transmitter`
2. Upload transmitter ECU:
	 - `pio run -d MPU_2024-Transmitter -t upload`
3. Open serial monitor (transmitter):
	 - `pio device monitor -d MPU_2024-Transmitter -b 115200`
4. Build intermediate ECU:
	 - `pio run -d espidf_ecu-main`
5. Upload intermediate ECU:
	 - `pio run -d espidf_ecu-main -t upload`
6. Open serial monitor (intermediate):
	 - `pio device monitor -d espidf_ecu-main -b 115200`

If multiple serial devices are connected, set `upload_port` and `monitor_port` in each `platformio.ini`.

## Functional Flow (Summary)

- Transmitter ECU:
	- Initializes LoRa, CAN, GPS, and Bluetooth.
	- Periodically publishes telemetry and status messages.
- Intermediate ECU:
	- Initializes CAN, SD, and modem.
	- Runs dedicated state machines for SD and connectivity.
	- Publishes MQTT packets and sends diagnostic status over CAN.

## Notes

- There is backup content in `espidf_ecu-main/backup/`.
- Current setup relies on FreeRTOS tasks on ESP32.
- For hardware mapping and protocol tuning (pins, CAN IDs, packets), check `include/` and `lib/Defines/` in each module.
