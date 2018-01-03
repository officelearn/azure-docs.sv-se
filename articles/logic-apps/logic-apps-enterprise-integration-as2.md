---
title: "AS2-meddelanden för B2B enterprise integration - Azure Logic Apps | Microsoft Docs"
description: "Exchange AS2-meddelanden för B2B enterprise integrering med Azure Logikappar"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 6a283d8772e48aa6671d88288c2083d891a220d5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Exchange AS2-meddelanden för enterprise-integrering med logic apps

Innan du kan utbyta AS2-meddelanden för Logikappar i Azure, måste du skapa ett AS2-avtal och spara avtalet i ditt konto för integrering. Här följer stegen för hur du skapar ett AS2-avtal.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* En [integrering konto](../logic-apps/logic-apps-enterprise-integration-accounts.md) som redan har definierat och som är associerade med din Azure-prenumeration
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som definieras i ditt konto för integrering och konfigurerad med AS2-kvalificerare under redan **Business identiteter**

> [!NOTE]
> När du skapar ett avtal måste innehållet i filen avtalet matcha en avtalstyp.    

När du [skapa ett konto för integrering](../logic-apps/logic-apps-enterprise-integration-accounts.md) och [lägga till partners](logic-apps-enterprise-integration-partners.md), du kan skapa ett AS2-avtal genom att följa dessa steg.

## <a name="create-an-as2-agreement"></a>Skapa ett AS2-avtal

