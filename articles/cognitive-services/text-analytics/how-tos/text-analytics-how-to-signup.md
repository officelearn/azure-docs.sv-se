---
title: Registrera dig för API för textanalys
titleSuffix: Azure Cognitive Services
description: Anvisningar för att registrera sig och använda Text Analytics-tjänsten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829675"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Hur du registrerar dig för API för textanalys

Textanalysresurser är tillgängliga dygnet runt i molnet. Innan du kan ladda upp ditt innehåll för analys måste du registrera dig för att hämta en åtkomstnyckel. Varje anrop till API:et kräver en åtkomstnyckel för begäran.

+ Börja med en Azure-prenumeration. Du kan skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) och experimentera utan kostnad.

+ Skapa ett [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) och välj **API för textanalys**. Din nyckel genereras när du registrerar dig.

För Textanalys finns det en kostnadsfri nivå för utforskning och utvärdering, och fakturerbara nivåer för produktionsarbetsbelastningar. Du kan ha flera registreringar i varje prenumeration: en kostnadsfri, en betald, och så vidare. Du kan växla till en nivå som erbjuder fler transaktioner om din begärandevolym ökar.

Det finns inget serviceavtal för tjänster i förhandsversion eller för den kostnadsfria nivån. Mer information finns i [Serviceavtal för Cognitive Services](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>Så ändrar du nivå

Börja med en kostnadsfri nivå och övergå sedan till en faktureringsbar nivå för produktionsarbetsbelastningar. Standarddebitering erbjuds på graderade nivåer. Du kan växla nivåer och ändå behålla samma slutpunkt och åtkomstnycklar.

1. Logga in på [Azure-portalen](https://portal.azure.com) och [hitta din tjänst](text-analytics-how-to-access-key.md).

2. Klicka på **Prisnivå**.

   ![Prisnivåkommando på den vänstra navigeringsmenyn](../media/portal-pricing-tier.png)

3. Välj en nivå och klicka på **Välj**.  De nya gränserna börjar gälla så snart valet har bearbetats. 

   ![Paneler och knappen Välj på sidan för val av nivå](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Så fungerar debiteringen

Debiteringen baseras på antalet transaktioner. Du kan köpa ett block med transaktioner på en specifik nivå i en månatlig faktureringsperiod, och om du sedan går över tillkommer en liten debitering för överanvändning per transaktion. Om du rutinmässigt överskrider den maximala gränsen bör du överväga att växla till en högre nivå.

Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Vad består en transaktion av i API:et för textanalys?
En kommentar till ett dokument räknas som en transaktion. Batch-poängsättningsanrop tar även hänsyn till antalet dokument som ska poängsättas i transaktionen. Så om till exempel 1 000 dokument skickas för sentimentanalys i ett enda API-anrop räknas det som 1 000 transaktioner.

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en åtkomstnyckel](text-analytics-how-to-access-key.md)
