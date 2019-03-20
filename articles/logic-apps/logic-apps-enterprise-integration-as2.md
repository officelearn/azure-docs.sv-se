---
title: AS2-meddelanden för B2B enterprise-integration – Azure Logic Apps | Microsoft Docs
description: Utbyta AS2-meddelanden för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.date: 06/08/2017
ms.openlocfilehash: 3413b235d9202530eb1a3129637e3746bbe6585b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872591"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Utbyta AS2-meddelanden för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket

Innan du kan utbyta AS2-meddelanden för Azure Logic Apps, måste du skapa ett AS2-avtal och lagra det avtalet i ditt integrationskonto. Här följer stegen för hur du skapar ett AS2-avtal.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-accounts.md) som redan har definierat och som är associerade med din Azure-prenumeration
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto och konfigurerad med AS2-kvalificerare under **Företagsidentiteter**

> [!NOTE]
> När du skapar ett avtal måste innehållet i filen avtal matcha avtalstypen.    

När du [skapar ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md) och [lägga till partner](logic-apps-enterprise-integration-partners.md), du kan skapa ett AS2-avtal genom att följa dessa steg.

## <a name="create-an-as2-agreement"></a>Skapa ett AS2-avtal

1.  Logga in på [Azure Portal](https://portal.azure.com "Azure Portal").  

2. Välj på Azure-huvudmenyn **alla tjänster**. Ange ”-integration” i sökrutan och välj sedan **integrationskonton**.

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Om du inte ser **alla tjänster**, du kan behöva expanderar du menyn först. Högst upp på menyn minimerade, Välj **visa textetiketter**.

3. Under **Integrationskonton**, Välj integrationskontot där du vill skapa avtalet.

   ![Välj var du vill skapa avtalet för integrationskontot](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Välj den **avtal** panelen. Om du inte har ett avtal panel, Lägg till panelen först.

    ![Välj panelen ”avtal”](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. Under **avtal**, Välj **Lägg till**.

    ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. Under **Lägg till**, ange en **namn** för ditt avtal. För **avtalstyp**väljer **AS2**. Välj den **värdpartner**, **Värdidentiteten**, **Gästpartner**, och **Gästidentitet** för ditt avtal.

    ![Ange avtalsinformation](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Egenskap  | Beskrivning |
    | --- | --- |
    | Namn |Avtalets namn |
    | Avtalstyp | Bör vara AS2 |
    | Värdpartner |Ett avtal måste både en värdens och gästens partner. Den mottagande partnern representerar organisationen som konfigurerar avtalet. |
    | Värd-identitet |En identifierare för den mottagande partnern |
    | Gästpartner |Ett avtal måste både en värdens och gästens partner. Gästpartner representerar den organisation som gör affärer med den mottagande partnern. |
    | Gästidentitet |En identifierare för gästpartner |
    | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal hanterar fått meddelanden

Nu när du har ställt in egenskaperna avtal kan konfigurera du hur detta avtal identifierar och hanterar inkommande meddelanden som tas emot från din partner via detta avtal.

1.  Under **Lägg till**väljer **ta emot inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellen i det här avsnittet.

    ![Konfigurera ”Mottagningsinställningarna”](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Du kan också du kan åsidosätta egenskaperna för inkommande meddelanden genom att välja **Åsidosätt meddelandeegenskaper**.

3. Om du vill att alla inkommande meddelanden signeras, Välj **meddelandet ska vara signerat**. Från den **certifikat** väljer du en befintlig [gäst partner offentligt certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md) för att verifiera signaturen på meddelanden. Eller skapa certifikatet om du inte har något.

4.  Om du vill att alla inkommande meddelanden som ska krypteras, Välj **meddelandet ska krypteras**. Från den **certifikat** väljer du en befintlig [privata värdcertifikatet för partner](../logic-apps/logic-apps-enterprise-integration-certificates.md) för dekryptering inkommande meddelanden. Eller skapa certifikatet om du inte har något.

5. För att kräva meddelanden som kan komprimeras, Välj **meddelandet ska vara komprimerat**.

6. Om du vill skicka en synkron disposition-meddelanden (MDN) för mottagna meddelanden, Välj **skicka MDN**.

7. Om du vill skicka signerad mdn måste specificeras för mottagna meddelanden, Välj **skicka signerad MDN**.

8. För att skicka asynkron mdn måste specificeras för mottagna meddelanden, Välj **skicka asynkron MDN**.

9. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

| Egenskap  | Beskrivning |
| --- | --- |
| Åsidosätt meddelandeegenskaper |Anger egenskaper i mottagna meddelanden kan åsidosättas. |
| Meddelandet ska vara signerat |Kräver meddelanden signeras digitalt. Konfigurera gästen partner offentliga certifikat för signaturverifiering.  |
| Meddelandet ska vara krypterat |Kräver meddelanden som ska krypteras. Icke-krypterade meddelanden avvisas. Konfigurera värd partner privata certifikat för att dekryptera meddelanden.  |
| Meddelandet ska vara komprimerat |Kräver att meddelanden ska komprimeras. Icke-komprimerade meddelanden avvisas. |
| MDN-text |Den standard disposition meddelanden (MDN) som ska skickas till meddelandets avsändare. |
| Skicka MDN |Kräver mdn måste specificeras som ska skickas. |
| Skicka signerad MDN |Kräver mdn måste specificeras signeras. |
| MIC-algoritm |Välj algoritmen som ska användas för signering av meddelanden. |
| Skicka asynkron MDN | Kräver att meddelanden ska skickas asynkront. |
| URL | Ange URL att skicka de mdn måste specificeras. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur detta avtal identifierar och hanterar utgående meddelanden som du skickar till dina partner via detta avtal.

1.  Under **Lägg till**väljer **skicka inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellen i det här avsnittet.

    ![Ange egenskaper för ”skicka inställningar”](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Om du vill skicka meddelanden till din partner, Välj **aktivera Meddelandesignering**. För att signera meddelanden i den **MIC-algoritm** väljer den *partner privata värdcertifikatet MIC-algoritm*. I den **certifikat** väljer du en befintlig [privata värdcertifikatet för partner](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Om du vill skicka krypterade meddelanden till partnern, Välj **aktivera meddelandekryptering**. För att kryptera meddelanden i den **krypteringsalgoritm** väljer den *gäst partner offentligt certifikat algoritmen*.
I den **certifikat** väljer du en befintlig [gäst partner offentligt certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Om du vill komprimera meddelandet, Välj **aktivera meddelandekomprimering**.

5. Om du vill Vik ut HTTP content-type-huvud som en enda rad, Välj **Vik ut HTTP-huvuden**.

6. För att få synkron mdn måste specificeras för skickade meddelanden, Välj **begär MDN**.

7. För att få signerad mdn måste specificeras för skickade meddelanden, Välj **begär signerad MDN**.

8. För att få asynkron mdn måste specificeras för skickade meddelanden, Välj **begär asynkron MDN**. Om du väljer det här alternativet anger du URL: en för att skicka de mdn måste specificeras.

9. För att kräva oavvislighet efter, Välj **aktivera NRR**.  

10. Välj för att ange algoritmformat som ska användas i MIC eller logga in utgående rubriker för AS2-meddelandet eller MDN **SHA2-algoritmformat**.  

11. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

| Egenskap  | Beskrivning |
| --- | --- |
| Aktivera meddelandesignering |Kräver att alla meddelanden som skickas från avtalet signeras. |
| MIC-algoritm |Algoritmen som ska användas för signering av meddelanden. Konfigurerar partner privata värdcertifikatet MIC-algoritm för att signera meddelanden. |
| Certifikat |Välj certifikatet som ska användas för signering av meddelanden. Konfigurerar partner privata värdcertifikatet för att signera meddelanden. |
| Aktivera meddelandekryptering |Kräver kryptering för alla meddelanden som skickas från detta avtal. Konfigurerar gäst partner offentligt certifikat algoritm för kryptering av meddelanden. |
| Krypteringsalgoritm |Krypteringsalgoritmen som ska användas för kryptering av meddelanden. Konfigurerar gäst partner offentligt certifikat för kryptering av meddelanden. |
| Certifikat |Certifikatet som ska användas för att kryptera meddelanden. Konfigurerar gäst partner privata certifikat för kryptering av meddelanden. |
| Aktivera meddelandekomprimering |Kräver komprimering för alla meddelanden som skickas från detta avtal. |
| Vik ut HTTP-huvuden |Placerar HTTP content-type-rubriken till en enda rad. |
| Begär MDN |Kräver en MDN för alla meddelanden som skickas från detta avtal. |
| Begär signerad MDN |Kräver mdn måste specificeras som skickas till detta avtal ska signeras. |
| Begär asynkron MDN |Kräver asynkron mdn måste specificeras som ska skickas till detta avtal. |
| URL |Ange URL att skicka de mdn måste specificeras. |
| Aktivera NRR |Kräver oavvislighet för mottagande (NRR), ett attribut för kommunikation som ger bevis som data togs emot som åtgärdas. |
| SHA2-algoritmformat |Välj att använda i MIC eller logga in utgående rubriker för AS2-meddelandet eller MDN-algoritmformat |

## <a name="find-your-created-agreement"></a>Hitta ditt skapade avtal

1. När du är klar med att alla dina avtal egenskaper på den **Lägg till** väljer **OK** har skapat ditt avtal och gå tillbaka till ditt integrationskonto.

    Ditt nyligen tillagda avtal nu visas i din **avtal** lista.

2. Du kan också visa dina avtal i din integrering Kontoöversikt. På din integration meny väljer **översikt**och välj sedan den **avtal** panelen. 

   ![Välj ”avtal” panelen om du vill visa alla avtal](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Visa swagger
Se den [swagger information](/connectors/as2/). 

## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
