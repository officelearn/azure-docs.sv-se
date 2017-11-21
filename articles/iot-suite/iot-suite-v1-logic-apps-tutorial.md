---
title: Azure IoT Suite och Logic Apps | Microsoft Docs
description: "En självstudiekurs om hur du koppla samman Logic Apps till Azure IoT Suite för affärsprocess."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Självstudier: Anslut Logikappen i Azure IoT Suite Fjärrövervaknings förkonfigurerade lösningen
Den [Microsoft Azure IoT Suite] [ lnk-internetofthings] fjärråtkomst övervakning förkonfigurerade lösningen är ett bra sätt att snabbt komma igång med en slutpunkt till slutpunkt funktionsuppsättning som är ett exempel på en IoT-lösning. Den här självstudiekursen vägleder dig igenom hur du lägger till Logikapp till din Microsoft Azure IoT Suite remote förkonfigurerade övervakningslösning. Dessa steg visar hur du kan dra ytterligare IoT-lösningen genom att ansluta till en affärsprocess.

*Om du letar efter en genomgång av hur du etablerar en fjärrövervaknings förkonfigurerade lösningen, se [Självstudier: Kom igång med förkonfigurerade IoT-lösningar][lnk-getstarted].*

Innan du börjar den här kursen bör du:

* Etablera fjärråtkomst övervakning förkonfigurerade lösningen i din Azure-prenumeration.
* Skapa ett SendGrid-konto så att du kan skicka ett e-postmeddelande som utlöser dina affärsprocesser. Du kan registrera dig för ett kostnadsfritt utvärderingskonto på [SendGrid](https://sendgrid.com/) genom att klicka på **försök gratis**. När du har registrerat för din kostnadsfritt konto, måste du skapa en [API-nyckeln](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) i SendGrid som ger behörighet att skicka e-post. Du behöver den här API-nyckeln senare under kursen.

Den här kursen behöver du Visual Studio 2015 eller Visual Studio-2017 för att ändra åtgärder i förkonfigurerade lösningens serverdel.

Under förutsättning att du redan har etablerat din fjärrövervaknings förkonfigurerade lösningen måste du gå till resursgruppen för lösningen i den [Azure-portalen][lnk-azureportal]. Resursgruppen har samma namn som lösningens namn du angav när du har etablerat din fjärranslutna övervakningslösning. Du kan se alla etablerade Azure-resurser för lösningen i en resursgrupp. Följande skärmbild visar ett exempel **resursgruppen** bladet för en fjärransluten övervakning förkonfigurerade lösningen:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Börja genom att ställer in logikappen för användning med förkonfigurerade lösningen.

## <a name="set-up-the-logic-app"></a>Ställ in Logikappen
1. Klicka på **Lägg till** längst upp i din blad för resursgrupp i Azure-portalen.
2. Sök efter **Logikapp**markerar du den och klicka sedan på **skapa**.
3. Fyll i den **namn** och använda samma **prenumeration** och **resursgruppen** som du använde när du har etablerat din fjärranslutna övervakningslösning. Klicka på **Skapa**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. När distributionen är klar kan du se Logikappen har listats som en resurs i resursgruppen.
5. Klicka på Logikappen att navigera till bladet Logikapp väljer den **tom Logikapp** mall för att öppna den **Logic Apps Designer**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Välj **begära**. Den här åtgärden anger att en inkommande HTTP-begäran med en viss JSON formateras nyttolast fungerar som en utlösare.
7. Klistra in följande kod i begäran brödtext JSON schemat:
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Du kan kopiera URL: en för HTTP-post när du sparar logikappen, men först måste du lägga till en åtgärd.
   > 
   > 
8. Klicka på **+ nytt steg** under manuell utlösaren. Klicka på **lägga till en åtgärd**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Sök efter **SendGrid - skicka e-post** och klicka på den.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Ange ett namn för anslutningen, till exempel **SendGridConnection**, ange den **SendGrid API-nyckel** du skapade när du konfigurerar ditt SendGrid-konto och klicka på **skapa**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Lägg till e-postadresser som du äger för båda de **från** och **till** fält. Lägg till **Remote övervakningsavisering [DeviceId]** till den **ämne** fältet. I den **e-postmeddelandets brödtext** fältet, lägga till **enheten [DeviceId] har rapporterat [measurementName] med [measuredValue]-värde.** Du kan lägga till **[DeviceId]**, **[measurementName]**, och **[measuredValue]** genom att klicka i den **du kan infoga data från föregående steg** avsnitt.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Klicka på **spara** på huvudmenyn.
13. Klicka på den **begära** utlösare och kopiera den **Http Post till denna URL** värde. URL: en måste senare i den här kursen.

> [!NOTE]
> Logic Apps gör det möjligt att köra [många olika typer av åtgärder] [ lnk-logic-apps-actions] inklusive åtgärder i Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Ställ in EventProcessor Web-jobb
I det här avsnittet kan ansluta du din förkonfigurerade lösning till Logikappen som du skapade. Lägg till URL: en för att utlösa Logikappen på den åtgärd som utlöses när en enhet sensor värdet överskrider ett tröskelvärde för att slutföra den här uppgiften.

1. Använda git-klienten för att klona den senaste versionen av den [azure iot-fjärr-övervakning github-lagringsplatsen][lnk-rmgithub]. Exempel:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. Öppna i Visual Studio den **RemoteMonitoring.sln** från den lokala kopian av databasen.
3. Öppna den **ActionRepository.cs** filen i den **infrastruktur\\databasen** mapp.
4. Uppdatering av **actionIds** ordlista med den **Http Post till denna URL** du antecknade från din Logikapp på följande sätt:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Spara ändringarna i lösningen och avsluta Visual Studio.

## <a name="deploy-from-the-command-line"></a>Distribuera från kommandoraden
I det här avsnittet kan du distribuera den uppdaterade versionen av den fjärranslutna övervakningslösning att ersätta den version som för närvarande körs i Azure.

1. Efter den [dev ställa in] [ lnk-devsetup] instruktioner för att konfigurera din miljö för distribution.
2. Om du vill distribuera lokalt följer den [lokala distributionen] [ lnk-localdeploy] instruktioner.
3. Om du vill distribuera till molnet och uppdatera den befintliga distributionen i molnet, följer du de [cloud deployment] [ lnk-clouddeploy] instruktioner. Använd namnet på din ursprungliga distribution som distributionens namn. Till exempel om den ursprungliga distributionen av anropades **demologicapp**, använder du följande kommando:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   När build-skriptet körs, måste du använda samma Azure-konto, prenumeration, region och Active Directory-instans som du använde när du har etablerat lösningen.

## <a name="see-your-logic-app-in-action"></a>Se din Logikapp i praktiken
Fjärråtkomst övervakning förkonfigurerade lösningen har två regler som ställts in som standard när du etablerar en lösning. Båda innehåller inte den **SampleDevice001** enhet:

* Temperatur > 38.00
* Fuktighet > 48.00

Utlösare för temperatur-regeln den **höja larm** åtgärden och fuktigheten regel utlösare i **SendMessage** åtgärd. Under förutsättning att du använder samma URL för båda åtgärder i **ActionRepository** class, logik app-utlösare för antingen regeln. Båda reglerna använder SendGrid för att skicka ett e-postmeddelande till den **till** adress med information om aviseringen.

> [!NOTE]
> Logikappen fortsätter att utlösa varje gång tröskeln uppfylls. För att undvika onödiga e-postmeddelanden, kan du antingen inaktivera reglerna i din lösning portal eller inaktivera Logikappen i den [Azure-portalen][lnk-azureportal].
> 
> 

Förutom e-postmeddelanden, kan du också se när Logikappen som körs i portalen:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har använt en Logikapp för att ansluta den förkonfigurerade lösningen till en affärsprocess, du kan lära dig mer om alternativen för att anpassa förkonfigurerade lösningar:

* [Använd dynamiska telemetri med fjärråtkomst övervakning förkonfigurerade lösningen][lnk-dynamic]
* [Enhetens information metadata i fjärråtkomst övervakning förkonfigurerade lösningen][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
