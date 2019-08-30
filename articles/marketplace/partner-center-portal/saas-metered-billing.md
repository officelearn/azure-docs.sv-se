---
title: Avgiftsbelagd fakturering med Marketplace-avläsning | Azure Marketplace
description: 'Den här dokumentationen är en guide för ISV: er som publicerar SaaS-erbjudanden med flexibla fakturerings modeller.'
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 3fa485c9fb2835b8270cb35fc75b57251476005f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141778"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Avgiftsbelagd fakturering med Marketplace-avläsning

Med Marketplace-mätnings tjänsten kan du skapa SaaS-erbjudanden (Software-as-a-Service) i det kommersiella Marketplace-programmet som debiteras enligt andra enheter än standard enheter.  Innan du publicerar det här erbjudandet definierar du de fakturerings dimensioner som bandbredd, biljetter eller e-postmeddelanden som bearbetas.  Kunderna betalar sedan efter deras användning av dessa dimensioner, med ditt system informerar Microsoft via API: et för avgiftsbelagda avläsning av fakturerbara händelser när de inträffar.  

## <a name="prerequisites-for-metered-billing"></a>Krav för fakturering av mätare

För ett SaaS-erbjudande för att använda avgiftsbelagda fakturering måste det:

* Uppfyll alla erbjudande krav för en [försäljning via Microsoft-erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) som beskrivs i [skapa ett SaaS-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integrera med [API: er för SaaS-utförande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) för kunder för att etablera och ansluta till ditt erbjudande.  
* Konfigureras för pris sättnings modellen med **fast** pris för att debitera kunder för din tjänst.  Dimensioner är ett valfritt tillägg till pris sättnings modellen för fasta priser. 
* Integrera med [API: erna för Marketplace för avläsning](./marketplace-metering-service-apis.md) av program vara för att informera Microsoft om fakturerbara händelser.

>[!Note]
>Marketplace för avläsning av program vara är bara tillgänglig för fakturerings modellen för fasta priser och gäller inte för fakturerings modellen per användare.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hur avgiftsbelagd fakturering passar in med priser

När det kommer att definiera erbjudandet tillsammans med pris sättnings modellerna är det viktigt att förstå erbjudandet.

* Varje SaaS-erbjudande är konfigurerat för att sälja antingen via Microsoft eller inte.  Den här inställningen kan inte ändras efter att ett erbjudande har publicerats.
* Varje SaaS-erbjudande, konfigurerat att sälja via Microsoft, kan ha en eller flera planer. En användare prenumererar på SaaS-erbjudandet, men det köps via Microsoft inom ramen för en plan.
* Varje plan har en pris modell kopplad till sig: **fast** pris eller **per användare**. Alla planer i ett erbjudande måste vara kopplade till samma pris modell. Det kan till exempel inte finnas något erbjudande där en av dess planer är en fast pris modell och en annan pris modell per användare.
* I varje plan som kon figurer ATS för en fast fakturerings modell ingår minst en återkommande avgift (som kan vara $0):
    * Återkommande **månads** avgift: fast månads avgift som är förskottsbetald varje månad när användaren köper planen.
    * Återkommande **årlig** avgift: fast årlig avgift som betalas för varje år när användaren köper planen.
* Utöver de återkommande avgifterna kan planen även omfatta valfria dimensioner som används för att debitera kunder för användning som inte ingår i den fasta taxan.   Varje dimension representerar en fakturerbar enhet som din tjänst kommer att kommunicera med Microsoft via [API: t för avläsning av marknads](./marketplace-metering-service-apis.md)plats.

## <a name="sample-offer"></a>Exempel erbjudande

Contoso är till exempel en utgivare med en SaaS-tjänst som heter Contoso Notification Services (CNS). CNS gör det möjligt för kunder att skicka meddelanden via e-post eller text. Contoso registreras som en utgivare i Partner Center för att publicera erbjudanden till Azure-kunder.  Det finns två planer kopplade till CNS, som beskrivs nedan:

* Bas plan
    * Skicka 10000 e-postmeddelanden och 1000 texter i $0/månad
    * Utöver 10000 e-postmeddelanden betalar du $1 för varje 100 e-post
    * Utöver 1000-texterna betalar du $0,02 för varje text
* Premiumplan
    * Skicka 50000 e-postmeddelanden och 10000 texter i $350/månad
    * Utöver 50000 e-postmeddelanden betalar du $0,5 för varje 100 e-post
    * Utöver 10000-texterna betalar du $0,01 för varje text

En Azure-kund som prenumererar på CNS-tjänsten kommer att kunna skicka den inkluderade mängden text och e-postmeddelanden per månad baserat på den valda planen.  När kunderna förbrukar mer än den mängd som ingår behöver de inte ändra några planer eller göra något annat.  Contoso mäter överförbrukningen utöver den mängd som ingår och startar sändning av användnings händelser till Microsoft för ytterligare användning med [API: et för avläsning](./marketplace-metering-service-apis.md)av program vara i Marketplace.  Microsoft kommer att debitera kunden för den ytterligare användningen som anges av utgivaren.

## <a name="billing-dimensions"></a>Fakturerings dimensioner

Fakturerings dimensioner används för att kommunicera med kunden om hur de kommer att faktureras för användning av program varan och även för att kommunicera användnings händelser till Microsoft. De definieras enligt följande:

* **Dimensions-ID**: den oåterkalleliga identifierare som refereras vid sändning av användnings händelser.
* **Dimensions namn**: det visnings namn som är associerat med dimensionen, t. ex. "textmeddelanden har skickats".
* Mått **enhet**: en beskrivning av fakturerings enheten, t. ex. "per textmeddelande" eller "per 100-e-post".
* **Pris per enhet**: priset för en dimensions enhet.  
* **Inkluderad kvantitet för månatlig period**: den mängd dimension som ingår per månad för kunder som betalar den återkommande månads avgiften måste vara ett heltal.
* **Inkluderad kvantitet för årlig period**: den mängd dimension som ingår per månad för kunder som betalar den återkommande årliga avgiften måste vara ett heltal.

Fakturerings dimensioner delas i alla planer för ett erbjudande.  Vissa attribut gäller för dimensionen i alla planer, och andra attribut är plan-/regionsspecifika.

De attribut som definierar själva dimensionen delas i alla planer för ett erbjudande.  Innan du publicerar erbjudandet påverkar en ändring av dessa attribut från kontexten för varje plan dimensions definitionen för alla planer.  När du har publicerat erbjudandet kan dessa attribut inte längre redige ras.  Dessa attribut är:

* identifierare
* Name
* Måttenhet

De andra attributen för en dimension är speciella för varje plan och kan ha olika värden för att planera.  Innan du publicerar planen kan du redigera dessa värden och endast den här planen kommer att påverkas.  När du publicerar planen går dessa attribut inte längre att redige ras.  Dessa attribut är:

* Pris per enhet
* Inkluderad kvantitet för månads kunder 
* Inkluderad kvantitet för års kunder 

Dimensioner har också två särskilda begrepp, "aktiverade" och "oändliga":

* **Aktiverat** anger att den här planen ingår i den här dimensionen.  Du kanske vill lämna detta omarkerat om du skapar en ny plan som inte skickar användnings händelser baserat på den här dimensionen.  Dessutom visas nya dimensioner som lagts till efter att en plan först publicerats som "inte aktive rad" i den redan publicerade planen.  En inaktive rad dimension visas inte i några listor med dimensioner för en plan som visas av kunderna.
* **Oändligt**, representerat av oändlighets symbolen "∞", anger att den här planen ingår i den här dimensionen, men inte mätar användning mot den här dimensionen.  Om du vill ange för dina kunder att de funktioner som representeras av den här dimensionen ingår i planen, men utan någon begränsad användning.  En dimension med oändlig användning visas i listor över dimensioner för en plan som ses av kunder, med en indikation om att den aldrig debiteras för den här planen.

>[!Note] 
>Följande scenarier stöds uttryckligen: <br> – Du kan lägga till en ny dimension till en ny plan.  Den nya dimensionen kommer inte att aktive ras för redan publicerade planer. <br> – Du kan publicera en plan för **fast pris** utan några dimensioner och sedan lägga till en ny plan och konfigurera en ny dimension för planen. Den nya dimensionen kommer inte att aktive ras för redan publicerade planer.

## <a name="constraints"></a>Begränsningar

### <a name="trial-behavior"></a>Utvärderings beteende

Mätning av fakturering med Marketplace-avläsning är inte kompatibel med erbjudande om en kostnads fri utvärderings version.  Det går inte att konfigurera en plan för att använda både avgiftsbelagd fakturering och en kostnads fri utvärderings version.

### <a name="locking-behavior"></a>Låsnings beteende

Eftersom en dimension som används med Marketplace-mätnings tjänsten representerar en förståelse för hur en kund ska betala för tjänsten, är all information om en dimension inte längre redigerbar när du publicerar den.  Det är viktigt att du har definierat dina dimensioner fullständigt för en plan innan du publicerar.
  
När ett erbjudande publiceras med en dimension kan informationen om erbjudande nivå för den dimensionen inte längre ändras:

* identifierare
* Name
* Måttenhet

När en plan har publicerats kan informationen på Plans nivå inte längre ändras:

* Pris per enhet
* Inkluderad kvantitet för månatlig period
* Inkluderad kvantitet för årlig period
* Om dimensionen är aktive rad för planen

### <a name="upper-limits"></a>Övre gränser

Det maximala antalet dimensioner som kan konfigureras för ett enda erbjudande är 18 unika dimensioner.

## <a name="get-support"></a>Få support

Om du har något av följande kan du öppna ett support ärende.

* Tekniska problem med API för avläsning av tjänst i Marketplace.
* Ett problem som måste eskaleras på grund av ett fel eller fel på sidan (t. ex. fel användnings händelse).
* Eventuella andra problem som rör mätning av fakturering. 

Följ stegen nedan för att skicka in ditt support ärende:

1. Gå till [support Sidan](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). De första menyerna fylls i automatiskt. För Marketplace-support identifierar du produkt familjen som **moln-och online tjänster**, produkten som **Marketplace-utgivare**.  Ändra inte List rutorna för förifyllda menyer.
2. Under "Välj produkt version" väljer du **hantering av Live-erbjudandet**.
3. Under "Välj en kategori som bäst beskriver problemet" väljer du **SaaS-appar**.
4. Under "Välj ett problem som bäst beskriver problemet" väljer du **avgiftsbelagd fakturering**.
5. Genom att välja knappen **Nästa** kommer du till sidan **ärende information** , där du kan ange mer information om problemet.

Se [Support för det kommersiella Marketplace-programmet i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) för fler support alternativ för Publisher.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [API: er för API för avläsning av tjänst](./marketplace-metering-service-apis.md) .
