---
title: Azure IoT Suite och Logic Apps | Microsoft Docs
description: En självstudiekurs om hur du kopplar in Logic Apps i Azure IoT Suite för affärsprocess.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107052"
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Självstudier: Ansluta Logic App till Azure IoT Suite förkonfigurerade lösningen för fjärrövervakning
Den [Microsoft Azure IoT Suite] [ lnk-internetofthings] förkonfigurerade lösningen för fjärrövervakning är ett bra sätt att komma igång snabbt med en slutpunkt till slutpunkt funktionsuppsättning som är ett exempel på en IoT-lösning. Den här självstudien visar hur du lägger till Logikappen till din Microsoft Azure IoT Suite förkonfigurerade lösningen för fjärrövervakning. De här stegen visar hur du kan dra ytterligare din IoT-lösning genom att ansluta den till en affärsprocess.

*Om du behöver en genomgång av hur man etablerar en för den förkonfigurerade lösningen, se [Självstudier: Kom igång med förkonfigurerade IoT-lösningar][lnk-getstarted].*

Innan du påbörjar den här självstudien bör du:

* Etablera den förkonfigurerade lösningen för fjärrövervakning i Azure-prenumerationen.
* Skapa ett SendGrid-konto så att du kan skicka ett e-postmeddelande som utlöser affärsprocessen. Du kan registrera dig för ett kostnadsfritt utvärderingskonto på [SendGrid](https://sendgrid.com/) genom att klicka på **testa kostnadsfritt**. När du har registrerat för ditt utvärderingskonto, måste du skapa en [API-nyckel](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) i SendGrid som ger behörighet att skicka e-post. Du behöver den här API-nyckeln senare under kursen.

Den här kursen måste du Visual Studio 2015 eller Visual Studio 2017 för att ändra åtgärderna i förkonfigurerade lösningen för serverdelen.

Om vi antar att du redan har etablerat din fjärrövervakning förkonfigurerad lösning, navigera till resursgruppen för lösningen i den [Azure-portalen][lnk-azureportal]. Resursgruppen har samma namn som Lösningsnamnet som du valde när du har etablerat din lösning för fjärrövervakning. Du kan se alla etablerade Azure-resurser för din lösning i resursgruppen. Följande skärmbild visar ett exempel **resursgrupp** bladet för en fjärrövervakning förkonfigurerade lösningen:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Börja genom att ställer in logikappen ska användas med den förkonfigurerade lösningen.

## <a name="set-up-the-logic-app"></a>Ställ in Logikappen
1. Klicka på **Lägg till** överst i resursgruppsbladet i Azure-portalen.
2. Sök efter **Logikapp**markerar du den och klicka sedan på **skapa**.
3. Fyll i **namn** och använda samma **prenumeration** och **resursgrupp** som du använde när du har etablerat din lösning för fjärrövervakning. Klicka på **Skapa**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. När distributionen är klar ser Logikappen har listats som en resurs i resursgruppen.
5. Klicka på Logikappen att navigera till bladet Logic App väljer de **tom Logikapp** mall för att öppna den **Logic Apps Designer**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Välj **begära**. Den här åtgärden anger att en inkommande HTTP-begäran med en viss JSON-formaterad nyttolast åtgärder som en utlösare.
7. Klistra in följande kod i begäran brödtext JSON-schemat:
   
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
8. Klicka på **+ nytt steg** manuella utlösaren. Klicka sedan på **Lägg till en åtgärd**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Sök efter **SendGrid – skicka e-post** och klicka på den.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Ange ett namn för anslutningen, till exempel **SendGridConnection**, ange den **SendGrid API-nyckel** du skapade när du konfigurerar ditt SendGrid-konto och klicka på **skapa**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Lägg till e-postadresser som du äger för båda de **från** och **till** fält. Lägg till **Remote övervakningsaviseringen [DeviceId]** till den **ämne** fält. I den **e-postmeddelandets brödtext** fältet, lägga till **enheten [DeviceId] har rapporterat [measurementName] med värdet [measuredValue].** Du kan lägga till **[DeviceId]**, **[measurementName]**, och **[measuredValue]** genom att klicka i den **du kan infoga data från föregående steg** avsnittet.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Klicka på **spara** på den översta menyn.
13. Klicka på den **begära** utlösare och kopiera den **Http Post till denna URL** värde. Du behöver den här URL: en senare i den här självstudien.

> [!NOTE]
> Logic Apps kan du köra [många olika typer av åtgärden] [ lnk-logic-apps-actions] inklusive åtgärder i Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Konfigurera EventProcessor Webbjobb
I det här avsnittet ska ansluta du din förkonfigurerade lösning till Logikappen som du skapade. För att slutföra den här uppgiften måste du lägga till URL: en för att utlösa Logikappen för åtgärden som utlöses när en enhet sensor värdet överskrider ett tröskelvärde.

1. Använd git-klient för att klona den senaste versionen av den [azure-iot-remote-monitoring github-lagringsplatsen][lnk-rmgithub]. Exempel:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. Visual Studio, öppna den **RemoteMonitoring.sln** från den lokala kopian av databasen.
3. Öppna den **ActionRepository.cs** fil i den **infrastruktur\\databasen** mapp.
4. Uppdatera den **actionIds** ordlista med den **Http Post till denna URL** du antecknade från din Logikapp på följande sätt:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Spara ändringarna i lösningen och avsluta Visual Studio.

## <a name="deploy-from-the-command-line"></a>Distribuera från kommandoraden
I det här avsnittet ska distribuera du den uppdaterade versionen av lösningen för fjärrövervakning att ersätta den version som för närvarande körs i Azure.

1. Efter den [dev konfiguration] [ lnk-devsetup] instruktioner för att konfigurera din miljö för distribution.
2. Distribuera lokalt att följa den [lokal distribution] [ lnk-localdeploy] instruktioner.
3. Om du vill distribuera till molnet och uppdatera den befintliga molndistributionen, följer du de [molnbaserad distribution] [ lnk-clouddeploy] instruktioner. Använd namnet på din ursprungliga distribution som distributionens namn. Till exempel om den ursprungliga distributionen anropades **demologicapp**, använder du följande kommando:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   När build-skriptet körs, måste du använda samma Azure-konto, prenumeration, region och Active Directory-instans som du använde när du etablerat lösningen.

## <a name="see-your-logic-app-in-action"></a>Se din Logikapp i praktiken
Den förkonfigurerade lösningen för fjärrövervakning har två regler som ställts in som standard när du etablerar en lösning. Båda innehåller inte den **SampleDevice001** enhet:

* Temperatur > 38.00
* Fuktighet > 48.00

Temperatur regeln utlöses den **generera larm** åtgärden och fuktigheten regel utlösare den **SendMessage** åtgärd. Om vi antar att du har använt samma URL för båda åtgärderna i **ActionRepository** klassen din logikapputlösare för antingen regeln. Både använda SendGrid för att skicka ett e-postmeddelande till den **till** -adress med information om aviseringen.

> [!NOTE]
> Logikappen fortsätter att utlösa varje gång tröskeln uppfylls. För att undvika onödiga e-postmeddelanden kan du antingen inaktivera reglerna i portalen för lösningen eller inaktivera Logikappen i den [Azure-portalen][lnk-azureportal].
> 
> 

Förutom e-postmeddelanden, kan du också se när Logikappen körs i portalen:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har använt en Logikapp för att ansluta den förkonfigurerade lösningen till en affärsprocess kan läsa du mer om alternativ för att anpassa de förkonfigurerade lösningarna:

* [Använd dynamisk telemetri med den förkonfigurerade lösningen för fjärrövervakning][lnk-dynamic]
* [Information för enhetsmetadata i den förkonfigurerade lösningen för fjärrövervakning][lnk-devinfo]

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
