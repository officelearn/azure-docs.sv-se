---
title: Snabbstart – Skapa en Traffic Manager-profil för hög tillgänglighet för program med hjälp av Azure-portalen
description: I den här snabbstartsartikeln beskrivs hur du skapar en Traffic Manager-profil för att bygga en webbapp med hög tillgänglighet.
services: traffic-manager
author: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: kumud
ms.openlocfilehash: 2cd8830f4b2b7c972ba8972e686be984bb96fd04
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760672"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Snabbstart: Skapa en Traffic Manager-profil med hjälp av Azure portal

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten läser du om två instanser av en webbapp. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

1. Uppe till vänster på skärmen väljer du **Skapa en resurs** > **Webb** > **Webbapp**.
2. I **Webbapp** anger eller väljer du de här inställningarna:

    | Inställning | Värde |
    | ------- | ----- |
    | Appnamn | Ange ett unikt namn för din webbapp.  |
    | Prenumeration | Välj den prenumeration som du vill att webbappen ska gälla för. |
    | Resursgrupp | Välj **Skapa ny** och ange *myResourceGroupTM1*. |
    | Operativsystem | Välj **Windows** som operativsystem. |
    | Publicera | Välj **Kod** som det format du vill publicera till. |

3. Välj **App Service-plan/plats**.
4. I **App Service-plan** väljer du **Skapa ny**.
5. I **Ny App Service-plan** anger eller väljer du de här inställningarna:

    | Inställning | Värde |
    | ------- | ----- |
    | App Service-plan | Ange *myAppServicePlanEastUS*. |
    | Plats | Östra USA |
    | Prisnivå | S1 Standard |

6. Välj **OK**.

7. I **Webbapp** väljer du **Skapa**. När webbappen har distribuerats skapar den en standardwebbplats.

8. För att skapa en andra webbplats i en annan Azure-region upprepar du steg 1–7 med de här inställningarna:

    | Inställning | Värde |
    | --------| ----- |
    | Namn | Ange ett unikt namn för din webbapp. |
    | Prenumeration | Välj den prenumeration som du vill att webbappen ska gälla för. |
    | Resursgrupp | Välj **Skapa ny** och skriv sedan *myResourceGroupTM2*. |
    | Operativsystem | Välj **Windows** som operativsystem. |
    | Publicera | Välj **Kod** som det format du vill publicera till. |
    | App Service-plan/plats | Ange *myAppServicePlanWestEurope*. |
    | Plats | Västra Europa |
    | Prisnivå | S1 Standard |

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som dirigerar användartrafik baserat på slutpunktsprioritet.

1. Uppe till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil**.
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
    | Målresurs | Välj **Välj en apptjänst** > **USA, östra**. |
    | Prioritet | Välj **1**. All trafik skickas till den här slutpunkten när den är felfri. |

    ![Skärmbild av det ställe där du lägger till en slutpunkt i Traffic Manager-profilen.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Välj **OK**.
6. För att skapa en redundansslutpunkt för din andra Azure-region upprepar du steg 3 och 4 med de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Namn | Ange *myFailoverEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en apptjänst** > **Europa, västra**. |
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
