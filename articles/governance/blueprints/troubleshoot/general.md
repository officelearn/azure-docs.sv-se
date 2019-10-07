---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem med att skapa, tilldela och ta bort ritningar.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
ms.openlocfilehash: b99e94bfdcbf12e82a094f14995b6b93aa3354ed
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978232"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Felsöka fel med Azure-ritningar

Du kan stöta på fel när du skapar eller tilldelar ritningar. I den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta fel information

Många fel är resultatet av att tilldela en skiss till ett omfång. När en tilldelning Miss lyckas innehåller skissen information om den misslyckade distributionen. Den här informationen anger problemet så att det kan åtgärdas och nästa distribution lyckas.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **tilldelade skisser** från sidan till vänster och Använd sökrutan för att filtrera skiss tilldelningarna för att hitta den misslyckade tilldelningen. Du kan också sortera tilldelnings tabellen i kolumnen **etablerings tillstånd** för att se alla misslyckade tilldelningar grupperade tillsammans.

1. Vänsterklicka på skissen med statusen _misslyckad_ eller högerklicka och välj **Visa tilldelnings information**.

1. En röd banderoll-varning om att tilldelningen misslyckades visas överst på sidan skiss tilldelning. Klicka var som helst på banderollen för att få mer information.

Det är vanligt att felet orsakas av en artefakt och inte skissen som helhet. Om en artefakt skapar ett Key Vault och Azure Policy förhindrar att Key Vault skapas, kommer hela tilldelningen att Miss förfalla.

## <a name="general-errors"></a>Allmänna fel

### <a name="policy-violation"></a>Situationen Princip överträdelse

#### <a name="issue"></a>Problem

Mallen kunde inte distribueras på grund av en princip överträdelse.

#### <a name="cause"></a>Orsak

En princip kan vara i konflikt med distributionen av ett antal orsaker:

- Den resurs som skapas begränsas av en princip (ofta SKU eller plats begränsningar)
- Distributionen anger fält som har kon figurer ATS av en princip (common med taggar)

#### <a name="resolution"></a>Lösning

Ändra skissen så att den inte hamnar i konflikt med principerna i fel informationen. Om den här ändringen inte är möjlig, är ett alternativt alternativ att omfånget för princip tilldelningen har ändrats, så att skissen inte längre strider mot principen.

### <a name="escape-function-parameter"></a>Situationen Skiss parametern är en funktion

#### <a name="issue"></a>Problem

Skiss parametrar som är funktioner bearbetas innan de skickas till artefakter.

#### <a name="cause"></a>Orsak

Att skicka en skiss parameter som använder en funktion, till exempel `[resourceGroup().tags.myTag]`, till en artefakt resulterar i att det behandlade resultatet av funktionen som anges i artefakten i stället för den dynamiska funktionen.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion genom att använda som parameter, escapea hela strängen med `[` så att skiss parametern ser ut som `[[resourceGroup().tags.myTag]`. Escape-tecken gör att ritningar hanterar värdet som en sträng när skissen bearbetas. Ritningar placerar sedan funktionen på artefakten så att den kan vara dynamisk som förväntat. Mer information finns [i syntax och uttryck i Azure Resource Manager mallar](../../../azure-resource-manager/template-expressions.md).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.