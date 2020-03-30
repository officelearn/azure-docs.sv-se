---
title: Installera Azure IoT Edge för Linux i Windows | Microsoft-dokument
description: Installationsinstruktioner för Azure IoT Edge för Linux-behållare i Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133165"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Använd IoT Edge i Windows för att köra Linux-behållare

Testa IoT Edge-moduler för Linux-enheter med en Windows-dator.

I ett produktionsscenario bör Windows-enheter endast köra Windows-behållare. Ett vanligt utvecklingsscenario är dock att använda en Windows-dator för att skapa IoT Edge-moduler för Linux-enheter. IoT Edge-körningen för Windows gör att du kan köra Linux-behållare för **testning och utveckling.**

I den här artikeln visas stegen för att installera Azure IoT Edge-körningen med Linux-behållare på ditt Windows x64 -system (AMD/Intel). Mer information om installationsprogrammet för IoT Edge-körning, inklusive information om alla installationsparametrar, finns [i Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md).

Information om vad som ingår i den senaste versionen av IoT Edge finns i [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Krav

Använd det här avsnittet om du vill granska om din Windows-enhet kan stödja IoT Edge och förbereda den för en behållarmotor före installationen.

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

Azure IoT Edge med Linux-behållare kan köras på alla versioner av Windows som uppfyller [kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Om du vill installera IoT Edge på en virtuell dator aktiverar du kapslad virtualisering och allokerar minst 2 GB-minne. Hur du aktiverar kapslad virtualisering är olika beroende på vilken hypervisor du använder. För Hyper-V har generation 2 virtuella datorer kapslade virtualisering aktiverat som standard. För VMWare finns det en växlingsknapp för att aktivera funktionen på din virtuella dator.

### <a name="prepare-the-container-engine"></a>Förbered behållarmotorn

Azure IoT Edge är beroende av en [OCI-kompatibel](https://www.opencontainers.org/) behållarmotor. Den största konfigurationsskillnaden mellan att köra Windows- och Linux-behållare på en Windows-dator är att IoT Edge-installationen innehåller en Windows-containerkörningstid, men du måste ange din egen körning för Linux-behållare innan du installerar IoT Edge.

Om du vill konfigurera en Windows-dator för att utveckla och testa behållare för Linux-enheter kan du använda [Docker Desktop](https://www.docker.com/docker-windows) som behållarmotor. Du måste installera Docker och konfigurera den för att [använda Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) innan du installerar IoT Edge.  

Om din IoT Edge-enhet är en Windows-dator kontrollerar du att den uppfyller [systemkraven](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) för Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge-programpaket omfattas av licensvillkoren i paketen (i LICENSE-katalogen). Läs licensvillkoren innan du använder paketet. Din installation och användning av paketet utgör ditt godkännande av dessa villkor. Om du inte godkänner licensvillkoren ska du inte använda paketet.

Ett PowerShell-skript hämtar och installerar säkerhetsdemonen Azure IoT Edge. Säkerhetsdemonen startar sedan den första av två runtime-moduler, IoT Edge-agenten, som möjliggör fjärrdistribution av andra moduler.

När du installerar IoT Edge-körningen för första gången på en enhet måste du etablera enheten med en identitet från en IoT-hubb. En enda IoT Edge-enhet kan etableras manuellt med hjälp av en enhetsanslutningssträng som tillhandahålls av din IoT-hubb. Du kan också använda tjänsten Enhetsetablering för att automatiskt etablera enheter, vilket är användbart när du har många enheter att konfigurera.

Du kan läsa mer om de olika installationsalternativen och parametrarna i artikeln [Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md). När du har Installerat och konfigurerat Docker Desktop för Linux-behållare deklarerar huvudinstallationsskillnaden Linux med parametern **-ContainerOs.** Ett exempel:

1. Om du inte redan har gjort det registrerar du en ny IoT Edge-enhet och hämtar enhetsanslutningssträngen. Kopiera anslutningssträngen som ska användas senare i det här avsnittet. Du kan slutföra det här steget med hjälp av följande verktyg:

   * [Azure-portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio-kod](how-to-register-device.md#register-with-visual-studio-code)

2. Kör PowerShell som administratör.

   >[!NOTE]
   >Använd en AMD64-session med PowerShell för att installera IoT Edge, inte PowerShell (x86). Om du är osäker på vilken sessionstyp du använder kör du följande kommando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Kommandot **Deploy-IoTEdge** kontrollerar att din Windows-dator finns på en version som stöds, aktiverar behållarfunktionen och hämtar sedan den moby-körningen (som inte används för Linux-behållare) och IoT Edge-körningen. Kommandot är som standard Windows-behållare, så deklarera Linux som önskat behållaroperativsystem.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Nu kan IoT Core-enheter startas om automatiskt. Andra Windows 10- eller Windows Server-enheter kan uppmana dig att starta om. Starta om enheten nu om så är fallet. När enheten är klar kör du PowerShell som administratör igen.

5. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge-körningen på datorn. Kommandot är som standard manuell etablering med en enhetsanslutningssträng. Deklarera Linux som önskat behållaroperativsystem igen.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Ange den enhetsanslutningssträng som du hämtade i steg 1 när du uppmanas att göra det. Enhetsanslutningssträngen associerar den fysiska enheten med ett enhets-ID i IoT Hub.

   Enhetsanslutningssträngen har följande format och bör inte innehålla citattecken:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Kontrollera status för IoT Edge-tjänsten:

```powershell
Get-Service iotedge
```

Undersök serviceloggar från de senaste 5 minuterna:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Kör en automatisk kontroll efter de vanligaste konfigurations- och nätverksfelen:

```powershell
iotedge check
```

Lista löpmoduler. Efter en ny installation är den enda modulen du bör se igång **edgeAgent**. När du [har distribuerat IoT Edge-moduler](how-to-deploy-modules-portal.md) för första gången startar även den andra systemmodulen **EdgeHub**på enheten.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat en IoT Edge-enhet med körningen installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge på rätt sätt kan du läsa [felsökningssidan.](troubleshoot.md)

Information om hur du uppdaterar en befintlig installation till den senaste versionen av IoT Edge finns i [Uppdatera säkerhetsdemonen och körningen i IoT Edge](how-to-update-iot-edge.md).
