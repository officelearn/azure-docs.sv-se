---
title: Översikt över Azure Policy
description: Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 51fd0c625ad7e600d54999ddd86e5e49a7c4f14d
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249884"
---
# <a name="what-is-azure-policy"></a>Vad är Azure Policy?

IT-styrning säkerställer att din organisation ska kunna uppnå sina mål via effektiv användning av IT. Detta sker genom att tydlighet skapas mellan affärsmålen och IT-projekten.

Har ditt företag ett stort antal IT-problem som aldrig verkar bli lösta?
God IT-styrning omfattar att planera dina initiativ och ange prioritet på en strategisk nivå för att underlätta hantering och förebyggande av problem. Det är där Azure Policy kommer in.

Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal. Azure Policy gör detta genom att köra utvärderingar av dina resurser och söka efter sådana som inte är kompatibla med de principer som du har skapat. Du kan till exempel ha en princip som endast tillåter en viss SKU-storlek för virtuella datorer i din miljö. När den här principen har implementerats utvärderas den när resurser skapas och uppdateras samt över dina befintliga resurser. Senare i den här dokumentationen går vi igenom mer information om hur du skapar och implementera principer med Azure Policy.

> [!IMPORTANT]
> Azure Policy:s kompatibilitetsutvärdering tillhandahålls nu för alla tilldelningar oavsett prisnivå. Om dina tilldelningar inte visar kompatibilitetsdata, ser du till att prenumerationen är registrerad med resursprovidern Microsoft.PolicyInsights.

## <a name="how-is-it-different-from-rbac"></a>Vad är skillnaden jämfört med RBAC?

Det finns några viktiga skillnader mellan princip- och rollbaserad åtkomstkontroll (RBAC). RBAC fokuserar på användaråtgärder i olika omfång. Du kan till exempel läggas till i deltagarrollen för en resursgrupp i det önskade omfånget. Rollen gör att du kan göra ändringar i resursgruppen.
Principer fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Med hjälp av principer kan du till exempel styra vilka typer av resurser som kan etableras. Eller du kan begränsa på vilka platser resurser kan etableras. Till skillnad från RBAC har principsystemet ”tillåt” som standard och ”neka” måste anges uttryckligen.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-behörigheter i Azure Policy

Azure Policy har behörigheter som representeras som åtgärder i två olika resursprovidrar:

