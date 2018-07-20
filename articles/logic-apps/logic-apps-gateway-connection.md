---
title: Åtkomst till datakällor lokalt för Azure Logic Apps | Microsoft Docs
description: Skapa och konfigurera en lokal datagateway så att du kan komma åt datakällor lokalt från logikappar
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: yshoukry, LADocs
ms.suite: integration
ms.openlocfilehash: 65c7e03b349314ad61fa5f1ea8322f4d1352b8e6
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145697"
---
# <a name="connect-to-data-sources-on-premises-from-azure-logic-apps-with-on-premises-data-gateway"></a>Ansluta till datakällor lokalt från Azure Logic Apps med lokala datagateway

Om du vill få åtkomst till datakällor lokalt från logikappar, du kan skapa en resurs till en Programgateway i Azure så att dina logic apps kan använda den [lokala anslutningsappar](../logic-apps/logic-apps-gateway-install.md#supported-connections). Den här artikeln visar hur du skapar din Azure gatewayresursen *när* du [ladda ned och installera gatewayen på den lokala datorn](../logic-apps/logic-apps-gateway-install.md). 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power BI lokala datagatewayen](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow lokal datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps lokal datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Förutsättningar

* Du har redan [hämtas och installeras den datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md).

* Din gateway-installation inte redan är kopplad till en resurs för gatewayen i Azure. Du kan länka din gateway-installation endast till en resurs för gatewayen som sker när du skapar gatewayresursen och välj gatewayinstallationen. Den här länka kan gatewayinstallationen inte andra resurser.

* När du loggar in på Azure Portal och skapa gatewayresursen, måste du använda samma inloggning konto som har använts tidigare till [installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md#requirements).
Du måste också använda samma [Azure-prenumeration](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) som användes för att installera gatewayen. Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Du skapar och underhåller den gatewayresursen i Azure-portalen din [Windows-tjänstkontot](../logic-apps/logic-apps-gateway-install.md#windows-service-account) måste ha minst **deltagare** behörigheter. Den lokala datagatewayen körs som en Windows-tjänst och har ställts in att använda `NT SERVICE\PBIEgwService` för Windows service inloggningsuppgifter. 

  > [!NOTE]
  > Windows-tjänstkontot som skiljer sig från kontot som används för att ansluta till lokala data datakällor och från Azure arbets- eller skolkonto som används för att logga in på molntjänster.

## <a name="download-and-install-gateway"></a>Hämta och installera gatewayen

Innan du fortsätter med stegen i den här artikeln måste du ha den gateway som redan är installerad på en lokal dator.
och om du inte redan gjort följer du stegen för att [ladda ned och installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Skapa Azure-resurs för gatewayen

Du kan sedan skapa en Azure-resurs för din gateway när du har installerat gatewayen på en lokal dator. Det här steget associerar även din gateway-resurs med din Azure-prenumeration.

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>. Kontrollera att du använder samma Azure arbets- eller skolans e-postadress som används för att installera gatewayen.

2. Välj på Azure-huvudmenyn **skapa en resurs** > 
**integrering** > **lokal datagateway**.

   ![Hitta ”lokal datagateway”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. På den **skapa anslutningsgatewayen** anger du den här informationen för din gatewayresursen:

   | Egenskap  | Beskrivning | 
   |----------|-------------|
   | **Namn** | Namn för din gateway-resurs | 
   | **Prenumeration** | Namn på din Azure-prenumeration, som ska vara samma prenumeration som din logikapp. Standard-prenumerationen baseras på Azure-konto du använde för att logga in. | 
   | **Resursgrupp** | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) för att organisera relaterade resurser | 
   | **Plats** | Azure begränsar den här platsen till samma region som valts för gateway-Molntjänsten under [gatewayinstallationen](../logic-apps/logic-apps-gateway-install.md). <p>**Obs**: se till att den här gatewayen resursplats matchar tjänstlokalisering för gateway-molnet. I annat fall visas inte gateway-installation i listan över installerade gatewayar och du kan välja i nästa steg. Du kan använda olika områden för din gateway-resurs och för din logikapp. | 
   | **Installationsnamn** | Om din gateway-installation inte redan är markerat, väljer du den gateway som du tidigare har installerat. | 
   | | | 

   Här är ett exempel:

   ![Ange information om hur du skapar din lokala datagateway](./media/logic-apps-gateway-connection/createblade.png)

4. Om du vill lägga till gatewayresursen på Azure-instrumentpanelen, Välj **fäst på instrumentpanelen**. När du är klar väljer du **Skapa**.

   För att hitta eller visa din gateway när som helst, från Azure-huvudmenyn, Välj **alla tjänster**. 
   Ange ”lokala datagatewayer” i sökrutan och välj sedan **lokala Datagatewayer**.

   ![Hitta ”lokala Datagatewayer”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du skapar din resurs för gatewayen och koppla din Azure-prenumeration med den här resursen kan skapa du nu en anslutning mellan din logikapp och din lokala datakälla med hjälp av gatewayen.

1. Skapa eller öppna logikappen i Logic App Designer i Azure-portalen.

2. Lägg till en anslutningsapp som stöder den lokala anslutningar, till exempel **SQL Server**.

3. Nu ställa in anslutningen:

   1. Välj **Anslut via lokal datagateway**. 

   2. För **gatewayer**, Välj den gateway-resurs som du skapade tidigare. 

      Även om din plats för gateway-anslutningen måste finnas i samma region som din logikapp, kan du välja en gateway i en annan region.

   3. Ange ett unikt namn och annan nödvändig information. 

      Det unika anslutningsnamnet hjälper dig att identifiera anslutningen senare, särskilt när du skapar flera anslutningar. Om så är tillämpligt, även innehålla domännamn för ditt användarnamn.
   
      Här är ett exempel:

      ![Skapa anslutning mellan logic app- och gateway](./media/logic-apps-gateway-connection/blankconnection.png)

   4. När du är klar väljer du **Skapa**. 

Din gateway-anslutning är nu redo för din logikapp att använda.

## <a name="edit-connection"></a>Redigera anslutning

När du skapar en gateway-anslutning för din logikapp kan vilja du uppdatera inställningarna för den specifika anslutningen.

1. Hitta din gateway-anslutning:

   * Du hittar alla API-anslutningar för just din logikapp på logikappens meny under **utvecklingsverktyg**väljer **API-anslutningar**. 
   
     ![Gå till din logikapp, Välj ”API-anslutningar”](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Hitta alla API-anslutningar som är associerade med din Azure-prenumeration: 

     * Från Azure-huvudmenyn går du till **alla tjänster** > **Web** > **API-anslutningar**. 
     * Från Azure-huvudmenyn går du till **alla resurser**.

2. Välj den gatewayanslutning och sedan välja **redigera API-anslutningen**.

   > [!TIP]
   > Om dina uppdateringar inte gälla försöka [stoppas och startas gatewayens Windows-tjänst](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gateway-resurs

Du kan ta bort gatewayresursen för att skapa en annan gateway-resurs, koppla din gateway till en annan resurs eller ta bort gatewayresursen, utan att påverka gateway-installation. 

1. Från Azure-huvudmenyn går du till **alla resurser**. 

2. Hitta och välj din gateway-resurs.

3. Om du inte redan har markerats på din gateway resurs-menyn, Välj **lokala Data Gateway**. 

4. Resurs-verktygsfältet **ta bort**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Skydda dina Logic Apps](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för logic apps](./logic-apps-examples-and-scenarios.md)
