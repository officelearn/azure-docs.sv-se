---
title: Azure Functions Event Grid lokal felsökning
description: Lär dig att felsöka lokalt Azure functions som utlöses av en Event Grid-händelse
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 96d88fafd6824ed85f1d91bab59374b3490a55b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60428326"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure-funktion Event Grid utlösa lokal felsökning

Den här artikeln visar hur du felsöker en lokal funktion som hanterar en Azure Event Grid-händelse som aktiverats av ett lagringskonto. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- Skapa eller använda en befintlig funktionsapp
- Skapa eller använda ett befintligt lagringskonto
- Ladda ned [ngrok](https://ngrok.com/) så att Azure för att anropa din lokala funktion

## <a name="create-a-new-function"></a>Skapa en ny funktion

Öppna din funktionsapp i Visual Studio och, högerklicka på projektnamnet i Solution Explorer och klicka på **Lägg till > Ny Azure Function**.

I den *ny Azure Function* väljer **Event Grid-utlösare** och klicka på **OK**.

![Skapa ny funktion](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

När funktionen har skapats kan du öppna kodfilen och kopiera URL: en som har kommenterats bort överst i filen. Den här platsen används när du konfigurerar Event Grid-utlösaren.

![Kopiera plats](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Ange sedan en brytpunkt på raden som börjar med `log.LogInformation`.

![Ange brytpunkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Nästa **tryck på F5** att starta en felsökningssession.

## <a name="allow-azure-to-call-your-local-function"></a>Tillåt Azure för att anropa din lokala funktion

Om du vill bryta in i en funktion som felsöks på din dator, måste du aktivera ett sätt för Azure för att kommunicera med din lokala funktion från molnet.

Den [ngrok](https://ngrok.com/) verktyget gör det möjligt för Azure för att anropa funktionen som körs på din dator. Starta *ngrok* med följande kommando:

```bash
ngrok http -host-header=localhost 7071
```
När verktyget har konfigurerats, bör kommandofönstret likna följande skärmbild:

![Starta ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopiera den **HTTPS** URL: en genereras när *ngrok* körs. Det här värdet används när du konfigurerar event grid event slutpunkten.

## <a name="add-a-storage-event"></a>Lägg till en storage-händelse

Öppna Azure-portalen och gå till ett lagringskonto och klicka på den **händelser** alternativet.

![Lägg till händelse för storage-konto](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

I den *händelser* fönstret, klicka på den **händelseprenumeration** knappen. I den *även prenumeration* fönstret, klicka på den *Slutpunktstyp* listrutan och välj **Webhook**.

![Välj typ av prenumeration](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

När typ av slutpunkt har konfigurerats klickar du på **väljer du en slutpunkt** konfigurera slutpunktsvärdet.

![Välj typ av slutpunkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Den *slutpunkt för prenumerant* värdet består från tre olika värden. Prefixet är HTTPS-URL som genererats av *ngrok*. Resten av URL: en kommer från URL: en finns i kodfilen funktionen med funktionsnamnet har lagts till i slutet. Från och med URL: en från funktionens kodfil, den *ngrok* URL ersätter `http://localhost:7071` och funktionen namnge ersätter `{functionname}`.

Skärmbilden nedan visar hur den slutliga URL bör se ut:

![Val av slutpunkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

När du har angett rätt värde, klickar du på **bekräfta valet**.

> [!IMPORTANT]
> Varje gång du startar *ngrok*, HTTPS-URL: en genereras och värdet ändras. Du måste därför skapa en händelseprenumeration varje gång som du exponera din funktion till Azure via *ngrok*.

## <a name="upload-a-file"></a>Överför en fil

Nu kan du överföra en fil till ditt lagringskonto för att aktivera en Event Grid-händelse att hantera lokala funktionen. 

Öppna [Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) och ansluta till i ditt storage-konto. 

- Expandera **Blob-behållare** 
- Högerklicka och välj **skapa Blobbehållare**.
- Namnge behållaren **testa**
- Välj den *testa* behållare
- Klicka på den **överför** knappen
- Klicka på **ladda upp filer**
- Välj en fil och överföra den till blob-behållaren

## <a name="debug-the-function"></a>Felsöka funktionen

När Event Grid identifierar en ny fil har överförts till behållaren, nå brytpunkten i din lokala funktion.

![Starta ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapades i den här artikeln kan du ta bort den **testa** behållare i ditt storage-konto.

## <a name="next-steps"></a>Nästa steg

- [Automatisera storleksändring av överförda bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid-utlösare för Azure Functions](./functions-bindings-event-grid.md)
