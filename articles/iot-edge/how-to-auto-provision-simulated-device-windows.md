---
title: Etablera Windows-enheter automatiskt med DPS – Azure IoT Edge | Microsoft-dokument
description: Använda en simulerad enhet på din Windows-dator för att testa automatisk enhetsetablering för Azure IoT Edge med enhetsetableringstjänst
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4f01c3ec57b0cf9e3ecf47254b57be95ea051a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510948"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Skapa och etablera en simulerad IoT Edge-enhet med en virtuell TPM i Windows

Azure IoT Edge-enheter kan etableras automatiskt med hjälp av [enhetsetableringstjänsten](../iot-dps/index.yml) precis som enheter som inte är edge-aktiverade. Om du inte känner till processen för automatisk etablering läser du [begreppen för automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

DPS stöder symmetrisk nyckelbelysning för IoT Edge-enheter i både individuell registrering och gruppregistrering. Om du markerar alternativet "är IoT Edge-enhet" för att vara sant i symmetrisk nyckeltysstation markeras alla enheter som är registrerade under den registreringsgruppen som IoT Edge-enheter.

I den här artikeln visas hur du testar automatisk etablering på en simulerad IoT Edge-enhet med följande steg:

* Skapa en instans av DPS (IoT Hub Device Provisioning Service).
* Skapa en simulerad enhet på din Windows-dator med en simulerad TPM (Trusted Platform Module) för maskinvarusäkerhet.
* Skapa en enskild registrering för enheten.
* Installera IoT Edge-körningen och anslut enheten till IoT Hub.

> [!TIP]
> I den här artikeln beskrivs automatisk etablering genom att använda TPM-attestation på virtuella enheter, men mycket av det gäller även när du använder fysisk TPM-maskinvara.

## <a name="prerequisites"></a>Krav

* En Windows-utvecklingsmaskin. I den här artikeln används Windows 10.
* En aktiv IoT Hub.

> [!NOTE]
> TPM 2.0 krävs när du använder TPM-intyg med DPS och kan endast användas för att skapa enskilda, inte grupp- registreringar.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera etableringstjänsten för IoT Hub Device Provisioning

Skapa en ny instans av IoT Hub Device Provisioning Service i Azure och länka den till din IoT-hubb. Du kan följa instruktionerna i [Konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

När du har igång enhetsetableringstjänsten kopierar du värdet för **ID-scopet** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge-körningen.

> [!TIP]
> Om du använder en fysisk TPM-enhet måste du bestämma **bekräftelsenyckeln**, som är unik för varje TPM-chip och erhålls från TPM-chiptillverkaren som är associerad med den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för godkännandenyckeln.
>
> Följ instruktionerna i artikeln [Så här hanterar du enhetsregistreringar med Azure Portal](../iot-dps/how-to-manage-enrollments.md) för att skapa din registrering i DPS och fortsätter sedan med avsnittet Installera [IoT Edge-körningen](#install-the-iot-edge-runtime) i den här artikeln för att fortsätta.

## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

Skapa en simulerad TPM-enhet på din Windows-utvecklingsdator. Hämta **registrerings-ID** och **bekräftelse för** din enhet och använd dem för att skapa en enskild registreringspost i DPS.

När du skapar en registrering i DPS har du möjlighet att deklarera ett **inledande enhets twin-tillstånd**. I enhetstvillingen kan du ställa in taggar för att gruppera enheter efter alla mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. Dessa taggar används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

Välj det SDK-språk som du vill använda för att skapa den simulerade enheten och följ stegen tills du skapar den enskilda registreringen.

När du skapar den enskilda registreringen väljer du **Sant** om du vill deklarera att den simulerade TPM-enheten på din Windows-utvecklingsdator är en **IoT Edge-enhet**.

Simulerade enhets- och enskilda registreringsguider:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

När du har skapat den enskilda registreringen sparar du värdet **för registrerings-ID.** Du använder det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod vid kanten.

Du behöver följande information när du etablerar enheten:

* **Dps-ID-scopevärdet**
* **Enhetsregistrerings-ID:et** som du skapade

Installera IoT Edge-körningen på enheten som kör den simulerade TPM:en. Du konfigurerar IoT Edge-körningen för automatisk, inte manuell etablering.

> [!TIP]
> Håll fönstret som kör TPM-simulatorn öppet under installationen och testningen.

Mer detaljerad information om hur du installerar IoT Edge i Windows, inklusive förutsättningar och instruktioner för uppgifter som att hantera behållare och uppdatera IoT Edge, finns i [Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md).

1. Öppna ett PowerShell-fönster i administratörsläge. Var noga med att använda en AMD64-session med PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Deploy-IoTEdge** kontrollerar att din Windows-dator finns i en version som stöds, aktiverar behållarfunktionen och hämtar sedan den moby-körningen och IoT Edge-körningen. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Nu kan IoT Core-enheter startas om automatiskt. Andra Windows 10- eller Windows Server-enheter kan uppmana dig att starta om. Starta om enheten nu om så är fallet. När enheten är klar kör du PowerShell som administratör igen.

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge-körningen på datorn. Kommandot är som standard manuell etablering med Windows-behållare. Använd `-Dps` flaggan för att använda enhetsetableringstjänsten i stället för manuell etablering.

   Ersätt platshållarvärdena för `{scope_id}` och `{registration_id}` med de data som du samlade in tidigare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startat kan du gå in i din IoT Hub och börja distribuera IoT Edge-moduler till enheten. Använd följande kommandon på enheten för att kontrollera att körningen har installerats och startats.  

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Undersök serviceloggar från de senaste 5 minuterna.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista löpmoduler.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Med registreringsprocessen för enhetsetableringstjänsten kan du ange enhets-ID- och enhetstvillingtaggarna samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur du [distribuerar och övervakar IoT Edge-moduler i stor skala med Hjälp av Azure-portalen](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md)
