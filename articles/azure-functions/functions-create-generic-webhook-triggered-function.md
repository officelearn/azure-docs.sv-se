---
title: "Skapa en funktion i Azure som utlöses av en allmän webhook | Microsoft Docs"
description: "Använd Azure Functions för att skapa en serverlösa funktion som anropas av en webhook i Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: fafc10c0-84da-4404-b4fa-eea03c7bf2b1
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/12/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: f283f8d79c5ae5fb6a72c84c9e9edb7bb8de4a83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-a-generic-webhook"></a>Skapa en funktion som utlöses av en allmän webhook

Med Azure Functions kan du köra kod i en serverfri miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. Du kan till exempel konfigurera en funktion som utlöses av en avisering som skapats av Azure-Monitor. Det här avsnittet visar hur du kör C#-kod när en resursgrupp läggs till din prenumeration.   

![Allmän webhook aktiveras funktionen i Azure-portalen](./media/functions-create-generic-webhook-triggered-function/function-completed.png)

## <a name="prerequisites"></a>Krav 

För att slutföra den här självstudien behöver du:

+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-function"></a>Skapa en allmän webhook utlöses-funktion

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om den här funktionen är den första i funktionen appen, Välj **anpassad funktionen**. Detta visar en fullständig uppsättning med funktionsmallar.

    ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-generic-webhook-triggered-function/add-first-function.png)

2. Välj den **allmän WebHook - C#** mall. Skriv ett namn för ditt C#-funktionen och sedan välja **skapa**.

     ![Skapa en allmän webhook utlöses funktion i Azure-portalen](./media/functions-create-generic-webhook-triggered-function/functions-create-generic-webhook-trigger.png) 

2. Klicka på den nya funktionen **<> / Get funktions-URL**, kopiera och spara värdet. Du kan använda det här värdet för att konfigurera webhooken. 

    ![Granska funktionskoden](./media/functions-create-generic-webhook-triggered-function/functions-copy-function-url.png)
         
Därefter skapar du en webhook-slutpunkt i en aktivitet loggen avisering i Azure-Monitor. 

## <a name="create-an-activity-log-alert"></a>Skapa en aktivitet loggen avisering

1. I Azure portal, navigerar du till den **övervakaren** tjänsten, Välj **aviseringar**, och klicka på **Lägg till aktivitet loggen avisering**.   

    ![Övervaka](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert.png)

2. Använd inställningarna på det sätt som beskrivs i tabellen:

    ![Skapa en aktivitet loggen avisering](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Aktiviteten loggnamn för avisering** | resurs-grupp-skapa-avisering | Namnet på aktiviteten loggen aviseringen. |
    | **Prenumeration** | Din prenumeration | Den prenumeration som du använder för den här kursen. | 
    |  **Resursgrupp** | myResourceGroup | Den resursgrupp som aviseringen resurser har distribuerats till. Med hjälp av samma resursgrupp som din funktionsapp gör det enklare att rensa när du har slutfört guiden. |
    | **Händelsekategori** | Administrativa | Den här kategorin innehåller ändringar som gjorts i Azure-resurser.  |
    | **Resurstyp** | Resursgrupper | Filtrerar aviseringar till resursen gruppaktiviteter. |
    | **Resursgrupp**<br/>och **resurs** | Alla | Övervaka alla resurser. |
    | **Åtgärdsnamn** | Skapa resursgrupp | Filtrerar aviseringar för att skapa åtgärder. |
    | **Nivå** | Information | Omfatta nivå informationsaviseringar. | 
    | **Status** | Lyckades | Filtrerar aviseringar till åtgärder som har slutförts. |
    | **Grupp** | Ny | Skapa en ny åtgärdsgrupp, som definierar åtgärden tar när en avisering utlöses. |
    | **Åtgärden gruppnamn** | funktionen webhooken | Ett namn som identifierar åtgärdsgruppen.  | 
    | **Kort namn** | funcwebhook | Ett kort namn för åtgärdsgruppen. |  

3. I **åtgärder**, lägga till en åtgärd med inställningar som anges i tabellen: 

    ![Lägg till en grupp](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings-2.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | CallFunctionWebhook | Ett namn för åtgärden. |
    | **Åtgärdstyp** | Webhook | Svaret på aviseringen är att en Webhooksadressen anropas. |
    | **Detaljer** | Funktions-URL | Klistra in i Webhooksadressen för den funktion som du kopierade tidigare. |v

4. Klicka på **OK** att skapa gruppen aviseringen och åtgärden.  

Webhooken kallas nu när gruppen skapas i din prenumeration. Därefter uppdaterar du koden i din funktion för att hantera loggdata JSON i brödtexten i begäran.   

## <a name="update-the-function-code"></a>Uppdatera funktionskoden

1. Gå tillbaka till din funktionsapp i portalen och expandera din funktion. 

2. Ersätt skriptkod C# i funktionen i portalen med följande kod:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"Webhook was triggered!");
    
        // Get the activityLog object from the JSON in the message body.
        string jsonContent = await req.Content.ReadAsStringAsync();
        JToken activityLog = JObject.Parse(jsonContent.ToString())
            .SelectToken("data.context.activityLog");
    
        // Return an error if the resource in the activity log isn't a resource group. 
        if (activityLog == null || !string.Equals((string)activityLog["resourceType"], 
            "Microsoft.Resources/subscriptions/resourcegroups"))
        {
            log.Error("An error occured");
            return req.CreateResponse(HttpStatusCode.BadRequest, new
            {
                error = "Unexpected message payload or wrong alert received."
            });
        }
    
        // Write information about the created resource group to the streaming log.
        log.Info(string.Format("Resource group '{0}' was {1} on {2}.",
            (string)activityLog["resourceGroupName"],
            ((string)activityLog["subStatus"]).ToLower(), 
            (DateTime)activityLog["submissionTimestamp"]));
    
        return req.CreateResponse(HttpStatusCode.OK);    
    }
    ```

Nu kan du testa funktionen genom att skapa en ny resursgrupp i din prenumeration.

## <a name="test-the-function"></a>Testa funktionen

1. Klicka på ikonen resurs grupp till vänster i Azure portal, Välj **+ Lägg till**, ange ett **resursgruppens namn**, och välj **skapa** att skapa en tom resursgrupp.
    
    ![Skapa en resursgrupp.](./media/functions-create-generic-webhook-triggered-function/functions-create-resource-group.png)

2. Gå tillbaka till din funktion och expandera den **loggar** fönster. När du har skapat resursgruppen aktivitet loggen aviseringen utlöser webhooken och funktionen körs. Du kan se namnet på den nya resursgruppen som skrivs till loggarna.  

    ![Lägga till en programinställning för testet.](./media/functions-create-generic-webhook-triggered-function/function-view-logs.png)

3. (Valfritt) Gå tillbaka och ta bort resursgruppen som du skapade. Observera att den här aktiviteten inte utlöses funktionen. Detta beror på att ta bort operations filtreras bort av aviseringen. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när en begäran tas emot från en allmän webhook. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information om webhook-utlösare finns i [Azure Functions HTTP och webhook-bindningar](functions-bindings-http-webhook.md). Mer information om hur du utvecklar funktioner i C# finns [Azure Functions C# skript för utvecklare](functions-reference-csharp.md).

