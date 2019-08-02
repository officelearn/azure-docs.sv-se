---
title: Säkerhets kompatibilitet med Azure Policy och Azure-ritningar
description: Säkerställa efterlevnad och framtvinga säkerhet med Azure Policy och Azure-ritningar för myndigheter i Australien som är relaterade till ASD-ISM och viktiga 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571749"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Säkerhets kompatibilitet med Azure Policy och Azure-ritningar

Utmaningen med att verkställa styrning i din IT-miljö, oavsett om det är en lokal, moln intern eller hybrid miljö, finns för alla organisationer. Ett robust tekniskt styrnings ramverk måste vara på plats för att säkerställa att din Microsoft Azure miljö överensstämmer med design-, reglerings-och säkerhets krav.

För de australiska myndigheterna i myndigheter är de viktiga kontroller att tänka på när de bedömer att risken är i den [australiska cyberhot Security Center (ACSC) information Security-hand boken](https://acsc.gov.au/infosec/ism/index.htm) (ISM). Majoriteten av kontrollerna som beskrivs i ISM-processen kräver att tekniska styrnings programmet hanteras och tillämpas effektivt. Det är viktigt att du har rätt verktyg för att utvärdera och tillämpa konfigurationen i dina miljöer.

Microsoft Azure erbjuder två kostnads fria tjänster som hjälper dig med dessa utmaningar, Azure Policy och Azure-ritningar.

## <a name="azure-policy"></a>Azure Policy

Azure Policy möjliggör tillämpning av de tekniska elementen i en organisations IT-styrning. Azure Policy innehåller ett ständigt växande bibliotek med inbyggda principer. Varje princip tillämpar regler och effekter på de riktade Azure-resurserna.

När en princip har tilldelats till resurser kan den övergripande efterlevnaden av principen utvärderas och åtgärdas om det behövs.

Det här biblioteket med inbyggda Azure-principer gör det möjligt för en organisation att snabbt verkställa de typer av kontroller som finns i ACSC-ISM. Exempel på kontroller är:

* Övervaka virtuella datorer för saknade system uppdateringar
* Granska konton med utökade behörigheter för Multi-Factor Authentication
* Identifiera okrypterade SQL-databaser
* Övervaka användningen av anpassad rollbaserad åtkomst kontroll i Azure (RBAC)
* Begränsa de Azure-regioner som resurser kan skapas i

Om styrnings-eller reglerings kontroller inte uppfylls av en inbyggd Azure Policy definition kan en anpassad definition skapas och tilldelas. Alla Azure Policy definitioner definieras i JSON och följer en standard [definitions struktur](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure). Befintliga Azure Policys definitioner kan också dupliceras och användas för att bilda grunden för en anpassad princip definition.

Genom att tilldela enskilda Azure-principer till resurser, särskilt i komplexa miljöer eller i miljöer med strikta reglerings krav, kan du skapa stora kostnader för dina administratörer. För att hjälpa till med dessa utmaningar kan en uppsättning Azure-principer grupperas tillsammans för att bilda ett Azure Policy initiativ. Princip initiativ används för att kombinera relaterade Azure-principer som, när de tillämpas tillsammans som en grupp, utgör grunden för ett bestämt mål för säkerhet eller efterlevnad. Microsoft lägger till inbyggda Azure Policy initiativ definitioner, inklusive definitioner som är utformade för att uppfylla särskilda regler för krav:

![Initiativ för regelefterlevnad](media/regulatory-initiatives.png)

Alla Azure-principer och-initiativ tilldelas till ett tilldelnings omfång. Det här omfånget definieras antingen i Azure-prenumerationen, i Azure-hanterings gruppen eller på Azures resurs grupps nivåer. När de nödvändiga Azure-principerna eller princip initiativen har tilldelats, kan en organisation tillämpa konfigurations kraven för alla nya Azure-resurser.

Att tilldela en ny Azure Policy eller initiativ påverkar inte befintliga Azure-resurser. Azure Policy kan. Du kan dock låta en organisation se efterlevnaden av befintliga Azure-resurser. Alla resurser som har identifierats som icke-kompatibla kan åtgärdas i organisationens eget gottfinnande

### <a name="azure-policy-and-initiatives-in-action"></a>Azure Policy och initiativ i praktiken

De tillgängliga inbyggda Azure Policy-och initiativ definitionerna finns under noden definition i avsnittet princip i Azure Portal:

![Inbyggda Azure Policys definitioner](media/policy-definitions.png)

Med hjälp av biblioteket med inbyggda definitioner kan du snabbt söka efter principer som uppfyller ett organisations krav, granska princip definitionen och tilldela principen till lämpliga resurser. Till exempel kräver ISM Multi-Factor Authentication (MFA) för alla privilegierade användare och för alla användare som har åtkomst till viktiga data centraler. I Azure Policy kan du söka efter "MFA" bland Azure Policy-definitionerna:

![Azure MFA-principer](media/mfa-policies.png)

När en lämplig princip har identifierats tilldelar du principen till önskad omfattning. Om det inte finns någon inbyggd princip som uppfyller dina krav kan du duplicera den befintliga principen och göra önskade ändringar:

![Duplicera befintliga Azure Policy](media/duplicate-policy.png)

Microsoft tillhandahåller också en samling Azure Policy exempel på [GitHub](https://github.com/Azure/azure-policy) som en snabb start för att bygga anpassade Azure-principer. Dessa princip exempel kan kopieras direkt till Azure Policy redigeraren i Azure Portal.

När du skapar Azure Policy initiativ kan du sortera listan över tillgängliga princip definitioner, både inbyggda och anpassade, och lägga till de nödvändiga definitionerna.

Du kan till exempel söka igenom listan över tillgängliga Azure Policy definitioner för alla principer som är relaterade till virtuella Windows-datorer. Sedan kan du använda de här definitionerna till ett initiativ som utformats för att genomdriva rekommenderade metoder för virtuell dator härdning:

![Lista över Azure-principer](media/initiative-definitions.png)

När du tilldelar ett Azure Policy-eller princip initiativ till en tilldelnings omfattning, är det möjligt att du undantar Azure-resurser från effekterna av principerna genom att undanta Azure-Hanteringsgrupper eller Azures resurs grupper.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Utvärdering av efterlevnad och efterlevnad i real tid

Azure Policy efterlevnad av Azure-resurser i omfånget sker när följande villkor uppfylls:

* När ett Azure Policy eller Azure Policy initiativ tilldelas
* När omfånget för en befintlig Azure Policy eller ett initiativ ändras
* På begäran via API upp till högst 10 genomsökningar per timme
* Var 24: e timme – standard beteendet

En sökning efter principer för en enda Azure-resurs sker 15 minuter efter att en ändring har gjorts i resursen.

En översikt över Azure Policy kompatibilitet för resurser kan granskas inom Azure Portal via instrument panelen för policyn för efterlevnad:

![Azure Policy regelefterlevnad](media/simple-compliance.png)

Bilden övergripande kompatibilitet i procent är en sammanställd del av alla distribuerade resurser i omfånget mot alla dina tilldelade Azure-principer. På så sätt kan du identifiera resurserna i en miljö som inte är kompatibla och som du kan använda för att åtgärda resurserna på bästa sätt.

Instrument panelen för policy efterlevnad innehåller också ändrings historiken för varje resurs. Om en resurs identifieras som att den inte längre är kompatibel med den tilldelade principen, och automatisk reparation inte är aktive rad, kan du se vem som gjort ändringen, vad som ändrades och när ändringarna gjordes i resursen.

## <a name="azure-blueprints"></a>Azure Blueprint

Azure-ritningar utökar funktionerna i Azure Policy genom att kombinera dem med:

* Azure RBAC
* Resurs grupper i Azure
* [Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

Med ritningar kan du skapa miljö design som distribuerar Azure-resurser från Resource Manager-mallar, konfigurerar RBAC och tillämpar och granskar konfigurationen genom att tilldela Azure Policy. Skisser formar en redigerbar och omdistributions bar miljö mall. När skissen har skapats kan den tilldelas till en Azure-prenumeration. När du har tilldelat skapas alla Azure-resurser som definierats i skissen och de Azure-principer som tillämpas. Distribution och konfiguration av resurser som definierats i en Azure Blueprint kan övervakas från Azure Blueprint-konsolen i Azure Portal.

Azure-ritningar som har redigerats måste publiceras om i Azure Portal. Varje gången en skiss återpubliceras, ökar versions numret för skissen. Med versions numret kan du avgöra vilken version av en skiss som har distribuerats till en organisations Azure-prenumerationer. Om du vill kan du uppdatera den aktuella tilldelade versionen av skissen till den senaste versionen.

Resurser som distribueras med hjälp av en Azure Blueprint kan konfigureras med [Azure resurs lås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) vid tidpunkten för distributionen. Resurs lås förhindrar att resurser ändras eller tas bort av misstag.

Microsoft utvecklar Azure Blueprint mallar för en rad olika branscher och reglerings krav. Det aktuella biblioteket med tillgängliga Azure Blueprint-definitioner kan visas på sidan Azure Portal eller på [handlingsplan för säkerhet och efterlevnad i Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) sidan i tjänst förtroende portalen.

### <a name="azure-blueprint-artifacts"></a>Azure Blueprint artefakter

Om du vill skapa en Azure Blueprint kan du börja med en tom formgivningsmall eller använda en av de befintliga exempel ritningarna som en start punkt. Du kan lägga till artefakter till den skiss som ska konfigureras som en del av distributionen:

![Azure Blueprint artefakter](media/blueprint-artifacts.png)

Dessa artefakter kan omfatta Azures resurs grupp och resurser och associerade Azure Policy-och princip initiativ för att genomdriva den konfiguration som krävs för att din miljö ska uppfylla kraven, till exempel ISM-kontrollerna för system härdning.

Var och en av dessa artefakter kan också konfigureras med parametrar. Dessa värden anges när skissen har tilldelats en Azure-prenumeration och distribuerats. Parametrar gör det möjligt för en enda skiss att skapas och användas för att distribuera resurser till olika miljöer utan att behöva redigera den underliggande skissen.

Microsoft utvecklar Azure PowerShell-och CLI-cmdletar för att skapa och hantera Azure-ritningar med avsikten att en skiss kan underhållas och distribueras av en organisation via en CI/CD-pipeline.

## <a name="next-steps"></a>Nästa steg

I den här artikeln förklaras hur styrning och säkerhet kan tillämpas med Azure Policy och Azure-ritningar. Nu när du har fått en hög nivå kan du lära dig hur du använder varje tjänst i detalj:

* [Översikt över Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
* [Översikt över Azure-ritningar](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy-exempel](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Azure Policy exempel lager](https://github.com/Azure/azure-policy)
* [Azure Policy definitions struktur](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Azure Policy effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
