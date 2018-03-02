---
title: "Komma åt datakällor lokalt för Logikappar i Azure | Microsoft Docs"
description: "Konfigurera lokala datagateway så att du kan komma åt datakällor lokalt från logikappar"
keywords: "komma åt data på lokala, dataöverföring, kryptering och datakällor"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 5160913b2f08a04f0a985d8ddadd6641a664b258
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>Ansluta till datakällor lokalt från logikappar med lokala datagateway

Konfigurera en lokal datagateway logikappar kan använda med kopplingar som stöds för att komma åt datakällor lokalt från dina logic apps. Gatewayen fungerar som en brygga som ger snabb överföring och kryptering mellan datakällor lokalt och dina logic apps. Gatewayen som vidarebefordrar data från lokala datakällor på krypterade kanaler via Azure Service Bus. Det kommer all trafik som säkra utgående trafik från gateway-agenten. Lär dig mer om [hur datagateway fungerar](logic-apps-gateway-install.md#gateway-cloud-service). 

Gatewayen stöder anslutningar till dessa datakällor lokalt:

*   BizTalk Server 2016
*   DB2  
*   Filsystem
*   Informix
*   MQ
*   MySQL
*   Oracle-databas
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint
*   SQL Server
*   Teradata

Dessa steg visar hur du ställer in lokala datagateway att fungera med dina logic apps. Läs mer om kopplingar som stöds, [kopplingar för Azure Logikappar](../connectors/apis-list.md). 

Information om hur du använder en gateway med andra tjänster finns i följande artiklar:

*   [Microsoft Power BI lokala datagateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services lokala datagateway](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow lokala datagateway](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps lokala datagateway](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Krav

* Du måste redan ha [installerat datagateway på en lokal dator](logic-apps-gateway-install.md).

* När du loggar in på Azure-portalen måste du använda samma arbets- eller skolkonto som används för att [installera lokala datagateway](logic-apps-gateway-install.md#requirements). Logga in kontot måste också ha en Azure-prenumeration som används när du skapar en gateway-resurs i Azure-portalen för gatewayinstallationen.

* Gatewayinstallationen kan inte redan begärts av en Azure gateway-resurs. Du kan associera din gateway-installation till en resurs i Azure gateway. Anspråk händer när du skapar gatewayresursen så att installationen är inte tillgängligt för andra resurser.

* Lokala datagateway körs som en Windows-tjänst och har konfigurerats att använda `NT SERVICE\PBIEgwService` tjänsten inloggningsuppgifter för Windows. Skapa och underhålla gateway-resurs i Azure-portalen på [Windows-tjänstkontot](../logic-apps/logic-apps-gateway-install.md) måste ha minst **deltagare** behörigheter. 

  > [!NOTE]
  > Windows-tjänstkontot skiljer sig från det konto som används för att ansluta till lokala data källor, och från Azure arbets- eller skolkonto som används för att logga in till molntjänster.

## <a name="install-the-on-premises-data-gateway"></a>Installera den lokala datagatewayen

Så om du inte redan gjort det [steg för att installera den lokala datagatewayen](logic-apps-gateway-install.md). Innan du fortsätter med andra steg, se till att du har installerat datagateway på en lokal dator.

<a name="create-gateway-resource"></a>

## <a name="create-an-azure-resource-for-the-on-premises-data-gateway"></a>Skapa en Azure-resurs för lokala datagateway

När du har installerat gatewayen på en lokal dator måste du skapa din datagateway som en resurs i Azure. Det här steget associerar även din gateway-resurs med din Azure-prenumeration.

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). Se till att använda samma Azure arbetet eller skolan e-postadress används för att installera gatewayen.

2. Välj på Azure Huvudmeny, **skapar du en resurs** > **Enterprise Integration** > **lokala datagateway**.

   ![Sök efter ”lokala datagateway”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. På den **skapa anslutningen gateway** sidan, Tillhandahåll följande information för att skapa din data gateway-resurs:

    * **Namnet**: Ange ett namn för din gateway-resurs. 

    * **Prenumerationen**: Välj den Azure-prenumerationen ska associeras med din gateway-resurs. 
    Den här prenumerationen måste vara samma prenumeration som din logikapp.
   
      Standard-prenumerationen är baserad på det Azure-konto som du använde för att logga in.

    * **Resursgruppen**: skapa en resursgrupp eller välj en befintlig resursgrupp för att distribuera din gateway-resurs. 
    Resursgrupper kan du hantera relaterade Azure-resurser som en samling.

    * **Plats**: Azure begränsar den här platsen till samma region som valts för gateway-Molntjänsten under [gatewayinstallationen](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Kontrollera att gateway-resursplats matchar gateway cloud service-plats. Annars visas inte din gateway-installation i listan över installerade gateways som du kan välja i nästa steg.
      > 
      > Du kan använda olika regioner för din gateway-resurs och för din logikapp.

    * **Installationen namnet**: Välj den gateway som du tidigare har installerat om gatewayinstallationen av inte redan har markerats. 

    Lägg till gateway-resurs i instrumentpanelen i Azure, Välj **fäst på instrumentpanelen**. 
    När du är klar väljer du **Skapa**.

    Exempel:

    ![Ange information för att skapa din lokala datagateway](./media/logic-apps-gateway-connection/createblade.png)

    Om du vill hitta eller visa din datagateway när som helst på Azure huvudmenyn väljer **alla tjänster**. 
    Ange ”lokala data gateway” i sökrutan och väljer sedan **lokala Data Gateways**.

    ![Sök efter ”lokala Data Gateways”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-your-logic-app-to-the-on-premises-data-gateway"></a>Ansluta din logikapp till lokala datagateway

Nu när du har skapat din data gateway-resurs och tillhörande din Azure-prenumeration med den här resursen, skapa en anslutning mellan din logikapp och datagateway.

> [!NOTE]
> Din plats för gateway-anslutningen måste finnas i samma region som din logikapp, men du kan använda en datagateway som finns i en annan region.

1. Skapa eller öppna logikappen i logik App Designer i Azure-portalen.

2. Lägg till en koppling som har stöd för lokala anslutningar, t.ex. SQL Server.

3. Efter den ordning som visas, Välj **Anslut via lokala datagateway**, ange ett unikt anslutningsnamn och informationen som krävs och välj den data gateway-resurs som du vill använda. När du är klar väljer du **Skapa**.

   > [!TIP]
   > Ett unikt anslutningsnamn hjälper dig att identifiera anslutningen senare, speciellt när du skapar flera anslutningar. Om tillämpligt, ange domännamn för ditt användarnamn. 

   ![Skapa anslutning mellan logik appen och data gateway](./media/logic-apps-gateway-connection/blankconnection.png)

Grattis, gateway-anslutningen är klar för din logikapp ska användas.

## <a name="edit-your-gateway-connection-settings"></a>Redigera anslutningsinställningarna gateway

När du har skapat en gateway-anslutningen för din logikapp kanske du vill uppdatera inställningarna för den specifika anslutningen.

1. Hitta gateway-anslutningen:

   * På menyn logiken i appen under **utvecklingsverktyg**väljer **API anslutningar**. 
   
     Den **API anslutningar** visar alla API-anslutningar som är associerade med din logikapp, inklusive gateway-anslutningar.

     ![Gå till din logikapp, Välj ”API-anslutningar](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * På Azure huvudmenyn går du till **fler tjänster** > **webb + mobilt** > **API anslutningar** för alla API-anslutningar, inklusive gateway anslutningar som är associerade med din Azure-prenumeration. 

   * På Azure huvudmenyn, går du till **alla resurser** för alla API-anslutningar, inklusive gateway-anslutningar som är associerade med din Azure-prenumeration.

2. Välj gateway-anslutningen som du vill visa eller redigera och välj **redigera API-anslutningen**.

   > [!TIP]
   > Om uppdateringarna inte gälla försök [stoppa och starta om Windows-tjänst för gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Växla eller ta bort dina lokala data gateway-resurs

För att skapa en annan gateway-resurs, associera din gateway med en annan resurs eller ta bort gateway-resurs, kan du ta bort gatewayresursen utan att påverka gateway-installationen. 

1. Gå till den Azure huvudmenyn **alla resurser**. 
2. Hitta och välj din data gateway-resurs.
3. Välj **lokala Data Gateway**, och välj verktygsfältet resurs **ta bort**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda dina Logic Apps](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för logic apps](./logic-apps-examples-and-scenarios.md)
