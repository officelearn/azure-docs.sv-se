---
title: Lokal felsökning av Azure Functions-händelserutnät
description: Lär dig att felsöka Azure-funktioner lokalt som utlöses av en eventrutnätshändelse
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227071"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Lokal felsökning av Event Grid-utlösare i Azure Functions

Den här artikeln visar hur du felsöker en lokal funktion som hanterar en Azure Event Grid-händelse som utlöses av ett lagringskonto. 

## <a name="prerequisites"></a>Krav

- Skapa eller använda en befintlig funktionsapp
- Skapa eller använda ett befintligt lagringskonto
- Ladda ner [ngrok](https://ngrok.com/) så att Azure kan anropa din lokala funktion

## <a name="create-a-new-function"></a>Skapa en ny funktion

Öppna funktionsappen i Visual Studio och högerklicka på projektnamnet i Lösningsutforskaren och klicka på **Lägg till > Ny Azure-funktion**.

I fönstret *Ny Azure-funktion* väljer du **Utlösare för Händelserutnät** och klickar på **OK**.

![Skapa ny funktion](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

När funktionen har skapats öppnar du kodfilen och kopierar webbadressen som kommenteras högst upp i filen. Den här platsen används när du konfigurerar utlösaren för händelserutnätet.

![Kopiera plats](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Ställ sedan in en brytpunkt på raden `log.LogInformation`som börjar med .

![Ange brytpunkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Tryck **sedan på F5** för att starta en felsökningssession.

## <a name="allow-azure-to-call-your-local-function"></a>Tillåt att Azure anropar din lokala funktion

Om du vill bryta dig in i en funktion som används på datorn måste du aktivera ett sätt för Azure att kommunicera med din lokala funktion från molnet.

[Ngrok-verktyget](https://ngrok.com/) är ett sätt för Azure att anropa funktionen som körs på din dator. Starta *ngrok* med följande kommando:

```bash
ngrok http -host-header=localhost 7071
```
När verktyget är konfigurerat bör kommandofönstret se ut ungefär som följande skärmbild:

![Starta ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopiera **HTTPS-URL:en** som genereras när *ngrok* körs. Det här värdet används när händelserutnätshändelseslutpunkten konfigureras.

## <a name="add-a-storage-event"></a>Lägga till en lagringshändelse

Öppna Azure-portalen och navigera till ett lagringskonto och klicka på alternativet **Händelser.**

![Lägga till lagringskontohändelse](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Klicka *Events* på knappen **Händelseprenumeration** i händelsefönstret. I fönstret *Jämn prenumeration* klickar du på listrutan *Slutpunktstyp* och väljer **Webbkrok**.

![Välj prenumerationstyp](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

När slutpunktstypen har konfigurerats klickar du på **Välj en slutpunkt** för att konfigurera slutpunktsvärdet.

![Välj slutpunktstyp](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

*Slutpunktsvärdet för Prenumeranten* består av tre olika värden. Prefixet är DEN HTTPS-URL som genereras av *ngrok*. Resten av webbadressen kommer från webbadressen som finns i funktionskodfilen, med funktionsnamnet tillagt i slutet. Från och med URL:en från funktionskodfilen `http://localhost:7071` ersätter `{functionname}` *ngrok-URL:en* och funktionsnamnet ersätter .

Följande skärmbild visar hur den slutliga webbadressen ska se ut:

![Markering av slutpunkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

När du har angett rätt värde klickar du på **Bekräfta markering**.

> [!IMPORTANT]
> Varje gång du startar *ngrok*återskapas HTTPS-URL:en och värdet ändras. Därför måste du skapa en ny händelseprenumeration varje gång du visar din funktion för Azure via *ngrok*.

## <a name="upload-a-file"></a>Överför en fil

Nu kan du ladda upp en fil till ditt lagringskonto för att utlösa en Event Grid-händelse som din lokala funktion kan hantera. 

Öppna [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och anslut till ditt lagringskonto. 

- Expandera **blob-behållare** 
- Högerklicka och välj **Skapa blob-behållare**.
- Namnge **behållartestet**
- Välj *testbehållaren*
- Klicka på knappen **Ladda upp**
- Klicka på **Ladda upp filer**
- Markera en fil och ladda upp den till blob-behållaren

## <a name="debug-the-function"></a>Felsök funktionen

När händelserutnätet känner igen en ny fil överförs till lagringsbehållaren träffas brytpunkten i din lokala funktion.

![Starta ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapas i den här artikeln tar du bort **testbehållaren** i ditt lagringskonto.

## <a name="next-steps"></a>Nästa steg

- [Automatisera storleksändring av överförda bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Utlösare av händelserutnät för Azure-funktioner](./functions-bindings-event-grid.md)
