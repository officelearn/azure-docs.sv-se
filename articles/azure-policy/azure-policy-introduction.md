---
title: Översikt över Azure Policy | Microsoft Docs
description: Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.reviewer: nini
ms.date: 03/29/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: f9cd00aec025748170a6576fe3ee4dbf794edfdb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="what-is-azure-policy"></a>Vad är Azure Policy?

IT-styrning skapar tydlighet mellan affärsmål och IT-projekt. God IT-styrning omfattar att planera dina initiativ och ange prioritet på en strategisk nivå. Har ditt företag ett stort antal IT-problem som aldrig verkar bli lösta? Att implementera principer hjälper dig att hantera och förhindra dem. Och det är där Azure Policy kommer in.

Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner. Principdefinitioner tillämpar olika regler och åtgärder på resurserna, så att resurserna följer företagets standarder och serviceavtal. Azure Policy kör en utvärdering av resurserna och söker efter sådana som inte är kompatibla med principdefinitionerna. Du kan till exempel ha en princip som endast tillåter vissa typer av virtuella datorer. En annan kräver att alla resurser har en viss tagg. Dessa principer utvärderas sedan resurser skapas och uppdateras.

## <a name="how-is-it-different-from-rbac"></a>Vad är skillnaden jämfört med RBAC?

Det finns några viktiga skillnader mellan princip- och rollbaserad åtkomstkontroll (RBAC). RBAC fokuserar på användaråtgärder i olika omfång. Du kan till exempel läggas till i deltagarrollen för en resursgrupp i det önskade omfånget. Rollen gör att du kan göra ändringar i resursgruppen. Principer fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Med hjälp av principer kan du till exempel styra vilka typer av resurser som kan etableras. Eller du kan begränsa på vilka platser resurser kan etableras. Till skillnad från RBAC har principsystemet ”tillåt” som standard och ”neka” måste anges uttryckligen.

Om du vill använda principer måste du autentiseras via RBAC. Mer specifikt måste ditt konto ha detta:

- `Microsoft.Authorization/policydefinitions/write`-behörighet för att definiera en princip.
- `Microsoft.Authorization/policyassignments/write`-behörighet för att tilldela en princip.
- `Microsoft.Authorization/policySetDefinitions/write`-behörighet för att definiera ett initiativ.
- `Microsoft.Authorization/policyassignments/write`-behörighet för att tilldela ett initiativ.


Dessa behörigheter ingår inte i den rollen **Deltagare**.


## <a name="policy-definition"></a>Definition av princip

Varje principdefinition har villkor för när den ska tillämpas. Och en tillhörande åtgärd som utförs om villkoren är uppfyllda.

Vi erbjuder några inbyggda principer som är tillgängliga för dig som standard i Azure Policy. Till exempel:

- **Kräv SQL Server 12.0**: Den här principdefinitionen har villkor/regler för att kontrollera att alla SQL-servrar använder version 12.0. Åtgärden är att neka alla servrar som inte uppfyller dessa villkor.
- **Tillåtna SKU:er för lagringskonton**: Den här principdefinitionen har en uppsättning villkor/regler som avgör om ett lagringskonto som distribueras finns inom en uppsättning SKU-storlekar. Åtgärden är att neka alla servrar som inte följer uppsättningen med definierade SKU-storlekar.
- **Tillåten resurstyp**: Den här principdefinitionen har en uppsättning villkor/regler som anger vilka resurstyper organisationen kan distribuera. Åtgärden är att neka alla resurser som inte finns på den definierade listan.
- **Tillåtna platser**: Med den här principen kan du begränsa vilka platser som kan anges när resurser distribueras i organisationen. Åtgärden används för att genomdriva kraven på geo-standard.
- **Tillåtna SKU:er för virtuella datorer**: Med den här principen kan du ange en uppsättning SKU:er för virtuella datorer som kan distribueras i organisationen.
- **Använd tagg med standardvärde**: Den här principen applicerar en obligatorisk tagg med ett standardvärde, om den inte anges av användaren.
- **Tvinga fram tagg med värde**: Den här principen tvingar fram en obligatorisk tagg och dess värde på en resurs.
- **Otillåtna resurstyper**: Med den här principen kan du ange vilka resurstyper som inte får distribueras i organisationen.

