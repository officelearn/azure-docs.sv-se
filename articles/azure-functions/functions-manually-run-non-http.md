---
title: Köra en Azure-funktion som inte utlösts av HTTP manuellt
description: Använda en HTTP-begäran för att köra icke-HTTP-utlöst Azure Functions
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 6c77e58e626ba370a6278a0f01b09578930cba09
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247120"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Köra en funktion som inte utlösts av HTTP manuellt

Den här artikeln visar hur du manuellt kör en icke HTTP-utlöst funktion via en särskilt formaterad HTTP-begäran.

I vissa sammanhang kan du behöva köra en Azure-funktion ”på begäran” som utlöses indirekt.  Exempel på indirekta utlösare är [funktioner enligt ett schema](./functions-create-scheduled-function.md) eller funktioner som fungerar som ett resultat av [en annan resursåtgärd](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) används i följande exempel, men du kan använda [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) eller något annat liknande verktyg för att skicka HTTP-begäranden.

## <a name="define-the-request-location"></a>Definiera platsen för begäran

Om du vill köra en icke HTTP-utlöst funktion behöver du ett sätt att skicka en begäran till Azure för att köra funktionen. Den URL som används för att göra denna begäran kräver ett visst formulär.

![Definiera platsen för begäran: värdnamn och mappsökväg + funktionsnamn](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Värdnamn:** Funktionsappens allmänna plats som består av funktionsappens namn plus *azurewebsites.net* eller din anpassade domän.
- **Mappsökväg:** För att komma åt icke HTTP-utlösta funktioner via en HTTP-begäran behöver du skicka begäran via mapparna *admin/functions*.
- **Funktionsnamn:** Namnet på funktionen som du vill köra.

Du kan använda den här platsen för begäran i Postman tillsammans med funktionens huvudnyckel i förfrågan till Azure om att köra funktionen.

## <a name="get-the-functions-master-key"></a>Hämta funktionens huvudnyckel

Gå till din funktion på Azure-portalen och klicka på **Hantera** och leta upp avsnittet **Host Keys** (Värdnycklar). Klicka på knappen **Kopiera** på raden *_master* som ska kopiera huvudnyckeln till Urklipp.

![Kopiera huvudnyckeln från hanteringsfunktionsskärmen](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Klicka på funktionsnamnet att återgå till kodfilfönstret när du har kopierat huvudnyckeln. Klicka sedan på fliken **Loggar**. Du ser loggade meddelanden från funktionen här när du kör funktionen manuellt från Postman.

> [!CAUTION]  
> På grund av de utökade behörigheterna i din funktionsapp som beviljats av huvudnyckeln bör du inte dela den här nyckeln med tredje part eller distribuera den i ett program.

## <a name="call-the-function"></a>Anropa funktionen

Öppna Postman och följ de här stegen:

1. Ange din **begäran om plats i URL-textrutan**.
2. Kontrollera att HTTP-metoden är inställd på **POST**.
3. **Klicka** på fliken **Rubriker**.
4. Ange **x-functions-key** som den första **nyckeln** och klistra in huvudnyckeln (från Urklipp) till **värderutan**.
5. Skriv **Content-Type** som den andra **nyckeln** och skriv **application/json** som **värdet**.

    ![Inställningar för rubriker i Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Klicka** på fliken **Brödtext**.
7. Ange **{”input”: ”test”}** som brödtext för begäran.

    ![Inställningar för brödtext i Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Klicka på **Skicka**.

    ![Skicka en begäran med Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman rapporterar statusen **202 - Godkänd**.

Gå därefter tillbaka till din funktion på Azure-portalen. Leta upp fönstret *Loggar* så ser du meddelanden som kommer från det manuella anropet till funktionen.

![Funktionsloggresultat från manuellt anrop](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Nästa steg

- [Strategier för att testa din kod i Azure Functions](./functions-test-a-function.md)
- [Lokal felsökning av Event Grid-utlösare i Azure Functions](./functions-debug-event-grid-trigger-local.md)
