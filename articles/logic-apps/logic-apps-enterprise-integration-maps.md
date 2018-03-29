---
title: Transformera XML med XSLT-maps - Azure Logic Apps | Microsoft Docs
description: Lägg till XSLT mappar för att omvandla XML-data med Azure Logikappar och Enterprise-Integrationspaket
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4b4d626028eed09e9ce6a45fa8fa69859c082da7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="add-maps-for-xml-data-transform"></a>Lägga till mappningar för transformering av XML-data

Enterprise integration använder maps för att omvandla XML-data mellan format. En karta är ett XML-dokument som definierar data i ett dokument som ska omvandlas till ett annat format. 

## <a name="why-use-maps"></a>Varför använda maps?

Anta att du regelbundet ta emot B2B order eller fakturor från en kund som använder YYYMMDD format för datum. Du kan dock lagra datum i formatet MMDDYYY i din organisation. Du kan använda en karta till *transformera* YYYMMDD datumformat i MMDDYYY innan de lagras informationen order eller faktura i kunddatabasen för aktiviteten.


## <a name="how-do-i-create-a-map"></a>Hur skapar jag en karta

Du kan skapa projekt BizTalk-integrering med den [Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om enterprise-integrationspaket") för Visual Studio 2015. Du kan sedan skapa en Integrationskarta-fil som kan du mappa visuellt objekt mellan två XML-schemafiler. När du skapar det här projektet har du en XSLT-dokument.

Om kartan har en referens till en extern sammansättningsresurs både måste överföras till kontot för integrering. De ska överföras i någon viss ordning först sammansättningen och sedan kartan som refererar till sammansättningen.


## <a name="how-do-i-add-a-map"></a>Hur lägger jag till en karta?

1. Välj i Azure-portalen **Bläddra**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Skriv i sökrutan filtrera **integrering**och välj **Integrationskonton** från resultatlistan över.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Välj integration konto där du vill lägga till kartan.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Välj den **Maps** panelen.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Välj när mappningarna sidan öppnas **Lägg till**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Ange en **namn** på kartan. Om du vill överföra filen karta, väljer du mappikonen på höger sida av den **kartan** textruta. När överföringen är klar väljer **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. När Azure läggs mappningen till ditt konto integration, få skärmen som visar om din mappningsfilen har lagts till eller inte. När du får detta meddelande, välja den **Maps** panelen så att du kan visa nytillagda kartan.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Hur lägger jag till en sammansättning?
Öppna det integration konto där du vill överföra sammansättningen.

1. Välj den **sammansättningar** panelen.

    ![integrationaccount-assembly-tile](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Välj när sammansättningarna sidan öppnas **Lägg till**. Ange en **namn** för sammansättningen. Om du vill överföra sammansättningsfilen väljer du mappikonen på höger sida av den **sammansättningen** textruta. När överföringen är klar väljer **OK**.

    ![Lägg till sammansättningen](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Hur redigerar en karta?

Du måste överföra en ny fil karta med ändringarna som du vill använda. Du kan först hämta kartan för redigering.

Följ dessa steg för att ladda upp en ny mappning som ersätter den befintliga mappningen.

1. Välj den **Maps** panelen.

2. När öppnas sidan Maps väljer du den mappning som du vill redigera.

3. På den **Maps** väljer **uppdatering**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Välj mappningsfilen som du vill ladda upp och välj sedan i filväljaren, **öppna**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Hur du tar bort en karta?

1. Välj den **Maps** panelen.

2. Välj kartan som du vill ta bort när öppnas sidan Maps.

3. Välj **ta bort**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Bekräfta att du vill ta bort mappningen.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  
* [Mer information om transformeringar](logic-apps-enterprise-integration-transform.md "Lär dig mer om enterprise integration transformeringar")  

