---
title: Lösa problem med grupplicenstilldelning - Azure Active Directory | Microsoft-dokument
description: Identifiera och lösa licenstilldelningsproblem när du använder Azure Active Directory-gruppbaserad licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025701"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifiera och lösa licenstilldelningsproblem för en grupp i Azure Active Directory

Gruppbaserad licensiering i Azure Active Directory (Azure AD) introducerar konceptet användare i ett licensieringsfeltillstånd. I den här artikeln förklarar vi varför användare kan hamna i det här tillståndet.

När du tilldelar licenser direkt till enskilda användare, utan att använda gruppbaserad licensiering, kan tilldelningsåtgärden misslyckas. När du till exempel kör PowerShell-cmdleten `Set-MsolUserLicense` på ett användarsystem kan cmdleten misslyckas av många orsaker som är relaterade till affärslogik. Det kan till exempel finnas ett otillräckligt antal licenser eller en konflikt mellan två tjänstplaner som inte kan tilldelas samtidigt. Problemet rapporteras omedelbart tillbaka till dig.

När du använder gruppbaserad licensiering kan samma fel uppstå, men de inträffar i bakgrunden medan Azure AD-tjänsten tilldelar licenser. Av denna anledning kan felen inte meddelas dig omedelbart. I stället registreras de på användarobjektet och rapporteras sedan via den administrativa portalen. Den ursprungliga avsikten att licensiera användaren går aldrig förlorad, men den registreras i ett feltillstånd för framtida undersökning och lösning.

## <a name="find-license-assignment-errors"></a>Hitta licenstilldelningsfel

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Så här hittar du användare i feltillstånd i en grupp

