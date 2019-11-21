---
title: Köra en Azure-funktion som inte utlösts av HTTP manuellt
description: Använda en HTTP-begäran för att köra icke-HTTP-utlöst Azure Functions
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 8198ff6579aff839ff9aacb729e2f3f8d3472fae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230464"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Köra en funktion som inte utlösts av HTTP manuellt

Den här artikeln visar hur du manuellt kör en icke HTTP-utlöst funktion via en särskilt formaterad HTTP-begäran.

I vissa sammanhang kan du behöva köra en Azure-funktion ”på begäran” som utlöses indirekt.  Exempel på indirekta utlösare är [funktioner enligt ett schema](./functions-create-scheduled-function.md) eller funktioner som fungerar som ett resultat av [en annan resursåtgärd](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) används i följande exempel, men du kan använda [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) eller något annat liknande verktyg för att skicka HTTP-begäranden.

## <a name="define-the-request-location"></a>Definiera platsen för begäran

Om du vill köra en icke HTTP-utlöst funktion behöver du ett sätt att skicka en begäran till Azure för att köra funktionen. Den URL som används för att göra denna begäran kräver ett visst formulär.

![Definiera platsen för begäran: värdnamn och mappsökväg + funktionsnamn](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Host name:** The function app's public location that is made up from the function app's name plus *azurewebsites.net* or your custom domain.
- **Folder path:** To access non HTTP-triggered functions via an HTTP request, you have to send the request through the folders *admin/functions*.
- **Function name:** The name of the function you want to run.

Du kan använda den här platsen för begäran i Postman tillsammans med funktionens huvudnyckel i förfrågan till Azure om att köra funktionen.

> [!NOTE]
> När du kör lokalt krävs inte funktionens huvudnyckel. Du kan [anropa funktionen](#call-the-function) direkt genom att utesluta `x-functions-key`-rubriken.

## <a name="get-the-functions-master-key"></a>Hämta funktionens huvudnyckel

Gå till din funktion på Azure-portalen och klicka på **Hantera** och leta upp avsnittet **Host Keys** (Värdnycklar). Klicka på knappen **Kopiera** på raden *_master* som ska kopiera huvudnyckeln till Urklipp.

![Kopiera huvudnyckeln från hanteringsfunktionsskärmen](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Klicka på funktionsnamnet att återgå till kodfilfönstret när du har kopierat huvudnyckeln. Next, click on the **Logs** tab. You'll see messages from the function logged here when you manually run the function from Postman.

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
