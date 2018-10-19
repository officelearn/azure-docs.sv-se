---
title: Ansluta till virtuella Azure-nätverk från Azure Logic Apps via en integration service-miljö (ISE)
description: Skapa en integration service-miljö (ISE) så att logic apps och integrationskonton kan komma åt Azure-nätverk, utan att förlora privata och isolerade från offentligt eller ”global” Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: b4e4e801c3c54b635f2f13b319257018ea544c03
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404128"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Ansluta till virtuella Azure-nätverk från Azure Logic Apps via en integration service-miljö (ISE)

> [!NOTE]
> Den här funktionen är i *privat förhandsgranskning*. Att begära åtkomst, [skapa din begäran om att ansluta till här](https://aka.ms/iseprivatepreview).

För integrationsscenarier där dina logic apps och integrationskonton behöver åtkomst till en [Azure-nätverk](../virtual-network/virtual-networks-overview.md), skapa en [ *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vilket är en privat och isolerad miljö som använder dedikerade lagring och andra resurser åtskilda från offentligt eller *globala* Logic Apps-tjänsten. Den här separationen minskar också påverkas som andra Azure-klienter kan ha på din apps prestanda. Du kan länka den här ISE till din Azure-nätverk som distribuerar sedan Logic Apps-tjänsten till ditt virtuella nätverk. När du skapar ett logic app eller varje konto, Välj den här ISE som deras plats. Ditt logic app eller varje konto kan sedan direkt åtkomst till resurser, till exempel virtuella datorer (VM), servrar, system och tjänster i ditt virtuella nätverk. 

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Den här artikeln visar hur du utför dessa uppgifter:

* Konfigurera behörigheter på Azure-nätverk så att den privata Logic Apps-instansen kan komma åt det virtuella nätverket.

* Skapa din integration service-environment (ISE). 

* Skapa en logikapp som kan köras i din ISE. 

* Skapa ett integrationskonto för logikappar i din ISE.

Läs mer om integreringstjänstmiljöer [åtkomst till Azure Virtual Network-resurser från isolerad Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Om du inte har ett Azure-nätverk kan du lära dig hur du [skapa en Azure-nätverk](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Även om du inte behöver ett Azure-nätverk för att skapa din miljö kan du kan *endast* välja ett virtuellt nätverk som peer för din miljö när du skapar den miljön. 

* Ge dina logikappar direkt åtkomst till Azure-nätverk, [konfigurera rollbaserad åtkomstkontroll (RBAC) behörigheter](#vnet-access) så att Logic Apps-tjänsten har behörigheter för åtkomst till ditt virtuella nätverk. 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Ange behörigheter för virtuella nätverk

När du skapar din integration service-miljö kan du välja ett Azure-nätverk som en *peer* för din miljö. Men du kan bara utföra det här steget eller *peering*, när du skapar din miljö. Den här relationen kan ansluta direkt till resurser i det virtuella nätverket Logic Apps-tjänsten och ger din miljöåtkomst till dessa resurser. 

Innan du kan välja det virtuella nätverket måste ställa du in behörigheter för rollbaserad åtkomstkontroll (RBAC) i det virtuella nätverket. För att slutföra den här uppgiften, måste du tilldela specifika roller till Azure Logic Apps-tjänsten.

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt virtuella nätverk. 

1. I det virtuella nätverket menyn väljer **åtkomstkontroll (IAM)**. 

1. Under **Access Control**väljer **rolltilldelning** om inte redan är valt. På den **rolltilldelning** verktygsfältet och välj **Lägg till**. 

   ![Lägg till rolltilldelningar](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. På den **Lägg till behörigheter** fönstret konfigurera varje roll i den här tabellen för Azure Logic Apps-tjänsten. Se till att välja **spara** när du har slutfört varje roll:

   | Roll | Tilldela behörighet till | Välj | 
   |------|------------------|--------|
   | **Nätverksdeltagare** | **Azure AD-användare, grupp eller program** | Ange **Azure Logic Apps**. När listan visas, väljer du samma värde. <p>**Tips**: Om du inte hittar den här tjänsten kan du ange Logic Apps-tjänsten app-ID: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Klassiska deltagare** | **Azure AD-användare, grupp eller program** | Ange **Azure Logic Apps**. När listan visas, väljer du samma värde. <p>**Tips**: Om du inte hittar den här tjänsten kan du ange Logic Apps-tjänsten app-ID: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Exempel:

   ![Lägga till behörigheter](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   Mer information om de rollbehörigheterna som krävs för peering finns i den [behörigheter avsnittet i Skapa, ändra eller ta bort en virtuell nätverkspeering](../virtual-network/virtual-network-manage-peering.md#permissions). 

Om det virtuella nätverket är anslutet via Azure ExpressRoute, Azure punkt-till-plats-VPN eller Azure plats-till-plats-VPN, fortsätter du med nästa avsnitt så att du kan lägga till nödvändiga gateway-undernätet. I annat fall fortsätter med [skapa miljön](#create-environment).

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>Lägg till gateway-undernät för virtuella nätverk med ExpressRoute eller VPN-anslutningar

När du har slutfört föregående steg, din integration service-environment (ISE) för att ge åtkomst till ett Azure-nätverk som har anslutit via [Azure ExpressRoute](../expressroute/expressroute-introduction.md), [Azure punkt-till-plats VPN](../vpn-gateway/point-to-site-about.md), eller [Azure plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), måste du också lägga till en [ *gatewayundernätet* ](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) till det virtuella nätverket:

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt virtuella nätverk. I det virtuella nätverket menyn väljer **undernät**, och välj sedan **gatewayundernätet** > **OK**.

   ![Lägg till gateway-undernät](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. Nu skapar du en [ *routningstabellen*](../virtual-network/manage-route-table.md), som du kan associera med gateway-undernätet som du skapade tidigare.

   1. Välj på Azure-huvudmenyn **skapa en resurs** > 
    **nätverk** > **routningstabellen**.

      ![Skapa routningstabell](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. Ange information om routningstabellen, till exempel namn, din Azure-prenumeration du använder, Azure-resursgrupp och plats. Kontrollera att den **BGP-spridning** är inställd på **aktiverad**, och välj sedan **skapa**.

      ![Ange tabellen flödesinformation](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. På vägen Tabell-menyn, Välj **undernät**, och välj sedan **associera**. 

      ![Ansluta routningstabellen för undernätet](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. Välj **virtuellt nätverk**, och välj sedan det virtuella nätverket.
   
   1. Välj **undernät**, och välj sedan tidigare skapade gateway-undernätet.

   1. När du är klar väljer **OK**.

1. Om du har en punkt-till-plats-VPN kan du slutföra stegen för:

   1. I Azure, hitta och välj den virtuella nätverksresursen för gateway.

   1. På den gateway-menyn, Välj **punkt-till-plats-konfiguration**. 
   Välj sedan **hämta VPN-klient** så att du har den senaste VPN-klientkonfigurationen.

      ![Ladda ned den senaste VPN-klienten](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

Du är nu klar med att konfigurera ett gateway-undernät för virtuella nätverk som använder ExpressRoute, punkt-till-plats-VPN eller VPN för plats-till-plats. Följ stegen nedan om du vill fortsätta skapa din integration service-miljö.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

Följ dessa steg för att skapa din integration service-environment (ISE):

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **skapa en resurs**.

   ![Skapa ny resurs](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. I sökrutan anger du ”integreringstjänstmiljön” som filter.
Listan med resultat väljer **Integreringstjänstmiljön (förhandsversion)**, och välj sedan **skapa**.

   ![Välj ”Integreringstjänstmiljön”](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Välj ”Skapa”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Ange följande information för din miljö:

   ![Ange information om miljön](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap  | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*miljö-name*> | Namn för att ge din miljö | 
   | **Prenumeration** | Ja | <*Azure-prenumerationsnamn*> | Azure-prenumeration för din miljö | 
   | **Resursgrupp** | Ja | <*Azure-resource-group-name*> | Azure-resursgrupp där du vill skapa en miljö |
   | **Plats** | Ja | <*Azure-datacenterregion*> | Azure-datacenterregion var du vill lagra information om din miljö |
   | **Peer-nätverket** | Nej | <*Azure-VNET-name*> | Azure-nätverket ska associeras med din miljö som en *peer* så logic apps i denna miljö kan komma åt det virtuella nätverket. Innan du kan skapa den här relationen, se till att du redan [konfigurera rollbaserad åtkomstkontroll i ditt virtuella nätverk för Azure Logic Apps](#vnet-access). <p>**Viktiga**: även om ett virtuellt nätverk krävs inte, du kan välja ett virtuellt nätverk *endast* när du skapar din miljö. | 
   | **Peering namn** | Ja, med en valda virtuella nätverket | <*peering-name*> | Namnet som ska ge peer-relation | 
   | **VNET-IP-intervall** | Ja, med en valda virtuella nätverket | <*IP-adressintervall*> | IP-adressintervall du använder för att skapa resurser i din miljö. Det här intervallet måste använda den [Classless Inter-Domain Routing CIDR-formatet](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), till exempel 10.0.0.1/16, och kräver en klass B-adressutrymme. Intervallet får inte finnas inom adressutrymmet för det virtuella nätverket som valts i den **Peer-VNET** egenskap, eller i alla andra privata IP-adresser där peer-nätverket är anslutet, antingen via peering eller gateways. <p><p>**Viktiga**: du *kan inte ändra* detta adressintervall när du har skapat din miljö. |
   |||||
   
1. När du är klar väljer du **Skapa**. 

   Azure börjar distribuera din miljö, men den här processen kan ta *upp till två timmar* innan du avslutar. 
   Välj meddelandeikonen, vilket öppnar meddelandefönstret för att kontrollera Distributionsstatus i din Azure verktygsfältet.

   ![Kontrollera Distributionsstatus för](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   När distributionen har slutförts visas det här meddelandet i Azure:

   ![Distribueringen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Om du vill visa din miljö, Välj **gå till resurs** om Azure inte automatiskt går till din miljö när distributionen är klar.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Skapa logikapp – ISE

För att skapa logikappar som använder din integration service-environment (ISE), följer du vanliga stegen i [så här skapar du en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) men med dessa skillnader och överväganden: 

* När du skapar din logikapp den **plats** egenskapslistor din ISEs under **integreringstjänstmiljöer** tillsammans med tillgängliga regioner. Välj din ISE i stället för en region, till exempel:

  ![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Du kan använda samma built-ins, till exempel HTTP-utlösare eller åtgärd som körs i samma ISE som överordnade logikappen. Kopplingar med den **ISE** märka också köras i samma ISE som överordnade logikappen. Anslutningar utan den **ISE** etikett som körs i tjänsten för global Logic Apps.

  ![Välj ISE-tjänster](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Om du tidigare har konfigurerat din ISE med ett Azure-nätverk som peer, logikappar i din ISE direkt åtkomst till resurser i det virtuella nätverket. För lokala system i ett virtuellt nätverk som är länkad till en ISE logikappar direkt åtkomst till dessa system med någon av dessa objekt: 

  * ISE-anslutning för systemet, till exempel SQL Server

  * HTTP-åtgärd 

  * Anpassad anslutningsapp

  För den lokala system som inte är i ett virtuellt nätverk eller så har inte ISE kopplingar, kan du fortfarande ansluta när du [konfigurera och använda den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Skapa integrationskonto – ISE

Om du vill använda en integrering med logic apps i en integreringstjänstmiljö (ISE) måste det integrationskontot måste använda den *samma miljö* som logic apps. Logic apps i en ISE kan referera till integrationskonton i samma ISE. 

Om du vill skapa ett integrationskonto som använder en ISE åtgärderna vanligt i [hur du skapar konton för logikappsintegration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) undantag för den **plats** egenskapen, som nu visar en lista över dina ISEs under  **Integreringstjänstmiljöer** tillsammans med tillgängliga regioner. Välj din ISE i stället för en region, till exempel:

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forumet för Azure Logic Apps</a>.
* Om du vill skicka in eller rösta på förslag på funktioner besöker du <a href="http://aka.ms/logicapps-wish" target="_blank">webbplatsen för Logic Apps-användarfeedback</a>.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [virtual network-integration för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
