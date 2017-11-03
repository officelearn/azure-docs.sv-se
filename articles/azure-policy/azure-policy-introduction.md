---
title: "Översikt över Azure princip | Microsoft Docs"
description: "Azure principen är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>Vad är Azure principen?

IT-styrning skapar tydlighetens mellan affärsmål och IT-projekt. Bra IT-styrning omfattar att planera din initiativ och ange prioritet på en strategisk nivå. Om ditt företag får ett stort antal IT-problem som aldrig verkar löses, implementera principer hjälper dig att bättre hantera och förhindra att de. Det är där Azure princip kommer in.

Azure principen är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner. Principdefinitioner tillämpa olika regler och åtgärder via dina resurser, så resurserna vara kompatibla med företagets standarder och servicenivåavtal. Det gör du genom att köra en utvärdering av dina resurser, skanna som de som inte är kompatibla med principdefinitioner som du har på plats.

## <a name="policy-definition"></a>Principdefinitionen

Varje principdefinitionen har villkoren för det aktiveras och tillhörande åtgärd som tar placera om villkoren är uppfyllda.

Vi erbjuder vissa inbyggda principer som är tillgängliga för dig som standard i Azure-princip. Exempel:

- **Kräver SQL Server 12.0**: definitionen för den här principen har villkor/regler för att kontrollera att alla SQL-servrar använder version 12.0. Åtgärden är att neka alla servrar som inte uppfyller dessa villkor.
- **Tillåtna Storage-konto SKU: er**: definitionen för den här principen har en uppsättning villkor/regler som avgör om det är ett lagringskonto som distribueras i en uppsättning SKU storlekar. Åtgärden är att neka alla servrar som inte följer uppsättningen definierade SKU-storlekar.
- **Tillåtna resurstypen**: definitionen för den här principen har en uppsättning villkor/regler för att ange de resurstyper som din organisation kan distribuera. Åtgärden är att neka alla resurser som inte är en del av den definierade listan.

