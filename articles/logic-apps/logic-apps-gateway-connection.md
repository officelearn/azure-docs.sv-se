---
title: Åtkomst till data källor lokalt
description: Anslut till lokala data källor från Azure Logic Apps genom att skapa en lokal Azure-datagateway-resurs
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80657142"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ansluta till lokala datakällor från Azure Logic Apps

Innan du kan komma åt data källor lokalt från dina Logic Apps måste du skapa en Azure-resurs när du har [installerat den lokala *datagatewayen* på en lokal dator](../logic-apps/logic-apps-gateway-install.md). Dina Logi Kap par använder sedan den här Azure Gateway-resursen i utlösare och åtgärder som tillhandahålls av de [lokala anslutningar](../connectors/apis-list.md#on-premises-connectors) som är tillgängliga för Azure Logic Apps.

Den här artikeln visar hur du skapar en Azure gateway-resurs för en tidigare [installerad gateway på den lokala datorn](../logic-apps/logic-apps-gateway-install.md). Mer information om gatewayen finns i [så här fungerar gatewayen](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Om du vill ansluta till virtuella Azure-nätverk kan du i stället skapa en [*integrerings tjänst miljö*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power automatisering av lokal datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI lokal datagateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokal datagateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

I Azure Logic Apps stöder den lokala datagatewayen de [lokala](../connectors/apis-list.md#on-premises-connectors) anslutningarna för dessa data Källor:

* BizTalk Server 2016
* Filsystem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle-databas
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure Logic Apps stöder Läs-och skriv åtgärder via datagatewayen. Dessa åtgärder har dock [gränser för deras nytto Last storlek](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Även om själva gatewayen inte ådrar sig ytterligare kostnader, gäller [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md) för dessa anslutningar och andra åtgärder i Azure Logic Apps.

## <a name="prerequisites"></a>Krav

* Du har redan [installerat den lokala datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md).

* Du använder [samma Azure-konto och prenumeration](../logic-apps/logic-apps-gateway-install.md#requirements) som användes när du installerade data gatewayen. Det här Azure-kontot måste tillhöra en enda [Azure Active Directory (Azure AD)-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology).

* Gateway-installationen har inte redan registrerats och begärts av en annan Azure gateway-resurs.

  När du skapar en gateway-resurs i Azure Portal väljer du en gateway-installation som länkar till din gateway-resurs och bara den gateway-resursen. I Azure Logic Apps använder lokala utlösare och åtgärder sedan Gateway-resursen för att ansluta till lokala data källor. I dessa utlösare och åtgärder väljer du din Azure-prenumeration och den tillhör ande gateway-resurs som du vill använda. Varje gateway-resurs länkar enbart till en gateway-installation, som endast länkar till ett Azure-konto.

  > [!NOTE]
  > Endast Gateway-administratören kan skapa Gateway-resursen i Azure Portal. För närvarande stöds inte tjänstens huvud namn. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Skapa Azure gateway-resurs

När du har installerat gatewayen på en lokal dator skapar du Azure-resursen för din gateway.

1. Logga in på [Azure Portal](https://portal.azure.com) med samma Azure-konto som användes för att installera gatewayen.

1. I sökrutan Azure Portal anger du "lokal datagateway" och väljer **lokala Datagatewayer**.

   ![Hitta en lokal datagateway](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Under **lokala Datagatewayer**väljer du **Lägg till**.

   ![Lägg till ny Azure-resurs för datagateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Under **skapa Gateway för anslutning**anger du den här informationen för din gateway-resurs. När du är färdig väljer du **Skapa**.

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Resursnamn** | Ange ett namn för din gateway-resurs som bara innehåller bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `(` , `)` ) eller punkter ( `.` ). |
   | **Prenumeration** | Välj Azure-prenumerationen för det Azure-konto som användes för gateway-installationen. Standard prenumerationen baseras på det Azure-konto som du använde för att logga in. |
   | **Resursgrupp** | Den [Azure-resurs grupp](../azure-resource-manager/management/overview.md) som du vill använda |
   | **Position** | Samma region eller plats som valdes för gateway-moln tjänsten under [Gateway-installationen](../logic-apps/logic-apps-gateway-install.md). Annars visas inte Gateway-installationen i listan **installations namn** . Din Logic app-plats kan skilja sig från din plats för gateway-resursen. |
   | **Installations namn** | Välj en gateway-installation som bara visas i listan när följande villkor uppfylls: <p><p>– Gateway-installationen använder samma region som den gateway-resurs som du vill skapa. <br>-Gateway-installationen är inte länkad till en annan Azure gateway-resurs. <br>– Gateway-installationen är länkad till samma Azure-konto som du använder för att skapa Gateway-resursen. <br>– Ditt Azure-konto tillhör en enda [Azure Active Directory (Azure AD)-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology) och är samma konto som användes för gateway-installationen. <p><p>Mer information finns i avsnittet [vanliga frågor och svar](#faq) . |
   |||

   Här är ett exempel som visar en gateway-installation som är i samma region som din gateway-resurs och som är länkad till samma Azure-konto:

   ![Ange information för att skapa en data gateway-resurs](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du har skapat din gateway-resurs och associerat din Azure-prenumeration med den här resursen kan du nu skapa en anslutning mellan din Logic app och din lokala data källa med hjälp av gatewayen.

1. I Azure Portal skapar eller öppnar du din Logic app i Logic App Designer.

1. Lägg till en anslutning som stöder lokala anslutningar, till exempel **SQL Server**.

1. Välj **Anslut via lokal datagateway**.

1. Under **gatewayer**väljer du din Azure-prenumeration som har den gateway-resurs som du vill använda från listan **prenumerationer** .

1. Välj den gateway-resurs som du vill använda från listan **anslutnings Gateway** , som visar tillgängliga gateway-resurser i den valda prenumerationen. Varje gateway-resurs är länkad till en enda Gateway-installation.

   > [!NOTE]
   > Gateway-listan innehåller gateway-resurser i andra regioner eftersom din Logi Kap par plats kan skilja sig från Gateway-resursens plats. 

1. Ange ett unikt anslutnings namn och annan information som krävs, beroende på vilken anslutning du vill skapa.

   Ett unikt anslutnings namn hjälper dig att enkelt hitta anslutningen senare, särskilt om du skapar flera anslutningar. Om tillämpligt, inkludera även den kvalificerade domänen för ditt användar namn.

   Här är ett exempel:

   ![Skapa anslutning mellan Logic app och datagateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. När du är färdig väljer du **Skapa**.

Din gateway-anslutning är nu klar för din Logic app att använda.

## <a name="edit-connection"></a>Redigera anslutning

Om du vill uppdatera inställningarna för en gateway-anslutning kan du redigera anslutningen.

1. Om du vill hitta alla API-anslutningar för just din Logic app går du till din Logic Apps-meny, under **utvecklingsverktyg**, och väljer **API-anslutningar**.

   ![På din Logic app-meny väljer du "API-anslutningar"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Välj den gateway-anslutning som du vill använda och välj sedan **Redigera API-anslutning**.

   > [!TIP]
   > Om dina uppdateringar inte börjar gälla kan du prova [med att stoppa och starta om gatewayens Windows-](../logic-apps/logic-apps-gateway-install.md#restart-gateway) tjänstkonto för gateway-installationen.

Så här hittar du alla API-anslutningar som är associerade med din Azure-prenumeration:

* Från Azure Portal-menyn väljer du **alla tjänster**  >  **Web**  >  **API-anslutningar**.
* Eller Välj **alla resurser**från Azure Portal-menyn. Ange **typ** filter till **API-anslutning**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gateway-resurs

Om du vill skapa en annan gateway-resurs länkar du Gateway-installationen till en annan gateway-resurs eller tar bort Gateway-resursen, kan du ta bort Gateway-resursen utan att det påverkar Gateway-installationen.

1. Från Azure Portal-menyn väljer du **alla resurser**eller söker efter och väljer **alla resurser** från vilken sida som helst. Hitta och välj din gateway-resurs.

1. Om du inte redan har valt väljer du **lokal datagateway**på menyn gateway-resurs. I verktygsfältet gateway-resurs väljer du **ta bort**.

   Ett exempel:

   ![Ta bort gateway-resurs i Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F**: Varför visas inte min Gateway-installation när jag skapar min gateway-resurs i Azure? <br/>
**A**: det här problemet kan inträffa av följande orsaker:

* Ditt Azure-konto måste vara samma konto som är länkat till gateway-installationen på den lokala datorn. Kontrol lera att du är inloggad på Azure Portal med samma identitet som är länkad till gateway-installationen. Kontrol lera också att ditt Azure-konto tillhör en enda [Azure AD-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology) och har angetts till samma Azure AD-klient eller katalog som användes vid installationen av gatewayen.

* Gateway-resursen och gateway-installationen måste använda samma region. Din Logic app-plats kan dock skilja sig från din plats för gateway-resursen.

* Gateway-installationen har redan registrerats och ansökts av en annan gateway-resurs. De här installationerna visas inte i listan **installations namn** . Om du vill granska dina gateway-registreringar i Azure Portal hittar du alla dina Azure-resurser som har den **lokala Datagatewayen** typ i *alla* dina Azure-prenumerationer. Information om hur du avlänkar Gateway-installationen från den andra gateway-resursen finns i [ta bort gateway-resurs](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda dina logikappar](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för Logic Apps](./logic-apps-examples-and-scenarios.md)
