---
title: Installera Azure IoT Edge för Linux på Windows | Microsoft Docs
description: Azure IoT Edge-installationsanvisningar för Linux-behållare i Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: bb47a1b828084673961a6d2c5657793b4437f294
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160582"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Använd IoT Edge på Windows för att köra Linux-behållare

Testa IoT Edge-moduler för Linux-enheter som använder en Windows-dator. 

Windows-enheter bör endast köra Windows-behållare i ett produktionsscenario för. Det är dock ett vanligt utvecklingsscenario och skapa IoT Edge-moduler för Linux-enheter med en Windows-dator. IoT Edge-körningen för Windows kan du köra Linux-behållare för **tester och utveckling** syften. 

Den här artikeln innehåller steg för att installera Azure IoT Edge-körningen med Linux-behållare på din Windows x64 (AMD/Intel) system. Läs mer om IoT Edge runtime-installationsprogrammet, inklusive information om alla installationsparametrar i [installera Azure IoT Edge-körningen på Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Använd det här avsnittet för att granska om din Windows-enhet kan ha stöd för IoT Edge och förbereda den för en motor för behållaren före installationen. 

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

Azure IoT Edge med Linux-behållare kan köras på följande versioner av Windows: 
* Windows 10 Anniversary update (version 14393) eller senare
* Windows Server 2016 eller senare

Läs mer om vad som ingår i den senaste versionen av IoT Edge, [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

Om du vill installera IoT Edge på en virtuell dator kan aktivera kapslad virtualisering och allokera minst 2 GB minne. Hur du aktiverar kapslad virtualisering är olika beroende på hypervisorn din användning. För Hyper-V har virtuella datorer i generation 2 kapslad virtualisering aktiverad som standard. Det finns en växlingsknappen för att aktivera funktionen på den virtuella datorn för VMWare. 

### <a name="prepare-the-container-engine"></a>Förbereda container-motorn 

Azure IoT Edge förlitar sig på en [OCI-kompatibla](https://www.opencontainers.org/) container-motorn. Den största konfiguration skillnaden mellan Windows och Linux-behållare som körs på en Windows som datorn är att IoT Edge-installationen innehåller en runtime för Windows-behållare, men du måste ange din egen runtime för Linux-behållare innan du installerar IoT Edge. 

Om du vill konfigurera en Windows-dator för att utveckla och testa behållare för Linux enheter, kan du använda [Docker Desktop](https://www.docker.com/docker-windows) som din behållare. Du behöver installera Docker och konfigurera den att [använder Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) innan du installerar IoT Edge.  

Om din IoT Edge-enhet är en Windows-dator, kontrollerar du att den uppfyller de [systemkrav](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) för Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge-programvarupaket omfattas licensvillkoren finns i paket (i katalogen licens). Läs igenom licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

Ett PowerShell-skript hämtar och installerar Azure IoT Edge security daemon. Daemonen security startar sedan först av två moduler för körning, IoT Edge-agenten, vilket gör det möjligt för Fjärrdistribution av andra moduler. 

När du installerar IoT Edge-körningen för första gången på en enhet kan behöva du etablera enheten med en identitet från en IoT-hubb. En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av din IoT-hubb. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter du ställer in. 

Du kan läsa mer om olika installationsalternativ och parametrar i artikeln [installera Azure IoT Edge-körningen på Windows](how-to-install-iot-edge-windows.md). När du har Docker Desktop installerad och konfigurerad för Linux-behållare, installation av huvudsakliga skillnaden är att deklarera Linux med den **- ContainerOs** parametern. Exempel: 

1. Om du inte redan gjort registrera en ny IoT Edge-enhet och hämta enhetens anslutningssträng. Kopiera anslutningssträngen för att använda senare i det här avsnittet. Du kan slutföra det här steget med hjälp av följande verktyg:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Kör PowerShell som administratör.

3. Den **distribuera IoTEdge** kommandot kontrollerar att din Windows-dator på en version som stöds, aktiverar funktionen behållare och laddar sedan ned moby körningen (som inte används för Linux-behållare) och IoT Edge-körningen. Kommando standardinställningarna för Windows-behållare, deklarera så Linux som önskade behållaren operativsystem. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. IoT Core-enheter kan nu startar om automatiskt. Andra Windows 10 eller Windows Server-enheter kan du uppmanas att starta om. I så fall startar du om enheten nu. När enheten är redo, Kör PowerShell som administratör igen.

5. Den **initiera IoTEdge** kommando konfigurerar IoT Edge-körningen på din dator. Kommandot som standard manuell tilldelning med en anslutningssträng för enheten. Deklarera Linux som operativsystem önskade behållaren igen. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. När du uppmanas, anger du enhetens anslutningssträng som du hämtade i steg 1. Enhetens anslutningssträng associerar den fysiska enheten med ett enhets-ID i IoT Hub. 

   Enhetens anslutningssträng tar följande format och får inte innehålla citattecken: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verifiera installationen

Kontrollera status för IoT Edge-tjänsten. Det bör anges som körs.  

```powershell
Get-Service iotedge
```

Granska loggarna för tjänsten från de senaste 5 minuterna. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler. När du har en ny installation endast modulen bör du se körs är **edgeAgent**. När du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md), ser du andra. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge korrekt kan du kolla den [felsökning](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).
