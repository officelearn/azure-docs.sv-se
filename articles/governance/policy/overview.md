---
title: Översikt över Azure Policy
description: Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 68005a9a07e7f081a646c75566a0be4046f078ad
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770590"
---
# <a name="what-is-azure-policy"></a>Vad är Azure Policy?

Azure Policy hjälper till att tillämpa organisationsstandarder och bedöma efterlevnad i stor skala. Genom instrumentpanelen för efterlevnad ger den en aggregerad vy för att utvärdera miljöns övergripande tillstånd, med möjlighet att öka detaljnivån till per resurs, per principgranularitet. Det bidrar också till att få dina resurser till efterlevnad genom massreparation för befintliga resurser och automatisk reparation för nya resurser.

Vanliga användningsfall för Azure Policy är implementering av styrning för resurskonsekvens, regelefterlevnad, säkerhet, kostnad och hantering. Principdefinitioner för dessa vanliga användningsfall är redan tillgängliga i din Azure-miljö som inbyggda för att enkelt komma igång.

## <a name="overview"></a>Översikt

Azure Policy utvärderar resurser i Azure genom att jämföra egenskaperna för dessa resurser med affärsregler. Dessa affärsregler, som beskrivs i [JSON-format,](./concepts/definition-structure.md)kallas [principdefinitioner](#policy-definition). För att förenkla hanteringen kan flera affärsregler grupperas tillsammans för att bilda ett [politiskt initiativ](#initiative-definition) (kallas ibland _policySet_). När dina affärsregler har [utformats tilldelas](#assignments) principdefinitionen eller initiativet till alla resurser som Azure stöder, till exempel [hanteringsgrupper,](../management-groups/overview.md)prenumerationer, [resursgrupper](../../azure-resource-manager/management/overview.md#resource-groups)eller enskilda resurser. Tilldelningen gäller för alla resurser som [omfattas](../../azure-resource-manager/management/overview.md#understand-scope) av tilldelningen.
Subscopes kan uteslutas, om det behövs.

Azure Policy använder ett [JSON-format](./concepts/definition-structure.md) för att skapa den logik som utvärderingen använder för att avgöra om en resurs är kompatibel eller inte. Definitioner inkluderar metadata och principregeln. Den definierade regeln kan använda funktioner, parametrar, logiska operatorer, villkor och [egenskapsalias](./concepts/definition-structure.md#aliases) för att matcha exakt det scenario du vill ha. Principregeln avgör vilka resurser i tilldelningens omfattning som utvärderas.

### <a name="understand-evaluation-outcomes"></a>Förstå utvärderingsresultat

Resurser utvärderas vid specifika tidpunkter under resurslivscykeln, livscykeln för principtilldelning och för regelbunden pågående efterlevnadsutvärdering. Följande är de tider eller händelser som gör att en resurs utvärderas:

- En resurs skapas, uppdateras eller tas bort i ett scope med en principtilldelning.
- En princip eller ett initiativ har nyligen tilldelats ett scope.
- En princip eller ett initiativ som redan har tilldelats ett scope uppdateras.
- Under standardutvärderingscykeln, som sker en gång var 24:e timme.

Detaljerad information om när och hur utvärdering av principen sker finns i [Utvärderingsutlösare](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Kontrollera svaret på en utvärdering

Affärsregler för hantering av icke-kompatibla resurser varierar kraftigt mellan organisationer. Exempel på hur en organisation vill att plattformen ska svara på en resurs som inte är klagomålsresurs är:

- Neka resursändringen
- Logga ändringen till resursen
- Ändra resursen före ändringen
- Ändra resursen efter ändringen
- Distribuera relaterade kompatibla resurser

Azure Policy gör var och en av dessa affärssvar möjliga genom tillämpning av [effekter](./concepts/effects.md). Effekter anges i **principregeldelen** av [principdefinitionen](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Åtgärda icke-kompatibla resurser

Även om dessa effekter främst påverkar en resurs när resursen skapas eller uppdateras, stöder Azure Policy också hantera befintliga icke-kompatibla resurser utan att behöva ändra den resursen. Mer information om hur du gör befintliga resurser kompatibla finns i [åtgärda resurser](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Policy är från Build 2018. För nedladdning av bilder eller video besöker du sidan om att [styra Azure-miljön via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) på Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Komma igång

### <a name="azure-policy-and-rbac"></a>Azure-principen och RBAC

Det finns några viktiga skillnader mellan Azure Policy och rollbaserad åtkomstkontroll (RBAC). Azure Policy utvärderar tillståndet genom att undersöka egenskaper för resurser som representeras i Resource Manager och egenskaper för vissa resursleverantörer. Azure Policy begränsar inte åtgärder (kallas även _åtgärder_). Azure Policy säkerställer att resurstillståndet är kompatibelt med dina affärsregler utan att bry sig om vem som har gjort ändringen eller vem som har behörighet att göra en ändring.

RBAC fokuserar på att hantera [användaråtgärder](../../role-based-access-control/resource-provider-operations.md) i olika omfattningar. Om kontroll av en åtgärd krävs är RBAC rätt verktyg att använda. Även om en person har åtkomst till att utföra en åtgärd, om resultatet är en icke-kompatibel resurs, blockerar Azure Policy fortfarande skapa eller uppdatera.

Kombinationen av RBAC och Azure Policy ger fullständig scopekontroll i Azure.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-behörigheter i Azure Policy

Azure Policy har flera behörigheter, som kallas åtgärder, i två olika resursprovidrar:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Många inbyggda roller beviljar behörighet till Azure Policy-resurser. Rollen **Resursprincipdeltagare** innehåller de flesta Azure-principåtgärder. **Ägare** har fullständiga behörigheter. Både **Contributor** och **Reader** har åtkomst till alla _lästa_ Azure-principåtgärder. **Deltagaren** kan utlösa resurssanering, men kan inte _skapa_ definitioner eller tilldelningar.

Om ingen av de inbyggda rollerna har de behörigheter som krävs skapar du en [anpassad roll](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Den hanterade identiteten för en **deployIfNotExists** principtilldelning behöver tillräckligt med behörighet för att skapa eller uppdatera resurser som ingår i mallen. Mer information finns i [Konfigurera principdefinitioner för reparation](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Resurser som omfattas av Azure-principen

Azure Policy utvärderar alla resurser i Azure. För vissa resursleverantörer som [Gästkonfiguration](./concepts/guest-configuration.md), [Azure Kubernetes Service](../../aks/intro-kubernetes.md)och [Azure Key Vault](../../key-vault/key-vault-overview.md)finns det en djupare integrering för hantering av inställningar och objekt. Mer information finns i [Resursproviderlägen](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Rekommendationer för principhantering

Här följer några råd och tips att tänka på:

- Börja med en spårningsseffekt i stället för en nekandeeffekt för att spåra den påverkan principdefinitionen får på resurserna i miljön. Om du redan har skript på plats för att automatiskt skala dina program kan dessa automatiserade uppgifter eventuellt blockeras om du anger en nekandeeffekt.

- Ta hänsyn till på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att du skapar definitioner på högre nivåer, som på hanteringsgrupps- eller prenumerationsnivå. Skapa sedan tilldelningen på nästa underordnade nivå. Om du skapar en definition för en hanteringsgrupp, kan tilldelningen begränsas till en prenumeration eller resursgrupp inom den hanteringsgruppen.

- Vi rekommenderar att du skapar och tilldelar initiativdefinitioner även för en enskild principdefinition.
  Om du som exempel har principdefinitionen _policyDefA_, så skapar du den under initiativdefinitionen _initiativeDefC_. Om du skapar en annan principdefinition senare för _policyDefB_ med mål som liknar dem för _policyDefA_, kan du lägga till den under _initiativeDefC_ och spåra dem tillsammans.

- När du har skapat en initiativtilldelning blir principdefinitioner som lagts till i initiativet också en del av de initiativtilldelningarna.

- När en initiativtilldelning utvärderas, utvärderas även alla principer inom initiativet.
  Om du behöver utvärdera en princip individuellt är det bättre att inte inkludera den i ett initiativ.

## <a name="azure-policy-objects"></a>Azure-principobjekt

### <a name="policy-definition"></a>Definition av princip

Resan med att skapa och implementera en princip i Azure Policy börjar med skapandet av en principdefinition. Varje principdefinition har villkor för när den ska tillämpas. Och den har en definierad effekt som träder ikraft om villkoren är uppfyllda.

Vi erbjuder flera inbyggda principer som är tillgängliga för dig som standard i Azure Policy. Ett exempel:

- **Tillåtna lagringskontoSkuser** (Neka): Avgör om ett lagringskonto som distribueras ligger inom en uppsättning SKU-storlekar. Effekten är att neka alla lagringskonton som inte överensstämmer med uppsättningen definierade SKU-storlekar.
- **Tillåten resurstyp** (Neka): Definierar de resurstyper som du kan distribuera. Effekten är att neka alla resurser som inte finns på den definierade listan.
- **Tillåtna platser** (Neka): Begränsar tillgängliga platser för nya resurser. Effekten används för att genomdriva kraven på geo-efterlevnad.
- **Tillåtna SKU:er** för virtuella datorer (Nekad): Anger en uppsättning SKU:er för virtuella datorer som du kan distribuera.
- **Lägg till en tagg i resurser** (Ändra): Använder en obligatorisk tagg och dess standardvärde om den inte anges av distributionsbegäran.
- **Lägg till tagg och dess standardvärde** (Lägg till): Framtvingar en obligatorisk tagg och dess värde till en resurs.
- **Inte tillåtna resurstyper** (Neka): Förhindrar att en lista över resurstyper distribueras.

För att implementera dessa principdefinitioner (både inbyggda och anpassade definitioner) måste du tilldela dem. Du kan tilldela de här principerna via Azure Portal, PowerShell eller Azure CLI.

Principutvärdering sker med flera olika åtgärder, till exempel tilldelning av principer eller principuppdateringar. En fullständig lista finns i [Principutvärderingsutlösare](./how-to/get-compliance-data.md#evaluation-triggers).

Läs mer om principdefinitionernas strukturer i artikeln, [struktur för principdefinitioner](./concepts/definition-structure.md).

Principparametrar underlättar hanteringen av principer genom att minska antalet principdefinitioner du måste skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Sedan kan du återanvända den principdefinitionen för olika scenarier. Det gör du genom att ange olika värden när du tilldelar principdefinitionen. Till exempel ange du en uppsättning platser för en prenumeration.

Parametrar definieras när du skapar en principdefinition. När en parameter definieras ges den ett namn och eventuellt ett givet värde. Du kan till exempel definiera en parameter för en princip med titeln _plats_. Sedan kan du ge den olika värden som _EastUS_ eller _WestUS_ när du tilldelar en princip.

Mer information om principparametrar finns i [Struktur för definitioner – parametrar](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Initiativdefinition

En initiativdefinition är en samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Initiativdefinitioner gör det enklare att hantera och tilldela principdefinitioner genom att de grupperar en uppsättning principer som ett enda objekt. Du kan till exempel skapa ett initiativ med titeln **Aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

> [!NOTE]
> SDK, till exempel Azure CLI och Azure PowerShell, använder egenskaper och parametrar med namnet **PolicySet** för att referera till initiativ.

Under det här initiativet skulle du ha principdefinitioner som dessa:

- **Övervaka okrypterade SQL-databaser i Security Center** – för att övervaka okrypterade SQL-databaser och -servrar.
- **Övervaka OS-säkerhetsproblem i Security Center** – för att övervaka servrar som inte uppfyller grundkonfigurationen.
- **Övervaka saknad Endpoint Protection i Security Center** – för att övervaka servrar utan en installerad Endpoint Protection-agent.

Precis som principparametrar underlättar initiativparametrar initiativhanteringen genom att minska redundansen. Initiativparametrar är parametrar som används av principdefinitionerna inom ett initiativ.

Ta till exempel scenariot där du har en initiativdefinition, **initiativeC**, med principdefinitionerna **policyA** och **policyB** som vardera förväntar sig olika typer av parametrar:

| Princip | Parameternamn |Parametertyp  |Obs! |
|---|---|---|---|
| principA | allowedLocations | matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
| principB | allowedSingleLocation |sträng |Den här parametern förväntar sig ett ord som värde eftersom parametertypen har definierats som en sträng |

I det här scenariot, när du definierar initiativparametrar för **initiativC**, har du tre alternativ:

- Använd parametrarna för principdefinitionerna i det här initiativet. I det här exemplet blir _allowedLocations_ och _allowedSingleLocation_ initiativparametrar för **initiativC**.
- Ange värden för parametrarna för principdefinitionerna i den här initiativdefinitionen. I det här exemplet kan du ange en lista över platser till **parametern policyA**– **allowedLocations** and **policyB's**parameter – **allowedSingleLocation**. Du kan också ange värden när du tilldelar det här initiativet.
- Ange en lista med alternativ _värden_ som kan användas när du tilldelar det här initiativet. När du tilldelar det här initiativet kan ärvda parametrarna från principdefinitionerna inom initiativet endast ha värden från den här listan.

När du skapar värdealternativ i en initiativdefinition kan du inte ange ett annat värde under initiativtilldelningen eftersom det inte ingår i listan.

### <a name="assignments"></a>Tilldelningar

En tilldelning är en principdefinition eller ett initiativ som har tilldelats att äga rum inom ett visst omfång. Det här omfånget kan vara allt från en [hanteringsgrupp](../management-groups/overview.md) till en enskild resurs. Termen _omfattning_ refererar till alla resurser, resursgrupper, prenumerationer eller hanteringsgrupper som definitionen har tilldelats. Tilldelningar ärvs av alla underordnade resurser. Den här designen innebär att en definition som tillämpas på en resursgrupp också tillämpas på resurser i resursgruppen. Du kan dock utesluta ett underscope från tilldelningen.

I prenumerationsomfånget kan du till exempel tilldela en definition som förhindrar att nätverksresurser skapas. Du kan undanta en resursgrupp i prenumerationen som är avsedd för nätverksinfrastruktur. Du beviljar därefter åtkomst till den här nätverksresursgruppen för användare som du litar på för att skapa nätverksresurser.

I ett annat exempel kanske du vill tilldela en resurstyp tillåt listdefinition på hanteringsgruppsnivå. Sedan kan du tilldela en mer tillåtande princip (som tillåter fler resurstyper) på en underordnad hanteringsgrupp eller till och med direkt på prenumerationer. Det här exemplet skulle dock inte fungera eftersom Azure Policy är ett explicit neka system. I stället måste du utesluta den underordnade hanteringsgruppen eller prenumerationen från tilldelningen på hanteringsgruppsnivå. Tilldela sedan den mer tillåtande definitionen på den underordnade hanteringsgruppen eller prenumerationsnivån. Om någon tilldelning resulterar i att en resurs nekas, är det enda sättet att tillåta resursen att ändra tilldelningen nekas.

Mer information om hur du anger tilldelningar via portalen finns i [Skapa en principtilldelning för att identifiera icke-kompatibla resurser i din Azure-miljö](assign-policy-portal.md). Steg för [PowerShell](assign-policy-powershell.md) och [Azure CLI](assign-policy-azurecli.md) är också tillgängliga.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximalt antal Azure-principobjekt

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure Policy och några av de centrala begreppen föreslår vi följande som nästa steg:

- [Granska principdefinitionsstrukturen](./concepts/definition-structure.md).
- [Tilldela en principdefinition med hjälp av portalen](./assign-policy-portal.md).
