---
title: "Översikt över Azure princip | Microsoft Docs"
description: "Azure principen är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/25/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 82721fe984ff7b3c7440b11d7526a9413b0770de
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="what-is-azure-policy"></a>Vad är Azure principen?

IT-styrning skapar tydlighetens mellan affärsmål och IT-projekt. Bra IT-styrning omfattar att planera din initiativ och ange prioritet på en strategisk nivå. Ditt företag ha ett stort antal IT-problem som aldrig verkar hämta löst? Implementera principer hjälper dig att hantera och förhindra att de. Implementera principer är där Azure princip kommer in.

Azure principen är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner. Principdefinitioner tillämpa olika regler och åtgärder via dina resurser, så resurserna vara kompatibla med företagets standarder och servicenivåavtal. Azure princip körs en utvärdering av dina resurser, söka efter de inte är kompatibla med principdefinitioner som du har. Du kan till exempel har en princip så att endast vissa typer av virtuella datorer är. En annan kräver att alla resurser som har en viss tagg. Dessa principer utvärderas sedan när du skapar och uppdaterar resurser.

## <a name="how-is-it-different-from-rbac"></a>Hur skiljer den från RBAC?

Det finns några viktiga skillnader mellan principen och rollbaserad åtkomstkontroll (RABC). RBAC fokuserar på användaråtgärder på olika omfång. Du kan till exempel läggas till i deltagarrollen för en resursgrupp i det önskade omfånget. Rollen kan du göra ändringar i resursgruppen. Principen fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Du kan till exempel styra vilka typer av resurser som kan etableras via principer. Eller så kan du begränsa de platser där resurser kan etableras. Till skillnad från RBAC, principen är en standard Tillåt och explicit neka system.

Om du vill använda principer måste du autentiseras via RBAC. Mer specifikt måste ditt konto den:

- `Microsoft.Authorization/policydefinitions/write`behörighet för att definiera en princip.
- `Microsoft.Authorization/policyassignments/write`behörighet för att tilldela en princip.

Dessa behörigheter ingår inte i den **deltagare** roll.


## <a name="policy-definition"></a>Principdefinitionen

Varje principdefinitionen har villkor som tillämpas. Och har en tillhörande åtgärd som utförs om villkoren är uppfyllda.

Vi erbjuder vissa inbyggda principer som är tillgängliga för dig som standard i Azure-princip. Exempel:

- **Kräver SQL Server 12.0**: definitionen för den här principen har villkor/regler för att kontrollera att alla SQL-servrar använder version 12.0. Åtgärden är att neka alla servrar som inte uppfyller dessa villkor.
- **Tillåtna Storage-konto SKU: er**: definitionen för den här principen har en uppsättning villkor/regler som avgör om det är ett lagringskonto som distribueras i en uppsättning SKU storlekar. Åtgärden är att neka alla servrar som inte följer uppsättningen definierade SKU-storlekar.
- **Tillåtna resurstypen**: definitionen för den här principen har en uppsättning villkor/regler för att ange de resurstyper som din organisation kan distribuera. Åtgärden är att neka alla resurser som inte är en del av den definierade listan.
- **Tillåtna platser**: den här principen kan du begränsa de platser som din organisation kan ange när du distribuerar resurser. Åtgärden för att genomdriva geo-efterföljandekrav.
- **Tillåtna virtuella SKU: er**: den här principen kan du ange en uppsättning virtuella SKU: er som kan distribueras i organisationen.
- **Tillämpa taggen och standardvärdet**: den här principen gäller obligatoriska taggen med sitt standardvärde om det inte anges av användaren.
- **Framtvinga taggen och dess värde**: denna princip tillämpar en nödvändig tagg och dess värde till en resurs.
- **Inte tillåtet resurstyper**: den här principen kan du ange de resurstyper som din organisation inte kan distribuera.

Du kan tilldela någon av dessa policys via Azure-portalen, PowerShell eller Azure CLI.

