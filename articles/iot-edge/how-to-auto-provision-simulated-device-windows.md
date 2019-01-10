---
title: Automatiskt etablera Windows-enheter med DPS – Azure IoT Edge | Microsoft Docs
description: Använda en simulerad enhet på din Windows-dator för att testa automatisk enhetsetablering för Azure IoT Edge med Device Provisioning-tjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: aa5e5fba3758fa3983924660b9b5f714d02613c6
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158609"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Skapa och etablera en simulerad TPM-Edge-enhet på Windows

Azure IoT Edge-enheter kan vara automatisk etablering med hjälp av den [Device Provisioning-tjänsten](../iot-dps/index.yml) precis som enheter som inte är Microsoft edge-aktiverade. Om du är bekant med processen för automatisk etablering kan du granska den [begrepp inom Automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

Den här artikeln visar hur du testar Automatisk etablering på en simulerad Edge-enhet med följande steg:

* Skapa en instans av IoT Hub enheten Provisioning Service (DPS).
* Skapa en simulerad enhet på din Windows-dator med en simulerad Trusted Platform Module (TPM) för maskinvara säkerhet.
* Skapa en enskild registrering för enheten.
* Installera IoT Edge-körningen och ansluta enheten till IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklingsdator. Den här artikeln använder Windows 10.
* En aktiv IoT-hubb.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning-tjänsten

Skapa en ny instans av IoT Hub Device Provisioning-tjänsten i Azure och länka det till din IoT hub. Du kan följa anvisningarna i [konfigurera IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

När du har Device Provisioning-tjänsten körs, kopierar du värdet för **ID-omfång** från översiktssidan. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

Skapa en simulerad TPM-enhet på en Windows-utvecklingsdator. Hämta den **registrerings-ID** och **bekräftelsenyckeln** för din enhet och använda dem för att skapa en individuell registreringspost i DPS.

När du skapar en registrering i DPS har möjlighet att deklarera en **starttillstånd för Enhetstvilling**. I enhetstvillingen kan du ställa in etiketter att gruppera enheter efter valfritt mått som du behöver i din lösning som region, miljö, plats eller enhet. Dessa taggar som används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

Välj den SDK-språk som du vill använda för att skapa den simulerade enheten och följ stegen förrän du har skapat den enskilda registreringen.

När du skapar den enskilda registreringen, Välj **aktivera** att deklarera att den simulerade TPM-enheten på utvecklingsdatorn Windows är en **IoT Edge-enhet**.

Simulerade enheter och guider för enskild registrering:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

När du har skapat den enskilda registreringen, spara värdet för den **registrerings-ID**. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

När du har slutfört föregående avsnitt, bör du se den nya enheten visas som en IoT Edge-enhet i IoT Hub. Nu kan behöva du installera IoT Edge-körningen på din enhet.

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod på gränsen.  

Följ anvisningarna för att installera IoT Edge-körningen på den enhet som kör den simulerade TPM i föregående avsnitt. Se till att konfigurera IoT Edge-körningen för automatisk och manuell och etablering.

Vet DPS **ID-omfång** och **registrerings-ID** innan du installerar IoT Edge på din enhet.

[Installera och automatiskt etablera IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Verifiera installationen

Om körningen har startats kan du gå till din IoT-hubb och börja distribuera IoT Edge-moduler till din enhet. Använd följande kommandon på din enhet för att kontrollera att körningen installerad och har startats.  

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Granska loggarna för tjänsten från de senaste 5 minuterna.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Lista med moduler.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Registreringen Device Provisioning-tjänsten kan du ange enhets-ID och device twin taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta enskilda enheter eller grupper av enheter med hjälp av automatisk enheter. Lär dig hur du [distribuera och övervaka IoT Edge-moduler i skala med Azure portal](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md)
