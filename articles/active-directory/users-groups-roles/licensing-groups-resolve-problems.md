---
title: Lös licensproblem för tilldelning för en grupp – Azure Active Directory | Microsoft Docs
description: Att identifiera och lösa licensproblem för tilldelning när du använder Azure Active Directory gruppbaserad licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c40a25cd2baebaaeedc012e8e9faff91b297f57
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082508"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifiera och lösa licensproblem för tilldelning för en grupp i Azure Active Directory

Gruppbaserad licensiering i Azure Active Directory (Azure AD) introducerar konceptet för användare med en licensiering feltillstånd. I den här artikeln förklarar vi orsaker varför användare hamna i det här tillståndet.

När du tilldelar licenser direkt till enskilda användare, utan att använda gruppbaserad licensiering, misslyckas åtgärden tilldelning. Till exempel när du kör PowerShell-cmdleten `Set-MsolUserLicense` på ett system för användaren, cmdlet: en kan misslyckas av flera orsaker som är relaterade till affärslogik. Det kan till exempel finnas tillräckligt många licenser eller en konflikt mellan två service-planer som inte kan tilldelas på samma gång. Problemet rapporteras omedelbart tillbaka till dig.

När du använder gruppbaserad licensiering, samma fel inträffar, men de sker i bakgrunden och Azure AD-tjänsten är tilldela licenser. Därför kan inte felen förmedlas till dig direkt. I stället de registreras på användarobjektet och sedan rapporteras via administrativa-portalen. Den ursprungliga avsikten att licensiera användaren går aldrig förlorad, men den inte har registrerats i ett feltillstånd för framtida undersökningar och upplösning.

## <a name="how-to-find-license-assignment-errors"></a>Så här hittar du licens Tilldelningsfel
**Att hitta licensen Tilldelningsfel**

1. Öppna fönstret för gruppen för att hitta användare i ett feltillstånd i en specifik grupp. Under **licenser**, visas ett meddelande om det finns några användare i ett feltillstånd.

   ![Gruppen felmeddelandet](./media/licensing-groups-resolve-problems/group-error-notification.png)

