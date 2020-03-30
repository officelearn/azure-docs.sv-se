---
title: Kontrollera publiceringsstatusen för ditt Commercial Marketplace-erbjudande
description: Kontrollera statusen för de steg för validering, certifiering och förhandsversionen som krävs för att publicera ett erbjudande via den kommersiella marknadsplatsen i Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275687"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Kontrollera publiceringsstatusen för ditt Commercial Marketplace-erbjudande

Du kan visa din aktuella **publiceringsstatus** på fliken **Erbjudandeöversikt** på [portalen Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partnercenter.

En av följande statusindikatorer bör visas för varje erbjudande.

| **Status**    | **Beskrivning**  |
| :---------- | :-------------------|
| **Utkast** | Erbjudandet har skapats men det publiceras inte. |
| **Publicera pågår** | Erbjudandet/planen arbetar sig igenom stegen i publiceringsprocessen. |
| **Uppmärksamhet behövs** | Ett kritiskt problem upptäcktes under certifieringen av Microsoft eller något av publiceringsstegen. |
| **Förhandsgranska** | Erbjudandet har certifierats av Microsoft och väntar nu på en slutlig verifiering av utgivaren. Välj gå live för att göra erbjudandet live. |
| **Live** | Erbjudandet är live på marknaden och kan ses och förvärvas av kunder. |
| **Väntande sluta sälja** | Publisher valde "stop sell" som erbjuds eller planerar, men åtgärden har ännu inte slutförts. |
| **Inte tillgängligt på marknaden** | Ett tidigare publicerat erbjudande/plan på marknadsplatsen har tagits bort. |

## <a name="automated-validation"></a>Automatiserad validering

Det första steget i publiceringsprocessen är en uppsättning automatiska valideringar. Varje valideringssteg motsvarar en funktion som du har valt att aktivera när du skapar erbjudandet. Om den funktionen inte var aktiverad hoppar valideringen vidare till nästa publiceringssteg. Varje valideringskontroll måste slutföras innan publiceringsstatusen godkänns.

- **Erbjudandet inköp flöde setup (<10 min)**

I det här steget säkerställer vi att ditt erbjudande kan uppfyllas när det köps av kunder via Azure-portalen. Det här steget gäller endast för erbjudanden som säljs via Microsoft.

- **Validering av testkörningsdata (~5 min)**

I det här steget validerar vi de data som du angav i avsnittet teknisk konfiguration av testlusten i erbjudandet. Testkörningsfunktionen är testad och godkänd. Det här steget gäller endast för erbjudanden med en provkörning aktiverad.

- **Provkörning etablering (~ 30 min)**

I det här steget, efter att ha validerat data och funktioner för din provkörning i föregående steg, distribuerar och replikerar vi instanser av din provkörning så att de är redo för kundanvändning.  Det här steget gäller endast för erbjudanden med en provkörning aktiverad.

- **Validering och registrering av leadhantering (<15 min)**

I det här steget bekräftar vi att ditt leadhanteringssystem kan ta emot kundleads baserat på de uppgifter som anges i inställningarna för Erbjudandet. Det här steget gäller endast för erbjudanden med leadhantering aktiverad.

## <a name="certification"></a>Certifiering

Innan de publiceras måste erbjudanden som skickas till Commercial Marketplace i Partner Center certifieras. Inlämnade erbjudanden genomgår rigorösa tester, vissa automatiserade och andras manuella, inklusive en kontroll mot [Azure Marketplace-certifieringsprinciperna](https://docs.microsoft.com/legal/marketplace/general-policies). Erbjudandeinlämningar måste markeras som berättigade till certifiering innan de går vidare till nästa steg i publiceringsflödet.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typer av validering som sker under certifieringen

Det finns tre valideringsnivåer som ingår i certifieringsprocessen för varje erbjudande som skickas in.

- Berättigande till Publisher-företag
- Validering av innehåll
- Teknisk validering

#### <a name="publisher-business-eligibility"></a>Berättigande till Publisher-företag

Varje erbjudandetyp kontrollerar en uppsättning grundläggande berättigandekriterier som utgivaren måste uppfylla. Behörighetskriterier kan omfatta utgivarens MPN-status, kompetenser som innehas, kompetensnivåer etc.

#### <a name="content-validation"></a>Validering av innehåll

Under innehållsvalideringen kontrolleras informationen när du skapade erbjudandet efter kvalitet och relevans. Dessa kontroller kommer att granska dina poster för marknadsplatsen notering detaljer, prissättning, tillgänglighet, tillhörande planer, etc. För att uppfylla kriterierna för Azure Marketplace- och/eller AppSource-listning validerar vi att ditt erbjudande innehåller:

- En titel som korrekt beskriver erbjudandet.
- välskrivna beskrivningar som ger en grundlig översikt och värdeerbjudande;
- kvalitet skärmdumpar och medföljande videor; Och
- en förklaring av hur erbjudandet använder Microsofts plattformar och verktyg.

Läs mer om kriterierna för innehållsverifiering genom att läsa de [allmänna listningsprinciperna](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Teknisk validering

Under teknisk validering genomgår erbjudandet (paket eller binär) följande kontroller.
- Skannad efter skadlig kod
- Nätverkssamtal övervakas
- Paketet analyseras
- Grundlig skanning av erbjudandets faktiska funktionalitet

Erbjudandet testas på olika plattformar och versioner för att säkerställa att det är robust.

Granska de specifika konfigurationsinformation som krävs för ditt erbjudande i avsnittet Teknisk konfiguration i det här dokumentet.

### <a name="certification-failure-report"></a>Rapport om certifieringsfel

Efter avslutad granskning, om ditt erbjudande har godkänts certifiering då det går vidare till nästa steg i publiceringsprocessen. Om ditt erbjudande har misslyckats med någon av list-, teknik- eller policykontrollerna, eller om du inte är berättigad att skicka ett erbjudande av den typen, genereras en rapport om certifieringsfel och skickas till dig via e-post.

Den här rapporten innehåller beskrivningar av alla principer som misslyckades, tillsammans med granskningsanteckningar. Granska den här e-postrapporten, åtgärda eventuella problem, gör uppdateringar av ditt erbjudande där det behövs och skicka erbjudandet igen med hjälp av [portalen Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center. (Du kan skicka in erbjudandet igen så många gånger som behövs tills certifieringen har godkänts).

## <a name="preview-creation"></a>Skapa förhandsgranskning

Under steget **Förhandsgranskning skapar** vi en version av ditt erbjudande som endast är tillgänglig för den målgrupp som du angav i förhandsgranskningsavsnittet i erbjudandet.

>[!Note]
> Använd inte det här steget för att ge personer utanför organisationen insyn i ett erbjudande. Använd alternativet **Privat erbjudande** i stället. Vid denna punkt, ditt erbjudande har inte testats fullt ut och valideras, och är inte redo för extern distribution.

## <a name="publisher-approval"></a>Godkännande av utgivare

I det här steget kommer du att skickas med en begäran om att du ska granska och godkänna din förhandsgranskning av erbjudandet innan det slutliga publiceringssteget.

Om du har valt att sälja ditt erbjudande via Microsoft kan du testa anskaffningen och distributionen av erbjudandet för att säkerställa att det uppfyller dina krav under den här fasen av förhandsgodkännandet. Ditt erbjudande kommer ännu inte att vara tillgängligt på den offentliga marknaden. När du har testat och godkänt den här förhandsversionen måste du välja **Go-Live** på [**instrumentpanelen Erbjudandeöversikt.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

Om du vill göra ändringar i erbjudandet under den här förhandsgranskningsfasen kan du redigera och skicka om för att publicera en ny förhandsgranskning. Mer information om fler ändringar finns i artikeln [Uppdatera befintliga marknadsplatserbjudanden.](#update-existing-marketplace-offers)

Om ditt erbjudande redan är live och tillgängligt för allmänheten på marknadsplatsen visas inte alla uppdateringar som du gör live förrän du väljer **Go-live** på instrumentpanelen [**erbjudandeöversikt.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

### <a name="publish-offer-to-the-public"></a>Publicera erbjudande till allmänheten

Logga in på Partner Center och få tillgång till erbjudandet. Du omdirigeras till översiktssidan **för Erbjudandet.** Högst upp på den här sidan ser du ett alternativ för **Go live**. Välj **Gå live,** och efter bekräftelsen börjar erbjudandet publiceras för allmänheten. Du kommer att få ett e-postmeddelande när erbjudandet är live.

## <a name="publish"></a>Publicera

Nu när du har valt att **gå live** med ditt erbjudande, vilket gör det tillgängligt på marknaden, finns det en rad slutliga valideringskontroller som kommer att träda igenom för att säkerställa att liveerbjudandet är konfigurerat precis som förhandsversionen av erbjudandet.

- **Erbjudandet inköp flöde setup (>10 min)**

I det här steget säkerställer vi att ditt erbjudande kan uppfyllas när det köps av kunder via Azure-portalen. Det här steget gäller endast för erbjudanden som säljs via Microsoft.

- **Validering av testkörningsdata (~5 min)**

I det här steget validerar vi de data som du angav i avsnittet teknisk konfiguration av testlusten i erbjudandet. Testkörningsfunktionen är testad och godkänd. Det här steget gäller endast för erbjudanden med en provkörning aktiverad.

- **Provkörning etablering (~ 30 min)**

I det här steget distribuerar och replikerar vi instanser av din provkörning så att de är redo för kundanvändning.  Det här steget gäller endast för erbjudanden med en provkörning aktiverad.

- **Validering och registrering av leadhantering (>15 min)**

I det här steget bekräftar vi att ditt leadhanteringssystem kan ta emot kundleads baserat på de uppgifter som anges i inställningarna för Erbjudandet. Det här steget gäller endast för erbjudanden med leadhantering aktiverad.

- **Slutlig publicering (>30 minuter)**

I det här steget ser vi till att ditt erbjudande blir offentligt tillgängligt på marknaden.

## <a name="update-existing-marketplace-offers"></a>Uppdatera befintliga marketplace-erbjudanden

Om du vill göra ändringar i ett erbjudande som du redan har publicerat måste du först uppdatera det befintliga erbjudandet och sedan publicera det igen.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