Du kan tilldela de här principerna via Azure Portal, PowerShell eller Azure CLI.

Mer information om principdefinitionernas strukturer finns i den här artikeln – [Struktur för principdefinitioner](policy-definition.md).

## <a name="policy-assignment"></a>Principtilldelning

En principtilldelning är en principdefinition som tilldelas att äga rum inom ett specifikt område. Området kan vara allt från en hanteringsgrupp till en resursgrupp. Termen *område* avser alla resursgrupper, prenumerationer eller hanteringsgrupper som principdefinitionen har tilldelats. Principtilldelningar ärvs av alla underordnade resurser. Så om en princip används för en resursgrupp tillämpas den på alla resurser i den resursgruppen. Du kan dock utesluta ett delområde från principtilldelningen. Du kan till exempel tilldela en princip som förhindrar skapande av nätverksresurser i ett prenumerationsområde. Men du kan undanta en resursgrupp i prenumerationen som är avsedd för nätverksinfrastruktur. Du beviljar åtkomst till den här nätverksresursgruppen till användare som du litar på för att skapa nätverksresurser.

Mer information om att ange principdefinitioner och tilldelningar finns i [Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön](assign-policy-definition.md).

## <a name="policy-parameters"></a>Principparametrar

Principparametrar underlättar hanteringen av principer genom att minska antalet principdefinitioner du måste skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Sedan kan du återanvända den principdefinitionen för olika scenarier. Det gör du genom att ange olika värden när du tilldelar principdefinitionen. Till exempel ange du en uppsättning platser för en prenumeration.

Parametrar definieras/skapas när du skapar en principdefinition. När en parameter har definierats ges den ett namn och eventuellt ett givet värde. Du kan till exempel definiera en parameter för en princip med titeln *plats*. Sedan kan du ge den olika värden som *EastUS* eller *WestUS* när du tilldelar en princip.

