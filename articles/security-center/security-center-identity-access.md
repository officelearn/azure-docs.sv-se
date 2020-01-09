---
title: Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552992"
---
# <a name="monitor-identity-and-access-preview"></a>Övervaka identitet och åtkomst (för hands version)
När Security Center identifierar potentiella säkerhets risker skapar den rekommendationer som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna för att skärp och skydda dina resurser.

I den här artikeln beskrivs sidan **identitets-och åtkomst** i avsnittet resurs säkerhet i Azure Security Center.

En fullständig lista över rekommendationer som du kan se på den här sidan finns i [rekommendationer för identitet och åtkomst](recommendations-reference.md#recs-identity).

> [!NOTE]
> Övervakning av identitet och åtkomst är i för hands version och endast tillgängligt på standard nivån för Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>

Identiteten bör vara kontroll planet för ditt företag och skydda identiteter bör vara din högsta prioritet. Säkerhetsperimetern har utvecklats från en nätverks gräns till en identitets perimeter. Säkerheten blir mindre säker på skyddet av ditt nätverk och mer om att skydda dina data, samt att hantera säkerheten för dina appar och användare. Nuförtiden, då mer data och fler appar flyttas till molnet, blir identiteten den nya perimetern.

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller reaktiva åtgärder för att stoppa ett angreppsförsök. Instrument panelen för identitets & åtkomst ger dig rekommendationer som:

- Aktivera MFA för privilegierade konton för prenumerationen
- Ta bort externa konton med skrivbehörigheter från prenumerationen
- Ta bort externa privilegierade konton från prenumerationen

> [!NOTE]
> Om din prenumeration har fler än 600 konton kan Security Center inte köra identitets rekommendationerna mot din prenumeration. Rekommendationer som inte körs visas under "otillgängliga utvärderingar" nedan.
Security Center kan inte köra identitets rekommendationer mot en CSP-partners (Cloud Solution Provider) administratörs agenter.
>

## <a name="monitor-identity-and-access"></a>Övervaka identitet och åtkomst

Öppna listan med identifierade identitets-och åtkomst problem genom att välja **identitets & åtkomst** från Security Center sid panelen (under **resurser**) eller från översikts sidan. 

Under **identitets & åtkomst**finns det två flikar:

- **Översikt**: rekommendationer som identifieras av Security Center.
- **Prenumerationer**: lista över prenumerationer och aktuella säkerhets status för varje.

[![identitets & åtkomst](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Avsnittet Översikt
Under **Översikt**finns det en lista över rekommendationer. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet prenumerationer som påverkas av den här rekommendationen. Den tredje kolumnen visar problemets allvarlighets grad.

1. Välj en rekommendation. Fönstret rekommendationer öppnas och visar:

   - Beskrivning av rekommendationen
   - Lista över felaktiga och felfria prenumerationer
   - Lista över resurser som inte har genomsökts på grund av en misslyckad utvärdering eller på grund av att resursen är under en prenumeration som körs på den kostnads fria nivån och inte utvärderas

    [fönstret ![rekommendationer](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Välj en prenumeration i listan om du vill ha mer information.

### <a name="subscriptions-section"></a>Avsnittet prenumerationer
Det finns en lista över prenumerationer under **prenumerationer**. Den första kolumnen visar prenumerationerna. Den andra kolumnen visar det totala antalet rekommendationer för varje prenumeration. Den tredje kolumnen visar allvarlighets graden för problemen.

[fliken ![prenumerationer](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Välj en prenumeration. En sammanfattningsvy öppnas med tre flikar:

   - **Rekommendationer**: baserat på har utförts av Security Center som misslyckades.
   - **Skickas utvärderingar**: lista över har utförts av Security Center som skickas.
   - **Ej tillgängliga utvärderingar**: lista över utvärderingar som inte kunde köras på grund av ett fel eller på grund av att prenumerationen har fler än 600 konton.

   Under **rekommendationer** är en lista över rekommendationer för den valda prenumerationen och allvarlighets graden för varje rekommendation.

   [![rekommendationer för Select-prenumeration](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Välj en rekommendation för en beskrivning av rekommendationen, en lista över felaktiga och felfria prenumerationer och en lista över ej genomsökta resurser.

   [![Beskrivning av rekommendation](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   Under **skickas utvärderingar** är en lista över skickade utvärderingar.  Allvarlighetsgraden för de här utvärderingar är alltid grönt.

   [utvärderingar som ![godkänts](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Välj en godkänd utvärdering i listan för en beskrivning av utvärderingen och en lista över felfria prenumerationer. Det finns en flik för felaktiga prenumerationer som visar alla misslyckade prenumerationer.

   [utvärderingar som ![godkänts](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Om du har skapat en princip för villkorlig åtkomst som kräver MFA men har angett undantag, anser den Security Center MFA rekommendations utvärderingen att principen inte är kompatibel, eftersom det gör att vissa användare kan logga in på Azure utan MFA.

## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande artiklar:

- [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
- [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
- [Skydda din Azure SQL-tjänst och data i Azure Security Center](security-center-sql-service-recommendations.md)