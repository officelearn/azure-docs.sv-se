---
title: Etablera Windows-enheter automatiskt med DPS-Azure IoT Edge | Microsoft Docs
description: Använda en simulerad enhet på din Windows-dator för att testa automatisk enhetsetablering för Azure IoT Edge med Device Provisioning-tjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4f01c3ec57b0cf9e3ecf47254b57be95ea051a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510948"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Skapa och etablera en simulerad IoT Edge enhet med en virtuell TPM i Windows

Azure IoT Edge-enheter kan vara automatisk etablering med hjälp av den [Device Provisioning-tjänsten](../iot-dps/index.yml) precis som enheter som inte är Microsoft edge-aktiverade. Om du är bekant med processen för automatisk etablering kan du granska den [begrepp inom Automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

DPS stöder symmetrisk nyckel attestering för IoT Edge enheter i både enskilda registreringar och grupp registrering. Om du markerar alternativet "är IoT Edge enhet" för att få en grupp registrering markeras alla enheter som är registrerade under den registrerings gruppen som IoT Edge enheter.

Den här artikeln visar hur du testar automatisk etablering på en simulerad IoT Edge enhet med följande steg:

* Skapa en instans av IoT Hub enheten Provisioning Service (DPS).
* Skapa en simulerad enhet på din Windows-dator med en simulerad Trusted Platform Module (TPM) för maskinvara säkerhet.
* Skapa en enskild registrering för enheten.
* Installera IoT Edge-körningen och ansluta enheten till IoT Hub.

> [!TIP]
> I den här artikeln beskrivs hur du testar automatisk etablering genom att använda TPM-attestering på virtuella enheter, men det är mycket som gäller när du använder den fysiska TPM-maskinvaran.

## <a name="prerequisites"></a>Krav

* En Windows-utvecklingsdator. Den här artikeln använder Windows 10.
* En aktiv IoT-hubb.

> [!NOTE]
> TPM 2,0 krävs när du använder TPM-attestering med DPS och kan endast användas för att skapa enskilda, inte grupper, registreringar.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning-tjänsten

Skapa en ny instans av IoT Hub Device Provisioning-tjänsten i Azure och länka det till din IoT hub. Du kan följa anvisningarna i [konfigurera IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

När du har Device Provisioning-tjänsten körs, kopierar du värdet för **ID-omfång** från översiktssidan. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen.

> [!TIP]
> Om du använder en fysisk TPM-enhet måste du bestämma **bekräftelse nyckeln**, som är unik för varje TPM-chip och hämtas från TPM-kretsen som är kopplad till den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för bekräftelse nyckeln.
>
> Följ anvisningarna i artikeln [Hantera enhets registreringar med Azure Portal](../iot-dps/how-to-manage-enrollments.md) för att skapa din registrering i DPS och fortsätt sedan med avsnittet [Installera IoT Edge runtime](#install-the-iot-edge-runtime) i den här artikeln för att fortsätta.

## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

Skapa en simulerad TPM-enhet på en Windows-utvecklingsdator. Hämta **registrerings-ID** och **bekräftelse nyckel** för enheten och Använd dem för att skapa en enskild registrerings post i DPS.

När du skapar en registrering i DPS har möjlighet att deklarera en **starttillstånd för Enhetstvilling**. I enhetstvillingen kan du ställa in etiketter att gruppera enheter efter valfritt mått som du behöver i din lösning som region, miljö, plats eller enhet. Dessa taggar som används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

Välj den SDK-språk som du vill använda för att skapa den simulerade enheten och följ stegen förrän du har skapat den enskilda registreringen.

När du skapar enskilda registreringar väljer du **Sant** för att deklarera att den SIMULERAde TPM-enheten på Windows Development-datorn är en **IoT Edge enhet**.

Simulerade enheter och guider för enskild registrering:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

När du har skapat den enskilda registreringen, spara värdet för den **registrerings-ID**. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod på gränsen.

Du behöver följande information när du konfigurerar din enhet:

* DPS **-ID omfångs** värde
* ID för enhets **registrering** som du har skapat

Installera IoT Edge runtime på enheten som kör den simulerade TPM: en. Du konfigurerar IoT Edge runtime för automatisk, inte manuell, etablering.

> [!TIP]
> Lämna fönstret med TPM-simulatorn öppen under installationen och testning.

Mer detaljerad information om hur du installerar IoT Edge i Windows, inklusive krav och instruktioner för aktiviteter som hantering av behållare och uppdatering av IoT Edge, finns i [installera Azure IoT Edge runtime i Windows](how-to-install-iot-edge-windows.md).

1. Öppna ett PowerShell-fönster i administratörsläge. Se till att använda en AMD64-session av PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Deploy-IoTEdge** kontrollerar att Windows-datorn finns på en version som stöds, aktiverar funktionen containers och laddar sedan ned Moby runtime och IoT Edge Runtime. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. I det här läget kan IoT core-enheter startas om automatiskt. Andra Windows 10-eller Windows Server-enheter kan bli ombedd att starta om. Om så är fallet startar du om enheten nu. När enheten är klar kör du PowerShell som administratör igen.

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge runtime på din dator. Kommandot är standardvärdet för manuell etablering med Windows-behållare. Använd `-Dps`-flaggan om du vill använda enhets etablerings tjänsten i stället för manuell etablering.

   Ersätt plats hållarnas värden för `{scope_id}` och `{registration_id}` med de data du samlat in tidigare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om körningen har startats kan du gå till din IoT-hubb och börja distribuera IoT Edge-moduler till din enhet. Använd följande kommandon på din enhet för att kontrollera att körningen installerad och har startats.  

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Granska loggarna för tjänsten från de senaste 5 minuterna.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Registreringen Device Provisioning-tjänsten kan du ange enhets-ID och device twin taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta enskilda enheter eller grupper av enheter med hjälp av automatisk enheter. Lär dig hur du [distribuera och övervaka IoT Edge-moduler i skala med Azure portal](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md)
