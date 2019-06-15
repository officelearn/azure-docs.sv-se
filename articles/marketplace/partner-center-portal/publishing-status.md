---
title: Kontrollera publiceringsstatus för ditt kommersiella Marketplace-erbjudande
description: Kontrollera status för validering, certifiering och förhandsversion steg som krävs för att publicera ett erbjudande via kommersiella Marketplace i Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474520"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Kontrollera publiceringsstatus för ditt kommersiella Marketplace-erbjudande

Du kan visa din aktuella **Publiceringsstatus** på den **erbjuder översikt** fliken den [kommersiella Marketplace portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) Partner Center.

En av följande status indikatorerna ska visas för varje erbjudande.

| **Status**    | **Beskrivning**  |
| :---------- | :-------------------|
| **Utkast** | Erbjudandet har skapats men publiceras inte. |
| **Publicering pågår** | Erbjudandet /-planen fungerar väg genom stegen för publiceringsprocessen. |
| **Åtgärd krävs** | Ett kritiskt problem har identifierats under certifiering av Microsoft eller någon av de publicera steg. |
| **Förhandsversion** | Erbjudandet har certifierats av Microsoft och nu väntar på dig en slutlig verifiering av utgivaren. Välj Driftsätt att göra erbjudandet live. |
| **Live** | Erbjudandet kan är aktiv i marketplace och ses och har köpt av kunder. |
| **Väntande stoppa säljer** | Utgivare har valt ”stoppa säljer” erbjudandet eller, men åtgärden har inte slutförts ännu. |
| **Inte tillgängligt i marketplace** | En tidigare publicerade erbjudande /-planen i marketplace har tagits bort. |

## <a name="automated-validation"></a>Automatiserad verifiering

Det första steget i publiceringsprocessen är en uppsättning automatiserade verifieringar. Varje validerngssteg motsvarar en funktion som du har valt att aktivera i skapandet av ditt erbjudande. Om funktionen inte har aktiverats, valideringen hoppar över vidare till nästa steg för publicering. Varje verifieringskontroll måste slutföras innan Publiceringsstatus har godkänts.

- **Erbjuda inköp flow installationsprogrammet (< 10: e minut)**

I det här steget ska vi se till att ditt erbjudande kan uppfyllas när de köps av kunder via Azure portal. Det här steget gäller endast för erbjudanden som säljs via Microsoft.

- **Testa enheten dataverifiering (~ 5 min)**

I det här steget ska vi för att validera data som du angav i provkörning teknisk konfigurationsavsnittet i erbjudandet. Test drive funktioner testats och godkänts. Det här steget gäller endast för erbjudanden med en testenhet aktiverat.

- **Testa enheten etablering (cirka 30 min)**

I det här steget när du har validerat data och funktioner i din provkörning i föregående steg, vi distribuera och replikera instanser av din provkörning så att de är klara för användning av kunden.  Det här steget gäller endast för erbjudanden med en testenhet aktiverat.

- **Leda management verifiering och registrering (< 15 min)**

I det här steget bekräftar att din lead-hanteringssystemet kan ta emot kundleads baserat på informationen i erbjudandet-installationen. Det här steget gäller endast för erbjudanden med aktiverad Lead-hantering.

## <a name="certification"></a>Certifiering