- [Microsoft.Authorization](../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsight](../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Flera av de inbyggda rollerna har olika behörighetsnivåer till Azure Policy-resurser, till exempel **säkerhetsadministratör**, som kan hantera principtilldelningar och definitioner men inte visa information om efterlevnad, och **läsare** , som kan läsa information om principtilldelningar och definitioner men inte göra ändringar eller visa information om efterlevnad. Medan **ägare** har fullständiga behörigheter har **deltagare** inga Azure Policy-behörigheter. Om du vill bevilja behörighet att visa information om principefterlevnad skapar du en [anpassad roll](../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definition av princip

Resan med att skapa och implementera en princip i Azure Policy börjar med skapandet av en principdefinition. Varje principdefinition har villkor för när den ska tillämpas. Och den har en tillhörande effekt som utförs om villkoren är uppfyllda.

Vi erbjuder några inbyggda principer som är tillgängliga för dig som standard i Azure Policy. Exempel:

- **Kräv SQL Server 12.0**: Den här principdefinitionen har villkor/regler för att kontrollera att alla SQL-servrar använder version 12.0. Dess effekt är att neka alla servrar som inte uppfyller dessa villkor.
- **Tillåtna SKU:er för lagringskonton**: Den här principdefinitionen har en uppsättning villkor/regler som avgör om ett lagringskonto som distribueras finns inom en uppsättning SKU-storlekar. Effekten är att neka alla lagringskonton som inte följer uppsättningen med definierade SKU-storlekar.
- **Tillåten resurstyp**: Den här principdefinitionen har en uppsättning villkor/regler som anger vilka resurstyper organisationen kan distribuera. Effekten är att neka alla resurser som inte finns på den definierade listan.
- **Tillåtna platser**: Med den här principen kan du begränsa vilka platser som kan anges när resurser distribueras i organisationen. Effekten används för att genomdriva kraven på geo-efterlevnad.
- **Tillåtna SKU:er för virtuella datorer**: Med den här principen kan du ange en uppsättning SKU:er för virtuella datorer som kan distribueras i organisationen.
- **Använd tagg med standardvärde**: Den här principen applicerar en obligatorisk tagg med ett standardvärde, om den inte anges av användaren.
- **Tvinga fram tagg med värde**: Den här principen tvingar fram en obligatorisk tagg och dess värde på en resurs.
- **Otillåtna resurstyper**: Med den här principen kan du ange vilka resurstyper som inte får distribueras i organisationen.

För att implementera dessa principdefinitioner (både inbyggda och anpassade definitioner) måste du tilldela dem. Du kan tilldela de här principerna via Azure Portal, PowerShell eller Azure CLI.

Tänk på att en principomvärdering görs cirka en gång i timmen, vilket innebär att om du gör ändringar i principdefinitionen efter tillämpning av principen (skapar en principtilldelning) omvärderas den över dina resurser inom en timme.

Läs mer om principdefinitionernas strukturer i artikeln, [struktur för principdefinitioner](policy-definition.md).

## <a name="policy-assignment"></a>Principtilldelning

En principtilldelning är en principdefinition som tilldelas att äga rum inom ett specifikt område. Omfånget kan vara allt från en [hanteringsgrupp](../azure-resource-manager/management-groups-overview.md) till en resursgrupp. Termen *område* avser alla resursgrupper, prenumerationer eller hanteringsgrupper som principdefinitionen har tilldelats. Principtilldelningar ärvs av alla underordnade resurser. Detta betyder att om en princip används för en resursgrupp så tillämpas den på alla resurser i den resursgruppen. Du kan dock utesluta ett delområde från principtilldelningen.

Du kan till exempel tilldela en princip som förhindrar skapande av nätverksresurser i ett prenumerationsområde. Men du kan undanta en resursgrupp i prenumerationen som är avsedd för nätverksinfrastruktur. Du beviljar åtkomst till den här nätverksresursgruppen till användare som du litar på för att skapa nätverksresurser.

I ett annat exempel kanske du vill tilldela en princip för tillåtna resurstyper på hanteringsgruppsnivån. Och sedan tilldela en mer tillåtande princip (som tillåter fler resurstyper) för en underordnad hanteringsgrupp eller till och med direkt för prenumerationer. Det här exemplet fungerar dock inte eftersom principen är ett system för uttryckligt nekande. Du måste i stället utesluta den underordnade hanteringsgruppen eller prenumerationen från principtilldelningen på hanteringsgruppsnivån. Tilldela sedan den mer tillåtande principen på nivån för den underordnade hanteringsgruppen eller prenumerationen. Sammanfattat innebär det att om en princip leder till att en resurs nekas är det enda sättet att tillåta resursen att ändra den nekande principen.

Mer information om att ange principdefinitioner och tilldelningar finns i [Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön](assign-policy-definition.md).

## <a name="policy-parameters"></a>Principparametrar

Principparametrar underlättar hanteringen av principer genom att minska antalet principdefinitioner du måste skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Sedan kan du återanvända den principdefinitionen för olika scenarier. Det gör du genom att ange olika värden när du tilldelar principdefinitionen. Till exempel ange du en uppsättning platser för en prenumeration.

Parametrar definieras/skapas när du skapar en principdefinition. När en parameter har definierats ges den ett namn och eventuellt ett givet värde. Du kan till exempel definiera en parameter för en princip med titeln *plats*. Sedan kan du ge den olika värden som *EastUS* eller *WestUS* när du tilldelar en princip.

Mer information om principparametrar finns i [Översikt över resursprinciper – parametrar](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Initiativdefinition

En initiativdefinition är en samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Initiativdefinitioner gör det enklare att hantera och tilldela principdefinitioner genom att de grupperar en uppsättning principer som ett enda objekt. Du kan till exempel skapa ett initiativ med titeln **Aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

Under det här initiativet skulle du ha principdefinitioner som dessa:

- **Övervaka okrypterade SQL-databaser i Security Center** – för att övervaka okrypterade SQL-databaser och -servrar.
- **Övervaka OS-säkerhetsproblem i Security Center** – för att övervaka servrar som inte uppfyller grundkonfigurationen.
- **Övervaka saknad Endpoint Protection i Security Center** – för att övervaka servrar utan en installerad Endpoint Protection-agent.

## <a name="initiative-assignment"></a>Initiativtilldelning

Precis som en principtilldelning är en initiativtilldelning en initiativdefinition som tilldelats ett specifikt område. Initiativtilldelningar minskar behovet av att göra flera initiativdefinitioner för varje område. Området kan även här vara allt från en hanteringsgrupp till en resursgrupp.

Initiativet **Aktivera övervakning i Azure Security Center** från föregående exempel kan tilldelas olika områden. En tilldelning kan till exempel vara **prenumerationA**.
En annan kan vara **prenumerationB**.

## <a name="initiative-parameters"></a>Initiativparametrar

Precis som principparametrar underlättar initiativparametrar initiativhanteringen genom att minska redundansen. Initiativparametrar är i stort sett listan över parametrar som används av principdefinitionerna inom ett initiativ.

Ta till exempel scenariot där du har en initiativdefinition, **initiativeC**, med principdefinitionerna **policyA** och **policyB** som vardera förväntar sig olika typer av parametrar:

| Princip | Parameternamn |Parametertyp  |Obs! |
|---|---|---|---|
| principA | allowedLocations | matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
| principB | allowedSingleLocation |sträng |Den här parametern förväntar sig ett ord som värde eftersom parametertypen har definierats som en sträng |

I det här scenariot, när du definierar initiativparametrar för **initiativC**, har du tre alternativ:

- Använd parametrarna för principdefinitionerna i det här initiativet. I det här exemplet blir *allowedLocations* och *allowedSingleLocation* initiativparametrar för **initiativC**.
- Ange värden för parametrarna för principdefinitionerna i den här initiativdefinitionen. I det här exemplet kan du ange en lista över platser för **principA-parametern allowedLocations** och **principB-parametern allowedSingleLocation**. Du kan också ange värden när du tilldelar det här initiativet.
- Ange en lista med alternativ *värden* som kan användas när du tilldelar det här initiativet. När du tilldelar det här initiativet kan ärvda parametrarna från principdefinitionerna inom initiativet endast ha värden från den här listan.

Du kan till exempel skapa en lista med värdealternativ i en initiativdefinition som innehåller *EastUS*, *WestUS*, *CentralUS* och *WestEurope*. Därmed kan du inte ange ett annat värde som *Southeast Asia* under initiativtilldelning, eftersom det inte finns på listan.

## <a name="maximum-count-of-policy-objects"></a>Maximalt antal principobjekt

Det finns ett maxantal för varje objekttyp för Azure Policy. En post av typen _Omfång_ betyder antingen prenumerationen eller hanteringsgruppen.

| Var | Vad | Maximalt antal |
|---|---|---|
| Omfång | Principdefinitioner | 250 |
| Omfång | Initiativdefinitioner | 100 |
| Klientorganisation | Initiativdefinitioner | 1000 |
| Omfång | Principtilldelning | 100 |
| Principdefinition | Parametrar | 20 |
| Initiativdefinition | Principer | 100 |
| Initiativdefinition | Parametrar | 100 |
| Principtilldelning | Undantag (notScopes) | 100 |
| Principregel | Kapslade villkor | 512 |

## <a name="recommendations-for-managing-policies"></a>Rekommendationer för principhantering

Här är några riktlinjer och tips som vi rekommenderar att du följer när du skapar och hanterar principdefinitioner och tilldelningar:

- Om du skapar principdefinitioner i din miljö rekommenderar vi att du börjar med en spårningsseffekt, till skillnad från en nekandeeffekt, för att spåra vilken effekt principdefinitionen har på resurserna i din miljö. Om du redan har skript på plats för att automatiskt skala upp dina program kan dessa automatiserade uppgifter eventuellt blockeras om du anger en nekandeeffekt.
- Det är viktigt att tänka på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att du skapar definitioner på en högre nivå, till exempel på hanteringsgrupps- eller prenumerationsnivån, och tilldelar vid nästa underordnade nivå. Om du till exempel skapar en principdefinition på hanteringsgruppsnivån kan en principtilldelnings område för den definitionen anges ned till en prenumerationsnivå inom den hanteringsgruppen.
- Vi rekommenderar att du alltid använder initiativdefinitioner i stället för principdefinitioner, även om du bara har en princip i åtanke. Om du till exempel har en principdefinition, *principdefA*, och du skapar den under initiativdefinitionen, *initiativdefC*. Om du senare vill skapa en till principdefinition för *principdefB* med mål som liknar *principdefA* kan du lägga till den under *initiativdefC* och på så vis spåra dem på ett bättre sätt.
- Tänk på att när du har skapat en initiativtilldelning från en initiativdefinition kommer eventuella nya principdefinitioner som läggs till i initiativdefinitionen automatiskt att distribueras under initiativtilldelningarna under den initiativdefinitionen.
- När en initiativtilldelning har utlösts kommer även alla principer inom initiativet att utlösas. Om du behöver köra en princip individuellt är det dock bättre att inte inkludera den i ett initiativ.

## <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Policy är från Build 2018. För nedladdning av bilder eller video besöker du sidan om att [styra Azure-miljön via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) på Channel 9.

> [!VIDEO https://channel9.msdn.com/events/Build/2018/THR2030/player]

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure Policy och några av de centrala begreppen föreslår vi följande som nästa steg:

- [Tilldela en principdefinition](assign-policy-definition.md)
- [Tilldela en principdefinition med hjälp av Azure CLI](assign-policy-definition-cli.md)
- [Tilldela en principdefinition med hjälp av PowerShell](assign-policy-definition-ps.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../azure-resource-manager/management-groups-overview.md)
- Visa sidan om att [styra Azure-miljön via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) på Channel 9