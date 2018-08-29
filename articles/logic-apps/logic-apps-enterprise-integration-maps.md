---
title: Transformera XML med XSLT-kartor – Azure Logic Apps | Microsoft Docs
description: Lägga till XSLT-kartor som omvandlar XML i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 07/08/2016
ms.openlocfilehash: c5e5e0a0a3f8bd5feedc00d5bbfb76a1453ccc84
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123564"
---
# <a name="add-maps-for-xml-transformation-in-azure-logic-apps-with-enterprise-integration-pack"></a>Lägga till kartor för XML-transformering i Azure Logic Apps med Enterprise-Integrationspaket

Enterprise-integration använder maps för att transformera XML-data mellan format. En karta är ett XML-dokument som definierar data i ett dokument som ska transformeras till ett annat format. 

## <a name="why-use-maps"></a>Varför använda maps?

Anta att du regelbundet får B2B order eller fakturor från en kund som använder YYYMMDD-formatet för datum. Du kan dock lagra datum i formatet MMDDYYY i din organisation. Du kan använda en karta till *transformera* YYYMMDD datumformat i MMDDYYY innan du lagrar information om ordning eller faktura i kunddatabasen för aktiviteten.


## <a name="how-do-i-create-a-map"></a>Hur gör jag för att skapa en karta?

Du kan skapa BizTalk Integration projekt med den [Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om enterprise-integrationspaketet") för Visual Studio 2015. Du kan sedan skapa en Integrationskarta-fil som kan du mappa visuellt objekt mellan två filer för XML-schema. När du skapar det här projektet har du en XSLT-dokument.

Om kartan har en referens till en extern sammansättning båda måste laddas upp till integrationskontot. De ska vara laddades upp i en viss ordning först sammansättningen och sedan kartan som refererar till sammansättningen.


## <a name="how-do-i-add-a-map"></a>Hur lägger jag till en karta?

1. I Azure-portalen väljer du **Bläddra**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. I sökrutan filtrera anger **integrering**och välj sedan **Integrationskonton** i resultatlistan.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Välj integrationskontot där du vill lägga till kartan.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Välj den **Maps** panelen.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. När öppnas sidan kartorna, väljer **Lägg till**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Ange en **namn** på kartan. Om du vill överföra filen karta väljer du mappikonen på höger sida av den **kartan** textrutan. När uppladdningen är klar väljer **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. När Azure lägger till kartan i ditt integrationskonto, få skärmen som visar om din mappningsfilen har lagts till eller inte. När du får det här meddelandet kan välja den **Maps** panelen så att du kan visa den nyligen tillagda kartan.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Hur lägger jag till en sammansättning?
Öppna integrationskontot där du vill ladda upp sammansättningen.

1. Välj den **sammansättningar** panelen.

    ![integrationaccount-sammansättningen-panel](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. När öppnas sidan sammansättningarna, väljer **Lägg till**. Ange en **namn** för sammansättningen. Om du vill överföra sammansättningsfilen, väljer du mappikonen på höger sida av den **sammansättningen** textrutan. När uppladdningen är klar väljer **OK**.

    ![Lägg till sammansättning](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Hur gör jag för att redigera en karta?

Du måste ladda upp en ny mappningsfil med ändringarna som du vill. Du kan först hämta kartan för redigering.

Följ dessa steg för att ladda upp en ny karta som ersätter den befintliga import.

1. Välj den **Maps** panelen.

2. När öppnas sidan kartor Välj kartan som du vill redigera.

3. På den **Maps** väljer **uppdatering**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. I filväljaren, väljer du filen som du vill ladda upp och välj sedan **öppna**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Hur du tar bort en karta?

1. Välj den **Maps** panelen.

2. När öppnas sidan kartor Välj kartan som du vill ta bort.

3. Välj **ta bort**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Bekräfta att du vill ta bort kartan.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  
* [Läs mer om transformeringar](logic-apps-enterprise-integration-transform.md "Lär dig mer om Företagsintegrering")  

