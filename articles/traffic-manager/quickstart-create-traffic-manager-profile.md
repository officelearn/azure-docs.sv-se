---
title: 'Snabb start: skapa en profil för HA med program – Azure Portal – Azure Traffic Manager'
description: I den här snabbstartsartikeln beskrivs hur du skapar en Traffic Manager-profil för att bygga en webbapp med hög tillgänglighet.
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: duau
ms.openlocfilehash: 7a347d5cd72fcf955dae0aa8319632fdb43d3bf7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89400270"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Snabb start: skapa en Traffic Manager profil med hjälp av Azure Portal

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten läser du om två instanser av en webbapp. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **webb**  >  **webbapp**.

1. I **skapa en webbapp**skriver eller väljer du följande värden på fliken **grundläggande** :

   - **Prenumeration**  >  **Resurs grupp**: Välj **Skapa ny** och skriv sedan **myResourceGroupTM1**.
   - **Instans information**  >  **Namn**: Skriv *myWebAppEastUS*.
   - **Instans information**  >  **Publicera**: Välj **kod**.
   - **Instans information**  >  **Körnings stack**: Välj **ASP.net v 4.7**
   - **Instans information**  >  **Operativ system**: Välj **Windows**.
   - **Instans information**  >  **Region**: Välj **USA, östra**.
   - **App Service plan**  >  **Windows-plan (USA, östra)**: Välj **Skapa ny** och skriv sedan **myAppServicePlanEastUS**
   - **App Service plan**  >  **SKU och storlek**: Välj **standard S1**.
   
3. Välj fliken **övervakning** eller Välj **Nästa: övervakning**.  Under **övervakning**, ange **Application Insights**  >  **Aktivera Application Insights** till **Nej**.

4. Välj **Granska och skapa**

5. Granska inställningarna och klicka sedan på **skapa**.  När webbappen har distribuerats skapar den en standardwebbplats.

6. Följ stegen för att skapa en andra webbapp med namnet *myWebAppWestEurope*, med **resurs grupp** namnet *MyResourceGroupTM2*, en **region** i *Västeuropa*, ett **App Service plan** namn för **myAppServicePlanWestEurope**och alla andra inställningar på samma sätt som *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som dirigerar användartrafik baserat på slutpunktsprioritet.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profilen**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du de här inställningarna:

    | Inställning | Värde |
    | --------| ----- |
    | Namn | Ange ett unikt namn för din Traffic Manager-profil.|
    | Routningsmetod | Välj **Prioritet**.|
    | Prenumeration | Välj den prenumeration som du vill att Traffic Manager-profilen ska gälla för. |
    | Resursgrupp | Välj *myResourceGroupTM1*.|
    | Plats |Den här inställningen refererar till platsen för resursgruppen. Den har ingen effekt på den Traffic Manager-profil som ska distribueras globalt.|

3. Välj **Skapa**.

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till webbplatsen i *USA, östra* som primär slutpunkt för att dirigera all användartrafik. Lägg till webbplatsen i *Europa, västra* som en redundansslutpunkt. När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

1. I portalens sökfält anger du det Traffic Manager-profilnamn som du skapade i föregående avsnitt.
2. Välj profilen bland sökresultatet.
3. I **Traffic Manager-profil** går du till avsnittet **Inställningar** och väljer **Slutpunkter** följt av **Lägg till**.
4. Ange eller välj de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Namn | Ange *myPrimaryEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en app service**  >  **östra USA**. |
    | Prioritet | Välj **1**. All trafik skickas till den här slutpunkten när den är felfri. |

    ![Skärmbild av det ställe där du lägger till en slutpunkt i Traffic Manager-profilen.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Välj **OK**.
6. För att skapa en redundansslutpunkt för din andra Azure-region upprepar du steg 3 och 4 med de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Namn | Ange *myFailoverEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en app service**  >  **Västeuropa**. |
    | Prioritet | Välj **2**. All trafik skickas till den här redundansslutpunkten om den primära slutpunkten är skadad. |

7. Välj **OK**.

När du är klar med att lägga till de två slutpunkterna visas de i **Traffic Manager-profilen**. Observera att deras övervakningsstatus är **Online** nu.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

### <a name="check-the-dns-name"></a>Kontrollera DNS-namnet

1. I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt.
2. Välj Traffic Manager-profilen. **Översikten** visas.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil.
  
   ![Skärmbild av platsen för ditt Traffic Manager DNS-namn](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

1. I en webbläsare anger du DNS-namnet för din Traffic Manager-profil för att visa webbappens standardwebbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.

    ![Skärmbild av webbplatsen för att bekräfta tillgängligheten för Traffic Manager-profil](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. För att se hur Traffic Manager-redundans fungerar i praktiken inaktiverar du din primära plats:
    1. På Traffic Manager-profilsidan går du till avsnittet **Översikt** och väljer **myPrimaryEndpoint**.
    2. I *myPrimaryEndpoint* väljer du **Inaktiverad** > **Spara**.
    3. Stäng **myPrimaryEndpoint**. Lägg märke till att statusen är **Inaktiverad** nu.
3. Kopiera DNS-namnet för din Traffic Manager-profil från det föregående steget för att visa webbplatsen i en ny webbläsarsession.
4. Kontrollera att webbappen fortfarande är tillgänglig.

Den primära slutpunkten är inte tillgänglig, så du dirigerades till redundansslutpunkten.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgrupperna, webbapparna och alla relaterade resurser när du är klar. Det gör du genom att markera varje enskilt objekt instrumentpanelen och välja **Ta bort** längst upp på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Traffic Manager-profil. Med den kan du dirigera användartrafik för webbappar med hög tillgänglighet. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
