---
title: Övervaka Azure Media Services-händelser med Event Grid med hjälp av portalen | Microsoft Docs
description: Den här artikeln visar hur du prenumererar på Event Grid för att övervaka Azure Media Services-händelser.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: d4592c93cb7969c45a107d7365a1b9dabf11f412
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326537"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Skapa och övervaka Media Services-händelser med Event Grid med Azure portal

Azure Event Grid är en händelsetjänst för molnet. Den här tjänsten använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter. Media Services-händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom egenskapen händelsetyp börjar med ”Microsoft.Media”. Mer information finns i [Media Services Händelsescheman](media-services-event-schemas.md).

I den här artikeln använder du Azure-portalen för att prenumerera på händelser för Azure Media Services-kontot. Sedan kan utlösa du händelser för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. I artikeln skicka vi händelser till en webbapp som samlar in och visar meddelandena.

När du är klar kan se du att händelsedata som har skickats till webbappen.

## <a name="prerequisites"></a>Nödvändiga komponenter 

* Ha en aktiv Azure-prenumeration.
* Skapa ett nytt Azure Media Services-konto, som beskrivs i [den här snabbstarten](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelser för Media Services-kontot ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. I den här artikeln får du distribuera en [färdiga webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelandena som händelsen. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Om du byter till webbplatsen ”Azure Grid Loggboken” kan se du den har inga händelser ännu.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Prenumerera på händelser för Media Services

Du prenumererar på ett ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna.

1. Välj Media Services-kontot i portalen och välj **händelser**.
1. För att skicka händelser till visningsprogrammet använder du en webhook för slutpunkten. 

   ![Välj webhook](./media/monitor-events-portal/select-web-hook.png)

1. Händelseprenumerationen är fylls i automatiskt med värden för Media Services-kontot. 
1. Välj ”Webhook” för den **Slutpunktstyp**.
1. I det här avsnittet vi lämnar den **prenumerera på alla händelsetyper** kontrolleras. Dock kan du avmarkera det och filtrera fram specifika händelsetyper. 
1. Klicka på den **väljer du en slutpunkt** länk.

    För webhookens slutpunkt anger du webbappens webbadress och lägger till `api/updates` till startsidans webbadress. 

1. Tryck på **bekräfta val**.
1. Tryck på **Skapa**.
1. Namnge prenumerationen.

   ![Välj loggar](./media/monitor-events-portal/create-subscription.png)

1. Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. 

    Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Slutpunkten har att ställa in `validationResponse` till `validationCode`. Mer information finns i [Event Grid säkerhet och autentisering](../../event-grid/security-authentication.md). Du kan visa koden för webbappen om du vill se hur den validerar prenumerationen.

Nu ska vi utlösa händelser för att se hur Event Grid distribuerar meddelandet till slutpunkten.

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Du kan utlösa händelser för Media Services-kontot genom att köra ett kodningsjobb. Du kan följa [snabbstarten](stream-files-dotnet-quickstart.md) att koda en fil och börja skicka händelser. Om du prenumererar på alla händelser, visas en skärm som liknar följande:

> [!TIP]
> Välj ögonikonen för att utöka informationen om händelsen. Uppdatera inte sidan, om du vill visa alla händelser.

![Visa prenumerationshändelse](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma](stream-files-tutorial-with-api.md)
