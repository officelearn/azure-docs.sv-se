---
title: Snabbstart – Skapa en Traffic Manager-profil för hög tillgänglighet för program med hjälp av Azure-portalen
description: I den här snabbstartsartikeln beskrivs hur du skapar en Traffic Manager-profil för att bygga en webbapp med hög tillgänglighet.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: kumud
ms.openlocfilehash: c969dbc347f0463f6f675a11f69776948d45a9e2
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42022432"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application"></a>Snabbstart: Skapa en Traffic Manager-profil för en webbapp med hög tillgänglighet

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp. 

Det scenario som beskrivs i den här snabbstarten innehåller två instanser av en webbapp som körs i olika Azure-regioner. En Traffic Manager-profil som baseras på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority) skapas som hjälper dig att dirigera användartrafik till den primära platsen där programmet körs. Traffic Manager övervakar webbappen kontinuerligt och ger automatisk redundans till säkerhetskopieringsplatsen när den primära platsen inte är tillgänglig.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på Azure Portal på https://portal.azure.com.

## <a name="prerequisites"></a>Nödvändiga komponenter
Den här snabbstarten kräver att du har distribuerat två instanser av en webbapp som körs i olika Azure-regioner (*USA, östra* och *Europa, västra*). Två instanser av webbapp fungerar som primär och sekundär slutpunkt för Traffic Manager.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Webb** > **Webbapp** > **Skapa**.
2. I **Webbapp** anger eller väljer du följande information och anger standardinställningarna där de inte har angetts:

     | Inställning         | Värde     |
     | ---              | ---  |
     | Namn           | Ange ett unikt namn för din webbapp  |
     | Resursgrupp          | Välj **Ny** och skriv sedan *myResourceGroupTM1* |
     | App Service-plan/plats         | Välj **Ny**.  I App Service-planen anger du *myAppServicePlanEastUS* och väljer sedan **OK**. 
     |      Plats  |   Östra USA        |
    |||

3. Välj **Skapa**.
4. En standardwebbplats skapas när webbappen har distribuerats.
5. Upprepa steg 1–3 för att skapa en andra webbplats i en annan Azure-region med följande inställningar:

     | Inställning         | Värde     |
     | ---              | ---  |
     | Namn           | Ange ett unikt namn för din webbapp  |
     | Resursgrupp          | Välj **Ny** och skriv sedan *myResourceGroupTM2* |
     | App Service-plan/plats         | Välj **Ny**.  I App Service-planen anger du *myAppServicePlanWestEurope* och väljer sedan **OK**. 
     |      Plats  |   Västra Europa      |
    |||


## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil som dirigerar användartrafik baserat på slutpunktsprioritet.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:
    
    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och genererar DNS-namnet, **trafficmanager.net**, som används för att komma åt din Traffic Manager-profil.|
    | Routningsmetod          | Välj routningsmetoden **Prioritet**.|
    | Prenumeration            | Välj din prenumeration.|
    | Resursgrupp          | Välj **Befintlig** och sedan *myResourceGroupTM1*.|
    |Plats |Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.|
    |||
    
    
   ![Skapa en Traffic Manager-profil](./media/quickstart-create-traffic-manager-profile/traffic-manager-profile.png)


## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till webbplatsen i *USA, östra* som primär slutpunkt för att dirigera all användartrafik. Lägg till webbplatsen i *Europa, västra* som en reservslutpunkt. När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till den sekundära slutpunkten.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myPrimaryEndpoint                                        |
    | Målresurstyp           | App Service                          |
    | Målresurs          | **Välj en apptjänst** för att visa listor över Web Apps i samma prenumeration. I **Resurs** väljer du den apptjänst som du vill lägga till som den första slutpunkten. |
    | Prioritet               | Välj **1**. Detta resulterar i all trafik går igenom den här slutpunkten, förutsatt att den är felfri.    |
    
4. Upprepa steg 2 och 3 för nästa Web Apps-slutpunkt. Se till att lägga till den med dess **Prioritet**-värde angivet som **2**.
5.  När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

    ![Lägga till en Traffic Manager-slutpunkt](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint2.png)

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil
I det här avsnittet bestämmer du först domännamnet för din Traffic Manager-profil och ser sedan hur Traffic Manager redundansväxlar till den sekundära slutpunkten när den primära slutpunkten är inte tillgänglig.
### <a name="determine-the-dns-name"></a>Bestämma DNS-namnet
1.  I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil.
  
   ![DNS-namn för Traffic Manager](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

1. I en webbläsare skriver du in DNS-namnet för din Traffic Manager-profil för att visa standardwebbplatsen för din webbapp. I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten som har angetts till **Prioritet 1**.

![Testa Traffic Manager-profil](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. För att se hur Traffic Manager-redundans fungerar i praktiken inaktiverar du din primära plats på följande sätt:
    1. På Traffic Manager-profilsidan väljer du**Inställningar**>**Slutpunkter**>*MyPrimaryEndpoint*.
    2. I *MyPrimaryEndpoint* väljer du **Inaktiverad**. 
    3. Status för den primära slutpunkten *MyPrimaryEndpoint* visas nu som **Inaktiverad**.
3. Kopiera DNS-namnet för din Traffic Manager-profil från det föregående steget för att visa webbplatsen i en webbläsare. När den primära slutpunkten är inaktiverad dirigeras användartrafiken till den sekundära slutpunkten.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort resursgrupperna, webbapparna och alla relaterade resurser när de inte längre behövs. För att göra det väljer du resursgrupperna (*myResourceGroupTM1* och *myResourceGroupTM2*) och klickar på **Ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en Traffic Manager-profil som gjorde att du kunde dirigera användartrafik för en webbapp med hög tillgänglighet. Om du vill veta mer om att dirigera trafik kan du gå vidare till självstudierna för Traffic Manager.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)






