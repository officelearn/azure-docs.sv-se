---
title: 'Snabbstart: Skapa frontdörrprofil för hög tillgänglighet av applikationer'
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
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521459"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snabbstart: Skapa en Front Door för en global webbapp med hög tillgänglighet

I den här snabbstartsartikeln beskrivs hur du skapar en Front Door-profil som ger hög tillgänglighet och höga prestanda för din globala webbapp. 

Det scenario som beskrivs i den här snabbstarten innehåller två instanser av en webbapp som körs i olika Azure-regioner. En Front Door-konfiguration som baseras på jämnt [viktade serverdelar med samma prioritet](front-door-routing-methods.md) skapas som hjälper dig att dirigera användartrafik till den närmaste uppsättningen webbplatsserverdelar där programmet körs. Front Door övervakar webbappen kontinuerligt och ger automatisk redundans till nästa tillgängliga serverdel när den närmaste platsen inte är tillgänglig.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på Azure Portal på https://portal.azure.com.

## <a name="prerequisites"></a>Krav
Den här snabbstarten kräver att du har distribuerat två instanser av en webbapp som körs i olika Azure-regioner (*USA, östra* och *Europa, västra*). Båda webbprograminstanserna körs med aktiv/aktiv-läget. De innebär att båda två kan hantera trafik när som helst till skillnad från en konfiguration av typen aktiv eller vänteläge där den ena fungerar som en redundansväxling.

1. Välj Skapa en > **resurswebbapp** > **Web App**längst **Create a resource**upp till vänster på skärmen .
2. I **Webbapp** anger eller väljer du följande information och anger standardinställningarna där de inte har angetts:

     | Inställning         | Värde     |
     | ---              | ---  |
     | Resursgrupp          | Välj **Ny** och skriv sedan *myResourceGroupFD1* |
     | Namn           | Ange ett unikt namn för din webbapp  |
     | Körningsstack          | Välj en Runtime-stack för din app |
     |      Region  |   USA, västra        |
     | App Service-plan/plats         | Välj **Ny**.  I App Service-planen anger du *myAppServicePlanEastUS* och väljer sedan **OK**.| 
     |SKU och storlek  | Välj **Ändra storlek** Välj Standard **S1 100 totalt ACU, 1,75 GB minne** |
     
3. Välj **Granska + Skapa**.
4. Granska sammanfattningsinformationen för webbappen. Välj **Skapa**.
5. Efter cirka 5 minuter skapas en standardwebbplats när webbappen har distribuerats.
6. Upprepa steg 1–3 för att skapa en andra webbplats i en annan Azure-region med följande inställningar:

     | Inställning         | Värde     |
     | ---              | ---  |
     | Resursgrupp          | Välj **Ny** och skriv sedan *myResourceGroupFD2* |
     | Namn           | Ange ett unikt namn för din webbapp  |
     | Körningsstack          | Välj en Runtime-stack för din app |
     |      Region  |   Europa, västra      |
     | App Service-plan/plats         | Välj **Ny**.  I App Service-planen anger du *myAppServicePlanWestEurope* och väljer sedan **OK**.|   
     |SKU och storlek  | Välj **Ändra storlek** Välj Standard **S1 100 totalt ACU, 1,75 GB minne** |
    
## <a name="create-a-front-door-for-your-application"></a>Skapa en Front Door för programmet
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Lägga till en klientdelsvärd för Front Door
Skapa en Front Door-konfiguration som dirigerar trafik för användare baserat på den kortaste svarstiden mellan två serverdelar.

1. På den övre vänstra sidan av skärmen väljer du **Skapa en resurs** > **Nätverksdörr** > **Front Door**.
2. Ange eller välj följande information i **Skapa en ytterdörr**och ange standardinställningar där ingen anges:

     | Inställning         | Värde     |
     | ---              | ---  |
     |Prenumeration  | Välj den prenumeration som du vill att Ytterdörren ska skapas i.|
     | Resursgrupp          | Välj **Nytt**och skriv sedan *myResourceGroupFD0* |
     | Resursgruppsplats  |   USA, centrala        |
     
     > [!NOTE]
     > Du behöver inte skapa en ny resursgrupp som du kan distribuera ytterdörren till.  Om du också kan välja en befintlig resursgrupp.
     
