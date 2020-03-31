---
title: Översikt över Azure Policy
description: Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241530"
---
# <a name="what-is-azure-policy"></a>Vad är Azure Policy?

Styrning bekräftar att din organisation kan uppnå sina mål genom effektiv och ändamålsenlig användning av IT. Detta sker genom att tydlighet skapas mellan affärsmålen och IT-projekten.

Har ditt företag ett stort antal IT-problem som aldrig verkar bli lösta? God IT-styrning omfattar att planera dina initiativ och ange prioritet på en strategisk nivå för att underlätta hantering och förebyggande av problem. Det är för det här strategiska behovet som Azure Policy kommer in i bilden.

Azure Policy är en tjänst i Azure som används till att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal. Azure Policy uppfyller detta behov genom att utvärdera dina resurser för icke-kompatibilitet med tilldelade principer. Alla data som lagras av Azure Policy krypteras i vila.

Du kan till exempel ha en princip som endast tillåter en viss SKU-storlek för virtuella datorer i din miljö. När den här principen har implementerats utvärderas nya och befintliga standardresurser för kompatibilitet. Med rätt typ av princip kan befintliga resurser bli kompatibla. Senare i den här dokumentationen går vi igenom mer information om hur man skapar och implementerar principer med Azure Policy.

> [!IMPORTANT]
> Azure Policy:s kompatibilitetsutvärdering tillhandahålls nu för alla tilldelningar oavsett prisnivå. Om dina tilldelningar inte visar kompatibilitetsdata, ser du till att prenumerationen är registrerad med resursprovidern Microsoft.PolicyInsights.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Vad är skillnaden jämfört med RBAC?

Det finns några viktiga skillnader mellan Azure Policy och rollbaserad åtkomstkontroll (RBAC). RBAC fokuserar på användaråtgärder i olika omfång. Du kan läggas till deltagarrollen för en resursgrupp, så att du kan göra ändringar i den resursgruppen. Azure Policy fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Azure Policy styr egenskaper som typen eller platsen för en resurs. Till skillnad från RBAC är Azure Policy ett standardalternativ och explicit neka system.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-behörigheter i Azure Policy

Azure Policy har flera behörigheter, som kallas åtgärder, i två olika resursprovidrar:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Många inbyggda roller beviljar behörighet till Azure Policy-resurser. Rollen **Resursprincipdeltagare** innehåller de flesta Azure-principåtgärder. **Ägare** har fullständiga behörigheter. Både **Contributor** och **Reader** kan använda alla lästa Azure-principåtgärder, men **Deltagaren** kan också utlösa reparation.

