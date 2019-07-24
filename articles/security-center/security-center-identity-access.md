---
title: Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: v-mohabe
ms.openlocfilehash: 73480c6e24ab13b7764c5b72280c19971eb3d039
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296480"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Övervaka identitet och åtkomst i Azure Security Center (för hands version)
Den här artikeln hjälper dig att använda Azure Security Center för att övervaka användarnas identitets- och åtkomstrelaterade aktiviteter.

> [!NOTE]
> Länken "Visa *klassisk* identitets & åtkomst" kommer att dras tillbaka den 31 juli 2019. Klicka [här](security-center-features-retirement-july2019.md#menu_classicidentity) om du vill lära dig mer om alternativa tjänster.

> [!NOTE]
> Övervakning av identitet och åtkomst är i för hands version och endast tillgängligt på standard nivån för Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>

Identiteten bör vara kontrollplanet för ditt företag, och din högsta prioritet bör vara att skydda din identitet. Säkerhetsperimetern har utvecklats från en nätverks gräns till en identitets perimeter. Säkerheten blir mindre säker på skyddet av ditt nätverk och mer om att skydda dina data, samt att hantera säkerheten för dina appar och användare. Nuförtiden, då mer data och fler appar flyttas till molnet, blir identiteten den nya perimetern.

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller reaktiva åtgärder för att stoppa ett angreppsförsök. Instrument panelen för identitets & åtkomst ger dig rekommendationer som:

- Aktivera MFA för privilegierade konton för prenumerationen
- Ta bort externa konton med skrivbehörigheter från prenumerationen
- Ta bort externa privilegierade konton från prenumerationen

> [!NOTE]
> Om din prenumeration har fler än 600 konton kan Security Center inte köra identitets rekommendationerna mot din prenumeration. Rekommendationer som inte körs visas under "ej tillgängliga utvärderingar" som beskrivs nedan.
Security Center kan inte köra identitets rekommendationer mot en CSP-partners (Cloud Solution Provider) administratörs agenter.
>

Se [rekommendationer](security-center-identity-access.md#recommendations) för en lista över identitets-och åtkomst rekommendationer som tillhandahålls av Security Center.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
Du kan övervaka dina resursers säkerhetstillstånd på den **Security Center – översikt** instrumentpanelen. Avsnittet  Resources är en hälso indikator som visar allvarlighets graderna för varje resurs typ.

Du kan visa en lista över alla problem genom att välja **rekommendationer**. Under **resurser**kan du Visa en lista med problem som är specifika för beräkning & appar, data säkerhet, nätverk eller identitets & åtkomst. Mer information om hur du utför rekommendationerna finns i [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

En fullständig lista över identitets-och åtkomst rekommendationer finns i [rekommendationer](security-center-identity-access.md#recommendations).

Fortsätt genom att välja **identitets & åtkomst** under **resurser** eller Security Center huvud menyn.

![Instrumentpanelen i Security Center][1]

## <a name="monitor-identity-and-access"></a>Övervaka identitet och åtkomst
Under **identitets & åtkomst**finns det två flikar:

- **Översikt**: rekommendationer som identifieras av Security Center.
- **Prenumerationer**: lista över prenumerationer och aktuella säkerhets status för varje.

![Identitet och åtkomst][2]

### <a name="overview-section"></a>Översikts avsnitt
Under **Översikt**finns det en lista över rekommendationer. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet prenumerationer som påverkas av den här rekommendationen. Den tredje kolumnen visar problemets allvarlighets grad.

1. Välj en rekommendation. Rekommendations fönstret öppnas och visas:

   - Beskrivning av rekommendationen
   - Lista över felaktiga och felfria prenumerationer
   - Lista över resurser som inte har genomsökts på grund av en misslyckad utvärdering eller på grund av att resursen är under en prenumeration som körs på den kostnads fria nivån och inte utvärderas

   ![Rekommendations fönster][3]

1. Välj en prenumeration i listan om du vill ha mer information.

### <a name="subscriptions-section"></a>Avsnittet prenumerationer
Det finns en lista över prenumerationer under prenumerationer. Den första kolumnen visar prenumerationerna. Den andra kolumnen visar det totala antalet rekommendationer för varje prenumeration. Den tredje kolumnen visar allvarlighets graden för problemen.

![Prenumerationens flik][4]

1. Välj en prenumeration. En sammanfattningsvy öppnas med tre flikar:

   - **Rekommendationer**: baserat på har utförts av Security Center som misslyckades.
   - **Skickas utvärderingar**: lista över har utförts av Security Center som skickas.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller på grund av att prenumerationen har fler än 600 konton.

   Under **rekommendationer** är en lista över rekommendationer för den valda prenumerationen och allvarlighets graden för varje rekommendation.

   ![Rekommendationer för Select-prenumeration][5]

1. Välj en rekommendation för en beskrivning av rekommendationen, en lista över felaktiga och felfria prenumerationer och en lista över ej genomsökta resurser.

   ![Beskrivning av rekommendation][6]

   Under **skickas utvärderingar** är en lista över skickade utvärderingar.  Allvarlighetsgraden för de här utvärderingar är alltid grönt.

   ![Skickade utvärderingar][7]

1. Välj en godkänd utvärdering i listan för en beskrivning av utvärderingen och en lista över felfria prenumerationer. Det finns en flik för felaktiga prenumerationer som visar alla misslyckade prenumerationer.

   ![Skickade utvärderingar][8]

## <a name="recommendations"></a>Rekommendationer
Använd tabellen nedan som referens för att hjälpa dig att förstå tillgängliga identitets & åtkomst rekommendationer och vad var och en gör om du använder den.

|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Subscription|50|MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration|Aktivera Multi-Factor Authentication (MFA) för alla prenumerations konton med administratörs behörighet för att förhindra överträdelser av konton eller resurser.|
|Subscription|40|MFA ska vara aktiverat på dina prenumerations konton med Skriv behörighet|Aktivera Multi-Factor Authentication (MFA) för alla prenumerations konton med Skriv behörighet för att förhindra en överträdelse av konton eller resurser.|
|Subscription|30|Externa konton med ägar behörigheter bör tas bort från din prenumeration|Ta bort externa konton med ägar behörigheter från din prenumeration för att förhindra oövervakad åtkomst.|
|Subscription|30|MFA ska vara aktiverat på dina prenumerations konton med Läs behörighet|Aktivera Multi-Factor Authentication (MFA) för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser.|
|Subscription|25|Externa konton med skrivbehörigheter bör tas bort från prenumerationen|Ta bort externa konton med Skriv behörigheter från din prenumeration för att förhindra oövervakad åtkomst. |
|Subscription|20|Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration|Ta bort inaktuella konton med ägar behörigheter från dina prenumerationer.|
|Subscription|5|Föråldrade konton bör tas bort från din prenumeration|Ta bort inaktuella konton från dina prenumerationer och ge endast åtkomst till aktuella användare. |
|Subscription|5|Det bör finnas fler än en ägare som tilldelats din prenumeration|Ange fler än en prenumerations ägare för att få administratörs åtkomst till redundans.|
|Subscription|5|Högst 3 ägare bör anges för din prenumeration|Ange färre än tre prenumerations ägare för att minska risken för intrång av en komprometterad ägare.|
|Nyckelvalv|5|Diagnostikloggar i Key Vault ska vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Subscription|15|Externa konton med Läs behörighet bör tas bort från din prenumeration|Ta bort externa konton med Läs privilegier från din prenumeration för att förhindra oövervakad åtkomst.| 

> [!NOTE]
> Om du har skapat en princip för villkorlig åtkomst som kräver MFA men har angett undantag, anser den Security Center MFA rekommendations utvärderingen att principen inte är kompatibel, eftersom det gör att vissa användare kan logga in på Azure utan MFA.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

- [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
- [Skydda din Azure SQL-tjänst och data i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:
* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om användningen av Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
