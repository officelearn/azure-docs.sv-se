---
title: Utvärdera effekten av en ny Azure-princip
description: Förstå processen att följa när du introducerar en ny principdefinition i din Azure-miljö.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463522"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Utvärdera effekten av en ny Azure-princip

Azure Policy är ett kraftfullt verktyg för att hantera dina Azure-resurser till affärsstandarder och för att uppfylla efterlevnadsbehov. När personer, processer eller pipelines skapar eller uppdaterar resurser granskar Azure Policy begäran. När principdefinitionseffekten är [Lägg till](./effects.md#deny) eller [DeployIfNotExists](./effects.md#deployifnotexists)ändrar principen begäran eller lägger till den. När principdefinitionseffekten är [Audit](./effects.md#audit) eller [AuditIfNotExists](./effects.md#auditifnotexists)skapas en aktivitetsloggpost. Och när den principdefinitionseffekten är [Neka](./effects.md#deny)stoppar princip skapandet eller ändringen av begäran.

Dessa resultat är exakt som önskas när du vet att principen är korrekt definierad. Det är dock viktigt att validera en ny princip fungerar som avsett innan den kan ändra eller blockera arbete. Valideringen måste säkerställa att endast de avsedda resurserna bedöms vara icke-kompatibla och inga kompatibla resurser inkluderas felaktigt (så kallat _falskt positivt)_ i resultaten.

Den rekommenderade metoden för att validera en ny principdefinition är följande:

- Noggrant definiera din policy
- Granska dina befintliga resurser
- Granska nya eller uppdaterade resursbegäranden
- Distribuera din princip till resurser
- Kontinuerlig övervakning

## <a name="tightly-define-your-policy"></a>Noggrant definiera din policy

Det är viktigt att förstå hur affärsprincipen implementeras som en principdefinition och relationen mellan Azure-resurser och andra Azure-tjänster. Det här steget uppnås genom [att identifiera kraven](../tutorials/create-custom-policy-definition.md#identify-requirements) och bestämma [resursegenskaperna](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Men det är också viktigt att se bortom den snäva definitionen av din affärspolitik. Har din princip tillstånd till exempel "Alla virtuella datorer måste ..."? Hur är det med andra Azure-tjänster som använder virtuella datorer, till exempel HDInsight eller AKS? När vi definierar en politik måste vi överväga hur denna politik påverkar resurser som används av andra tjänster.

Därför bör principdefinitionerna vara så noggrant definierade och fokuserade på de resurser och egenskaper som du behöver utvärdera för efterlevnad som möjligt.

## <a name="audit-existing-resources"></a>Granska befintliga resurser

Innan du vill hantera nya eller uppdaterade resurser med den nya principdefinitionen är det bäst att se hur den utvärderar en begränsad delmängd av befintliga resurser, till exempel en testresursgrupp. Använd [tvingande läge](./assignment-structure.md#enforcement-mode)
_Inaktiverat_ (DoNotEnforce) på principtilldelningen för att förhindra att [effekten](./effects.md) utlöses eller aktivitetsloggposter skapas.

Det här steget ger dig en chans att utvärdera efterlevnadsresultaten för den nya principen på befintliga resurser utan att påverka arbetsflödet. Kontrollera att inga kompatibla resurser är markerade som icke-kompatibla (_falskt positiva_) och att alla resurser som du förväntar dig inte är kompatibla är korrekt markerade.
När den första delmängden av resurser har validerats som förväntat expanderar du långsamt utvärderingen till alla befintliga resurser.

Att utvärdera befintliga resurser på detta sätt ger också möjlighet att åtgärda resurser som inte uppfyller kraven innan den nya principen genomförs fullt ut. Den här rensningen kan göras manuellt eller genom en [reparationsuppgift](../how-to/remediate-resources.md) om principdefinitionseffekten är _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Granska nya eller uppdaterade resurser

När du har validerat den nya principdefinitionen rapporterar korrekt om befintliga resurser är det dags att titta på policyns inverkan när resurser skapas eller uppdateras. Om principdefinitionen stöder effektparametrisering använder du [Granska](./effects.md#audit). Med den här konfigurationen kan du övervaka skapande och uppdatering av resurser för att se om den nya principdefinitionen utlöser en post i Azure Activity-loggen för en resurs som inte är kompatibel utan att påverka befintligt arbete eller begäranden.

Vi rekommenderar att du både uppdaterar och skapar nya resurser _Audit_ som matchar principdefinitionen för att se att granskningseffekten utlöses korrekt när det förväntas. Var på jakt efter resursbegäranden som inte ska påverkas av den nya principdefinitionen som utlöser _granskningseffekten._
Dessa påverkade resurser är ett annat exempel på _falska positiva identifieringar_ och måste fastställas i principdefinitionen innan den genomförs fullt ut.

I händelse av att principdefinitionen ändras i det här skedet av testningen rekommenderas att valideringsprocessen börjar med granskningen av befintliga resurser. En ändring av principdefinitionen för ett _falskt positivt_ på nya eller uppdaterade resurser kommer sannolikt också att påverka befintliga resurser.

## <a name="deploy-your-policy-to-resources"></a>Distribuera din princip till resurser

När du har slutfört valideringen av den nya principdefinitionen med både befintliga resurser och nya eller uppdaterade resursbegäranden börjar du implementera principen. Vi rekommenderar att du skapar principtilldelningen för den nya principdefinitionen till en delmängd av alla resurser först, till exempel en resursgrupp. När du har validerat den första distributionen utökar du principens omfattning till bredare och bredare nivåer, till exempel prenumerationer och hanteringsgrupper. Den här expansionen uppnås genom att ta bort tilldelningen och skapa en ny i målomfången tills den har tilldelats hela omfattningen av resurser som är avsedda att omfattas av den nya principdefinitionen.

Om resurser finns som ska undantas från den nya principdefinitionen under distributionen, adresserar du dem på något av följande sätt:

- Uppdatera principdefinitionen så att den blir tydligare för att minska oavsiktlig påverkan
- Ändra principtilldelningens omfattning (genom att ta bort och skapa en ny tilldelning)
- Lägga till resursgruppen i undantagslistan för principtilldelningen

Alla ändringar av omfattningen (nivå eller undantag) bör valideras fullständigt och kommuniceras med dina säkerhets- och efterlevnadsorganisationer för att säkerställa att det inte finns några luckor i täckningen.

## <a name="monitor-your-policy-and-compliance"></a>Övervaka din policy och efterlevnad

Att implementera och tilldela principdefinitionen är inte det sista steget. Övervaka kontinuerligt [efterlevnadsnivån](../how-to/get-compliance-data.md) för resurser till din nya principdefinition och konfigurera lämpliga [Azure Monitor-aviseringar och meddelanden](../../../azure-monitor/platform/alerts-overview.md) för när icke-kompatibla enheter identifieras. Vi rekommenderar också att du utvärderar principdefinitionen och relaterade tilldelningar på schemalagd basis för att validera principdefinitionen uppfyller affärsprinciper och efterlevnadsbehov. Principer bör tas bort om det inte längre behövs. Principer behöver också uppdateras då och då när de underliggande Azure-resurserna utvecklas och lägger till nya egenskaper och funktioner.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [principdefinitionsstrukturen](./definition-structure.md).
- Läs mer om [principtilldelningsstrukturen](./assignment-structure.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).