Om ingen av de inbyggda rollerna har de behörigheter som krävs skapar du en [anpassad roll](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definition av princip

Resan med att skapa och implementera en princip i Azure Policy börjar med skapandet av en principdefinition. Varje principdefinition har villkor för när den ska tillämpas. Och den har en definierad effekt som träder ikraft om villkoren är uppfyllda.

Vi erbjuder flera inbyggda principer som är tillgängliga för dig som standard i Azure Policy. Ett exempel:

- **Tillåtna lagringskontoSku:** Avgör om ett lagringskonto som distribueras ligger inom en uppsättning SKU-storlekar. Effekten är att neka alla lagringskonton som inte överensstämmer med uppsättningen definierade SKU-storlekar.
- **Tillåten resurstyp:** Definierar de resurstyper som du kan distribuera. Effekten är att neka alla resurser som inte finns på den definierade listan.
- **Tillåtna platser**: Begränsar tillgängliga platser för nya resurser. Effekten används för att genomdriva kraven på geo-efterlevnad.
- **Tillåtna SKU:er för virtuella datorer:** Anger en uppsättning SKU:er för virtuella datorer som du kan distribuera.
- **Lägg till en tagg i resurser**: Använder en obligatorisk tagg och dess standardvärde om den inte anges av distributionsbegäran.
- **Framtvinga tagg och dess värde**: Framtvingar en obligatorisk tagg och dess värde till en resurs.
- **Inte tillåtna resurstyper**: Förhindrar att en lista över resurstyper distribueras.

För att implementera dessa principdefinitioner (både inbyggda och anpassade definitioner) måste du tilldela dem. Du kan tilldela de här principerna via Azure Portal, PowerShell eller Azure CLI.

Principutvärdering sker med flera olika åtgärder, till exempel tilldelning av principer eller principuppdateringar. En fullständig lista finns i [Principutvärderingsutlösare](./how-to/get-compliance-data.md#evaluation-triggers).

Läs mer om principdefinitionernas strukturer i artikeln, [struktur för principdefinitioner](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Principtilldelning

En principtilldelning är en principdefinition som tilldelas att äga rum inom ett specifikt område. Det här omfånget kan vara allt från en [hanteringsgrupp](../management-groups/overview.md) till en enskild resurs. Termen *omfattning* refererar till alla resurser, resursgrupper, prenumerationer eller hanteringsgrupper som principdefinitionen har tilldelats. Principtilldelningar ärvs av alla underordnade resurser. Den här designen innebär att en princip som används för en resursgrupp även används för resurserna i den resursgruppen. Du kan dock utesluta ett delområde från principtilldelningen.

Du kan till exempel tilldela en princip som förhindrar skapande av nätverksresurser i ett prenumerationsområde. Du kan undanta en resursgrupp i prenumerationen som är avsedd för nätverksinfrastruktur. Du beviljar därefter åtkomst till den här nätverksresursgruppen för användare som du litar på för att skapa nätverksresurser.

I ett annat exempel kanske du vill tilldela en resurstyp tillåt listprincip på hanteringsgruppsnivå. Och sedan tilldela en mer tillåtande princip (som tillåter fler resurstyper) för en underordnad hanteringsgrupp eller till och med direkt för prenumerationer. Det här exemplet fungerar dock inte eftersom principen är ett system för uttryckligt nekande. Du måste i stället utesluta den underordnade hanteringsgruppen eller prenumerationen från principtilldelningen på hanteringsgruppsnivån. Tilldela sedan den mer tillåtande principen på nivån för den underordnade hanteringsgruppen eller prenumerationen. Om en princip leder till att en resurs nekas är det enda sättet att tillåta resursen att ändra den nekande principen.

Mer information om att ange principdefinitioner och tilldelningar via portalen finns i [Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön](assign-policy-portal.md). Steg för [PowerShell](assign-policy-powershell.md) och [Azure CLI](assign-policy-azurecli.md) är också tillgängliga.

## <a name="policy-parameters"></a>Principparametrar

Principparametrar underlättar hanteringen av principer genom att minska antalet principdefinitioner du måste skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Sedan kan du återanvända den principdefinitionen för olika scenarier. Det gör du genom att ange olika värden när du tilldelar principdefinitionen. Till exempel ange du en uppsättning platser för en prenumeration.

Parametrar definieras när du skapar en principdefinition. När en parameter definieras ges den ett namn och eventuellt ett givet värde. Du kan till exempel definiera en parameter för en princip med titeln *plats*. Sedan kan du ge den olika värden som *EastUS* eller *WestUS* när du tilldelar en princip.

Mer information om principparametrar finns i [Struktur för definitioner – parametrar](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Initiativdefinition

En initiativdefinition är en samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Initiativdefinitioner gör det enklare att hantera och tilldela principdefinitioner genom att de grupperar en uppsättning principer som ett enda objekt. Du kan till exempel skapa ett initiativ med titeln **Aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

> [!NOTE]
> SDK, till exempel Azure CLI och Azure PowerShell, använder egenskaper och parametrar med namnet **PolicySet** för att referera till initiativ.

Under det här initiativet skulle du ha principdefinitioner som dessa:

- **Övervaka okrypterade SQL-databaser i Security Center** – för att övervaka okrypterade SQL-databaser och -servrar.
- **Övervaka OS-säkerhetsproblem i Security Center** – för att övervaka servrar som inte uppfyller grundkonfigurationen.
- **Övervaka saknad Endpoint Protection i Security Center** – för att övervaka servrar utan en installerad Endpoint Protection-agent.

## <a name="initiative-assignment"></a>Initiativtilldelning

Precis som en principtilldelning är en initiativtilldelning en initiativdefinition som tilldelats ett specifikt område. Initiativtilldelningar minskar behovet av att göra flera initiativdefinitioner för varje område. Det här omfånget kan också vara allt från en hanteringsgrupp till en enskild resurs.

Varje initiativ kan tilldelas till olika omfång. Ett initiativ kan tilldelas till båda **prenumeration A** och **prenumeration B**.

## <a name="initiative-parameters"></a>Initiativparametrar

Precis som principparametrar underlättar initiativparametrar initiativhanteringen genom att minska redundansen. Initiativparametrar är parametrar som används av principdefinitionerna inom ett initiativ.

Ta till exempel scenariot där du har en initiativdefinition, **initiativeC**, med principdefinitionerna **policyA** och **policyB** som vardera förväntar sig olika typer av parametrar:

| Princip | Parameternamn |Parametertyp  |Obs! |
|---|---|---|---|
| principA | allowedLocations | matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
| principB | allowedSingleLocation |sträng |Den här parametern förväntar sig ett ord som värde eftersom parametertypen har definierats som en sträng |

I det här scenariot, när du definierar initiativparametrar för **initiativC**, har du tre alternativ:

- Använd parametrarna för principdefinitionerna i det här initiativet. I det här exemplet blir *allowedLocations* och *allowedSingleLocation* initiativparametrar för **initiativC**.
- Ange värden för parametrarna för principdefinitionerna i den här initiativdefinitionen. I det här exemplet kan du ange en lista över platser till **principAs parameter – allowedLocations** och **policyB's parameter – allowedSingleLocation**. Du kan också ange värden när du tilldelar det här initiativet.
- Ange en lista med alternativ *värden* som kan användas när du tilldelar det här initiativet. När du tilldelar det här initiativet kan ärvda parametrarna från principdefinitionerna inom initiativet endast ha värden från den här listan.

När du skapar värdealternativ i en initiativdefinition kan du inte ange ett annat värde under initiativtilldelningen eftersom det inte ingår i listan.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximalt antal Azure-principobjekt

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Rekommendationer för principhantering

Här följer några råd och tips att tänka på:

- Börja med en spårningsseffekt i stället för en nekandeeffekt för att spåra den påverkan principdefinitionen får på resurserna i miljön. Om du redan har skript på plats för att automatiskt skala dina program kan dessa automatiserade uppgifter eventuellt blockeras om du anger en nekandeeffekt.

- Ta hänsyn till på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att du skapar definitioner på högre nivåer, som på hanteringsgrupps- eller prenumerationsnivå. Skapa sedan tilldelningen på nästa underordnade nivå. Om du skapar en definition för en hanteringsgrupp, kan tilldelningen begränsas till en prenumeration eller resursgrupp inom den hanteringsgruppen.

- Vi rekommenderar att du skapar och tilldelar initiativdefinitioner även för en enskild principdefinition.
  Om du som exempel har principdefinitionen *policyDefA*, så skapar du den under initiativdefinitionen *initiativeDefC*. Om du skapar en annan principdefinition senare för *policyDefB* med mål som liknar dem för *policyDefA*, kan du lägga till den under *initiativeDefC* och spåra dem tillsammans.

- När du har skapat en initiativtilldelning blir principdefinitioner som lagts till i initiativet också en del av de initiativtilldelningarna.

- När en initiativtilldelning utvärderas, utvärderas även alla principer inom initiativet.
  Om du behöver utvärdera en princip individuellt är det bättre att inte inkludera den i ett initiativ.

## <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Policy är från Build 2018. För nedladdning av bilder eller video besöker du sidan om att [styra Azure-miljön via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) på Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure Policy och några av de centrala begreppen föreslår vi följande som nästa steg:

- [Tilldela en principdefinition med hjälp av portalen](./assign-policy-portal.md).
- [Tilldela en principdefinition med Hjälp av Azure CLI](./assign-policy-azurecli.md).
- [Tilldela en principdefinition med PowerShell](./assign-policy-powershell.md).
