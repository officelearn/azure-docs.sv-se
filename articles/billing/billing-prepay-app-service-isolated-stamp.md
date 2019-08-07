---
title: Förskotts betalning för Azure App Service isolerad stämpel med reserverad kapacitet
description: Lär dig hur du kan förskottsbetala Azure App Service isolerad stämpel avgift med reserverad kapacitet för att spara pengar.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: f122ec2474c09cdd6c9ada4ddc59b1adb44f619f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780005"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Förskotts betalning för Azure App Service isolerad stämpel med reserverad kapacitet

Du kan spara pengar på Azure App Service isolerade stämpla avgifter genom att betala för stämplings användningen under en varaktighet på tre år. Om du vill köpa reserverad kapacitet för isolerad stämpel måste du välja den Azure-region där stämpeln ska distribueras och antalet stämplar som ska köpas.

När du köper en reservation debiteras inte längre den isolerade stämplings avgiften som matchar reservations attributen enligt priserna för betala per användning. Reservationen tillämpas automatiskt på antalet isolerade stämplar som matchar omfattning och region för reserverad kapacitet. Du behöver inte tilldela någon reservation till en isolerad stämpel. Reservationen gäller inte för arbetare, så andra resurser som är kopplade till stämpeln debiteras separat.

När den reserverade kapaciteten går ut fortsätter de isolerade stämplarna att köras, men de debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt.

## <a name="determine-the-right-reservation-to-purchase"></a>Fastställ rätt reservation att köpa

Genom att köpa en reservation är du förbetald på att använda reserverade kvantiteter hela tiden under de närmaste tre åren. Kontrol lera användnings data för att ta reda på hur många App Service Isolerad stämplar du använder och hur du kan använda dem i framtiden.

Se dessutom till att du förstår hur den isolerade stämpeln genererar Linux-eller Windows-mätare.

- Som standard genererar en tom isolerad stämpel Windows Stamp-mätare. Till exempel utan att några medarbetare har distribuerats. Den fortsätter att generera den här mätaren om Windows-arbetarna distribueras på stämpeln.
- Mätaren ändras till Linux-stämpelns mätare om du distribuerar en Linux-arbetsprocess.
- I de fall där både Linux-och Windows-arbetare distribueras, genererar stämpel Windows-mätaren.

Dessutom kan stämplings mätaren ändras mellan Windows och Linux under stämpelns livs längd.

Köp Windows-tidsstämplar om du har en eller flera Windows-arbetare på stämpeln. Den enda gången du köper en Linux-stämpel är om du planerar att _bara_ ha Linux-arbetare på stämpeln.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Köp isolerad stämpel reserverad kapacitet

Du kan köpa isolerad reserverad kapacitet i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). För att köpa reserverad kapacitet måste du ha ägar rollen för minst en företags prenumeration eller en enskild prenumeration med priser enligt principen betala per användning.

- För företags prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör.
- För Cloud Solution Provider (CSP)-programmet kan endast administratörs agenter eller försäljnings agenter köpa SQL Data Warehouse reserverad kapacitet.

**Köp:**

1. Gå till [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Välj en prenumeration. Använd **prenumerations** listan för att välja den prenumeration som används för att betala för den reserverade kapaciteten. Betalnings metoden för prenumerationen debiteras de direkta kostnaderna för den reserverade kapaciteten. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller "betala per användning" (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P) eller en CSP-prenumeration.
    - För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning.
    - Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.
1. Välj ett **omfång** för att välja ett prenumerations omfång.
    - **Definitions område för enskild resurs grupp** – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.
    - **Enda prenumerations omfång** – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.
    - **Delat omfång** – tillämpar reservations rabatten på matchande resurser i berättigade prenumerationer som finns i fakturerings kontexten. För Enterprise-avtal kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med priser enligt principen betala per användning är fakturerings omfånget alla berättigade prenumerationer som skapats av konto administratören.
1. Välj en **region** för att välja en Azure-region som omfattas av den reserverade kapaciteten och Lägg till reservationen i vagnen.
1. Välj en isolerad plan typ och klicka sedan på **Välj**.  
    ![Exempel](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Ange antalet App Service Isolerad stämplar som ska reserveras. En kvantitet av tre skulle till exempel ge dig tre reserverade stämplar en region. Klicka på **Nästa: Granska och Köp**.
1. Granska och klicka på **Köp nu**.

Efter köpet går du till [reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) för att visa inköps status och övervakar det när som helst.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut eller återbetala reservationer med vissa begränsningar. Mer information finns i självbetjänings [utbyten och åter betalningar för Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>Rabatt program som visas i användnings data

Dina användnings data har ett effektivt pris på noll för användningen som erhåller en reservations rabatt. Användnings informationen visar reservations rabatten för varje Stamp-instans i varje reservation.

Mer information om hur reservations rabatt visas i användnings data finns i [hämta Enterprise-avtal reservations kostnader och användning](billing-understand-reserved-instance-usage-ea.md) om du är en Enterprise-avtal EA-kund. I annat fall kan du [förstå Azure reservation-användning för din enskilda prenumeration med priser enligt principen betala per](billing-understand-reserved-instance-usage.md)användning.

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Reservations finns i följande artiklar:
  - [Vad är Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Förstå hur en Azure App Service isolerad stämpel reservations rabatt tillämpas](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)
