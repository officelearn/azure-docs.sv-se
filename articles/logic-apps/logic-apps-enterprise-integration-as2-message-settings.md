---
title: Inställningar för AS2-meddelanden
description: Referensguide för AS2 skicka och ta emot inställningar i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793039"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referens för AS2-meddelandeinställningar i Azure Logic Apps med Enterprise Integration Pack

Den här referensen beskriver de egenskaper som du kan ange för att ange hur ett AS2-avtal hanterar meddelanden som skickas och tas emot mellan handelspartner. Ställ in dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 Ta emot inställningar

![Välj "Ta emot inställningar"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Åsidosätt meddelandeegenskaper** | Inga | Åsidosätter egenskaperna för inkommande meddelanden med egenskapsinställningarna. |
| **Meddelandet ska signeras** | Inga | Anger om alla inkommande meddelanden måste signeras digitalt. Om du behöver signera väljer du ett befintligt offentligt certifikat för gästpartner i **listan Certifikat** för att validera signaturen på meddelandena. Om du inte har något certifikat kan du läsa mer om [hur du lägger till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Meddelandet ska krypteras** | Inga | Anger om alla inkommande meddelanden måste krypteras. Icke-krypterade meddelanden avvisas. Om du behöver kryptering väljer du ett privat värdcertifikat i **listan Certifikat** för att dekryptera inkommande meddelanden. Om du inte har något certifikat kan du läsa mer om [hur du lägger till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Meddelandet ska komprimeras** | Inga | Anger om alla inkommande meddelanden måste komprimeras. Meddelanden som inte är komprimerade avvisas. |
| **Tillåt inte meddelande-ID-dubbletter** | Inga | Anger om meddelanden med dubblett-ID ska tillåtas. Om du inte tillåter dubblett-ID väljer du antalet dagar mellan checkarna. Du kan också välja om du vill pausa dubbletter. |
| **MDN-text** | Inga | Anger det standardmeddelandebehörelse (MDN) som du vill ha skickat till meddelandeavsändaren. |
| **Skicka MDN** | Inga | Anger om synkrona MDN ska skickas för mottagna meddelanden.  |
| **Skicka signerad MDN** | Inga | Anger om signerade MDN ska skickas för mottagna meddelanden. Om du behöver signera väljer du den algoritm som ska användas för signering av meddelanden i listan **MIC-algoritm.** |
| **Skicka asynkron MDN** | Inga | Anger om MDN ska skickas asynkront. Om du väljer asynkrona MDN anger du url:en för var MDN ska skickas i rutan **URL.** |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 Skicka inställningar

![Välj "Skicka inställningar"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Aktivera meddelandesignering** | Inga | Anger om alla utgående meddelanden måste signeras digitalt. Om du behöver signera väljer du följande värden: <p>- I listan **Signeringsalgoritm** väljer du den algoritm som ska användas för signering av meddelanden. <br>- I **listan Certifikat** väljer du ett privat värdpartnercertifikat för signering av meddelanden. Om du inte har något certifikat kan du läsa mer om [hur du lägger till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Aktivera meddelandekryptering** | Inga | Anger om alla utgående meddelanden måste krypteras. Om du behöver kryptering väljer du dessa värden: <p>- I listan **Krypteringsalgoritm** väljer du den offentliga certifikatalgoritm för gästpartner som ska användas för kryptering av meddelanden. <br>- I **listan Certifikat** väljer du ett privat certifikat för en befintlig gästpartner för kryptering av utgående meddelanden. Om du inte har något certifikat kan du läsa mer om [hur du lägger till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Aktivera meddelandekomprimering** | Inga | Anger om alla utgående meddelanden måste komprimeras. |
| **Utveckla HTTP-huvuden** | Inga | Placerar `content-type` HTTP-huvudet på en enda rad. |
| **Överföra filnamn i MIME-sidhuvud** | Inga | Anger om filnamnet ska tas med i MIME-huvudet. |
| **Begär MDN** | Inga | Anger om meddelandedispositionsmeddelanden ska ta emot meddelanden för alla utgående meddelanden. |
| **Begär signerad MDN** | Inga | Anger om signerade MDN ska ta emot signerade MDN för alla utgående meddelanden. Om du behöver signera väljer du den algoritm som ska användas för signering av meddelanden i listan **MIC-algoritm.** |
| **Begär asynkron MDN** | Inga | Anger om MDN ska ta emot MDN asynkront. Om du väljer asynkrona MDN anger du url:en för var MDN ska skickas i rutan **URL.** |
| **Aktivera NRR** | Inga | Anger om du vill kräva icke-avpudisationsinleverans (NRR). Detta kommunikationsattribut ger bevis för att data togs emot som adresserade. |
| **SHA2-algoritmformat** | Inga | Anger det MIC-algoritmformat som ska användas för att logga in rubrikerna för utgående AS2-meddelanden eller MDN |
||||

## <a name="next-steps"></a>Nästa steg

[Utbyta AS2-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2.md)
