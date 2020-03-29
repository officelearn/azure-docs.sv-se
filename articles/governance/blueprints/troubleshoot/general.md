---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem med att skapa, tilldela och ta bort ritningar som principöverträdelser och funktioner för skissparameter.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157091"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Felsöka fel med Azure Blueprints

Du kan stöta på fel när du skapar, tilldelar eller tar bort ritningar. I den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta felinformation

Många fel kommer att vara resultatet av att tilldela en skiss till ett scope. När en tilldelning misslyckas innehåller skissen information om den misslyckade distributionen. Den här informationen anger problemet så att det kan åtgärdas och nästa distribution lyckas.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Tilldelade ritningar** från sidan till vänster och använd sökrutan för att filtrera skisstilldelningarna för att hitta den misslyckade tilldelningen. Du kan också sortera tilldelningstabellen efter kolumnen **Etableringstillstånd** om du vill visa alla misslyckade tilldelningar grupperade tillsammans.

1. Vänsterklicka på skissen med statusen _Misslyckad_ status eller högerklicka och välj **Visa tilldelningsinformation**.

1. En röd banderoll som varnar för att tilldelningen har misslyckats finns högst upp på skisstilldelningssidan. Klicka var som helst på bannern för att få mer information.

Det är vanligt att felet orsakas av en artefakt och inte ritningen som helhet. Om en artefakt skapar ett nyckelvalv och Azure-princip förhindrar att nyckelvalv skapas, misslyckas hela tilldelningen.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scenario: Policyöverträdelse

#### <a name="issue"></a>Problem

Malldistributionen misslyckades på grund av principöverträdelse.

#### <a name="cause"></a>Orsak

En princip kan stå i konflikt med distributionen av flera skäl:

- Resursen som skapas begränsas av principen (vanligtvis SKU eller platsbegränsningar)
- Distributionen anger fält som är konfigurerade av principen (vanliga med taggar)

#### <a name="resolution"></a>Lösning

Ändra skissen så att den inte står i konflikt med principerna i felinformationen. Om den här ändringen inte är möjlig är ett alternativt alternativ att ändra principtilldelningens omfattning så att skissen inte längre står i konflikt med principen.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scenario: Skissparameter är en funktion

#### <a name="issue"></a>Problem

Skissparametrar som är funktioner bearbetas innan de skickas till artefakter.

#### <a name="cause"></a>Orsak

Skickar en skissparameter som använder `[resourceGroup().tags.myTag]`en funktion, till exempel , till en artefakt resulterar i det bearbetade resultatet av funktionen som ställs in på artefakten i stället för den dynamiska funktionen.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion genom `[` som en parameter, `[[resourceGroup().tags.myTag]`escape hela strängen med så att skissparametern ser ut som . Escape-tecknet gör att ritningar behandlar värdet som en sträng när skissen bearbetas. Skisser placerar sedan funktionen på artefakten så att den kan vara dynamisk som förväntat. Mer information finns [i Syntax och uttryck i Azure Resource Manager-mallar](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Ta bort fel

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scenario: Timeout för borttagning av tilldelning

#### <a name="issue"></a>Problem

Borttagning av en skisstilldelning slutförs inte.

#### <a name="cause"></a>Orsak

En skisstilldelning kan fastna i ett icke-terminaltillstånd när den tas bort. Det här tillståndet orsakas när resurser som skapats av skisstilldelningen fortfarande väntar på borttagning eller inte returnerar en statuskod till Azure Blueprints.

#### <a name="resolution"></a>Lösning

Skisstilldelningar i ett icke-terminaltillstånd **markeras** automatiskt Misslyckades efter en _6 timmars_ timeout. När tidsgränsen har justerat ritningstilldelningens tillstånd kan borttagningen göras på nytt.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.