Mer information om strukturerna för principdefinitioner titta på den här artikeln - [Definition principstruktur](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Tilldelning av principer

En principtilldelning är en principdefinition som tilldelats äga rum inom ett specifikt omfång. Det här området kan röra sig om en hanteringsgrupp till en resursgrupp. Principtilldelningar ärvs av alla underordnade resurser. Så om en princip används för en resursgrupp, tillämpas den till alla resurser i resursgruppen. Termen *omfång* avser alla resursgrupper, prenumerationer eller hanteringsgrupper som har tilldelats principdefinitionen.

Mer information om inställningen principdefinitioner och tilldelningar finns [skapa en tilldelning av principer för att identifiera icke-kompatibla resurser i Azure-miljön](assign-policy-definition.md).

## <a name="policy-parameters"></a>För principparametrar

Principparametrar underlätta hantering av din genom att minska antalet principdefinitioner måste du skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Sedan kan du återanvända den principdefinitionen för olika scenarier. Det gör du genom att passera i olika värden när du tilldelar principdefinitionen. Till exempel ange en uppsättning platser för en prenumeration.

Parametrar skapas definieras/när du skapar en principdefinition. Det är ett namn och eventuellt ett givet när en parameter har definierats. Du kan t ex definiera en parameter för en princip med titeln *plats*. Du kan och ange sedan olika värden som *EastUS* eller *WestUS* när du tilldelar en princip.

<!--
Next link should point to new Concept page for Parameters
-->
Mer information om principparametrar finns [princip Resursöversikt - parametrar](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Initiativet definition
Ett initiativ definition är samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Initiativet definitioner förenkla hantering och tilldela principdefinitioner. De förenkla genom att gruppera en uppsättning principer som ett enda objekt. Du kan till exempel skapa ett initiativ med titeln **aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

Under den här initiativ har du principdefinitioner som:

1. **Övervakaren okrypterade SQL-databas i Security Center** – för att övervaka okrypterad SQL-databaser och servrar.
2. **Övervaka OS-säkerhetsproblem i Security Center** – för övervakning av servrar som inte uppfyller de konfigurerade baslinjen.
3. **Övervaka saknas Endpoint Protection i Security Center** – för att övervaka servrar utan en installerade endpoint protection-agenten.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Initiativet tilldelning
Som en principtilldelning är en initiativ tilldelning ett initiativ definition som tilldelats ett specifikt omfång. Initiativet tilldelningar minska behovet av att göra flera definitioner av initiativet för varje scope. Det här området kan också röra sig om en hanteringsgrupp till en resursgrupp.

Från föregående exempel är den **aktivera övervakning i Azure Security Center** initiativ kan tilldelas till olika omfång. Till exempel en tilldelning kan tilldelas **subscriptionA**. En annan kan tilldelas **subscriptionB**.

## <a name="initiative-parameters"></a>Initiativet parametrar
Som principparametrar underlätta initiativ parametrar initiativ management genom att minska redundans. Initiativet parametrar är i stort sett listan över parametrar som används av principdefinitioner inom initiativ.

Till exempel ta ett scenario där du har ett initiativ definition - **initiativeC**, med två principdefinitioner. Varje principdefinitionen med en definierad parameter:

| Princip | parameterns namn |Typen för parametern  |Obs! |
|---|---|---|---|
| policyA | allowedLocations | matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
| policyB | allowedSingleLocation |Sträng |Den här parametern förväntar sig ett ord för ett värde eftersom parametertypen har definierats som en sträng |

I det här scenariot när du definierar initiativ parametrar för **initiativeC**, finns det tre alternativ:

1. Använda parametrarna principdefinitioner i den här initiativ: I det här exemplet *allowedLocations* och *allowedSingleLocation* blir initiativ parametrar för **initiativeC** .
2. Ange värden för parametrarna för principdefinitioner i denna initiativ definition. I det här exemplet kan du ange en lista över platser för att **Policya's parametern – allowedLocations** och **Policyb's parametern – allowedSingleLocation**.
Du kan också ange värden när du tilldelar den här initiativ.
3. Ange en lista över *värdet* alternativ som kan användas när du tilldelar den här initiativ. När du tilldelar den här initiativ kan ärvda parametrarna från principdefinitioner inom initiativ, endast ha värdena från den här listan.

Du kan till exempel skapa en lista med alternativ i en definition av initiativ som innehåller *EastUS*, *WestUS*, *CentralUS*, och *WestEurope*. Om så är fallet bör du inte att ange ett annat värde som *Sydostasien* under initiativ tilldelning eftersom den inte ingår i listan.

## <a name="recommendations-for-managing-policies"></a>Rekommendationer för att hantera principer

Här följer några pekare rekommenderar vi att du följer när du skapar och hanterar principdefinitioner och tilldelningar:

- Om du skapar principdefinitioner i din miljö, rekommenderar vi börjar med en audit effekt, till skillnad från en neka effekt genom att hålla reda på effekten av definitionen av principen för de resurser i din miljö. Om du redan skript för att Autoskala in dina program, kan ange en neka-effekten hindra uppgifterna automatiseringar som du redan har på plats.
- Det är viktigt att tänka på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att skapa definitioner på en högre nivå, till exempel på hanteringsgruppen eller prenumerationsnivå och tilldela vid nästa underordnad nivå. Om du skapar en principdefinition på gruppnivå management kan en principtilldelning i definitionen begränsas till en prenumerationsnivå inom den hanteringsgruppen.
- Vi rekommenderar att du använder standardprisnivån, för att bättre förstå kompatibilitetsstatusen för din miljö. Mer information om våra prisnivå modeller och vilka erbjuder, ta en titt på [priser](https://azure.microsoft.com/pricing/details/azure-policy).
- Vi rekommenderar att alltid använda initiativ definitioner i stället för principdefinitioner, även om du bara har en princip i åtanke. Om du har en principdefinition – till exempel *policyDefA* och du skapar den enligt initiativ definition - *initiativeDefC*, om du vill skapa en annan principdefinitionen senare för *policyDefB* med mål som liknar *policyDefA*, du kan lägga till den under *initiativeDefC* och spåra dem bättre sätt.

   Tänk på att när du har skapat en initiativ tilldelning från en initiativ definition eventuella nya principdefinitioner läggs till initiativ definitionen automatiskt distribuera under initiativ tilldelningarna under initiativ definitionen. Men om det finns en ny parameter som lades till den nya principdefinitionen, behöver du uppdatera initiativ definitionen och tilldelningar genom att redigera initiativ definition eller tilldelning.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure princip och vissa av de viktiga begrepp som vi introduktion är här de föreslagna nästa steg:

- [Tilldela en principdefinition](./assign-policy-definition.md)
- [Tilldela en principdefinition med hjälp av Azure CLI](./assign-policy-definition-cli.md)
- [Tilldela en principdefinition med hjälp av PowerShell](./assign-policy-definition-ps.md)
