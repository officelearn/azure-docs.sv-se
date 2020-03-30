---
title: Arbetsflöden för att utforma princip som kod
description: Lär dig att utforma arbetsflöden för att distribuera dina Azure-principdefinitioner som kod och validera resurser automatiskt.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267265"
---
# <a name="design-policy-as-code-workflows"></a>Arbetsflöden för att utforma princip som kod

När du går vidare med din resa med molnstyrning, vill du gå från att manuellt hantera varje principdefinition i Azure-portalen eller genom de olika SDK:erna till något mer hanterbart och repeterbart i företagsskala. Två av de dominerande metoderna för att hantera system i stor skala i molnet är:

- Infrastruktur som kod: Bruket att behandla innehållet som definierar dina miljöer, allt från Resource Manager-mallar till Azure-principdefinitioner till Azure Blueprints, som källkod.
- DevOps: Föreningen av människor, processer och produkter för att möjliggöra kontinuerlig leverans av värde till våra slutanvändare.

Policy som kod är en kombination av dessa idéer. I huvudsak hålla dina principdefinitioner i källkontroll och när en ändring görs, testa och validera den ändringen. Det bör dock inte vara omfattningen av principer som är inblandade i infrastruktur som kod eller DevOps.

Valideringssteget bör också vara en komponent i andra arbetsflöden för kontinuerlig integrering eller kontinuerlig distribution. Exempel på detta är att distribuera en programmiljö eller virtuell infrastruktur. Genom att göra Azure Policy-validering till en tidig komponent i bygg- och distributionsprocessen upptäcker program- och driftteamen om deras ändringar inte är klagomål, långt innan det är för sent och de försöker distribuera i produktion.

## <a name="workflow-overview"></a>Översikt över arbetsflöden

Det rekommenderade allmänna arbetsflödet för princip som kod ser ut så här:

