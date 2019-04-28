---
title: Azure-snabbstart – Skapa en Front Door-profil för hög tillgänglighet för program som använder Azure-portalen
description: I den här snabbstartsartikeln beskrivs hur du skapar en Front Door för ditt globala webbprogram för hög tillgänglighet och höga prestanda.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 6bcd5bcc2463ec1ab9dcc97644d5046c31bfc78b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461994"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snabbstart: Skapa en ytterdörren för en högtillgänglig globala webbprogram

I den här snabbstartsartikeln beskrivs hur du skapar en Front Door-profil som ger hög tillgänglighet och höga prestanda för din globala webbapp. 

Det scenario som beskrivs i den här snabbstarten innehåller två instanser av en webbapp som körs i olika Azure-regioner. En Front Door-konfiguration som baseras på jämnt [viktade serverdelar med samma prioritet](front-door-routing-methods.md) skapas som hjälper dig att dirigera användartrafik till den närmaste uppsättningen webbplatsserverdelar där programmet körs. Front Door övervakar webbappen kontinuerligt och ger automatisk redundans till nästa tillgängliga serverdel när den närmaste platsen inte är tillgänglig.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på Azure Portal på https://portal.azure.com.

## <a name="prerequisites"></a>Nödvändiga komponenter
Den här snabbstarten kräver att du har distribuerat två instanser av en webbapp som körs i olika Azure-regioner (*USA, östra* och *Europa, västra*). Båda webbprograminstanserna körs med aktiv/aktiv-läget. De innebär att båda två kan hantera trafik när som helst till skillnad från en konfiguration av typen aktiv eller vänteläge där den ena fungerar som en redundansväxling.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Webb** > **Webbapp** > **Skapa**.
2. I **Webbapp** anger eller väljer du följande information och anger standardinställningarna där de inte har angetts:

     | Inställning         | Värde     |
     | ---              | ---  |
     | Namn           | Ange ett unikt namn för din webbapp  |
     | Resursgrupp          | Välj **Ny** och skriv sedan *myResourceGroupFD1* |
     | App Service-plan/plats         | Välj **Ny**.  I App Service-planen anger du *myAppServicePlanEastUS* och väljer sedan **OK**. 
     |      Location  |   Östra USA        |
    |||

3. Välj **Skapa**.
4. En standardwebbplats skapas när webbappen har distribuerats.
5. Upprepa steg 1–3 för att skapa en andra webbplats i en annan Azure-region med följande inställningar:

     | Inställning         | Värde     |
     | ---              | ---  |
     | Namn           | Ange ett unikt namn för din webbapp  |
     | Resursgrupp          | Välj **Ny** och skriv sedan *myResourceGroupFD2* |
     | App Service-plan/plats         | Välj **Ny**.  I App Service-planen anger du *myAppServicePlanWestEurope* och väljer sedan **OK**. 
     |      Location  |   Västra Europa      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Skapa en Front Door för programmet
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Lägga till en klientdelsvärd för Front Door
Skapa en Front Door-konfiguration som dirigerar trafik för användare baserat på den kortaste svarstiden mellan två serverdelar.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Front Door** > **Skapa**.
2. I **Skapa en Front Door** börjar du med att lägga till den grundläggande informationen och ange en prenumeration där du vill att åtkomsten ska konfigureras. På samma sätt som för andra Azure-resurser måste du också ange en ResourceGroup och region för en resursgrupp om du skapar en ny. Slutligen måste du ange ett namn på din Front Door.
3. När den grundläggande informationen har fyllts i är det första steget du behöver definiera **klientdelsvärden** för konfigurationen. Resultatet bör vara ett giltigt domännamn som `myappfrontend.azurefd.net`. Det här värdnamnet måste vara globalt unikt men Front Door tar hand om den verifieringen. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Lägg till serverdelen och serverdelspooler för programmet

Därefter måste du konfigurera programmets serverdelar i en serverdelspool för att Front Door ska veta var ditt program finns. 

1. Klicka på ikonen ”+” för att lägga till en serverdelspool och ange därefter ett **namn** på serverdelspoolen, till exempel `myBackendPool`.
2. Klicka på alternativet för att lägga till serverdelar och lägg till dina webbplatser som skapades tidigare.
3. Välj **Typ av målvärd** som ”App Service”, välj prenumerationen där du skapade webbplatsen och välj sedan den första webbplatsen från **Målvärdsnamn**, det vill säga  *myAppServicePlanEastUS.azurewebsites.net*.
4. Lämna de återstående fälten som de är för tillfället och klicka på **Lägg till**.
5. Upprepa steg 2 till 4 för att lägga till den andra webbplatsen, det vill säga *myAppServicePlanWestEurope.azurewebsites.net*
6. Du kan välja att uppdatera inställningarna för Hälsoavsökningar och belastningsutjämning för serverdelspoolen, men standardvärdena bör också fungera. Klicka på **Lägg till**.


### <a name="c-add-a-routing-rule"></a>C. Lägga till en hanteringsregel
Till sist ska du klicka på ikonen ”+” vid Hanteringsregler för att konfigurera en hanteringsregel. Detta behövs för att mappa din serverdelsvärd till serverdelspoolen som i praktiken är konfigurerad som om en begäran kommer till `myappfrontend.azurefd.net` och därefter vidarebefordrar den till serverdelspoolen `myBackendPool`. Klicka på **Lägg till** att lägga till en hanteringsregel för din Front Door. Nu kan du skapa åtkomsten och klicka på **Granska och skapa**.

>[!WARNING]
> Du **måste** se till att alla klientdelsvärdar i din Front Door har en hanteringsregel med en standardsökväg ('/\*') som är associerad med den. Det betyder att för alla dina hanteringsregler måste det finnas minst en hanteringsregel för alla dina klientdelsvärdar som har definierats på standardsökvägen ('/\*'). Om du inte gör det kan det resultera i att slutanvändartrafiken inte dirigeras korrekt.

## <a name="view-front-door-in-action"></a>Se Front Door i praktiken
När du har skapat en Front Door tar det några minuter innan konfigurationen distribueras globalt överallt. När det är klart öppnar du serverdelsvärden du skapade, det vill säga, du går till en webbläsare och anger webbadressen `myappfrontend.azurefd.net`. Din begäran dirigeras automatiskt till närmaste den serverdelen från den angivna serverdelen i serverdelspoolen. 

### <a name="view-front-door-handle-application-failover"></a>Visa när Front Door hanterar programredundans
Om du vill testa hur Front Doors omedelbara globala redundans fungerar i praktiken går du till en av de webbplatser som du skapade och stoppar den. Baserat på inställningen för Hälsoavsökning som definierats för serverdelspoolen växlar vi omedelbart trafiken till den andra webbplatsdistributionen. Du kan också testa beteende genom att inaktivera serverdelen i serverdelspoolens konfiguration för Front Door. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort resursgrupperna, webbapparna och alla relaterade resurser när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en Front Door där du kan dirigera användartrafik för webbprogram som kräver hög tillgänglighet och maximala prestanda. Mer information om att dirigera trafik finns i [Routningsmetoder](front-door-routing-methods.md) som används av Front Door.