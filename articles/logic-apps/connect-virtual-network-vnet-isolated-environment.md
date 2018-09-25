---
title: Ansluta till virtuella Azure-nätverk från Azure Logic Apps
description: För att komma åt Azure-nätverk (Vnet) från Azure Logic Apps kan du skapa privata, dedikerade och isolerade integration service-miljöer som håller logikappar och andra resurser som är separata från offentligt eller ”global” Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b1a75c140376c1e2e2fdfdcd1581978301ab32f1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996476"
---
# <a name="create-isolated-environments-to-access-azure-virtual-networks-vnets-from-azure-logic-apps"></a>Skapa isolerade miljöer för att komma åt Azure-nätverk (Vnet) från Azure Logic Apps

> [!NOTE]
> Den här funktionen är i *privat förhandsgranskning*. Att begära åtkomst, [skapa din begäran om att ansluta till här](https://aka.ms/iseprivatepreview).

För integrationsscenarier där dina logic apps och integrationskonton behöver åtkomst till en [Azure-nätverk (VNET)](../virtual-network/virtual-networks-overview.md), kan du skapa en [ *integreringstjänstmiljön* (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) som länkar till ditt virtuella nätverk och distribuerar Logic Apps-tjänsten i ditt virtuella nätverk. När du skapar logikappar och integrationskonton, väljer du den här ISE som deras plats. Dina logikappar och integrationskonton kan sedan direkt åtkomst till resurser, till exempel virtuella datorer (VM), servrar, system och tjänster i ditt VNET. 

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Din ISE är en privat och isolerad miljö som använder dedikerade lagringsutrymmen och andra resurser som existerar separat från offentligt eller *globala* Logic Apps-tjänsten. Den här separationen hjälper också att minska risken för andra Azure-klienter kan ha på din apps prestanda. 

Den här artikeln visar hur du utför dessa uppgifter:

* Konfigurera behörigheter för ditt Azure VNET så att den privata Logic Apps-instansen har åtkomst till ditt virtuella nätverk.

* Skapa din integration service-environment (ISE). 

* Skapa en logikapp som kan köras i din ISE. 

* Skapa ett integrationskonto för logikappar i din ISE.

Läs mer om integreringstjänstmiljöer [åtkomst till resurser i Azure Virtual Network (VNET) från isolerade Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Om du inte har ett virtuellt Azure nätverk kan du lära dig hur du [skapa en Azure-nätverk](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Även om du inte behöver ett Azure VNET för att skapa din miljö kan du kan *endast* välja ett virtuellt nätverk som peer för din miljö när du skapar den miljön. 

* Ge dina logikappar direkt åtkomst till ditt Azure VNET, [konfigurera rollbaserad åtkomstkontroll (RBAC) behörigheter](#vnet-access) så att Logic Apps-tjänsten har behörigheter för åtkomst till ditt virtuella nätverk. 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-up-vnet-permissions"></a>Konfigurera VNET-behörigheter

När du skapar din integration service-miljö kan du välja ett Azure-nätverk (VNET) som en *peer* för din miljö. Men du kan bara utföra det här steget eller *peering*, när du skapar din miljö. Den här relationen kan ansluta direkt till resurser i det virtuella nätverket Logic Apps-tjänsten och ger din miljöåtkomst till dessa resurser. 

Innan du kan välja det virtuella nätverket måste ställa du in behörigheter för rollbaserad åtkomstkontroll (RBAC) i ditt virtuella nätverk. För att slutföra den här uppgiften, måste du tilldela specifika roller till Azure Logic Apps-tjänsten.

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt virtuella nätverk. I det virtuella Nätverkets menyn väljer **åtkomstkontroll (IAM)**. 

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
   | **Peer-nätverket** | Nej | <*Azure-VNET-name*> | Azure-nätverk (VNET) ska associeras med din miljö som en *peer* så logic apps i denna miljö kan komma åt ditt virtuella nätverk. Innan du kan skapa den här relationen, se till att du redan [konfigurera rollbaserad åtkomstkontroll i ditt virtuella nätverk för Azure Logic Apps](#vnet-access). <p>**Viktiga**: även om ett virtuellt nätverk krävs inte, du kan välja ett virtuellt nätverk *endast* när du skapar din miljö. | 
   | **Peering namn** | Ja med valda virtuella nätverk | <*peering-name*> | Namnet som ska ge peer-relation | 
   | **VNET-IP-intervall** | Ja med valda virtuella nätverk | <*IP-adressintervall*> | IP-adressintervall du använder för att skapa resurser i din miljö. Det här intervallet måste använda den [Classless Inter-Domain Routing CIDR-formatet](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), till exempel 10.0.0.1/16, och kräver en klass B-adressutrymme. Intervallet får inte finnas inom adressutrymmet för det virtuella nätverket har valt i den **Peer-VNET** egenskap, eller i alla andra privata IP-adresser där peer-nätverket är anslutet, antingen via peering eller gateways. <p><p>**Viktiga**: du *kan inte ändra* detta adressintervall när du har skapat din miljö. |
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

* Om du tidigare har konfigurerat din ISE med ett virtuellt Azure-nätverk som peer, logikappar i din ISE direkt åtkomst till resurser i det virtuella nätverket. För lokala system i ett virtuellt nätverk som är länkad till en ISE logikappar direkt åtkomst till dessa system med någon av dessa objekt: 

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