<!--
Next link should point to new Concept page for Parameters
-->
Mer information om principparametrar finns i [Översikt över resursprinciper – parametrar](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Initiativdefinition
En initiativdefinition är samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Initiativdefinitioner gör det enklare att hantera och tilldela principdefinitioner genom att de grupperar en uppsättning principer som ett enda objekt. Du kan till exempel skapa ett initiativ med titeln **Aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

Under det här initiativet skulle du ha principdefinitioner som dessa:

1. **Övervaka okrypterade SQL-databaser i Security Center** – för att övervaka okrypterade SQL-databaser och -servrar.
2. **Övervaka OS-säkerhetsproblem i Security Center** – för att övervaka servrar som inte uppfyller grundkonfigurationen.
3. **Övervaka saknad Endpoint Protection i Security Center** – för att övervaka servrar utan en installerad Endpoint Protection-agent.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Initiativtilldelning
Precis som en principtilldelning är en initiativtilldelning en initiativdefinition som tilldelats ett specifikt område. Initiativtilldelningar minskar behovet av att göra flera initiativdefinitioner för varje område. Området kan även här vara allt från en hanteringsgrupp till en resursgrupp.

Initiativet **Aktivera övervakning i Azure Security Center** från föregående exempel kan tilldelas olika områden. En tilldelning kan till exempel vara **prenumerationA**. En annan kan vara **prenumerationB**.

## <a name="initiative-parameters"></a>Initiativparametrar
Precis som principparametrar underlättar initiativparametrar initiativhanteringen genom att minska redundansen. Initiativparametrar är i stort sett listan över parametrar som används av principdefinitionerna inom ett initiativ.

Ta till exempel ett scenario där du har en initiativdefinition, **initiativC**, med två principdefinitioner. Varje principdefinitionen har en definierad parameter:

| Princip | Parameternamn |Parametertyp  |Obs! |
|---|---|---|---|
| principA | allowedLocations | matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
| principB | allowedSingleLocation |sträng |Den här parametern förväntar sig ett ord som värde eftersom parametertypen har definierats som en sträng |

I det här scenariot, när du definierar initiativparametrar för **initiativC**, har du tre alternativ:

1. Använd parametrarna för principdefinitionerna i det här initiativet. I det här exemplet blir *allowedLocations* och *allowedSingleLocation* initiativparametrar för **initiativC**.
2. Ange värden för parametrarna för principdefinitionerna i den här initiativdefinitionen. I det här exemplet kan du ange en lista över platser för **principA-parametern allowedLocations** och **principB-parametern allowedSingleLocation**.
Du kan också ange värden när du tilldelar det här initiativet.
3. Ange en lista med alternativ *värden* som kan användas när du tilldelar det här initiativet. När du tilldelar det här initiativet kan ärvda parametrarna från principdefinitionerna inom initiativet endast ha värden från den här listan.

Du kan till exempel skapa en lista med värdealternativ i en initiativdefinition som innehåller *EastUS*, *WestUS*, *CentralUS* och *WestEurope*. Därmed kan du inte ange ett annat värde som *Southeast Asia* under initiativtilldelning, eftersom det inte finns på listan.

## <a name="recommendations-for-managing-policies"></a>Rekommendationer för principhantering

Här är några riktlinjer som vi rekommenderar att du följer när du skapar och hanterar principdefinitioner och tilldelningar:

- Om du skapar principdefinitioner i din miljö rekommenderar vi att du börjar med en spårningsseffekt, till skillnad från en nekandeeffekt, för att spåra vilken effekt principdefinitionen har på resurserna i din miljö. Om du redan har skript på plats för att automatiskt skala upp dina program kan dessa automatiserade uppgifter eventuellt blockeras om du anger en nekandeeffekt.
- Det är viktigt att tänka på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att du skapar definitioner på en högre nivå, till exempel på hanteringsgrupps- eller prenumerationsnivån, och tilldelar vid nästa underordnade nivå. Om du till exempel skapar en principdefinition på hanteringsgruppsnivån kan en principtilldelnings område för den definitionen anges ned till en prenumerationsnivå inom den hanteringsgruppen.
- Vi rekommenderar att du använder standardprisnivån för att bättre förstå kompatibilitetsstatusen för din miljö. Mer information om våra prisnivåmodeller och vad de omfattar finns under [Priser](https://azure.microsoft.com/pricing/details/azure-policy).
- Vi rekommenderar att du alltid använder initiativdefinitioner i stället för principdefinitioner, även om du bara har en princip i åtanke. Om du till exempel har en principdefinition, *principdefA*, och du skapar den under initiativdefinitionen, *initiativdefC*. Om du senare vill skapa en till principdefinition för *principdefB* med mål som liknar *principdefA* kan du lägga till den under *initiativdefC* och på så vis spåra dem på ett bättre sätt.

   Tänk på att när du har skapat en initiativtilldelning från en initiativdefinition kommer eventuella nya principdefinitioner som läggs till i initiativdefinitionen automatiskt att distribueras under initiativtilldelningarna under den initiativdefinitionen. Men om en ny parameter läggs till i den nya principdefinitionen måste du uppdatera initiativdefinitionen och tilldelningarna genom att redigera initiativdefinitionen eller tilldelningen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure Policy och några av de centrala begreppen som vi har presenterat föreslår vi följande som nästa steg:

- [Tilldela en principdefinition](./assign-policy-definition.md)
- [Tilldela en principdefinition med hjälp av Azure CLI](./assign-policy-definition-cli.md)
- [Tilldela en principdefinition med hjälp av PowerShell](./assign-policy-definition-ps.md)