Mer information om strukturerna för principdefinitioner titta på den här artikeln - [Definition principstruktur](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Tilldelning av principer
En principtilldelning är en principdefinition som tilldelats äga rum inom ett specifikt omfång. Det här området kan röra sig om en Hanteringsgrupp till en resursgrupp.

Mer information om inställningen principdefinitioner och tilldelningar finns [resurs uppgifter](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>För principparametrar
Principparametrar underlätta hantering av din genom att minska antalet principdefinitioner måste du skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Och du kan återanvända den principdefinitionen för olika scenarier genom att passera i olika värden (t.ex att ange en uppsättning platser för en prenumeration) när tilldelade principen.

Parametrar skapas definieras/när du skapar en principdefinition. Det är ett namn och eventuellt ett givet när en parameter har definierats. T.ex, du kan definiera en parameter för en princip som en plats och sedan ge den olika värden som *EastUS* eller *WestUS* när du tilldelar en princip.

Mer information om principparametrar finns [princip Resursöversikt - parametrar](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Initiativet definition
Ett initiativ definition är samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Du kan till exempel skapa ett initiativ med titeln **aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

Under den här initiativ har du principdefinitioner som:

1. **Övervakaren okrypterade SQL-databas i Security Center** – för att övervaka okrypterad SQL-databaser och servrar.
2. **Övervaka OS-säkerhetsproblem i Security Center** – för övervakning av servrar som inte uppfyller de konfigurerade baslinjen.
3. **Övervaka saknas Endpoint Protection i Security Center** – för att övervaka servrar utan en installerade endpoint protection-agenten.

## <a name="initiative-assignment"></a>Initiativet tilldelning
Som en principtilldelning är en initiativ tilldelning ett initiativ definition som tilldelats ett specifikt omfång. Initiativet tilldelningar minska behovet av att göra flera definitioner av initiativet för varje scope. Det här området kan också röra sig om en hanteringsgrupp till en resursgrupp.

Från föregående exempel är den **aktivera övervakning i Azure Security Center** initiativ kan tilldelas till olika omfång. Till exempel en tilldelning kan tilldelas **subscriptionA**, medan en annan kan tilldelas **subscriptionB**.

## <a name="initiative-parameters"></a>Initiativet parametrar
Som principparametrar underlätta initiativ parametrar initiativ management genom att minska redundans. Initiativet parametrar är i stort sett listan över parametrar som används av principdefinitioner inom initiativ.

Till exempel ta ett scenario där du har ett initiativ definition - **initiativeC**, med två principdefinitioner. Varje principdefinitionen med en definierad parameter:

|Princip  |parameterns namn     |Typen för parametern  |Obs!                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
|policyB |allowedSingleLocation |Sträng |Den här parametern förväntar sig ett ord för ett värde eftersom parametertypen har definierats som en sträng          |

I det här scenariot när du definierar initiativ parametrar för **initiativeC**, finns det tre alternativ:

1. Använda parametrarna för principdefinitioner i den här initiativ: I det här exemplet *allowedLocations* och *allowedSingleLocation* blir initiativ parametrar för  **initiativeC**.
2. Ange värden för parametrarna för principdefinitioner i denna initiativ definition. I det här exemplet kan du ange en lista över platser för att **Policya's parametern – allowedLocations** och **Policyb's parametern – allowedSingleLocation**.
Du kan också ange värden när du tilldelar den här initiativ.
3. Ange en lista över *värdet* alternativ som kan användas när du tilldelar den här initiativ. Det innebär att när du tilldelar den här initiativ ärvda parametrarna från principdefinitioner inom initiativ, kan bara ha värden från den här listan.

Till exempel, om den angivna listan med värdet alternativ när du skapar initiativ definition, har – *EastUS*, *WestUS*, *CentralUS*, och *WestEurope* , du kan inte ange ett annat värde som *Sydostasien* under initiativ tilldelning eftersom den inte ingår i listan.

## <a name="recommendations-for-managing-policies"></a>Rekommendationer för att hantera principer

Här följer några pekare rekommenderar vi att du följer när du skapar och hanterar principdefinitioner och tilldelningar:

- Om du skapar principdefinitioner i din miljö, rekommenderar vi börjar med en audit effekt, till skillnad från effekten neka att spåra effekten av principdefinition i din miljö. Om du redan skript för att Autoskala in dina program, kan ange en neka-effekten hindra uppgifterna automatiseringar som du redan har på plats.
- Det är viktigt att tänka på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att skapa definitioner på en högre nivå, till exempel på hanteringsgruppen eller prenumerationsnivå och tilldela vid nästa underordnad nivå. Om du skapar en principdefinition på gruppnivå management, kan en principtilldelning för denna definition begränsas en nivå i prenumerationen.
- Vi rekommenderar att du använder standardprisnivån, för att bättre förstå kompatibilitetsstatusen för din miljö.
- Vi rekommenderar att alltid använda initiativ definitioner i stället för principdefinitioner, även om du bara har en princip i åtanke. Om du har en principdefinition – till exempel *policyDefA* och du skapar den enligt initiativ definition - *initiativeDefC*, om du vill skapa en annan principdefinitionen med mål som det i *policyDefA*, kan du bara lägga till den under *initiativeDefC* och spåra dem bättre sätt.

   Tänk på att när du har skapat en initiativ tilldelning från en initiativ definition eventuella nya principdefinitioner läggs till initiativ definitionen görs automatiskt under initiativ tilldelningarna under initiativ definitionen. Men om det finns en ny parameter som lades till den nya principdefinitionen, behöver du uppdatera initiativ definitionen och tilldelningar efter detta genom att redigera definition eller tilldelning.

## <a name="next-steps"></a>Nästa steg:
Nu när du har en översikt över Azure princip och vissa av de viktiga begrepp som vi introduktion är här de föreslagna nästa steg:

- [Tilldela en principdefinition](./assign-policy-definition.md)
- [Tilldela en principdefinition med hjälp av Azure CLI](./assign-policy-definition-cli.md)
- [Tilldela en principdefinition med hjälp av PowerShell](./assign-policy-definition-ps.md)