3. Klicka på **Nästa: Konfiguration**.
4. Klicka på +-ikonen på frontends/domänkortet.  Ange för `<Your Initials>frontend` **Värdnamn** . Detta värdnamn måste vara globalt unikt, Ytterdörren tar hand om valideringen.
5. Klicka på **Lägg till**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Lägg till serverdelen och serverdelspooler för programmet

Därefter måste du konfigurera dina frontends/domäner och programserverhend i en serverdelspool för att kunna veta var ditt program finns. 

1. Klicka på ikonen +på Backend-poolkortet om du vill lägga till en backend-pool för **Namn** för backend-poolen anger du `myBackendPool`.
2. Klicka sedan på **Lägg till en backend** för att lägga till dina webbplatser som skapats tidigare.
3. Välj **Värdtyp för Backend** som "Apptjänst", välj den prenumeration där du skapade webbplatsen och välj sedan den första webbplatsen i listrutan **För namn på backend.Select Backend** host type as 'App Service', select the subscription in which you created the site and then choose the first web site from the Backend host name dropdown.
4. Lämna de återstående fälten som de är för tillfället och klicka på **Lägg till**.
5. Välj **Värdtyp för Backend** som "Apptjänst", välj den prenumeration där du skapade webbplatsen och välj sedan den andra webbplatsen i listrutan **För namn i Backend.Select Backend** host type as 'App Service', select the subscription in which you created the site and then choose the **second** web site from the Backend host name dropdown.
6. Lämna de återstående fälten som de är för tillfället och klicka på **Lägg till**.
7. Du kan också välja att uppdatera inställningarna för hälsoavsökningar och belastningsutjämning för serverdapoolen, men standardvärdena bör också fungera. I båda fallen klickar du på **Lägg till**.


### <a name="c-add-a-routing-rule"></a>C. Lägga till en hanteringsregel
1. Klicka slutligen på +-ikonen på routningsregler för att konfigurera en routningsregel. Detta behövs för att mappa din serverdelsvärd till serverdelspoolen som i praktiken är konfigurerad som om en begäran kommer till `myappfrontend.azurefd.net` och därefter vidarebefordrar den till serverdelspoolen `myBackendPool`. 
2. För **Namn** ange 'LocationRule'.
3. Klicka på **Lägg till** att lägga till en hanteringsregel för din Front Door. 
4. Klicka på **Granska och skapa**.
5. Granska inställningarna för skapandet av ytterdörren. Klicka på **Skapa**

>[!WARNING]
> Du **måste** se till att alla klientdelsvärdar i din Front Door har en hanteringsregel med en standardsökväg ('/\*') som är associerad med den. Det betyder att för alla dina hanteringsregler måste det finnas minst en hanteringsregel för alla dina klientdelsvärdar som har definierats på standardsökvägen ('/\*'). Om du inte gör det kan det resultera i att slutanvändartrafiken inte dirigeras korrekt.

## <a name="view-front-door-in-action"></a>Se Front Door i praktiken
När du har skapat en Front Door tar det några minuter innan konfigurationen distribueras globalt överallt. När det är klart öppnar du serverdelsvärden du skapade, det vill säga, du går till en webbläsare och anger webbadressen `myappfrontend.azurefd.net`. Din begäran dirigeras automatiskt till närmaste den serverdelen från den angivna serverdelen i serverdelspoolen. 

### <a name="view-front-door-handle-application-failover"></a>Visa när Front Door hanterar programredundans
Om du vill testa hur Front Doors omedelbara globala redundans fungerar i praktiken går du till en av de webbplatser som du skapade och stoppar den. Baserat på inställningen för Hälsoavsökning som definierats för serverdelspoolen växlar vi omedelbart trafiken till den andra webbplatsdistributionen. Du kan också testa beteende genom att inaktivera serverdelen i serverdelspoolens konfiguration för Front Door. 

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs tar du bort resursgrupperna **myResourceGroupFD1,** **myResourceGroupFD2**och **myResourceGroupFD0:**

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en Front Door där du kan dirigera användartrafik för webbprogram som kräver hög tillgänglighet och maximala prestanda. Mer information om att dirigera trafik finns i [Routningsmetoder](front-door-routing-methods.md) som används av Front Door.
