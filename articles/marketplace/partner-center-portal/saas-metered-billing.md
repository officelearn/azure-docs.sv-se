---
title: Fakturering med datapriser med hjälp av marknadsplatsmätningstjänsten | Azure Marketplace
description: Den här dokumentationen är en guide för ISV:er som publicerar SaaS-erbjudanden med flexibla faktureringsmodeller.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281331"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Fakturering med datapriser med hjälp av marknadsplatsmätningstjänsten

Med Marketplace-mätartjänsten kan du skapa SaaS-erbjudanden (Software-as-a-Service) i det kommersiella marknadsplatsprogrammet som debiteras enligt icke-standardiserade enheter.  Innan du publicerar det här erbjudandet definierar du faktureringsdimensioner som bandbredd, biljetter eller e-postmeddelanden som bearbetas.  Kunderna betalar sedan enligt sin förbrukning av dessa dimensioner, med ditt system informera Microsoft via Marketplace mätning tjänsten API av fakturerbara händelser när de inträffar.  

## <a name="prerequisites-for-metered-billing"></a>Förutsättningar för fakturering med datapriser

För att ett SaaS-erbjudande ska kunna använda fakturering med datapriser måste det:

* Uppfylla alla erbjudandekrav för en [försäljning via Microsoft-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) som beskrivs i [Skapa ett SaaS-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integrera med [SaaS Uppfyllelse API:er](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) för kunder att etablera och ansluta till ditt erbjudande.  
* Konfigureras för **schablonprismodellen** för att debitera kunder för din tjänst.  Dimensioner är ett valfritt tillägg till schablonprisprismodellen. 
* Integrera med [Marketplace-tjänst-API:erna](./marketplace-metering-service-apis.md) för att informera Microsoft om fakturerbara händelser.

>[!Note]
>Marketplace-mätartjänst är endast tillgänglig för faktureringsmodellen med fast pris och gäller inte faktureringsmodellen per användare.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Så passar fakturering med datapriser in i prissättningen

När det gäller att definiera erbjudandet tillsammans med sina prissättningsmodeller är det viktigt att förstå erbjudandehierarkin.

* Varje SaaS-erbjudande är konfigurerat för att sälja antingen via Microsoft eller inte.  Den här inställningen kan inte ändras när ett erbjudande har publicerats.
* Varje SaaS-erbjudande, konfigurerat för att sälja via Microsoft, kan ha en eller flera planer. En användare prenumererar på SaaS-erbjudandet, men det köps via Microsoft inom ramen för en plan.
* Varje plan har en prismodell som är associerad med den: **schablonbelopp** eller **per användare.** Alla planer i ett erbjudande måste associeras med samma prismodell. Det kan till exempel inte finnas ett erbjudande där en av dess planer är schablonprisprismodell och en annan är per användarprismodell.
* Inom varje plan som konfigurerats för en faktureringsmodell med fast pris ingår minst en återkommande avgift (som kan vara $0):
    * Återkommande **månadsavgift:** fast månadsavgift som är förbetald vid en månatlig upprepning när användaren köper planen.
    * Återkommande **årsavgift:** fast årsavgift som är förbetald vid årlig återkommande när användaren köper planen.
* Utöver de återkommande avgifterna kan planen även innehålla valfria dimensioner som används för att debitera kunder för användning som inte ingår i schablonpriset.   Varje dimension representerar en fakturerbar enhet som din tjänst kommer att kommunicera till Microsoft med hjälp av [Marketplace-mätartjänstens API](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Exempel erbjudande

Contoso är till exempel en utgivare med en SaaS-tjänst som heter Contoso Notification Services (CNS). CNS tillåter kunder att skicka meddelanden antingen via e-post eller text. Contoso är registrerat som utgivare i Partner Center för det kommersiella marketplace-programmet för att publicera erbjudanden till Azure-kunder.  Det finns två planer i samband med CNS, som beskrivs nedan:

* Basplan
    * Skicka 10000 e-post och 1000 texter för $ 0/month
    * Utöver de 10000 e-postmeddelanden, betala $ 1 för varje 100 e-postmeddelanden
    * Utöver de 1000 texter, betala $ 0,02 för varje text
* Premiumplan
    * Skicka 50000 e-post och 10000 texter för $ 350/month
    * Utöver de 50000 e-postmeddelanden, betala $ 0,5 för varje 100 e-postmeddelanden
    * Utöver de 10000 texter, betala $ 0,01 för varje text

En Azure-kund som prenumererar på CNS-tjänsten kan skicka den inkluderade mängden text och e-postmeddelanden per månad baserat på den valda planen.  Contoso mäter användningen upp till den inkluderade kvantiteten utan att skicka några användningshändelser till Microsoft.  När kunderna förbrukar mer än den inkluderade kvantiteten behöver de inte ändra planer eller göra något annorlunda.  Contoso mäter övertaget utöver den inkluderade kvantiteten och börjar sända ut användningshändelser till Microsoft för ytterligare användning med hjälp av [Marketplace-mätartjänstens API](./marketplace-metering-service-apis.md).  Microsoft debiterar i sin tur kunden för den ytterligare användning som anges av utgivaren.

## <a name="billing-dimensions"></a>Faktureringsdimensioner

Faktureringsdimensioner används för att kommunicera med kunden om hur de ska faktureras för att använda programvaran och även för att kommunicera användningshändelser till Microsoft. De definieras på följande sätt:

* **Dimensionsidentifierare:** den oföränderliga identifierare som refereras när användningshändelser avges.
* **Dimensionsnamn:** det visningsnamn som är associerat med dimensionen, t.ex.
* **Måttenhet**: Beskrivning av faktureringsenheten, t.ex.
* **Pris per enhet:** priset för en enhet av dimensionen.  
* **Inkluderad kvantitet för månadsperiod:** kvantitet av dimension som ingår per månad för kunder som betalar den återkommande månadsavgiften, måste vara ett heltal.
* **Inkluderad kvantitet för årsperiod:** dimensionskvantitet som ingår per månad för kunder som betalar den återkommande årsavgiften måste vara ett heltal.

Faktureringsdimensioner delas mellan alla planer för ett erbjudande.  Vissa attribut gäller för dimensionen i alla planer och andra attribut är planspecifika.

De attribut som definierar själva dimensionen delas mellan alla planer för ett erbjudande.  Innan du publicerar erbjudandet påverkar en ändring av dessa attribut från kontexten för en plan dimensionsdefinitionen i alla planer.  När du har publicerat erbjudandet kan dessa attribut inte längre redigeras.  Dessa attribut är:

* Identifierare
* Namn
* Måttenhet

De andra attributen för en dimension är specifika för varje plan och kan ha olika värden från plan till plan.  Innan du publicerar planen kan du redigera dessa värden och endast den här planen påverkas.  När du har publicerat planen kan dessa attribut inte längre redigeras.  Dessa attribut är:

* Pris per enhet
* Inkluderad kvantitet för månadskunder 
* Inkluderad kvantitet för årskunder 

Måtten har också två speciella begrepp, "aktiverade" och "oändliga":

* **Aktiverad** anger att den här planen deltar i den här dimensionen.  Du kanske vill lämna det här okontrollerat om du skapar en ny plan som inte skickar användningshändelser baserat på den här dimensionen.  Dessutom visas alla nya dimensioner som läggs till efter att en plan först publicerades som "inte aktiverad" på den redan publicerade planen.  En inaktiverad dimension visas inte i några listor med dimensioner för en plan som visas av kunder.
* **Oändlig**, representerad av oändlighetssymbolen "∞", anger att den här planen deltar i den här dimensionen, men mäter inte användningen mot den här dimensionen.  Om du vill ange för dina kunder att de funktioner som representeras av den här dimensionen ingår i planen, men utan någon begränsning för användning.  En dimension med oändlig användning visas i listor med dimensioner för en plan som kunderna ser, med en indikation på att den aldrig kommer att debiteras för den här planen.

>[!Note] 
>Följande scenarier stöds uttryckligen: <br> - Du kan lägga till en ny dimension i en ny plan.  Den nya dimensionen kommer inte att aktiveras för några redan publicerade planer. <br> - Du kan publicera en **platt plan** utan några dimensioner, sedan lägga till en ny plan och konfigurera en ny dimension för den planen. Den nya dimensionen kommer inte att aktiveras för redan publicerade planer.

## <a name="constraints"></a>Villkor

### <a name="trial-behavior"></a>Trial beteende

Fakturering med datapriser med marketplace-mättjänsten är inte kompatibel med att erbjuda en kostnadsfri utvärderingsversion.  Det går inte att konfigurera en plan för att använda både fakturering med datapriser och en kostnadsfri utvärderingsversion.

### <a name="locking-behavior"></a>Låsbeteende

Eftersom en dimension som används med marketplace-avläsningstjänsten representerar en förståelse för hur en kund ska betala för tjänsten, kan inte längre all information för en dimension redigeras när du publicerar den.  Det är viktigt att du har definierat dimensionerna helt för en plan innan du publicerar.
  
När ett erbjudande har publicerats med en dimension kan information på erbjudandenivå för den dimensionen inte längre ändras:

* Identifierare
* Namn
* Måttenhet

När en plan har publicerats kan information på plannivå inte längre ändras:

* Pris per enhet
* Inkluderad kvantitet för månadsperiod
* Inkluderad kvantitet för årstid
* Om dimensionen är aktiverad för planen

### <a name="upper-limits"></a>Övre gränser

Det maximala antalet dimensioner som kan konfigureras för ett enda erbjudande är 18 unika dimensioner.

## <a name="get-support"></a>Få support

Om du har något av följande kan du öppna en supportbiljett.

* Tekniska problem med marketplace-tjänst-API.
* Ett problem som måste eskaleras på grund av ett fel eller fel på din sida (t.ex. fel användningshändelse).
* Andra frågor som rör fakturering med datapriser. 

Följ stegen nedan för att skicka in din supportbiljett:

1. Gå till [supportsidan](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). De första rullgardinsmenyerna fylls automatiskt i för dig. För Marketplace-support identifierar du produktfamiljen som **Moln- och onlinetjänster**, produkten som **Marketplace Publisher**.  Ändra inte de förifyllda rullgardinsmenyns val.
2. Under "Välj produktversionen" väljer du **Live-erbjudandehantering**.
3. Välj **SaaS-appar**under "Välj en kategori som bäst beskriver problemet".
4. Välj ett problem som bäst beskriver problemet **under**"Välj ett problem som bäst beskriver problemet".
5. Genom att välja knappen **Nästa** dirigeras du till sidan **Probleminformation,** där du kan ange mer information om ditt problem.

Mer information finns [i Support för det kommersiella marketplace-programmet i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) för fler supportalternativ för utgivare.

## <a name="next-steps"></a>Nästa steg

- Mer information [finns i API:er](./marketplace-metering-service-apis.md) för tjänsten Marketplace för mer information.
