---
title: Exportera dina data i Azure IoT Central | Microsoft Docs
description: Exportera data från Azure IoT Central-programmet
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: cba0bad2e81ffddedfc4ca04e82e17e4286b389b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312127"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportera dina data i Azure IoT Central

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen löpande export i Azure IoT Central för att exportera data till dina egna **Azure Blob Storage**, **Azure Event Hubs**, och **Azure Service Bus** instanser. Du kan exportera **mätningar av**, **enheter**, och **enheten mallar** till dina egna mål för varma sökvägen och kalla sökvägen analyser. Du kan exportera data till Blob storage för att köra långsiktig trendanalys i Microsoft Power BI eller exportera data till Händelsehubbar och Service Bus för att omvandla och utöka dina data i nära realtid med Azure Logic Apps eller Azure Functions.

> [!Note]
> När du aktiverar löpande dataexport, Hämta endast data från det ögonblick då och uppåt. För närvarande går inte att hämta data under en tid när löpande dataexport var inaktiverat. Om du vill behålla fler historiska data, aktivera löpande dataexport tidigt.

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara administratör i din IoT Central-App

## <a name="export-to-blob-storage"></a>Exportera till Blob Storage

Mått, enheter och mallar enhetsdata exporteras till ditt storage-konto en gång per minut, med varje fil som innehåller batch med ändringar sedan senast exporterade filen. Exporterade data är i [Apache AVRO](https://avro.apache.org/docs/current/index.html) format.

Läs mer om [exporterar till Blob storage](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Exportera till Händelsehubbar och Service Bus

Mått, enheter och mallar enhetsdata exporteras till din händelsehubb eller Service Bus-kö eller ämne. Exporterade mätningar av data tas emot i nära realtid och innehåller i sin helhet meddelandet dina enheter som skickas till IoT Central, inte bara värdena för själva mätningarna. Exporterade enheter data tas emot i batchar varje minut och innehåller ändringar i egenskaper och inställningar för alla enheter och exporterade enheten mallar innehåller ändringar i alla mallar för enheten.


Läs mer om [exportera till Event Hubs och Service Bus](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Konfigurera exportera

Följ dessa steg om du inte har en befintlig lagring/Event Hubs/Service Bus för att exportera till:

### <a name="create-storage-account"></a>Skapa lagringskonto

1. Skapa en [nytt lagringskonto i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan läsa mer i [Azure Storage-docs](https://aka.ms/blobdocscreatestorageaccount).
2. Kontotyp, Välj **generella** eller **Blob-lagring**.
3. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som är **inte samma** som programmets användningsbaserad IoT Central. Du ansluter med en anslutningssträng i det här fallet.

4. Skapa en behållare i ditt storage-konto. Gå till ditt lagringskonto. Under **Blobtjänsten**väljer **Bläddra efter Blobar**. Välj **+ behållare** överst för att skapa en ny behållare.

### <a name="create-event-hubs-namespace"></a>Skapa Event Hubs-namnområdet

1. Skapa en [nya Event Hubs-namnområde i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Händelsehubbar i Azure-docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som är **inte samma** som programmets användningsbaserad IoT Central. Du ansluter med en anslutningssträng i det här fallet.
3. Skapa en händelsehubb i namnområdet för Event Hubs. Gå till ditt namnområde och välj **+ Event Hub** överst för att skapa en event hub-instans.

### <a name="create-service-bus-namespace"></a>Skapa Service Bus-namnområde

1. Skapa en [nya Service Bus-namnområde i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Du kan läsa mer i [Azure Service Bus docs](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som är **inte samma** som programmets användningsbaserad IoT Central. Du ansluter med en anslutningssträng i det här fallet.

3. Gå till ditt Service Bus-namnområde och välj **+ kö** eller **+ ämne** överst för att skapa en kö eller ämne att exportera till.

## <a name="set-up-continuous-data-export"></a>Konfigurera löpande dataexport

Nu när du har ett mål för lagring/Event Hubs/Service Bus att exportera data för att följa dessa steg för att konfigurera löpande dataexport. 

1. Logga in på ditt IoT Central-program.

2. I den vänstra menyn klickar du på **löpande Export av Data**.

    > [!Note]
    > Om du inte ser löpande Export av Data på den vänstra menyn kan är du inte administratör i din app. Kontakta en administratör att ställa in export av data.

    ![Skapa ny cde Event Hub](media/howto-export-data/export_menu.PNG)

3. Klicka på den **+ ny** knappen uppe till höger. Välj något av **Azure Blob Storage**, **Azure Event Hubs**, eller **Azure Service Bus** som mål för exporten. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny löpande dataexport](media/howto-export-data/export_new.PNG)

4. I den nedrullningsbara listrutan väljer du din **Storage-konto/Event Hubs-namnområde/Service Bus-namnområde**. Du kan också välja alternativet sist i listan som **ange en anslutningssträng**. 

    > [!NOTE] 
    > Du ser bara Storage konton/Event Hubs-namnområden/Service Bus-namnområden i den **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen kan du välja **ange en anslutningssträng** och finns i steg 5.

    > [!NOTE] 
    > 7 dagars utvärderingsversion appar, det enda sättet att konfigurera kontinuerlig data exportera är i via en anslutningssträng. Det beror på att 7 dagars utvärderingsversion appar inte har en associerad Azure-prenumeration.

    ![Skapa ny cde Event Hub](media/howto-export-data/export_create.PNG)

5. (Valfritt) Om du har valt **ange en anslutningssträng**, en ny ruta visas där du kan klistra in anslutningssträngen. Att hämta anslutningssträngen för din:
    - Storage-konto, gå till Storage-konto i Azure Portal.
        - Under **inställningar**, klickar du på **åtkomstnycklar**
        - Kopiera anslutningssträngen key1 och key2-anslutningssträng
    - Event Hubs eller Azure Service Bus, går du till namnområdet i Azure Portal.
        - Under **inställningar**, klickar du på **principer för delad åtkomst**
        - Välj standard **RootManageSharedAccessKey** eller skapa en ny
        - Kopiera den primär eller sekundär anslutningssträngen
 
6. Välj en behållare/Event hub/kö eller ämne i nedrullningsbara listrutan.

7. Under **Data som ska exporteras**, ange varje typ av data som ska exporteras genom att ställa in typen **på**.

6. Se till att aktivera löpande dataexport genom **dataexport** är **på**. Välj **Spara**.

  ![Konfigurera löpande dataexport](media/howto-export-data/export_list.PNG)

7. Efter ett par minuter visas dina data i ditt valda mål.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data kan du fortsätta till nästa steg:

> [!div class="nextstepaction"]
> [Exportera data till Azure Blob Storage](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Exportera data till Azure Event Hubs och Azure Service Bus](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Så här att visualisera dina data i Power BI](howto-connect-powerbi.md)
