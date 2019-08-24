---
title: Få åtkomst till data källor lokalt från Azure Logic Apps | Microsoft Docs
description: Ansluta till lokala data källor från Logic Apps genom att skapa en lokal datagateway
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 029dc8daaf456c155d46eefa699772882bdabee5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982876"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ansluta till lokala data källor från Azure Logic Apps

Om du vill komma åt data källor lokalt från dina Logi Kap par skapar du en lokal datagateway-resurs i Azure Portal. Dina Logi Kap par kan sedan använda [lokala anslutnings](../logic-apps/logic-apps-gateway-install.md#supported-connections)program. Den här artikeln visar hur du skapar en Azure gateway-resurs *när* du har [laddat ned och installerat gatewayen på den lokala datorn](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Om du vill ansluta till virtuella Azure-nätverk kan du i stället skapa en [*integrerings tjänst miljö*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power BI lokal datagateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow lokal datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps lokal datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Förutsättningar

* Du har redan [laddat ned och installerat datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md).

* Gateway-installationen är inte redan kopplad till en gateway-resurs i Azure. Du kan bara länka din gateway-installation till en gateway-resurs, som inträffar när du skapar Gateway-resursen och väljer Gateway-installationen. Den här länkningen gör att Gateway-installationen inte är tillgänglig för andra resurser.

* När du loggar in på Azure Portal och skapar en gateway-resurs, se till att du använder samma inloggnings konto som tidigare användes för att [installera den lokala](../logic-apps/logic-apps-gateway-install.md#requirements) datagatewayen tillsammans med samma [Azure-prenumeration](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) som användes för att installera nyckeln. Om du inte har någon Azure-prenumeration ännu kan du <a href="https://azure.microsoft.com/free/" target="_blank">Registrera dig för ett kostnads fritt Azure-konto</a>.

* För att skapa och underhålla Gateway-resursen i Azure Portal måste ditt [Windows-tjänstkonto](../logic-apps/logic-apps-gateway-install.md#windows-service-account) ha minst **deltagar** behörighet. Den lokala datagatewayen körs som en Windows-tjänst och har kon figurer ATS för `NT SERVICE\PBIEgwService` att användas för inloggnings uppgifter för Windows-tjänst. 

  > [!NOTE]
  > Windows-tjänstkontot skiljer sig från det konto som används för att ansluta till lokala data källor och från Azures arbets-eller skol konto som används för att logga in på moln tjänster.

## <a name="download-and-install-gateway"></a>Ladda ned och installera Gateway

Innan du kan fortsätta med stegen i den här artikeln kontrollerar du att din gateway redan är installerad på en lokal dator.
Om du inte redan gjort det följer du stegen för att [Ladda ned och installera den lokala](../logic-apps/logic-apps-gateway-install.md)datagatewayen. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Skapa Azure-resurs för gateway

När du har installerat gatewayen på en lokal dator kan du skapa en Azure-resurs för din gateway. Det här steget associerar även din gateway-resurs med din Azure-prenumeration.

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Se till att du använder samma e-postadress för Azures arbete eller skola som används för att installera gatewayen.

2. På huvud menyn i Azure väljer du **skapa en** > 
**lokal datagateway för**resurs**integrering** > .

   ![Hitta en lokal datagateway](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. På sidan **Skapa anslutning Gateway** anger du den här informationen för din gateway-resurs:

   | Egenskap | Beskrivning | 
   |----------|-------------|
   | **Resursnamn** | Namnet på din gateway-resurs, som endast får innehålla bokstäver, siffror, bindestreck`-`(), under streck (`_`), parenteser (`(`, `)`) och punkter (`.`). | 
   | **Prenumeration** | Din Azure-prenumerations namn, som ska vara samma prenumeration som din Logic app. Standard prenumerationen baseras på det Azure-konto som du använde för att logga in. | 
   | **Resursgrupp** | Namnet på Azure- [resurs gruppen](../azure-resource-manager/resource-group-overview.md) för att organisera relaterade resurser | 
   | **Location** | Azure begränsar den här platsen till samma region som valdes för gateway-moln tjänsten under Gateway- [installationen](../logic-apps/logic-apps-gateway-install.md). <p>**Obs!** Kontrol lera att denna gateway-resurs plats matchar gatewayens moln tjänst plats. Annars kanske Gateway-installationen inte visas i listan installerade gatewayer som du kan välja i nästa steg. Du kan använda olika regioner för din gateway-resurs och för din Logic app. | 
   | **Installations namn** | Om Gateway-installationen inte redan är vald väljer du den gateway som du tidigare har installerat. | 
   | | | 

   Här är ett exempel:

   ![Ange information för att skapa din lokala datagateway](./media/logic-apps-gateway-connection/createblade.png)

4. Om du vill lägga till gateway-resursen i Azure-instrumentpanelen väljer du **Fäst på instrument panelen**. När du är klar väljer du **Skapa**.

   Om du vill hitta eller Visa din gateway när som helst väljer du **alla tjänster**från huvud menyn i Azure. 
   I sökrutan anger du lokala datagatewayer och väljer sedan **lokala**datagatewayer.

   ![Hitta lokala datagatewayer](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du har skapat din gateway-resurs och associerat din Azure-prenumeration med den här resursen kan du nu skapa en anslutning mellan din Logic app och din lokala data källa med hjälp av gatewayen.

1. I Azure Portal skapar eller öppnar du din Logic app i Logic App Designer.

2. Lägg till en anslutning som stöder lokala anslutningar, till exempel **SQL Server**.

3. Nu har du konfigurerat anslutningen:

   1. Välj **Anslut via lokal datagateway**. 

   2. För **gatewayer**väljer du den gateway-resurs som du skapade tidigare. 

      Även om din gateway-anslutningssträng måste finnas i samma region som din Logic app, kan du välja en gateway i en annan region.

   3. Ange ett unikt namn på anslutningen och övrig information som krävs. 

      Det unika anslutnings namnet hjälper dig att enkelt identifiera anslutningen senare, särskilt när du skapar flera anslutningar. Om tillämpligt, inkludera även den kvalificerade domänen för ditt användar namn.
   
      Här är ett exempel:

      ![Skapa anslutning mellan Logic app och datagateway](./media/logic-apps-gateway-connection/blankconnection.png)

   4. När du är klar väljer du **Skapa**. 

Din gateway-anslutning är nu klar för din Logic app att använda.

## <a name="edit-connection"></a>Redigera anslutning

När du har skapat en gateway-anslutning för din Logic app kanske du vill uppdatera inställningarna för den aktuella anslutningen senare.

1. Hitta din gateway-anslutning:

   * Om du vill hitta alla API-anslutningar för just din Logic app går du till din Logic Apps-meny, under **utvecklingsverktyg**, och väljer **API-anslutningar**. 
   
     ![Gå till din Logic-app och välj "API-anslutningar"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Så här hittar du alla API-anslutningar som är associerade med din Azure-prenumeration: 

     * Från huvud menyn i Azure går du till **alla tjänster** > **Web API-**  > **anslutningar**. 
     * Du kan också gå till **alla resurser**från huvud menyn i Azure.

2. Välj den gateway-anslutning som du vill använda och välj sedan **Redigera API-anslutning**.

   > [!TIP]
   > Om dina uppdateringar inte börjar gälla kan [du prova med att stoppa och starta om gatewayens Windows-tjänst](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gateway-resurs

Om du vill skapa en annan gateway-resurs, associera din gateway med en annan resurs eller ta bort Gateway-resursen, kan du ta bort Gateway-resursen utan att påverka Gateway-installationen. 

1. Gå till **alla resurser**från huvud menyn i Azure. 

2. Hitta och välj din gateway-resurs.

3. Om du inte redan har valt väljer du **lokal datagateway**på menyn gateway-resurs. 

4. I verktygsfältet resurs väljer du **ta bort**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Skydda dina Logic Apps](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för Logic Apps](./logic-apps-examples-and-scenarios.md)