Erbjudanden som skickas till kommersiella Marketplace Partner Center måste certifieras innan det publicerades. Skickat erbjudanden genomgå rigorös testning, vissa automatiserade och andra manuell, inklusive en kontroll mot den [principer för Azure Marketplace-certifiering](https://docs.microsoft.com/legal/marketplace/general-policies). Erbjud bidrag måste vara markerad berättigade för certifiering innan de går vidare till nästa steg i flödet publicerar.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typer av verifiering som äger rum under certifikatutfärdare

Det finns tre nivåer av verifiering som ingår i certifieringsprocessen för varje erbjudande som har skickats.

- Utgivaren företag behörighet
- Innehållsvalidering
- Teknisk verifiering

#### <a name="publisher-business-eligibility"></a>Utgivaren företag behörighet

Varje typ av erbjudande kontrollerar en uppsättning grundläggande kriterierna som utgivaren måste uppfylla. Kriterier för berättigande kan omfatta utgivarens MPN status, uppnådda lagras, för att kvalificera nivåer osv.

#### <a name="content-validation"></a>Innehållsvalidering

Vid verifiering av innehåll kontrolleras informationen som anges när du har skapat ditt erbjudande om kvalitet och relevans. De här kontrollerna ska granska posterna för marketplace listinformation, prissättning, tillgänglighet, associerade planer och så vidare. För att uppfylla Azure Marketplace och/eller visa en lista över villkor AppSource, kommer vi att verifiera att ditt erbjudande omfattar:

- en rubrik som beskriver korrekt erbjudandet;
- välskriven beskrivningar som tillhandahåller en omfattande översikt och förslagsvärde;
- kvalitet skärmbilder och medföljande videor; och
- en förklaring av hur erbjudandet använder Microsofts plattformar och verktyg.

Läs mer om content verifieringsvillkoren genom att läsa den [Allmän lista principer](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies).

#### <a name="technical-validation"></a>Teknisk verifiering

Vid verifiering av teknisk görs följande kontroller i erbjudandet (paket eller binär).
- Sökning efter skadlig kod
- Nätverksanrop som övervakas
- Paketet analyseras
- Omfattande genomsökning av erbjudandets faktiska funktioner

Erbjudandet testas över olika plattformar och versioner för att säkerställa att den är robust.

Granska specifik konfigurationsinformation som krävs för ditt erbjudande i avsnittet teknisk konfiguration i det här dokumentet.

### <a name="certification-failure-report"></a>Felrapport för certifiering

Vid slutförandet av granskningen om ditt erbjudande har passerat certifiering flyttas sedan till nästa steg i publiceringsprocessen. Om ditt erbjudande har misslyckats någon lista, technical eller princip kontroll, eller om du inte har behörighet att skicka in ett erbjudande av den typen, genereras en felrapport för certifiering och via e-post till dig.

Den här rapporten innehåller beskrivningar av alla principer som har misslyckats, tillsammans med anteckningar om granskning. Granska den här postrapporten för e-, åtgärda eventuella problem som gör uppdateringar i ditt erbjudande där det behövs och skicka erbjudandet med hjälp av den [kommersiella Marketplace portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) Partner Center. (Du kan skicka erbjudandet så många gånger som behövs förrän skicka certifiering).

## <a name="preview-creation"></a>Skapa en förhandsversion

Under den **Förhandsgranska skapa** steget ska vi skapa en version av ditt erbjudande tillgängligt för den målgrupp som du angav i avsnittet för förhandsversioner i ditt erbjudande.

## <a name="publisher-approval"></a>Publisher-godkännande

I det här steget kommer du att skickas med en begäran om att granska och Godkänn förhandsversionen av erbjudandet innan det sista steget för publicering.

Om du har valt för att sälja ditt erbjudande via Microsoft, kommer du att kunna testa anskaffning och distribution av ditt erbjudande till att säkerställa att den uppfyller dina krav under det här steget för godkännande av förhandsversionen. Ditt erbjudande ännu inte tillgängliga i blygdbenets marketplace. När du testar och godkänner den här förhandsversionen kan du måste välja **Go Live** på den [ **erbjuder översikt** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) instrumentpanelen.

Om du vill göra ändringar i erbjudandet under det här steget i förhandsversion, kan du redigera och skicka igen om du vill publicera en ny förhandsgranskning. Finns i artikeln [uppdatering befintliga marketplace erbjuder](#update-existing-marketplace-offers) mer information om ytterligare ändringar.

Om ditt erbjudande redan är live och är tillgängliga för allmänheten i marketplace, alla uppdateringar som du gör inte Driftsätt förrän du väljer **Go live** på den [ **erbjuder översikt** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) instrumentpanelen.

### <a name="publish-offer-to-the-public"></a>Publicera erbjudande till allmänheten

Logga in på Partner Center och komma åt erbjudandet. Du kommer att omdirigeras till den **erbjuder översikt** sidan. Längst ned på den här sidan ser du ett alternativ för **Driftsätt**. Välj **Driftsätt,** och när du har bekräftat, erbjudandet börjar komma publiceras till allmänheten. Du får ett e-postmeddelande när erbjudandet är live.

## <a name="publish"></a>Publicera

Nu när du har valt att **Driftsätt** ditt erbjudande gör den tillgänglig på marketplace, det är en serie slutlig verifiering kontrollerar att stegvis genom att se till att live erbjudandet konfigureras precis som förhandsversionen version av erbjudandet.

- **Erbjuda inköp flow installationsprogrammet (> 10: e minut)**

I det här steget ska vi se till att ditt erbjudande kan uppfyllas när de köps av kunder via Azure portal. Det här steget gäller endast för erbjudanden som säljs via Microsoft.

- **Testa enheten dataverifiering (~ 5 min)**

I det här steget ska vi för att validera data som du angav i provkörning teknisk konfigurationsavsnittet i erbjudandet. Test drive funktioner testats och godkänts. Det här steget gäller endast för erbjudanden med en testenhet aktiverat.

- **Testa enheten etablering (cirka 30 min)**

I det här steget ska vi distribuera och replikera instanser av din provkörning så att de är klara för användning av kunden.  Det här steget gäller endast för erbjudanden med en testenhet aktiverat.

- **Leda management verifiering och registrering (> 15 min)**

I det här steget bekräftar att din lead-hanteringssystemet kan ta emot kundleads baserat på informationen i erbjudandet-installationen. Det här steget gäller endast för erbjudanden med aktiverad Lead-hantering.

- **Sista publicera (> 30 minuter)**

I det här steget ska vi se till att ditt erbjudande blir offentligt tillgänglig i marketplace.

## <a name="update-existing-marketplace-offers"></a>Uppdatera befintliga marketplace-erbjudanden

Om du vill göra ändringar i ett erbjudande som du redan har publicerat, måste du först uppdatera befintligt erbjudande och sedan publicera den igen.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Marketplace för kommersiella](./update-existing-offer.md)
