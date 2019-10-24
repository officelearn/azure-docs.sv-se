---
title: Kontrol lera publicerings statusen för ditt kommersiella Marketplace-erbjudande
description: Kontrol lera status för de verifierings-, certifierings-och förhands gransknings steg som krävs för att publicera ett erbjudande via den kommersiella Marketplace i Microsoft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 84e8f1d7b723bb86b31abb1ff60bce5351d7d996
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750145"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Kontrol lera publicerings statusen för ditt kommersiella Marketplace-erbjudande

Du kan visa din aktuella **publicerings status** på fliken **erbjudande översikt** i den [kommersiella Marketplace-portalen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center.

En av följande status indikatorer ska visas för varje erbjudande.

| **Status**    | **Beskrivning**  |
| :---------- | :-------------------|
| **Föreslagna** | Erbjudandet har skapats men det har inte publicerats. |
| **Publicering pågår** | Erbjudandet/planen fungerar på ett sätt genom stegen i publicerings processen. |
| **Åtgärd krävs** | Ett kritiskt problem upptäcktes under certifieringen av Microsoft eller något av publicerings stegen. |
| **Förhandsversion** | Erbjudandet certifierades av Microsoft och väntar nu på en slutgiltig verifiering av utgivaren. Välj Go Live för att göra erbjudandet Live. |
| **Realtidsinformation** | Erbjudandet är Live på Marketplace och kan ses och förvärvas av kunder. |
| **Väntande stoppa försäljning** | Utgivare valde "stoppa försäljning" på erbjudandet eller planen, men åtgärden har ännu inte slutförts. |
| **Inte tillgängligt i Marketplace** | Ett tidigare publicerat erbjudande/plan i Marketplace har tagits bort. |

## <a name="automated-validation"></a>Automatiserad verifiering

Det första steget i publicerings processen är en uppsättning automatiserade verifieringar. Varje validerings steg motsvarar en funktion som du valde att aktivera när du skapar erbjudandet. Om funktionen inte har Aktiver ATS hoppar valideringen framåt till nästa publicerings steg. Varje verifierings kontroll måste slutföras innan publicerings statusen godkänns.

- **Konfigurera konfiguration av inköps flöde (< 10 min)**

I det här steget ser vi till att ditt erbjudande kan uppfyllas när kunderna köpts via Azure Portal. Det här steget gäller bara för erbjudanden som säljs via Microsoft.

- **Test enhet för data verifiering (~ 5 min)**

I det här steget validerar vi de data du angav i avsnittet teknisk konfiguration för test enhet i erbjudandet. Testen hets funktioner testas och godkänns. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Test av enhets etablering (~ 30 min)**

I det här steget kan vi när du har verifierat data och funktioner på test enheten i föregående steg distribuera och replikera instanser av test enheten så att de är redo för kund användning.  Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Verifiering och registrering av lead-hantering (< 15 min)**

I det här steget bekräftar vi att ditt lead-hanterings system kan ta emot kund leads baserat på den information som finns i installations programmet för erbjudandet. Det här steget gäller bara för erbjudanden där lead management har Aktiver ATS.

## <a name="certification"></a>Certifiering

Innan erbjudanden skickas till den kommersiella Marketplace i Partner Center måste vara certifierade. Skickade erbjudanden genomgår rigorös testning, vissa automatiserade och andra manuella, inklusive en kontroll av [certifierings principerna för Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Erbjudanden för erbjudanden måste markeras som kvalificerade för certifiering innan de fortsätter till nästa steg i publicerings flödet.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typer av verifieringar som sker under certifieringen

Det finns tre nivåer av verifiering som ingår i certifierings processen för varje erbjudande som skickas.

- Affärs berättigande för utgivare
- Innehålls validering
- Teknisk validering

#### <a name="publisher-business-eligibility"></a>Affärs berättigande för utgivare

Varje erbjudande typ kontrollerar en uppsättning grundläggande berättiganderegler som utgivaren måste uppfylla. Kriterier för berättigande kan omfatta utgivarens MPN status, kompetenser, kompetens nivåer osv.

#### <a name="content-validation"></a>Innehålls validering

Under innehålls valideringen kontrol leras informationen som angavs när du skapade erbjudandet för kvalitet och relevans. Dessa kontroller granskar dina poster för information om Marketplace-listor, prissättning, tillgänglighet, associerade planer osv. För att uppfylla villkoren för Azure Marketplace och/eller AppSource, kommer vi att validera att erbjudandet innehåller:

- en rubrik som korrekt beskriver erbjudandet.
- välskrivna beskrivningar som ger en grundlig översikt och ett värde förslag.
- kvalitets skärm bilder och medföljande videor särskilt
- en förklaring av hur erbjudandet använder Microsoft-plattformar och-verktyg.

Läs mer om kriterierna för innehålls validering genom att läsa [allmänna registrerings principer](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Teknisk validering

Under teknisk validering genomgår erbjudandet (paketet eller den binära) följande kontroller.
- Genomsökt efter skadlig kod
- Övervakade nätverks anrop
- Paketet har analyser ATS
- Grundlig genomsökning av erbjudandets faktiska funktion

Erbjudandet testas på olika plattformar och versioner för att säkerställa att det är robust.

Granska den konfigurations information som krävs för ditt erbjudande i avsnittet teknisk konfiguration i det här dokumentet.

### <a name="certification-failure-report"></a>Rapport över certifierings problem

När granskningen är klar flyttas den till nästa steg i publicerings processen, om ditt erbjudande har klarat certifieringen. Om ditt erbjudande har misslyckats med att utföra någon av listorna för registrering, teknik eller princip, eller om du inte är berättigad till att skicka ett erbjudande av den typen, genereras en rapport över fel rapporter och skickas till dig via e-post.

Den här rapporten innehåller beskrivningar av eventuella principer som misslyckats, tillsammans med gransknings anteckningar. Läs den här e-postrapporten, åtgärda eventuella problem, gör uppdateringar till ditt erbjudande vid behov och skicka erbjudandet på nytt med hjälp av den [kommersiella Marketplace-portalen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center. (Du kan skicka in erbjudandet igen så många gånger som behövs tills du skickar certifieringen).

## <a name="preview-creation"></a>Skapa för hands version

Under steget för att skapa för **hands** versioner skapar vi en version av ditt erbjudande som är tillgängligt för den mål grupp som du angav i förhands gransknings avsnittet i ditt erbjudande.

>[!Note]
> Använd inte det här steget för att ge personer utanför organisationen insyn i ett erbjudande. Använd alternativet för **privat erbjudande** i stället. I det här läget har ditt erbjudande inte testats fullständigt och godkänts och är inte redo för distribution utanför företaget.

## <a name="publisher-approval"></a>Utgivarens godkännande

I det här steget får du ett e-postmeddelande med en förfrågan om att granska och godkänna din för hands version av erbjudandet innan det sista publicerings steget.

Om du har valt att sälja ditt erbjudande via Microsoft, kan du testa förvärvet och distributionen av ditt erbjudande för att säkerställa att det uppfyller dina krav under för hands godkännande steget. Ditt erbjudande är ännu inte tillgängligt i den offentliga marknads platsen. När du har testat och godkänt den här för hands versionen måste du välja **Go-Live** på instrument panelen för [**erbjudande översikt**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

Om du vill göra ändringar i erbjudandet under förhands gransknings fasen kan du redigera och skicka om för att publicera en ny för hands version. Se artikeln [Uppdatera befintliga Marketplace-erbjudanden](#update-existing-marketplace-offers) för mer information om fler ändringar.

Om ditt erbjudande redan är aktivt och tillgängligt för allmänheten i Marketplace, kommer alla uppdateringar du gör inte att vara aktiva förrän du väljer **Go-Live** på instrument panelen för [**erbjudande översikt**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

### <a name="publish-offer-to-the-public"></a>Publicera erbjudande till allmänheten

Logga in på Partner Center och få åtkomst till erbjudandet. Du kommer att omdirigeras till sidan **erbjudande översikt** . Överst på sidan visas ett alternativ för **Go Live**. Välj **Go Live** och efter att du har bekräftat börjar Erbjudandet bli publicerat till allmänheten. Du får ett e-postmeddelande när erbjudandet är Live.

## <a name="publish"></a>Publicera

Nu när du har valt att **sätta igång** med ditt erbjudande, vilket gör det tillgängligt i Marketplace, finns det en serie slutliga verifierings kontroller som kommer att stegas igenom för att säkerställa att Live-erbjudandet är konfigurerat precis som för hands versionen av erbjudandet.

- **Konfigurera konfiguration av inköps flöde (> 10 min)**

I det här steget ser vi till att ditt erbjudande kan uppfyllas när kunderna köpts via Azure Portal. Det här steget gäller bara för erbjudanden som säljs via Microsoft.

- **Test enhet för data verifiering (~ 5 min)**

I det här steget validerar vi de data du angav i avsnittet teknisk konfiguration för test enhet i erbjudandet. Testen hets funktioner testas och godkänns. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Test av enhets etablering (~ 30 min)**

I det här steget distribuerar och replikerar vi instanser av test enheten så att de är redo för kund användning.  Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Verifiering och registrering av lead-hantering (> 15 min)**

I det här steget bekräftar vi att ditt lead-hanterings system kan ta emot kund leads baserat på den information som finns i installations programmet för erbjudandet. Det här steget gäller bara för erbjudanden där lead management har Aktiver ATS.

- **Slutgiltig publicering (> 30 minuter)**

I det här steget ser vi till att erbjudandet blir offentligt tillgängligt på Marketplace.

## <a name="update-existing-marketplace-offers"></a>Uppdatera befintliga Marketplace-erbjudanden

Om du vill göra ändringar i ett erbjudande som du redan har publicerat måste du först uppdatera det befintliga erbjudandet och sedan publicera det igen.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](./update-existing-offer.md)
