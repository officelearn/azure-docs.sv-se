---
title: Anslut MXChip IoT DevKit till Azure IoT Hub Fjärrövervakning
description: I den här självstudien kan du läsa om hur du skickar status för sensorer på IoT DevKit AZ3166 till Azure IoT Remote Monitoring-lösningsacceleratorn.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 598e361949b000724645c841910b1682a7bbb1a3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258464"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Anslut MXChip IoT DevKit till Azure IoT Remote Monitoring solution accelerator

I den här självstudien får du lära dig hur du kör en exempelapp på din DevKit för att skicka sensordata till din Azure IoT Remote Monitoring-lösningsaccelerator.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel styrelse med rik kringutrustning och sensorer. Du kan utveckla för det med [Visual Studio Kod förlängning för Arduino](https://aka.ms/arduino). Och den levereras med en växande [projektkatalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) som hjälper dig prototyplösningar för Sakernas Internet (IoT) som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Avsluta [komma igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Ha din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har en, kan du registrera dig via en av dessa två metoder:

* Aktivera ett [kostnadsfritt 30-dagars testversionskonto för Microsoft Azure](https://azure.microsoft.com/free/)

* Hämta din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN- eller Visual Studio-prenumerant

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Skapa en Azure IoT Remote Monitoring-lösningsaccelerator

1. Gå till [Azure IoT-lösningsacceleratorer och](https://www.azureiotsolutions.com/) klicka på **Skapa en ny lösning**.

   ![Välj acceleratortyp för Azure IoT-lösning](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Som standard skapar det här exemplet en S2 IoT Hub när den har skapat en IoT Remote Monitoring-lösningsaccelerator. Om den här IoT-hubben inte används med ett stort antal enheter rekommenderar vi starkt att du nedgraderar den från S2 till S1 och tar bort IoT Remote Monitoring-lösningsacceleratorn så att den relaterade IoT Hub också kan tas bort, när du inte längre behöver den. 

2. Välj **Fjärrövervakning**.

3. Ange ett lösningsnamn, välj en prenumeration och en region och klicka sedan på **Skapa lösning**. Lösningen kan ta ett tag att etablera.
  
   ![Skapa lösning](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. När etableringen är klar klickar du på **Starta**. Vissa simulerade enheter skapas för lösningen under etableringsprocessen. Klicka på **ENHETER** för att kolla in dem.

   ![Instrumentpanel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsol](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klicka på **LÄGG TILL EN ENHET**.

6. Klicka på **Lägg till ny** för anpassad **enhet**.
  
   ![Lägg till en ny enhet](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klicka på **Låt mig definiera mitt eget enhets-ID,** ange `AZ3166`och klicka sedan på **Skapa**.
  
   ![Skapa enhet med ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anteckna **IoT Hub Hostname**och klicka på **Klar**.

## <a name="open-the-remotemonitoring-sample"></a>Öppna exemplet fjärrövervakning

1. Koppla bort DevKit från datorn om den är ansluten.

2. Starta VS Code.

3. Anslut DevKit till datorn. VS-kod identifierar din DevKit automatiskt och öppnar följande sidor:

   * Utvecklingstabellens introduktionssida.
   * Arduino Exempel: Hands-on prover för att komma igång med DevKit.

4. Expandera avsnittet **ARDUINO EXAMPLES på** vänster sida, bläddra till Exempel för **MXCHIP AZ3166 > AzureIoT**och välj **RemoteMonitoring**. Det öppnar ett nytt VS-kodfönster med en projektmapp i.

   > [!NOTE]
   > Om du råkar stänga fönstret kan du öppna den igen. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna kommandopaletten, skriv **Arduino**och leta sedan upp och välja **Arduino: Exempel**.

## <a name="provision-required-azure-services"></a>Tillhandahålla nödvändiga Azure-tjänster

I lösningsfönstret kör du `Ctrl+P` uppgiften via `Cmd+P`(macOS: ) genom att ange `task cloud-provision` den medföljande textrutan.

I VS-kodterminalen guidar en interaktiv kommandorad dig genom att etablera de nödvändiga Azure-tjänsterna.

![Etablera Azure-resurser](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Skapa och ladda upp enhetskoden

1. Använd `Ctrl+P` (macOS: `Cmd + P`) och skriv **aktivitet config-device-connection**.

2. Terminalen frågar om du vill använda en anslutningssträng som hämtas från steget. `task cloud-provision` Du kan också mata in din egen enhetsanslutningssträng genom att klicka på Skapa ny...'

3. Terminalen uppmanar dig att gå in i konfigurationsläge. För att göra det, håll ned knapp A och tryck sedan på och släpp återställningsknappen. På skärmen visas DevKit ID och "Konfiguration".

   ![Sträng för indataanslutning](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. När `task config-device-connection` du är `F1` klar klickar du för `Arduino: Upload`att läsa in VS-kodkommandon och välj . VS Code börjar verifiera och ladda upp Arduino skiss.
  
   ![Verifiering och uppladdning av Arduino-skissen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit startar om och börjar köra koden.

## <a name="test-the-project"></a>Testa projektet

När exempelappen körs skickar DevKit sensordata via WiFi till din Azure IoT Remote Monitoring-lösningsaccelerator. Så här ser du resultatet:

1. Gå till din Azure IoT Remote Monitoring-lösningsaccelerator och klicka på **DASHBOARD**.

2. På lösningskonsolen för fjärrövervakning visas din DevKit-sensorstatus.

   ![Sensordata i Azure IoT Remote Monitoring-lösningsaccelerator](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Ändra enhets-ID

Om du vill ändra det hårdkodade **AZ3166** till ett anpassat enhets-ID i koden ändrar du kodraden som visas i [exemplet med fjärrövervakning](../iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, se [vanliga frågor om IoT-utvecklarpaket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss via följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till din Azure IoT Remote Monitoring-lösningsaccelerator och visualiserar sensordata, här är de föreslagna nästa stegen:

* [Översikt över Azure IoT-lösningsacceleratorer](https://docs.microsoft.com/azure/iot-suite/)

* [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](/azure/iot-central/core/howto-connect-devkit)

* [IoT-utvecklarpaket](https://microsoft.github.io/azure-iot-developer-kit/) 