1.  Logga in på [Azure Portal](http://portal.azure.com "Azure Portal").  

2.  I den vänstra menyn, Välj **fler tjänster**. I sökrutan anger **integrering** som filter. Välj i resultatlistan **Integrationskonton**.

    > [!TIP]
    > Om du inte ser **fler tjänster**, du kan behöva expandera menyn först. Längst upp i den komprimerade menyn, Välj **menyn Visa**.

    ![Fler tjänster, filtret på ”integration”, Välj ”Integrationskonton”](./media/logic-apps-enterprise-integration-as2/overview-1.png)

3. I den **Integrationskonton** bladet som öppnas väljer du det integration konto där du vill skapa avtal.
Om du inte ser några integrationskonton [skapa en första](../logic-apps/logic-apps-enterprise-integration-accounts.md "om integrationskonton").  

    ![Välj kontot integration var du vill skapa avtalet](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Välj den **avtal** panelen. Om du inte har ett avtal sida vid sida, lägga till panelen först.

    ![Välj ikonen ”avtal”](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. I bladet avtal som öppnas väljer du **Lägg till**.

    ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. Under **Lägg till**, ange en **namn** för ditt avtal. För **avtalstyp**väljer **AS2**. Välj den **värden Partner**, **Värdidentiteten**, **gäst Partner**, och **gäst identitet** för ditt avtal.

    ![Ange avtalsuppgifter](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Egenskap | Beskrivning |
    | --- | --- |
    | Namn |Avtalets namn |
    | Avtalstyp | Ska vara AS2 |
    | Värd-Partner |Ett avtal måste både värden och gästen partner. Den mottagande partnern representerar den organisation som konfigurerar avtalet. |
    | Värdidentiteten |En identifierare för värdpartnern som |
    | Gästen Partner |Ett avtal måste både värden och gästen partner. Gästen partnern representerar den organisation som har att göra affärer med den mottagande partnern. |
    | Gästen identitet |En identifierare för gäst-partner |
    | Ta emot inställningarna |Dessa egenskaper gäller för alla meddelanden som tagits emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal handtag mottagna meddelanden

Nu när du har angett egenskaperna avtal, kan du konfigurera hur detta avtal identifierar och hanterar inkommande meddelanden som tagits emot från din partner via det här avtalet.

1.  Under **Lägg till**väljer **tar emot inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellen i det här avsnittet.

    ![Konfigurera ”ta emot inställningar”](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Alternativt kan du kan åsidosätta egenskaper för inkommande meddelanden genom att välja **åsidosätta meddelandeegenskaper**.

3. Välj om du vill alla inkommande meddelanden ska vara signerade, **meddelandet måste vara signerade**. Från den **certifikat** väljer du en befintlig [gäst partner offentliga certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md) för att validera signaturen på meddelanden. Eller skapa certifikatet om du inte har någon.

4.  Välj om du vill alla inkommande meddelanden som ska krypteras, **meddelandet ska krypteras**. Från den **certifikat** väljer du en befintlig [värden partner privat certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md) för att dekryptera inkommande meddelanden. Eller skapa certifikatet om du inte har någon.

5. Välj om du vill meddelanden som kan komprimeras, **meddelandet ska komprimeras**.

6. Om du vill skicka en synkron disposition meddelanden (MDN) för mottagna meddelanden, Välj **skicka MDN**.

7. Om du vill skicka signerade MDNs för mottagna meddelanden, Välj **Skicka signerat MDN**.

8. Om du vill skicka asynkront MDNs för mottagna meddelanden, Välj **skicka asynkront MDN**.

9. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

| Egenskap | Beskrivning |
| --- | --- |
| Åsidosätt meddelandeegenskaper |Anger egenskaper i mottagna meddelanden som kan åsidosättas. |
| Meddelandet måste vara signerade |Kräver meddelanden som ska signeras digitalt. Konfigurera gäst partner offentliga certifikat för signaturverifiering.  |
| Meddelandet ska krypteras |Kräver meddelanden som ska krypteras. Icke-krypterade meddelanden avvisas. Konfigurera värd partner privata certifikat för att dekryptera meddelanden.  |
| Meddelandet som ska komprimeras |Kräver att meddelanden ska komprimeras. Icke-komprimerade meddelanden avvisas. |
| MDN Text |I standard disposition meddelanden (MDN) som ska skickas till den som skickade meddelandet. |
| Skicka MDN |Kräver MDNs skickas. |
| Skicka signerat MDN |Kräver MDNs signeras. |
| MIC algoritm |Välj algoritmen som ska användas för signering av meddelanden. |
| Skicka asynkront MDN | Kräver att meddelanden ska skickas asynkront. |
| Webbadress | Ange URL: en var att skicka MDNs. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur detta avtal identifierar och hanterar utgående meddelanden som du skickar till dina partners via det här avtalet.

1.  Under **Lägg till**väljer **skicka inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellen i det här avsnittet.

    ![Ange egenskaperna ”skicka inställningar”](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Om du vill skicka meddelanden till din partner, Välj **aktivera Meddelandesignering**. För att signera meddelanden, i den **MIC algoritmen** väljer den *partner privata värdcertifikatet MIC algoritmen*. I den **certifikat** väljer du en befintlig [värden partner privat certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Om du vill skicka krypterade meddelanden till partnern, Välj **aktivera meddelandekryptering**. För att kryptera meddelanden, i den **krypteringsalgoritm** väljer den *gäst partner offentliga certifikat algoritmen*.
I den **certifikat** väljer du en befintlig [gäst partner offentliga certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Om du vill komprimera meddelandet, Välj **aktivera komprimering av meddelandet**.

5. Om du vill låta programmet HTTP content-type-huvudet till en enda rad väljer **vika HTTP-huvuden**.

6. För att ta emot synkron MDNs för skickade meddelanden, Välj **begära MDN**.

7. För att ta emot signerade MDNs för skickade meddelanden, Välj **begäran signerade MDN**.

8. För att ta emot asynkrona MDNs för skickade meddelanden, Välj **begära asynkron MDN**. Om du väljer det här alternativet anger du URL att skicka MDNs.

9. För att kräva oavvislighet mottogs, Välj **aktivera NRR**.  

10. Välj för att ange algoritmen format som ska användas i MIC eller logga in utgående huvuden AS2-meddelande eller MDN **SHA2 algoritmen format**.  

11. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

| Egenskap | Beskrivning |
| --- | --- |
| Aktivera Meddelandesignering |Kräver att alla meddelanden som skickas från avtalet signeras. |
| MIC algoritm |Algoritmen som ska användas för signering av meddelanden. Konfigurerar partner privata värdcertifikatet MIC algoritmen för att signera meddelanden. |
| Certifikat |Välj certifikatet som ska användas för signering av meddelanden. Konfigurerar värden partner privata certifikat för signering av meddelanden. |
| Aktivera kryptering av meddelanden |Kräver kryptering av alla meddelanden som skickas från det här avtalet. Konfigurerar gäst partner offentliga certifikat algoritm för kryptering av meddelanden. |
| Krypteringsalgoritm |Krypteringsalgoritm som ska användas för kryptering av meddelanden. Konfigurerar gäst partner offentliga certifikat för kryptering av meddelanden. |
| Certifikat |Certifikatet som du använder för att kryptera meddelanden. Konfigurerar gäst partner privat certifikat för kryptering av meddelanden. |
| Aktivera komprimering för meddelandet |Kräver komprimering av alla meddelanden som skickas från det här avtalet. |
| Vika HTTP-huvuden |Placerar HTTP innehållstyp-sidhuvud till en enda rad. |
| Begäran MDN |Kräver en MDN för alla meddelanden som skickas från det här avtalet. |
| Begäran signerade MDN |Kräver att alla MDNs som skickas till det här avtalet signeras. |
| Asynkron MDN för begäran |Kräver asynkron MDNs skickas till det här avtalet. |
| Webbadress |Ange URL: en var att skicka MDNs. |
| Aktivera NRR |Kräver oavvislighet mottogs (NRR) attributet kommunikation som ger bevis som data togs emot som åtgärdas. |
| SHA2-algoritmformat |Välj algoritm format i MIC eller logga in utgående huvuden AS2-meddelande eller MDN |

## <a name="find-your-created-agreement"></a>Hitta din skapade avtal

1.  När du är klar med inställningen alla dina avtal egenskaper på den **Lägg till** bladet välj **OK** har skapat ditt avtal och gå tillbaka till ditt kontoblad för integrering.

    Ditt nya avtal nu visas i din **avtal** lista.

2.  Du kan också visa dina avtal i ditt Kontoöversikt för integrering. Välj på ditt kontoblad integration **översikt**och välj den **avtal** panelen. 

    ![Välj ”avtal” panelen om du vill visa alla avtal](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Visa swagger
Finns det [swagger information](/connectors/as2/). 

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