1. Öppna gruppen på översiktssidan och välj **Licenser**. Ett meddelande visas om det finns några användare i feltillstånd.

   ![Grupp- och felmeddelanden](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Välj meddelandet om du vill öppna en lista över alla berörda användare. Du kan välja varje användare individuellt för att se mer information.

   ![lista över användare i feltillstånd för grupplicensiering](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Om du vill söka efter alla grupper som innehåller minst ett fel väljer du **Licenser**i **Azure Active Directory** och sedan **Översikt**. En informationsruta visas när grupper kräver din uppmärksamhet.

   ![Översikt och information om grupper i feltillstånd](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Markera rutan om du vill visa en lista över alla grupper med fel. Du kan välja varje grupp för mer information.

   ![Översikt och lista över grupper med fel](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

Följande avsnitt ger en beskrivning av varje potentiellt problem och hur det ska lösas.

## <a name="not-enough-licenses"></a>Inte tillräckligt med licenser

**Problem:** Det finns inte tillräckligt med tillgängliga licenser för någon av de produkter som anges i gruppen. Du måste antingen köpa fler licenser för produkten eller frigöra oanvända licenser från andra användare eller grupper.

Om du vill se hur många licenser som är tillgängliga går du till **Azure Active Directory** > **Licenses** > **All products**.

Om du vill se vilka användare och grupper som konsumerar licenser väljer du en produkt. Under **Licensierade användare**visas en lista över alla användare som har tilldelats licenser direkt eller via en eller flera grupper. Under **Licensierade grupper**visas alla grupper som har tilldelats produkter.

**PowerShell:** PowerShell-cmdletar rapportera det här felet som _CountViolation_.

## <a name="conflicting-service-plans"></a>Motstridiga serviceplaner

**Problem:** En av produkterna som anges i gruppen innehåller en serviceplan som står i konflikt med en annan serviceplan som redan har tilldelats användaren via en annan produkt. Vissa serviceplaner är konfigurerade på ett sätt som de inte kan tilldelas samma användare som en annan, relaterad serviceplan.

Se följande exempel. En användare har en licens för Office 365 Enterprise *E1* tilldelad direkt, med alla abonnemang aktiverade. Användaren har lagts till i en grupp som har tilldelats Office 365 Enterprise *E3-produkten.* E3-produkten innehåller serviceplaner som inte kan överlappa de planer som ingår i E1, så grupplicenstilldelningen misslyckas med felet "Motstridiga serviceplaner". I det här exemplet är de motstridiga serviceplanerna:

- SharePoint Online (plan 2) står i konflikt med SharePoint Online (plan 1).
- Exchange Online (Plan 2) står i konflikt med Exchange Online (plan 1).

För att lösa den här konflikten måste du inaktivera två av planerna. Du kan inaktivera E1-licensen som är direkt tilldelad användaren. Du måste också ändra hela grupplicenstilldelningen och inaktivera planerna i E3-licensen. Alternativt kan du välja att ta bort E1-licensen från användaren om den är överflödig i samband med E3-licensen.

Beslutet om hur du löser produktlicenser i konflikt tillhör alltid administratören. Azure AD löser inte automatiskt licenskonflikter.

**PowerShell:** PowerShell-cmdlets rapportera det här felet som _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andra produkter är beroende av denna licens

**Problem:** En av de produkter som anges i gruppen innehåller en serviceplan som måste vara aktiverad för att en annan serviceplan, i en annan produkt, ska fungera. Det här felet uppstår när Azure AD försöker ta bort den underliggande serviceplanen. Detta kan till exempel inträffa när du tar bort användaren från gruppen.

För att lösa det här problemet måste du se till att den nödvändiga planen fortfarande är tilldelad till användare via någon annan metod eller att de beroende tjänsterna är inaktiverade för dessa användare. När du har gjort det kan du ta bort grupplicensen från dessa användare på rätt sätt.

**PowerShell:** PowerShell-cmdlets rapportera det här felet som _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Användningsplats är inte tillåten

**Problem:** Vissa Microsoft-tjänster är inte tillgängliga på alla platser på grund av lokala lagar och förordningar. Innan du kan tilldela en licens till en användare måste du ange egenskapen **Användningsplats** för användaren. Du kan ange platsen under > **avsnittet** Inställningar för > **användarprofil**i Azure-portalen. **User**

När Azure AD försöker tilldela en grupplicens till en användare vars användningsplats inte stöds, misslyckas den och registrerar ett fel på användaren.

Lös problemet genom att ta bort användare från platser som inte stöds från den licensierade gruppen. Om de aktuella värdena för användningsplats inte representerar den faktiska användarplatsen kan du ändra dem så att licenserna tilldelas korrekt nästa gång (om den nya platsen stöds).

**PowerShell:** PowerShell-cmdlets rapportera det här felet som _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> När Azure AD tilldelar grupplicenser ärver alla användare utan angiven användningsplats platsen för katalogen. Vi rekommenderar att administratörer anger rätt värden för användningsplats för användare innan de använder gruppbaserad licensiering för att följa lokala lagar och förordningar.

## <a name="duplicate-proxy-addresses"></a>Duplicera proxyadresser

Om du använder Exchange Online kan vissa användare i klienten vara felaktigt konfigurerade med samma proxyadressvärde. När gruppbaserad licensiering försöker tilldela en licens till en sådan användare misslyckas den och visar "Proxy-adressen används redan".

> [!TIP]
> Om du vill se om det finns en dubblettproxyadress kör du följande PowerShell-cmdlet mot Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Felmeddelandet ["Proxyadress används redan" i Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artikeln innehåller också information om [hur du ansluter till Exchange Online med hjälp av fjärr-PowerShell](https://technet.microsoft.com/library/jj984289.aspx).

När du har löst eventuella proxyadressproblem för de berörda användarna kontrollerar du att licensbearbetningen för gruppen ska tvingas till att licenserna nu kan tillämpas.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Azure AD Mail och ProxyAddresses attributändring

**Problem:** När du uppdaterar licenstilldelningen för en användare eller en grupp kan du se att attributet Azure AD Mail och ProxyAddresses för vissa användare ändras.

Om du uppdaterar licenstilldelningen för en användare utlöses beräkningen av proxyadressen, vilket kan ändra användarattribut. Information om hur du förstår den exakta orsaken till ändringen och löser problemet finns i den här artikeln om [hur attributet proxyAddresses fylls i i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributConcurrencyException i granskningsloggar

**Problem:** Användaren har LicenseAssignmentAttributConcurrencyException för licenstilldelning i granskningsloggar.
När gruppbaserad licensiering försöker bearbeta samtidig licenstilldelning av samma licens till en användare registreras det här undantaget på användaren. Detta händer vanligtvis när en användare är medlem i mer än en grupp med samma tilldelade licens. AZure AD kommer att försöka bearbeta användarlicensen igen och löser problemet. Det krävs ingen åtgärd från kunden för att åtgärda problemet.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Mer än en produktlicens som tilldelats en grupp

Du kan tilldela mer än en produktlicens till en grupp. Du kan till exempel tilldela Office 365 Enterprise E3 och Enterprise Mobility + Security till en grupp för att enkelt aktivera alla inkluderade tjänster för användare.

Azure AD försöker tilldela alla licenser som anges i gruppen till varje användare. Om Azure AD inte kan tilldela en av produkterna på grund av affärslogikproblem, tilldelar den inte de andra licenserna i gruppen heller. Ett exempel är om det inte finns tillräckligt med licenser för alla, eller om det finns konflikter med andra tjänster som är aktiverade för användaren.

Du kan se de användare som inte kunde tilldelas och kontrollera vilka produkter som påverkas av det här problemet.

## <a name="when-a-licensed-group-is-deleted"></a>När en licensierad grupp tas bort

Du måste ta bort alla licenser som tilldelats en grupp innan du kan ta bort gruppen. Det kan dock ta tid att ta bort licenser från alla användare i gruppen. När du tar bort licenstilldelningar från en grupp kan det finnas fel om användaren har tilldelat en beroende licens eller om det finns ett problem med proxyadresskonflikt som förbjuder borttagning av licensen. Om en användare har en licens som är beroende av en licens som tas bort på grund av gruppborttagning, konverteras licenstilldelningen till användaren från ärvd till direkt.

Tänk dig till exempel en grupp som har Office 365 E3/E5 tilldelad med en Skype för företag-tjänstplan aktiverad. Föreställ dig också att några medlemmar i gruppen har ljudkonferenslicenser som tilldelats direkt. När gruppen tas bort försöker gruppbaserad licensiering ta bort Office 365 E3/E5 från alla användare. Eftersom ljudkonferenser är beroende av Skype för företag konverterar gruppbaserad licensiering office 365 E3/E5-licenserna för alla användare med tilldelat ljudkonferenser till direktlicenstilldelning.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Hantera licenser för produkter med förutsättningar

Vissa Microsoft *Online-produkter*som du kanske äger är tillägg. Tillägg kräver en nödvändig serviceplan som ska aktiveras för en användare eller en grupp innan de kan tilldelas en licens. Med gruppbaserad licensiering kräver systemet att både nödvändiga och tilläggstjänstplaner finns i samma grupp. Detta görs för att säkerställa att alla användare som läggs till i gruppen kan ta emot den fullt fungerande produkten. Vi tittar på följande exempel:

Microsoft Workplace Analytics är en tilläggsprodukt. Den innehåller en enda serviceplan med samma namn. Vi kan bara tilldela den här serviceplanen till en användare eller grupp när en av följande förutsättningar också har tilldelats:

- Exchange Online (Plan 1)
- Exchange Online (Plan 2)

Om vi försöker tilldela den här produkten på egen hand till en grupp returnerar portalen ett meddelande. Om vi väljer objektinformation visas följande felmeddelande:

  "Licensåtgärden misslyckades. Kontrollera att gruppen har nödvändiga tjänster innan du lägger till eller tar bort en beroende tjänst. **Tjänsten Microsoft Workplace Analytics kräver att Exchange Online (Plan 2) också aktiveras.**"

Om du vill tilldela den här tilläggslicensen till en grupp måste vi se till att gruppen också innehåller den nödvändiga serviceplanen. Vi kan till exempel uppdatera en befintlig grupp som redan innehåller hela Office 365 E3-produkten och sedan lägga till tilläggsprodukten i den.

Det är också möjligt att skapa en fristående grupp som endast innehåller de minsta nödvändiga produkterna för att få tillägget att fungera. Det kan användas för att licensiera endast utvalda användare för tilläggsprodukten. Baserat på föregående exempel skulle du tilldela följande produkter till samma grupp:

- Office 365 Enterprise E3 med endast Serviceplanen Exchange Online (Plan 2) aktiverad
- Microsoft Workplace Analytics

Från och med nu förbrukar alla användare som läggs till i den här gruppen en licens för E3-produkten och en licens för Workplace Analytics-produkten. Samtidigt kan dessa användare vara medlemmar i en annan grupp som ger dem hela E3-produkten, och de förbrukar fortfarande bara en licens för den produkten.

> [!TIP]
> Du kan skapa flera grupper för varje nödvändig serviceplan. Om du till exempel använder både Office 365 Enterprise E1 och Office 365 Enterprise E3 för användarna kan du skapa två grupper för att licensiera Microsoft Workplace Analytics: en som använder E1 som en förutsättning och den andra som använder E3. På så sätt kan du distribuera tillägget till E1- och E3-användare utan att förbruka ytterligare licenser.

## <a name="force-group-license-processing-to-resolve-errors"></a>Tvinga grupplicensbearbetning för att lösa fel

Beroende på vilka åtgärder du har vidtagit för att lösa felen kan det vara nödvändigt att manuellt utlösa bearbetningen av en grupp för att uppdatera användartillståndet.

Om du till exempel frigör vissa licenser genom att ta bort direkta licenstilldelningar från användare måste du utlösa bearbetning av grupper som tidigare inte helt licensierats för alla användarmedlemmar. Om du vill bearbeta om en grupp går du till gruppfönstret, öppnar Licenser och väljer sedan knappen **Bearbeta i** **verktygsfältet.**

## <a name="force-user-license-processing-to-resolve-errors"></a>Tvinga bearbetning av användarlicenser att lösa fel

Beroende på vilka åtgärder du har vidtagit för att lösa felen kan det vara nödvändigt att manuellt utlösa bearbetningen av en användare för att uppdatera användartillståndet.

När du till exempel har löst problemet med dubblettproxyadresser för en berörd användare måste du utlösa bearbetningen av användaren. Om du vill bearbeta om en användare går du till användarfönstret, öppnar Licenser och väljer sedan knappen **Bearbeta i** **verktygsfältet.**

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via grupper finns i följande:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Migrera användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