![Översikt över princip som kodarbetsflöde](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Skapa och uppdatera principdefinitioner

Principdefinitionerna skapas med JSON och lagras i källkontrollen. Varje princip har sin egen uppsättning filer, till exempel parametrar, regler och miljöparametrar, som ska lagras i samma mapp. Följande struktur är ett rekommenderat sätt att behålla principdefinitionerna i källkontrollen.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

När en ny princip läggs till eller en befintlig uppdateras bör arbetsflödet automatiskt uppdatera principdefinitionen i Azure. Testning av den nya eller uppdaterade principdefinitionen kommer i ett senare steg.

### <a name="create-and-update-initiative-definitions"></a>Skapa och uppdatera initiativdefinitioner

På samma sätt har initiativ en egen JSON-fil och relaterade filer som ska lagras i samma mapp. Initiativdefinitionen kräver att principdefinitionen redan finns, så kan inte skapas eller uppdateras förrän källan för principen har uppdaterats i källkontrollen och sedan uppdaterats i Azure. Följande struktur är ett rekommenderat sätt att hålla dina initiativdefinitioner i källkontroll:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Precis som principdefinitioner bör arbetsflödet uppdateras automatiskt när du lägger till eller uppdaterar ett befintligt initiativ. Testning av den nya eller uppdaterade initiativdefinitionen kommer i ett senare steg.

### <a name="test-and-validate-the-updated-definition"></a>Testa och validera den uppdaterade definitionen

När automatiseringen har tagit dina nyligen skapade eller uppdaterade princip- eller initiativdefinitioner och gjort uppdateringen till objektet i Azure är det dags att testa de ändringar som har gjorts. Antingen den eller de initiativ som den är en del av bör sedan tilldelas resurser i miljön längst bort från produktionen. Den här miljön är vanligtvis _Dev_.

Tilldelningen bör använda [enforcementMode](./assignment-structure.md#enforcement-mode) för _inaktiverad_ så att resursskapande och uppdateringar inte blockeras, men att befintliga resurser fortfarande granskas för efterlevnad av den uppdaterade principdefinitionen. Även med enforcementMode rekommenderas tilldelningsomfånget antingen är en resursgrupp eller en prenumeration som används specifikt för att validera principer.

> [!NOTE]
> Även om tvingande läge är till hjälp, är det inte en ersättning för att noggrant testa en principdefinition under olika förhållanden. Principdefinitionen bör testas `PUT` med `PATCH` och REST API-anrop, kompatibla och icke-kompatibla resurser och kantfall som en egenskap som saknas i resursen.

När tilldelningen har distribuerats använder du princip-SDK för att [hämta efterlevnadsdata](../how-to/get-compliance-data.md) för den nya tilldelningen. Den miljö som används för att testa principer och tilldelningar bör ha både kompatibla och icke-kompatibla resurser. Som ett bra enhetstest för kod, vill du testa att resurser är som förväntat och att du också inte har några falskt positiva eller falska negativ. Om du bara testar och validerar för det du förväntar dig kan det finnas oväntade och oidentifierade effekter från principen. Mer information finns i [Utvärdera effekten av en ny Azure-princip](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Aktivera reparationsuppgifter

Om valideringen av tilldelningen uppfyller förväntningarna är nästa steg att validera reparation.
Principer som använder antingen [deployIfNotExists](./effects.md#deployifnotexists) eller [ändra](./effects.md#modify) kan omvandlas till en reparationsaktivitet och korrigera resurser från ett icke-kompatibelt tillstånd.

Det första steget för att göra detta är att ge principtilldelningen den rolltilldelning som definierats i principdefinitionen. Den här rolltilldelningen ger principtilldelningen hanterad identitet tillräckligt med rättigheter för att göra de nödvändiga ändringarna för att göra resursen kompatibel.

När principtilldelningen har lämpliga rättigheter använder du SDK för princip för att utlösa en reparationsaktivitet mot en uppsättning resurser som är kända för att inte vara kompatibla. Tre tester bör slutföras mot dessa åtgärdade uppgifter innan du fortsätter:

- Verifiera att reparationsuppgiften har slutförts
- Kör principutvärdering för att se att resultaten för regelefterlevnad uppdateras som förväntat
- Kör ett miljöenhetstest mot resurserna direkt för att verifiera att deras egenskaper har ändrats

Testa både uppdaterade resultat principutvärdering och miljön direkt ge bekräftelse på att reparationsuppgifterna ändrat vad som förväntades och att principdefinitionen såg efterlevnadsändringen som förväntat.

### <a name="update-to-enforced-assignments"></a>Uppdatera till verkställda tilldelningar

När alla valideringsgrindar har slutförts uppdaterar du tilldelningen så att den använder **enforcementMode** för _aktiverad_. Denna förändring bör inledningsvis göras i samma miljö långt från produktionen. När den miljön har validerats som arbetssätt som förväntat bör ändringen begränsas till att omfatta nästa miljö och så vidare tills principen distribueras till produktionsresurser.

## <a name="process-integrated-evaluations"></a>Processintegrerade utvärderingar

Det allmänna arbetsflödet för princip som kod är för att utveckla och distribuera principer och initiativ till en miljö i stor skala. Principutvärdering bör dock vara en del av distributionsprocessen för alla arbetsflöden som distribuerar eller skapar resurser i Azure, till exempel distribuera program eller köra Resource Manager-mallar för att skapa infrastruktur.

I dessa fall, när programmet eller infrastrukturdistributionen har gjorts till en testprenumeration eller resursgrupp, bör principutvärdering göras för den scopekontrollvalideringen av alla befintliga principer och initiativ. Även om de kan konfigureras som **enforcementMode** _inaktiveras_ i en sådan miljö, är det bra att veta tidigt om ett program eller infrastrukturdistribution bryter mot principdefinitioner tidigt. Den här principutvärderingen bör därför vara ett steg i dessa arbetsflöden och misslyckas med distributioner som skapar resurser som inte är kompatibla.

## <a name="review"></a>Granska

Den här artikeln beskriver det allmänna arbetsflödet för princip som kod och även där principutvärdering bör ingå i andra distributionsarbetsflöden. Det här arbetsflödet kan användas i alla miljöer som stöder skriptsteg och automatisering baserat på utlösare.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [principdefinitionsstrukturen](./definition-structure.md).
- Läs mer om [principtilldelningsstrukturen](./assignment-structure.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).