---
title: Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: terrylan
ms.openlocfilehash: b2243b10c20a0c8ed0faccbcc82e24193bd4adac
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460270"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Övervaka identitet och åtkomst i Azure Security Center (förhandsversion)
Den här artikeln hjälper dig att använda Azure Security Center för att övervaka användarnas identitets- och åtkomstrelaterade aktiviteter.

> [!NOTE]
> Övervaka identitet och åtkomst finns i förhandsversion och bara tillgängliga på standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>
>

Identiteten bör vara kontrollplanet för ditt företag, och din högsta prioritet bör vara att skydda din identitet. Säkerhetsperimeter har utvecklats ur en nätverksperimeter en perimeter-identitet. Säkerheten blir mindre om försvar ditt nätverk och mer information om försvar dina data, samt hanterar säkerheten för dina appar och användare. Nuförtiden, då mer data och fler appar flyttas till molnet, blir identiteten den nya perimetern.

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller reaktiva åtgärder för att stoppa ett angreppsförsök. Instrumentpanelen identitet och åtkomst ger dig rekommendationer som:

- Aktivera MFA för privilegierade konton för din prenumeration
- Ta bort externa konton med skrivbehörigheter från prenumerationen
- Ta bort externa Privilegierade konton från prenumerationen

> [!NOTE]
> Om din prenumeration har fler än 250 konton kan kan Security Center inte köra identitet rekommendationer mot din prenumeration. Rekommendationer som inte körs visas under ”ej tillgängliga utvärderingar” som beskrivs nedan.
Security Center kan inte köra identitet rekommendationer mot en Cloud Solution Provider (CSP) partners admin agenter.
>
>

Se [rekommendationer](security-center-identity-access.md#recommendations) för en lista över identitets- och rekommendationer som tillhandahålls av Security Center.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
Du kan övervaka dina resursers säkerhetstillstånd på den **Security Center – översikt** instrumentpanelen. Den **resurser** avsnittet är en indikator för hälsotillstånd som visar allvarlighetsgrader för varje resurstyp.

Du kan visa en lista över alla problem genom att välja **rekommendationer**. Under **resurser**, du kan visa en lista över problem som är specifika för beräkning och appar, datasäkerhet, nätverk, eller identitet och åtkomst. Mer information om hur du utför rekommendationerna finns i [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

En fullständig lista över rekommendationer för identitets- och se [rekommendationer](security-center-identity-access.md#recommendations).

Om du vill fortsätta, Välj **identitet och åtkomst** under **resurser** eller huvudmenyn i Security Center.

![Instrumentpanelen i Security Center][1]

## <a name="monitor-identity-and-access"></a>Övervaka identitet och åtkomst
Under **identitet och åtkomst**, det finns två flikar:

- **Översikt över**: rekommendationer identifierades av Security Center.
- **Prenumerationer**: lista över dina prenumerationer och aktuella säkerhetstillståndet för var och en.

![Identitet och åtkomst][2]

### <a name="overview-section"></a>Översiktsavsnittet
Under **översikt**, det finns en lista över rekommendationer. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet prenumerationer som påverkas av den här rekommendationen. Den tredje kolumnen visar hur allvarligt problemet.

1. Välj en rekommendation. Den rekommendationen fönster öppnas och visar:

  - Beskrivning av rekommendationen
  - Lista över prenumerationer feltillstånd och felfri
  - Lista över resurser som är ej genomsökta på grund av en misslyckad utvärdering eller resursen är under en prenumeration som körs på den kostnadsfria nivån och utvärderat ej

  ![Rekommendationens fönster][3]

1. Välj en prenumeration i listan för ytterligare information.

### <a name="subscriptions-section"></a>Avsnittet för prenumerationer
Under **prenumerationer**, det finns en lista över prenumerationer. Den första kolumnen visar en lista över prenumerationer. Den andra kolumnen visar det totala antalet rekommendationer för varje prenumeration. Den tredje kolumnen visar allvarlighetsgraderna problem.

![Prenumerationens fliken][4]

1.  Välj en prenumeration. En sammanfattningsvy öppnas med tre flikar:

  - **Rekommendationer**: baserat på har utförts av Security Center som misslyckades.
  - **Skickas utvärderingar**: lista över har utförts av Security Center som skickas.
  - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller eftersom prenumerationen har fler än 250 konton.

  Under **rekommendationer** är en lista över rekommendationer för den valda prenumerationen och allvarlighetsgraden för varje rekommendation.

  ![Rekommendationer för Välj en prenumeration][5]

1. Välj en rekommendation för en beskrivning av rekommendationen, en lista över feltillstånd och felfri prenumerationer och en lista över ej genomsökta resurser.

  ![Beskrivning av rekommendation][6]

  Under **skickas utvärderingar** är en lista över skickade utvärderingar.  Allvarlighetsgraden för de här utvärderingar är alltid grönt.

  ![Skickade utvärderingar][7]

1. Välj en skickade utvärdering från listan för en beskrivning av utvärderingen och en lista över felfri prenumerationer. Det finns en flik för ohälsosamt prenumerationer som visar en lista över alla prenumerationer som misslyckades.

  ![Skickade utvärderingar][8]

## <a name="recommendations"></a>Rekommendationer
Använd tabellen nedan som referens för att förstå de tillgängliga rekommendationerna för identitet och åtkomst och vad var och en gör om du använder den.

| Rekommendation | Beskrivning |
| --- | --- |
| Ange mer än en ägare av din prenumeration | Rekommenderar att du anger mer än en prenumerationsägare för att du har administratörsåtkomst. |
| Ange upp till 3 ägare av din prenumeration | Rekommenderar att du anger färre än 3 prenumerationsägare för att minska risken för intrång genom en komprometterad ägare. |
| Aktivera MFA för konton med ägarbehörigheter för din prenumeration | Rekommenderar att du aktiverar Multi-Factor Authentication (MFA) för alla prenumerationskonton med administratörsbehörighet för att förhindra att överträdelser görs avseende konton och resurser. |
| Aktivera MFA för konton med skrivbehörigheter för din prenumeration | Rekommenderar att du aktiverar Multi-Factor Authentication (MFA) för alla prenumerationskonton med skrivbehörighet för att förhindra att överträdelser görs avseende konton och resurser. |
| Aktivera MFA för konton med läsbehörigheter för din prenumeration | Rekommenderar att du aktiverar Multi-Factor Authentication (MFA) för alla prenumerationskonton med läsbehörighet för att förhindra att överträdelser görs avseende konton och resurser. |
| Ta bort externa konton med läsbehörigheter från prenumerationen | Rekommenderar att du tar bort externa konton med läsprivilegier från din prenumeration för att förhindra oövervakad åtkomst. |
| Ta bort externa konton med skrivbehörigheter från prenumerationen | Rekommenderar att du tar bort externa konton med skrivprivilegier från din prenumeration för att förhindra oövervakad åtkomst. |
| Ta bort externa konton med ägarbehörigheter från prenumerationen | Rekommenderar att du tar bort externa konton med ägarbehörigheter från prenumerationen för att förhindra oövervakad åtkomst. |
| Ta bort inaktuella konton från prenumerationen | Rekommenderar att du tar bort inaktuella konton från dina prenumerationer. |
| Ta bort inaktuella konton med ägarbehörigheter från prenumerationen | Rekommenderar att du tar bort inaktuella konton med ägarbehörigheter från dina prenumerationer. |

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

- [Skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
- [Skydda Azure SQL-tjänst och data i Azure Security Center](security-center-sql-service-recommendations.md)

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
