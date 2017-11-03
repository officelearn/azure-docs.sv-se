---
title: "Lösa problem med en licens för en grupp i Azure Active Directory | Microsoft Docs"
description: "Hur du identifiera och lösa problem med licens tilldelning när du använder Azure Active Directory gruppbaserade licensiering"
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa16cf14def7c6b4555d6624b25e267ef01d5adb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifiera och lösa problem med licensen tilldelning för en grupp i Azure Active Directory

Gruppbaserade licensiering i Azure Active Directory (AD Azure) introducerar konceptet för användare i en licensiering feltillstånd. I den här artikeln förklarar vi skälen varför användare kan hamna i det här tillståndet.

När du tilldelar licenser direkt till enskilda användare, utan att använda gruppbaserade licensiering kan tilldelningens misslyckas. Till exempel när du kör PowerShell-cmdleten `Set-MsolUserLicense` på ett system som användaren cmdlet kan misslyckas av flera orsaker som är relaterade till affärslogik. Det kan till exempel finnas ett otillräckligt antal licenser eller en konflikt mellan två serviceplaner som inte kan tilldelas på samma gång. Problemet rapporteras omedelbart tillbaka till dig.

När du använder gruppbaserade licensiering, samma fel inträffar, men de sker i bakgrunden medan Azure AD-tjänsten är tilldela licenser. Därför går inte att felen meddelas du omedelbart. I stället de registreras i användarobjektet och sedan rapporteras via administrativa portal. Den ursprungliga avsikten att licensiera användaren går aldrig förlorade, men den registreras i ett feltillstånd för framtida undersökningar och upplösning.

