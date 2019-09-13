---
title: Få åtkomst till data källor lokalt från Azure Logic Apps
description: Ansluta till lokala data källor från Logic Apps genom att skapa en lokal datagateway
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: ed2ba70b803940700044e900a1b2bb6607c0f051
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934047"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ansluta till lokala data källor från Azure Logic Apps

Om du vill komma åt data källor lokalt från dina Logi Kap par skapar du en lokal datagateway-resurs i Azure Portal. Dina Logi Kap par kan sedan använda [lokala anslutnings](../connectors/apis-list.md#on-premises-connectors)program. Den här artikeln visar hur du skapar en Azure gateway-resurs *när* du har [laddat ned och installerat gatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md). Mer information om gatewayen finns i [så här fungerar gatewayen](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Om du vill ansluta till virtuella Azure-nätverk kan du i stället skapa en [*integrerings tjänst miljö*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power BI lokal datagateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow lokal datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps lokal datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

För Azure Logic Apps har den lokala datagatewayen stöd för [lokala anslutningar](../connectors/apis-list.md#on-premises-connectors) för dessa data Källor:

* BizTalk Server 2016
* Filsystem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Även om själva gatewayen inte ådrar sig ytterligare kostnader, gäller [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md) för dessa anslutningar och andra åtgärder i Azure Logic Apps.

## <a name="prerequisites"></a>Förutsättningar

* Du har redan [installerat den lokala datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md).

* Du har [samma Azure-konto och Azure-prenumeration](../logic-apps/logic-apps-gateway-install.md#requirements) som du använde när du installerade den lokala datagatewayen.

* Du har inte tidigare länkat din gateway-installation till en annan gateway-resurs i Azure.

  När du skapar en gateway-resurs väljer du en gateway-installation som ska associeras med din gateway-resurs. Det finns redan en länkad Gateway-installation som du kan välja när du skapar gateway-resurser.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Skapa Azure gateway-resurs

När du har installerat gatewayen på en lokal dator skapar du Azure-resursen för din gateway. 

1. Logga in på [Azure Portal](https://portal.azure.com) med samma Azure-konto som användes för att installera gatewayen.

1. I sökrutan Azure Portal anger du "lokal datagateway" och väljer **lokala Datagatewayer**.

   ![Hitta en lokal datagateway](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. Under **lokala Datagatewayer**väljer du **Lägg till**.

   ![Lägg till datagateway](./media/logic-apps-gateway-connection/add-gateway.png)

1. Under **skapa Gateway för anslutning**anger du den här informationen för din gateway-resurs. När du är klar väljer du **Skapa**.

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Resursnamn** | Namnet på din gateway-resurs, som endast får innehålla bokstäver, siffror, bindestreck`-`(), under streck (`_`), parenteser (`(`, `)`) och punkter (`.`). |
   | **Prenumeration** | Din Azure-prenumeration, som måste vara samma som gateway-installationen och Logic app. Standard prenumerationen baseras på det Azure-konto som du använde för att logga in. |
   | **Resursgrupp** | Den [Azure-resurs grupp](../azure-resource-manager/resource-group-overview.md) som du vill använda |
   | **Location** | Samma region som den plats som valdes för gateway-moln tjänsten under [Gateway-installationen](../logic-apps/logic-apps-gateway-install.md). Annars visas inte Gateway-installationen i listan med **installations namn** som du kan välja. Din Logic app-plats kan skilja sig från din plats för gateway-resursen. |
   | **Installations namn** | Om Gateway-installationen inte redan är vald väljer du den gateway som du tidigare har installerat. Tidigare länkade Gateway-installationer visas inte i den här listan som du kan välja. |
   |||

   Här är ett exempel:

   ![Ange information för att skapa din lokala datagateway](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du har skapat din gateway-resurs och associerat din Azure-prenumeration med den här resursen kan du nu skapa en anslutning mellan din Logic app och din lokala data källa med hjälp av gatewayen.

1. I Azure Portal skapar eller öppnar du din Logic app i Logic App Designer.

1. Lägg till en anslutning som stöder lokala anslutningar, till exempel **SQL Server**.

1. Välj **Anslut via lokal datagateway**. 

1. För **gatewayer**väljer du den gateway-resurs som du har skapat.

   > [!NOTE]
   > Gateway-listan innehåller gateway-resurser i andra regioner eftersom din Logi Kap par plats kan skilja sig från Gateway-resursens plats.

1. Ange ett unikt anslutnings namn och annan information som krävs, beroende på vilken anslutning du vill skapa.

   Ett unikt anslutnings namn hjälper dig att enkelt hitta anslutningen senare, särskilt om du skapar flera anslutningar. Om tillämpligt, inkludera även den kvalificerade domänen för ditt användar namn.
   
   Här är ett exempel:

   ![Skapa anslutning mellan Logic app och datagateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. När du är klar väljer du **Skapa**. 

Din gateway-anslutning är nu klar för din Logic app att använda.

## <a name="edit-connection"></a>Redigera anslutning

Om du vill uppdatera inställningarna för en gateway-anslutning kan du redigera anslutningen.

1. Om du vill hitta alla API-anslutningar för just din Logic app går du till din Logic Apps-meny, under **utvecklingsverktyg**, och väljer **API-anslutningar**.
   
   ![På din Logic app-meny väljer du "API-anslutningar"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Välj den gateway-anslutning som du vill använda och välj sedan **Redigera API-anslutning**.

   > [!TIP]
   > Om dina uppdateringar inte börjar gälla kan du prova [med att stoppa och starta om gatewayens Windows-](../logic-apps/logic-apps-gateway-install.md#restart-gateway) tjänstkonto för gateway-installationen.

Så här hittar du alla API-anslutningar som är associerade med din Azure-prenumeration: 

* Från huvud menyn i Azure går du till **alla tjänster** > **Web API-**  > **anslutningar**.
* Du kan också gå till **alla resurser**från huvud menyn i Azure. Ange **typ** filter till **API-anslutning**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gateway-resurs

Om du vill skapa en annan gateway-resurs länkar du Gateway-installationen till en annan gateway-resurs eller tar bort Gateway-resursen, kan du ta bort Gateway-resursen utan att det påverkar Gateway-installationen. 

1. Välj **alla resurser**från huvud menyn i Azure. Hitta och välj din gateway-resurs.

1. Om du inte redan har valt väljer du **lokal datagateway**på menyn gateway-resurs. I verktygsfältet gateway-resurs väljer du **ta bort**.

   Exempel:

   ![Ta bort Gateway](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F**: Varför visas inte min Gateway-installation när jag skapar en gateway-resurs i Azure? <br/>
**S**: Det här problemet kan inträffa av följande orsaker:

* Gateway-installationen har redan registrerats och begärts av en annan gateway-resurs i Azure. Gateway-installationer visas inte i listan instanser när gateway-resurserna har skapats för dem. Om du vill kontrol lera dina gateway-registreringar i Azure Portal granskar du alla Azure-resurser med den **lokala Datagatewayens** typ för *alla* Azure-prenumerationer.

* Azure AD-identiteten för den person som installerade gatewayen skiljer sig från den person som loggade in på Azure Portal. Kontrol lera att du har loggat in med samma identitet som installerade gatewayen.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda dina Logic Apps](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för Logic Apps](./logic-apps-examples-and-scenarios.md)
