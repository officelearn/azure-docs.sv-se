---
title: Inställningar för AS2-meddelande – Azure Logic Apps
description: Referensguide för AS2 skicka och ta emot inställningar i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769458"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referens för inställningar för AS2-meddelande i Azure Logic Apps med Enterprise-Integrationspaket

Den här referensen beskriver de egenskaper som du kan ange för att ange hur ett AS2-avtal hanterar meddelanden skickas och tas emot mellan affärspartner. Ställa in egenskaperna baserat på ditt avtal med partner som utbyter meddelanden med dig.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 ta emot inställningar

![Välj ”Ta emot inställningarna”](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Egenskap  | Krävs | Beskrivning |
|----------|----------|-------------|
| **Åsidosätt meddelandeegenskaper** | Nej | Åsidosätter egenskaperna på inkommande meddelanden med inställningar för enhetsegenskaper. |
| **Meddelandet ska vara signerat** | Nej | Anger om alla inkommande meddelanden måste vara digitalt signerade. Om du behöver registrera, från den **certifikat** väljer du ett befintligt gäst partner offentliga certifikat för att verifiera signaturen på meddelanden. Om du inte har ett certifikat kan du läsa mer om [certifikat läggs till](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Meddelandet ska krypteras** | Nej | Anger om alla inkommande meddelanden måste vara krypterat. Icke-krypterade meddelanden avvisas. Om du behöver kryptering, från den **certifikat** väljer du ett befintligt värd partner privata certifikat för dekryptering av inkommande meddelanden. Om du inte har ett certifikat kan du läsa mer om [certifikat läggs till](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Meddelandet ska vara komprimerat** | Nej | Anger om alla inkommande meddelanden måste komprimeras. Icke-komprimerade meddelanden avvisas. |
| **Tillåt inte dubbletter av meddelande-ID** | Nej | Anger om du vill tillåta meddelanden med dubbla ID: N. Om du inte tillåta dubbla ID väljer du antalet dagar mellan kontroller. Du kan också välja om du vill pausa dubbletter. |
| **MDN Text** | Nej | Anger den standard disposition meddelanden (MDN) som ska skickas till meddelandets avsändare. |
| **Skicka MDN** | Nej | Anger om du vill skicka synkron mdn måste specificeras för mottagna meddelanden.  |
| **Skicka signerad MDN** | Nej | Anger om du vill skicka signerad mdn måste specificeras för mottagna meddelanden. Om du behöver registrera, från den **MIC-algoritm** väljer du algoritmen som ska användas för att signera meddelanden. |
| **Skicka asynkron MDN** | Nej | Anger om du vill skicka mdn måste specificeras asynkront. Om du väljer asynkron mdn måste specificeras i den **URL** skriver du URL: en för att skicka de mdn måste specificeras. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 skicka inställningar

![Välj ”Skicka inställningar”](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Egenskap  | Krävs | Beskrivning |
|----------|----------|-------------|
| **Aktivera Meddelandesignering** | Nej | Anger om alla utgående meddelanden måste vara digitalt signerade. Om du behöver registrera, väljer du dessa värden: <p>-Från den **signering algoritmen** väljer du algoritmen som ska användas för att signera meddelanden. <br>-Från den **certifikat** väljer du ett befintligt värd partner privata certifikat för signering av meddelanden. Om du inte har ett certifikat kan du läsa mer om [certifikat läggs till](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Aktivera meddelandekryptering** | Nej | Anger om alla utgående meddelanden måste vara krypterat. Om du kräver kryptering, väljer du de här värdena: <p>-Från den **krypteringsalgoritm** väljer algoritmen gäst partner offentligt certifikat ska användas för kryptering av meddelanden. <br>-Från den **certifikat** väljer du ett befintligt gäst partner privata certifikat för kryptering av utgående meddelanden. Om du inte har ett certifikat kan du läsa mer om [certifikat läggs till](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Aktivera meddelandekomprimering** | Nej | Anger om alla utgående meddelanden måste komprimeras. |
| **Vik ut HTTP-huvuden** | Nej | Placerar HTTP `content-type` rubriken till en enda rad. |
| **Överföra filnamn i MIME-huvud** | Nej | Anger om du vill inkludera namnet på filen i MIME-huvudet. |
| **Begär MDN** | Nej | Anger om du vill få meddelanden disposition (mdn måste specificeras) för alla utgående meddelanden. |
| **Begär signerad MDN** | Nej | Anger om du vill få signerad mdn måste specificeras för alla utgående meddelanden. Om du behöver registrera, från den **MIC-algoritm** väljer du algoritmen som ska användas för att signera meddelanden. |
| **Begär asynkron MDN** | Nej | Anger om du vill få mdn måste specificeras asynkront. Om du väljer asynkron mdn måste specificeras i den **URL** skriver du URL: en för att skicka de mdn måste specificeras. |
| **Aktivera NRR** | Nej | Anger om icke-repudiation kvitto (NRR). Det här attributet kommunikation ger bevis som data togs emot som åtgärdas. |
| **SHA2-algoritmformat** | Nej | Anger formatet för MIC-algoritmen som används för att logga in rubriker för AS2-meddelanden eller MDN |
||||

## <a name="next-steps"></a>Nästa steg

[Utbyta AS2-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2.md)
