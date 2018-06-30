---
title: Automatiskt etablera Azure IoT insticksenhet med DP - Windows | Microsoft Docs
description: Använda en simulerad enhet på din Windows-dator för att testa automatisk enhetsetableringen för Azure IoT kanten med etablering av tjänst
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a973b248022cf3c0497f72bc2fcdd45a6527e65
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116287"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Skapa och etablera en simulerad TPM Edge-enhet i Windows

Azure IoT-gränsenheterna kan automatiskt-etableras med den [enhet Etableringstjänsten](../iot-dps/index.yml) precis som i enheter som inte är aktiverade kant. Om du är bekant med processen för automatisk etablering kan du granska den [Automatisk etablering begrepp](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter. 

Den här artikeln visar hur du testa Automatisk etablering på en simulerad Edge-enhet med följande steg: 

* Skapa en instans av IoT Hub enheten etablering Service (DP).
* Skapa en simulerad enhet på din Windows-dator med en simulerad Trusted Platform Module (TPM) för säkerhet i maskinvara.
* Skapa en enskild registreringen för enheten.
* Installera IoT kant-runtime och ansluta enheten till IoT-hubb.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklingsdator. Den här artikeln använder Windows 10. 
* En aktiv IoT-hubb. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Ställ in Etableringstjänsten IoT-hubb enhet

Skapa en ny instans av Etableringstjänsten IoT-hubb enheten i Azure och koppla den till din IoT-hubb. Du kan följa anvisningarna i [ställa in IoT-hubb dp](../iot-dps/quick-setup-auto-provision.md).

När du har enheten etablering Service körs, kopierar du värdet för **ID Scope** från översiktssidan. Du kan använda det här värdet när du konfigurerar IoT kant-körningsmiljön. 

## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

Skapa en simulerad TPM-enhet på utvecklingsdatorn Windows. Hämta den **registrerings-ID** och **bekräftelsenyckel** för din enhet och använda dem för att skapa en post för enskilda registrering i DP. 

När du skapar en registrering i DP har möjlighet att deklarera en **starttillstånd enheten dubbla**. Dubbla för enheten att du kan ange taggar gruppera enheter efter alla mått som du behöver i din lösning som region, miljö, plats eller enhet. Dessa taggar används för att skapa [automatiska uppdateringar](how-to-deploy-monitor.md). 

Välj SDK-språk som du vill använda för att skapa den simulerade enheten och följ stegen tills du skapa enskilda registreringen. 

När du skapar enskilda registreringen väljer **aktivera** att deklarera att den virtuella datorn är en **IoT gränsenheten**.

Simulerade enheten och enskilda registreringsguider: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

När du har skapat enskilda registreringen spara värdet för den **registrerings-ID**. Du kan använda det här värdet när du konfigurerar IoT kant-körningsmiljön. 

## <a name="install-the-iot-edge-runtime"></a>Installera IoT kant runtime

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare, och att du kan distribuera ytterligare behållare till enheten så att du kan köra kod i utkanten. På enheter som kör Windows, kan du antingen använda Windows-behållare eller Linux-behållare. Välj typ av behållare som du vill använda och följ anvisningarna. Se till att konfigurera IoT kant-körning för automatisk, manuell och etablering. 

Följ instruktionerna för att installera runtime IoT kanten på enheten som kör simulerade TPM från föregående avsnitt. 

Veta din dp **ID Scope** och **registrerings-ID** innan du påbörjar de här artiklarna. 

* [Windows-behållare](how-to-install-iot-edge-windows-with-windows.md)
* [Linux-behållare](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>Skapa en TPM-miljövariabel

Ändra på den dator som kör den simulerade enheten den **iotedge** service registret för att ange en miljövariabel.

1. Från den **starta** öppna menyn **regedit**. 
2. Gå till **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**. 
3. Välj **redigera** > **nya** > **Flersträngsvärde**. 
4. Ange namnet **miljö**. 
5. Dubbelklicka på den nya variabeln och ange värdet **IOTEDGE_USE_TPM_DEVICE = ON**. 
6. Spara ändringarna genom att klicka på **OK**. 

## <a name="restart-the-iot-edge-runtime"></a>Starta om IoT kant-körning

Starta om IoT kant runtime så att den hämtar alla konfigurationsändringar som du har gjort på enheten. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

Kontrollera att IoT kant runtime körs. 

   ```bash
   sudo systemctl status iotedge
   ```

## <a name="verify-successful-installation"></a>Kontrollera att installationen lyckades

Du kan gå till din IoT-hubb och den nya enheten automatiskt var etablerad och att det är klart att köras IoT kant moduler om körningen har startats. 

Kontrollera status för tjänsten IoT kant.

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Lista med moduler.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Etablering av tjänst registreringen kan du ange enhets-ID och enheten dubbla taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att tillämpa enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur du [distribuera och övervaka IoT kant moduler i skala med Azure-portalen](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md)