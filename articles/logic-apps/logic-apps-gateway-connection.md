---
title: Få tillgång till datakällor lokalt
description: Ansluta till lokala datakällor från Azure Logic Apps genom att skapa en azure-lokal datagatewayresurs
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657142"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ansluta till lokala datakällor från Azure Logic Apps

Innan du kan komma åt datakällor lokalt från dina logikappar måste du skapa en Azure-resurs när du [har installerat den lokala *datagatewayen* på en lokal dator](../logic-apps/logic-apps-gateway-install.md). Dina logikappar använder sedan den här Azure gateway-resursen i utlösare och åtgärder som tillhandahålls av [de lokala anslutningsapparna](../connectors/apis-list.md#on-premises-connectors) som är tillgängliga för Azure Logic Apps.

Den här artikeln visar hur du skapar din Azure gateway-resurs för en tidigare [installerad gateway på den lokala datorn](../logic-apps/logic-apps-gateway-install.md). Mer information om gatewayen finns i [Så här fungerar gatewayen](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Om du vill ansluta till virtuella Azure-nätverk kan du överväga att skapa en [*integrationstjänstmiljö*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i stället. 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power Automate lokal datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI lokal datagateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokal datagateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

I Azure Logic Apps stöder den lokala datagatewayen de [lokala anslutningsapparna](../connectors/apis-list.md#on-premises-connectors) för dessa datakällor:

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

Azure Logic Apps stöder läs- och skrivåtgärder via datagatewayen. Dessa åtgärder har dock [gränser för deras nyttolaststorlek.](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations) Även om själva gatewayen inte medför ytterligare kostnader gäller [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md) för dessa kopplingar och andra åtgärder i Azure Logic Apps.

## <a name="prerequisites"></a>Krav

* Du har redan [installerat den lokala datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md).

* Du använder [samma Azure-konto och prenumeration](../logic-apps/logic-apps-gateway-install.md#requirements) som användes vid installation av den datagatewayen. Det här Azure-kontot måste tillhöra en enda [Azure Active Directory-klientorganisation eller -katalog (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* Gateway-installationen är inte redan registrerad och begärs av en annan Azure gateway-resurs.

  När du skapar en gateway-resurs i Azure-portalen väljer du en gatewayinstallation som länkar till gatewayresursen och endast den gatewayresursen. I Azure Logic Apps använder lokala utlösare och åtgärder sedan gatewayresursen för att ansluta till lokala datakällor. I dessa utlösare och åtgärder väljer du din Azure-prenumeration och den associerade gatewayresursen som du vill använda. Varje gateway-resurs länkar till endast en gateway-installation, som länkar till endast ett Azure-konto.

  > [!NOTE]
  > Endast gateway-administratören kan skapa gatewayresursen i Azure-portalen. För närvarande stöds inte tjänsthuvudnamn. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Skapa Azure gateway-resurs

När du har installerat gatewayen på en lokal dator skapar du Azure-resursen för din gateway.

1. Logga in på [Azure-portalen](https://portal.azure.com) med samma Azure-konto som användes för att installera gatewayen.

1. I sökrutan för Azure portal anger du "lokal datagateway" och väljer **Lokala datagateways**.

   ![Hitta "Lokal datagateway"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Under **Lokala datagateways**väljer du **Lägg till**.

   ![Lägga till ny Azure-resurs för datagateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Under **Skapa anslutningsgateway**anger du den här informationen för gatewayresursen. När du är klar väljer du **Skapa**.

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Resursnamn** | Ange ett namn för gatewayresursen som bara innehåller bokstäver, siffror,`_`bindestreck`(`( `)``-`), understreck ( ), parenteser ( , eller punkter (`.`). |
   | **Prenumeration** | Välj Azure-prenumerationen för Azure-kontot som användes för gateway-installationen. Standardprenumerationen baseras på det Azure-konto som du använde för att logga in. |
   | **Resursgrupp** | Den [Azure-resursgrupp](../azure-resource-manager/management/overview.md) som du vill använda |
   | **Location** | Samma region eller plats som valdes för gatewaymolntjänsten under [gatewayinstallationen](../logic-apps/logic-apps-gateway-install.md). Annars visas inte gatewayinstallationen i listan **Installationsnamn.** Logikappens plats kan skilja sig från gateway-resursplatsen. |
   | **Installationsnamn** | Välj en gateway-installation som bara visas i listan när dessa villkor är uppfyllda: <p><p>- Gateway-installationen använder samma region som gatewayresursen som du vill skapa. <br>- Gateway-installationen är inte länkad till en annan Azure gateway-resurs. <br>- Gateway-installationen är länkad till samma Azure-konto som du använder för att skapa gateway-resursen. <br>- Ditt Azure-konto tillhör en enda [Azure Active Directory (Azure AD) klient eller katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology) och är samma konto som användes för gateway-installationen. <p><p>Mer information finns i avsnittet Vanliga frågor och [svar.](#faq) |
   |||

   Här är ett exempel som visar en gateway-installation som finns i samma region som din gatewayresurs och som är länkad till samma Azure-konto:

   ![Ange information för att skapa datagatewayresurs](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du har skapat din gateway-resurs och associerat din Azure-prenumeration med den här resursen kan du nu skapa en anslutning mellan logikappen och din lokala datakälla med hjälp av gatewayen.

1. Skapa eller öppna logikappen i Logic App Designer i Azure-portalen.

1. Lägg till en anslutningsapp som stöder lokala anslutningar, till exempel **SQL Server**.

1. Välj **Anslut via lokal datagateway**.

1. Under Gateways väljer du din Azure-prenumeration som har den gatewayresurs du vill ha under **Gateways**i listan **Prenumerationer.**

1. Välj den gatewayresurs som du vill använda i listan **Anslutningsgateway,** som visar tillgängliga gatewayresurser i den valda prenumerationen. Varje gateway-resurs är länkad till en enda gateway-installation.

   > [!NOTE]
   > Gateways-listan innehåller gatewayresurser i andra regioner eftersom logikappens plats kan skilja sig från gatewayresursens plats. 

1. Ange ett unikt anslutningsnamn och annan nödvändig information, vilket beror på vilken anslutning du vill skapa.

   Ett unikt anslutningsnamn hjälper dig att enkelt hitta anslutningen senare, särskilt om du skapar flera anslutningar. Om tillämpligt, inkludera även den kvalificerade domänen för ditt användarnamn.

   Här är ett exempel:

   ![Skapa anslutning mellan logikapp och datagateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. När du är klar väljer du **Skapa**.

Gateway-anslutningen är nu klar för logikappen.

## <a name="edit-connection"></a>Redigera anslutning

Om du vill uppdatera inställningarna för en gatewayanslutning kan du redigera anslutningen.

1. Om du vill hitta alla API-anslutningar för just logikappen väljer du **API-anslutningar**under **Utvecklingsverktyg**på logikappens meny .

   ![På logikappmenyn väljer du "API-anslutningar"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Välj den gatewayanslutning du vill använda och välj sedan **Redigera API-anslutning**.

   > [!TIP]
   > Om uppdateringarna inte börjar gälla kan du prova [att stoppa och starta om windows-tjänstkontot](../logic-apps/logic-apps-gateway-install.md#restart-gateway) för gatewayen för gatewayinstallationen.

Så här hittar du alla API-anslutningar som är associerade med din Azure-prenumeration:

* På Portal-menyn i Azure väljer du **Alla tjänster** > **Webb-API-anslutningar****Web** > .
* Eller välj **Alla resurser**på Azure-portalmenyn . Ange **typfiltret** på **API-anslutning**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gateway-resurs

Om du vill skapa en annan gateway-resurs, länka gatewayinstallationen till en annan gatewayresurs eller ta bort gatewayresursen kan du ta bort gatewayresursen utan att påverka gatewayinstallationen.

1. På Portal-menyn i Azure väljer du **Alla resurser**eller söker efter och väljer **Alla resurser** på valfri sida. Sök efter och välj din gatewayresurs.

1. Om du inte redan är markerad väljer du **Lokal datagateway**på gateway-resursmenyn . Välj **Ta bort**i verktygsfältet gateway-resurs .

   Ett exempel:

   ![Ta bort gatewayresurs i Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F**: Varför visas inte min gatewayinstallation när jag skapar min gatewayresurs i Azure? <br/>
**S:** Det här problemet kan inträffa av följande skäl:

* Ditt Azure-konto måste vara samma konto som är kopplat till gateway-installationen på den lokala datorn. Kontrollera att du är inloggad på Azure-portalen med samma identitet som är länkad till gateway-installationen. Kontrollera också att ditt Azure-konto tillhör en enda [Azure AD-klientorganisation eller -katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology) och är inställd på samma Azure AD-klient eller katalog som användes under gatewayinstallationen.

* Gateway-resurs- och gatewayinstallationen måste använda samma region. Logikappens plats kan dock skilja sig från gatewayresursplatsen.

* Gateway-installationen är redan registrerad och begärd av en annan gatewayresurs. Dessa installationer visas inte i listan **Installationsnamn.** Om du vill granska dina gatewayregistreringar i Azure-portalen hittar du alla dina Azure-resurser som har den **lokala datagatewaystypen** för *alla* dina Azure-prenumerationer. Mer om du vill ta bort länken till gatewayinstallationen från den andra gatewayresursen finns i [Ta bort gatewayresursen](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda dina logikappar](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för logikappar](./logic-apps-examples-and-scenarios.md)