2. Välj meddelandet för att öppna en lista över alla användare som påverkas. Du kan välja varje användare separat om du vill ha mer information.

   ![Gruppen, lista över användare med feltillstånd](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

3. Du hittar alla grupper som innehåller minst ett fel på den **Azure Active Directory** bladet välj **licenser**, och välj sedan **översikt**. Dialogrutan visas när grupper kräver din uppmärksamhet.

   ![Översikt över, information om grupper i feltillstånd](./media/licensing-groups-resolve-problems/group-errors-widget.png)

4. Markera kryssrutan om du vill se en lista över alla grupper med fel. Du kan välja varje grupp för mer information.

   ![Översikt över grupper med fel](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


I följande avsnitt ger en beskrivning av varje potentiella problem och sätt att lösa problemet.

## <a name="not-enough-licenses"></a>Det finn inte tillräckligt med licenser

**Problem:** Det finns inte tillräckligt med tillgängliga licenser för en av de produkter som har angetts i gruppen. Du måste antingen köpa flera licenser för produkten eller frigöra oanvända licenser från andra användare eller grupper.

Om du vill se hur många licenser som är tillgängliga går du till **Azure Active Directory** > **licenser** > **alla produkter**.

Välj en produkt om du vill se vilka användare och grupper förbrukar licenser. Under **licensierade användare**, visas en lista över alla användare som har haft licenser direkt eller via en eller flera grupper. Under **licensierade grupper**, visas alla grupper som innehåller de produkter som tilldelats.

**PowerShell:** PowerShell-cmdletar rapportera felet som _CountViolation_.

## <a name="conflicting-service-plans"></a>Tjänstplanerna är i konflikt

**Problem:** En av de produkter som har angetts i gruppen innehåller en serviceplan som står i konflikt med en annan service-plan som redan har tilldelats till användaren via en annan produkt. Vissa service-planer är konfigurerade på ett sätt att de inte kan tilldelas samma användare som en annan, relaterade service-plan.

Se följande exempel. En användare har en licens för Office 365 Enterprise *E1* tilldelas direkt, med alla prenumerationer som är aktiverad. Användaren har lagts till en grupp som har Office 365 Enterprise *E3* produkten som är tilldelade till den. E3-produkt innehåller service-planer som inte överlappar med planer som ingår i E1, så licenstilldelning gruppen misslyckas med felmeddelandet ”pågår service-planer”. I det här exemplet är i konflikt service-planer

-   SharePoint Online (Plan 2) är i konflikt med SharePoint Online (Plan 1).
-   Exchange Online (Plan 2) är i konflikt med Exchange Online (Plan 1).

Lös konflikten genom måste du inaktivera två av prenumerationerna. Du kan inaktivera E1-licens som tilldelas direkt till användaren. Eller så kan du behöva ändra licenstilldelningen för hela gruppen och att inaktivera planer i E3-licens. Alternativt kanske du vill ta bort E1-licens från användaren om det är redundant i samband med E3-licens.

Beslut om hur du löser motstridiga produktlicenser alltid tillhör administratören. Azure AD lösa inte automatiskt konflikter licens.

**PowerShell:** PowerShell-cmdletar rapportera felet som _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andra produkter är beroende av den här licensen

**Problem:** En av de produkter som har angetts i gruppen innehåller en tjänstplan som måste aktiveras för en annan service-plan i en annan produkt ska fungera. Det här felet uppstår när Azure AD försöker ta bort den underliggande service-planen. Detta kan till exempel hända när du tar bort användaren från gruppen.

Det här problemet måste du kontrollera att nödvändiga planen fortfarande är tilldelad till användare via någon annan metod eller att de beroende tjänsterna är inaktiverat för dessa användare. När du gör det kan du korrekt ta bort grupplicens från dessa användare.

**PowerShell:** PowerShell-cmdletar rapportera felet som _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Användningsplats är inte tillåten

**Problem:** Vissa Microsoft-tjänster är inte tillgängliga på alla platser på grund av lokala lagar och föreskrifter. Innan du kan tilldela en licens till en användare, måste du ange den **användningsplats** -egenskapen för användaren. Du kan ange plats under den **användaren** > **profil** > **inställningar** avsnitt i Azure-portalen.

När Azure AD försöker tilldela en grupplicens till en användare vars användningsplats inte stöds, misslyckas och registrerar ett fel på användaren.

Lös problemet genom att ta bort användare från platser som inte stöds från gruppen licensierade. Även om de aktuella värdena för användning plats inte motsvarar den faktiska platsen, kan du ändra dem så att licenserna tilldelas korrekt nästa gång (om den nya platsen stöds).

**PowerShell:** PowerShell-cmdletar rapportera felet som _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> När Azure AD tilldelar grupplicenserna, ärver alla användare utan att användningsplats angivna platsen för katalogen. Vi rekommenderar att administratörer anger rätt användningen plats värden på användare innan du använder gruppbaserad licensiering för att uppfylla lokala lagar och föreskrifter.

## <a name="duplicate-proxy-addresses"></a>Duplicera proxyadresser

Om du använder Exchange Online kanske vissa användare i din klient konfigureras felaktigt med det samma värdet för proxyadress. När gruppbaserad licensiering försöker tilldela en licens till användaren, misslyckas och visar ”Proxyadressen används redan”.

> [!TIP]
> Om du vill se om det finns en dubblett proxyadress kör du följande PowerShell-cmdlet mot Exchange Online:
> ```
> Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Mer information om det här problemet finns i [”Proxyadressen används redan” visas i Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artikeln innehåller även information på [hur du ansluter till Exchange Online med fjärr-PowerShell](https://technet.microsoft.com/library/jj984289.aspx). Se den här artikeln för mer information [på så här fylls attributet proxyAddresses i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

När du har löst problemen proxy-adress för de berörda användarna se till att tvinga licens bearbetning på gruppen för att se till att licenser nu kan användas.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Vad händer om det finns fler än en produktlicens i en grupp?

Du kan tilldela mer än en produktlicens till en grupp. Exempelvis kan du tilldela en grupp för att enkelt aktivera ingår tjänster för användare med Office 365 Enterprise E3 och Enterprise Mobility + Security.

Azure AD försöker tilldela alla licenser som anges i gruppen att varje användare. Om Azure AD inte kan tilldela en av produkterna på grund av logiken affärsproblem, tilldela inte den antingen andra licenser i gruppen. Ett exempel är om det inte finns tillräckligt med licenser för alla, eller om det finns konflikter med andra tjänster som är aktiverade på användaren.

Du kan se vilka användare som inte kunde tilldelas och kontrollera vilka produkter som påverkas av problemet.

## <a name="what-happens-when-a-group-with-licenses-assigned-is-deleted"></a>Vad händer när en grupp med tilldelade licenser har tagits bort?

Du måste ta bort alla licenser som tilldelats en grupp innan du kan ta bort gruppen. Ta bort licenser från alla användare i gruppen kan dock ta tid. Ta bort licenstilldelningar från en grupp, kan det vara fel om användaren har en beroende tilldelad licens eller om det finns en proxy-adress konflikt förhindrar borttagningen licens. Om en användare har en licens som är beroende av en licens som tas bort på grund av borttagning av, konverteras licenstilldelning för användaren från ärvt för att dirigera.

Anta exempelvis att en grupp som har Office 365 E3/E5 tilldelas med en Skype för företag-serviceplan som är aktiverad. Anta också att några medlemmar i gruppen har ljud konferenser licenser direkt. När gruppen tas bort, försöker gruppbaserad licensiering att ta bort Office 365 E3/E5 från alla användare. Eftersom ljud konferenser är beroende av Skype för företag, för alla användare med ljud konferenser konverterar tilldelade, gruppbaserad licensiering Office 365 E3/E5-licenser för att dirigera licenstilldelning.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Hur hanterar du licenser för produkter med krav?

Vissa Microsoft Online-produkter som du kanske äger *tillägg*. Tillägg kräver en nödvändig service-plan som ska aktiveras för en användare eller grupp innan de kan tilldelas en licens. Med gruppbaserad licensiering kräver systemet att både nödvändiga och tillägg service-planer finns i samma grupp. Detta görs för att säkerställa att alla användare som läggs till i gruppen kan ta emot fullt fungerande produkten. Anta att du i följande exempel:

Microsoft Workplace Analytics är en produkt för tillägg. Den innehåller en enda service-plan med samma namn. Vi kan bara tilldela service-plan till en användare eller grupp, när något av följande krav är även kopplad:
- Exchange Online (Plan 1) 
- Exchange Online (Plan 2)

Om vi försöker tilldela den här produkten på egen hand till en grupp, returneras ett fel i portalen. Om du väljer på felmeddelandet visas följande information:

![Gruppen, nödvändiga saknas](./media/licensing-groups-resolve-problems/group-prerequisite-required.png)

Om vi väljer informationen visas följande felmeddelande visas:

>Licensåtgärden misslyckades. Kontrollera att gruppen har tillräckliga tjänster innan du lägger till eller ta bort en beroende tjänst. **Tjänsten Microsoft Workplace Analytics kräver Exchange Online (Plan 2) aktiveras också.**

Om du vill tilldela den här tilläggslicens till en grupp, måste vi Kontrollera att gruppen även innehåller den nödvändiga service-planen. Vi kan till exempel uppdatera en befintlig grupp som innehåller redan den fullständiga versionen av Office 365 E3 och sedan lägga till tillägg produkten i den.

Det är också möjligt att skapa en fristående-grupp som innehåller endast minsta produkterna som krävs för att få det att fungera. Den kan användas för att licensiera endast de valda användarna för produkten som tillägg. I det här exemplet har vi tilldelat följande produkter till samma grupp:
- Office 365 Enterprise E3 med endast Exchange Online (Plan 2) service-plan som aktiverats
- Microsoft Workplace Analytics

![Gruppen, krav som ingår](./media/licensing-groups-resolve-problems/group-addon-with-prerequisite.png)

Hädanefter kan använder alla användare som läggs till i den här gruppen en licens E3 och en licens Workplace Analytics. Dessa användare kan vara medlemmar i någon annan grupp som ger dem den fullständiga E3-produkten på samma gång, och de fortfarande använder bara en licens för produkten.

> [!TIP]
> Du kan skapa flera grupper för varje nödvändig service-plan. Om du använder både Office 365 Enterprise E1 och Office 365 Enterprise E3 för dina användare kan du till exempel skapa två grupper till licens Microsoft Workplace Analytics: en som använder E1 som ett krav och andra som använder E3. På så sätt kan du distribuera tillägg till E1 och E3-användare utan att förbruka ytterligare licenser.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Hur gör du för att tvinga licens bearbetning i en grupp för att åtgärda fel?

Beroende på vilka steg som du har tagit för att åtgärda felen, kan det vara nödvändigt att manuellt utlösa bearbetningen av en grupp för att uppdatera användarens tillstånd.

Om du frigör vissa licenser genom att ta bort direkta licenstilldelningar från användare, måste du utlösa bearbetningen av grupper som tidigare inte har fullständigt licensiera alla användarmedlemmar. För att Ombearbeta en grupp, gå till fönstret grupp öppna **licenser**, och välj sedan den **Ombearbeta** i verktygsfältet.

## <a name="how-do-you-force-license-processing-on-a-user-to-resolve-errors"></a>Hur gör du för att tvinga licens belastning på en användare att åtgärda fel?

Beroende på vilka steg som du har tagit för att åtgärda felen, kan det vara nödvändigt för att utlösa bearbetningen av en användare att uppdatera tillståndet användare manuellt.

När du problemet duplicerade proxy-adress för en användare som påverkas, måste du aktivera bearbetning av användaren. För att Ombearbeta en användare, gå till fönstret användare öppna **licenser**, och välj sedan den **Ombearbeta** i verktygsfältet.

## <a name="next-steps"></a>Nästa steg

Mer information om övriga scenarier för hantering av programvarulicenser via grupper finns i:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