## <a name="how-to-find-license-assignment-errors"></a>Så här hittar du licens Tilldelningsfel
**Att hitta licensen Tilldelningsfel**

   1. Öppna fönstret för gruppen för att söka efter användare i ett feltillstånd i en specifik grupp. Under **licenser**, visas ett meddelande om det finns några användare i ett feltillstånd.

   ![Gruppen felmeddelanden](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

   2. Välj meddelandet om du vill öppna en lista över alla användare som påverkas. Du kan välja varje användare individuellt för att se mer information.

   ![Gruppen, lista över användare i feltillstånd](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

   3. Söker efter alla grupper som innehåller minst ett fel på den **Azure Active Directory** bladet välj **licenser**, och välj sedan **översikt**. Dialogrutan visas när grupper kräver din uppmärksamhet.

   ![Översikt, information om grupper i feltillstånd](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

   4. Markera kryssrutan för att visa en lista över alla grupper med fel. Du kan välja varje grupp för mer information.

   ![Översikt över grupper med fel](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


Följande avsnitt ger en beskrivning av varje potentiella problem och hur man löser problemet.

## <a name="not-enough-licenses"></a>Det finns inte tillräckligt med licenser

**Problem:** det inte finns tillräckligt många tillgängliga licenser för en av de produkter som anges i gruppen. Du måste köpa fler licenser för produkten eller frigör oanvända licenser från andra användare eller grupper.

Hur många licenser är tillgängliga, gå till **Azure Active Directory** > **licenser** > **alla produkter**.

Om du vill se vilka användare och grupper som förbrukar licenser, välja en produkt. Under **licensierade användare**, visas en lista över alla användare som har haft användarlicenser direkt eller via en eller flera grupper. Under **licensierad grupper**, du se alla grupper som har de produkt som tilldelats.

**PowerShell:** PowerShell-cmdlets rapportera felet som _CountViolation_.

## <a name="conflicting-service-plans"></a>Motstridiga service-planer

**Problem:** en av de produkter som har angetts i gruppen innehåller en serviceplan som står i konflikt med en annan service-plan som är redan tilldelad till användare via en annan produkt. Vissa serviceplaner konfigureras så att de inte kan tilldelas samma användare som en annan, relaterade service-plan.

Överväg följande exempel. En användare har en licens för Office 365 Enterprise *E1* tilldelats direkt, med alla planer aktiveras. Användaren har lagts till en grupp som har Office 365 Enterprise *E3* produkten som är kopplade till den. E3 produkten innehåller serviceplaner som inte får överlappa för energischeman som ingår i E1, så licenstilldelning gruppen misslyckas med felmeddelandet ”i konflikt serviceplaner”. I det här exemplet är i konflikt service-planer

-   SharePoint Online (planera 2) i konflikt med SharePoint Online (planera 1).
-   Exchange Online (planera 2) i konflikt med Exchange Online (planera 1).

Du kan lösa konflikten måste du inaktivera två av planer. Du kan inaktivera E1 licensen som är direkt kopplade till användaren. Eller så måste du ändra licenstilldelningen för hela gruppen och inaktivera planer i E3-licens. Alternativt kanske du vill ta bort E1-licens från användaren om det är redundant i samband med E3-licens.

Beslut om hur du löser motstridiga produktlicenser alltid tillhör administratören. Azure AD lösa inte automatiskt konflikter licens.

**PowerShell:** PowerShell-cmdlets rapportera felet som _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andra produkter är beroende av den här licensen

**Problem:** en av de produkter som anges i gruppen innehåller en serviceplan som måste aktiveras för en annan service-plan i en annan produkt ska fungera. Det här felet uppstår när Azure AD som försöker ta bort den underliggande service-planen. T.ex, kan detta inträffa när du tar bort användaren från gruppen.

För att lösa problemet måste du se till att planen krävs fortfarande är tilldelad till användare via någon annan metod eller att de beroende tjänsterna är inaktiverat för dessa användare. När du har gjort som du ska ta bort gruppen licens från dessa användare.

**PowerShell:** PowerShell-cmdlets rapportera felet som _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Användningsplats är inte tillåten

**Problem:** vissa Microsoft-tjänster är inte tillgängliga på alla platser på grund av lokala lagar och förordningar. Innan du kan tilldela en licens till en användare, måste du ange den **användningsplats** -egenskapen för användaren. Du kan ange plats under den **användare** > **profil** > **inställningar** avsnitt i Azure-portalen.

När Azure AD försöker tilldela en licens för grupp till en användare vars användningsplats inte stöds, misslyckas och innehåller ett fel på användaren.

Om du vill lösa problemet genom att ta bort användare från platser som inte stöds från gruppen licensierade. Alternativt, om de aktuella värdena för användning plats inte representerar den faktiska platsen, du kan ändra dem så att licenserna som tilldelas korrekt nästa gång (om den nya platsen stöds).

**PowerShell:** PowerShell-cmdlets rapportera felet som _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> När Azure AD tilldelar grupp licenser, ärver alla användare utan angiven användningsplats platsen för katalogen. Vi rekommenderar att administratörer korrekt användning plats värden på användare innan du använder gruppbaserade licensiering för att följa lokala lagar och förordningar.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Vad händer när det finns fler än en produktlicens på en grupp?

Du kan tilldela fler än en produktlicensen till en grupp. Exempelvis kan du tilldela en grupp för att enkelt aktivera alla inkluderade tjänster för användare med Office 365 Enterprise E3 och Enterprise Mobility + Security.

Azure AD försöker tilldela alla licenser som anges i gruppen för varje användare. Om Azure AD inte kan tilldela en av produkterna på grund av problem i verksamheten logik, tilldela inte den antingen licenser i gruppen. Ett exempel är om det inte finns tillräckligt många licenser för alla, eller om det är i konflikt med andra tjänster som är aktiverade på användaren.

Du kan se vilka användare som inte kunde tilldelas och kontrollera vilka produkter som påverkas av problemet.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Hur hanterar du licenser för produkter med krav?

Vissa Microsoft Online-produkter som du kan äger *tillägg*. Tillägg kräver en nödvändig service-plan som ska aktiveras för en användare eller grupp innan de kan tilldelas en licens. Med gruppbaserade licensiering kräver systemet att både nödvändiga och tillägg service-planer finns i samma grupp. Detta görs för att säkerställa att alla användare som har lagts till i gruppen kan ta emot fullt fungerande produkten. Nu ska vi titta i följande exempel:

Microsoft arbetsplats Analytics är en tilläggsprodukt. Den innehåller en enda serviceplan med samma namn. Vi kan bara tilldela service-plan till en användare eller grupp, när något av följande krav är även tilldelad:
- Exchange Online (Plan 1) 
- Exchange Online (Plan 2)

Om vi försöker tilldela den här produkten i sin egen till en grupp, returneras ett fel i portalen. Om du väljer felmeddelanden visas följande information:

![Gruppen, nödvändiga saknas](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Om vi väljer information visas följande felmeddelande:

>Det gick inte att licens. Kontrollera att gruppen har nödvändiga tjänster innan du lägger till eller ta bort en beroende tjänst. **Tjänsten Microsoft arbetsplats Analytics kräver Exchange Online (planera 2) aktiveras också.**

Om du vill tilldela den här tilläggslicens till en grupp, måste vi Kontrollera att gruppen även innehåller nödvändiga service-plan. Vi kan till exempel uppdatera en befintlig grupp som innehåller redan den fullständiga versionen av Office 365 E3 och Lägg sedan till tilläggsprodukt till den.

Det är också möjligt att skapa en fristående-grupp som innehåller endast de minsta nödvändiga produkterna för att få det att fungera. Det kan användas för att licensiera de valda användarna för tilläggsprodukt. I det här exemplet har vi tilldelat följande produkter till samma grupp:
- Office 365 Enterprise E3 med endast Exchange Online (Plan 2) serviceplan aktiverad
- Microsoft arbetsplats Analytics

![Gruppen nödvändiga ingår](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

Alla användare som läggs till i den här gruppen använda baserade på en licens E3 och en licens arbetsplats Analytics. Dessa användare kan vara medlemmar i någon annan grupp som ger dem den fullständiga produkten E3 samtidigt, och de fortfarande använda endast en licens för produkten.

> [!TIP]
> Du kan skapa flera grupper för varje nödvändiga service-plan. Om du använder både Office 365 Enterprise E1 och Office 365 Enterprise E3 för dina användare kan du till exempel skapa två grupper till licens Microsoft arbetsplats Analytics: en som använder E1 som en förutsättning och andra som använder E3. På så sätt kan du distribuera tillägget till E1 och E3-användare utan ytterligare licenser.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>Licenstilldelning misslyckas utan meddelanden för en användare på grund av dubbla proxyadresser i Exchange Online

Om du använder Exchange Online, kan vissa användare i din klient är felaktigt konfigurerad med samma värde för proxy-adress. När gruppbaserade licensiering försöker tilldela en licens till användaren, misslyckas och registreras inte i ett fel. Det gick inte att registrera felet i den här instansen är en begränsning i av förhandsversionen av den här funktionen och vi kommer att åtgärda detta innan *allmän tillgänglighet*.

> [!TIP]
> Om du märker att vissa användare fick inte en licens och det finns inget fel registreras för dessa användare kan du först kontrollera om de har en dubblett proxyadress.
> Om det finns en dubblett proxyadress, kör du följande PowerShell-cmdlet mot Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> Mer information om det här problemet finns [”proxyadress används redan” felmeddelande i Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artikeln innehåller även information om [hur du ansluter till Exchange Online med fjärr-PowerShell](https://technet.microsoft.com/library/jj984289.aspx).

När du har löst problemen proxy-adress för de berörda användarna, se till att tvinga licens bearbetning på gruppen för att kontrollera licenserna kan nu användas.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Hur du för att tvinga licens bearbetning i en grupp för att åtgärda fel?

Beroende på vilka steg som du har gjort för att åtgärda felen kan det vara nödvändigt att utlösa bearbetning av en grupp för att uppdatera användartillstånd manuellt.

Om du Frigör några licenser genom att ta bort direkt licenstilldelning från användare, måste du aktivera bearbetning av grupper som tidigare inte har fullständigt licens alla användare medlemmar. Om du vill bearbeta en grupp, gå till fönstret grupp öppna **licenser**, och välj sedan den **Ombearbeta** i verktygsfältet.

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för hantering av programvarulicenser genom grupper finns i följande avsnitt:

* [Tilldela licenser till en grupp i Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Vad är gruppbaserade licensiering i Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Hur du migrerar enskilda licensierade användare till gruppbaserade licensiering i Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory gruppbaserade licensiering fler scenarier](active-directory-licensing-group-advanced.md)
