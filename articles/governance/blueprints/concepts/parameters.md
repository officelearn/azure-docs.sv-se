---
title: Använda parametrar för att skapa dynamiska ritningar
description: Lär dig mer om statiska och dynamiska parametrar och hur du använder dem för att skapa säkra och dynamiska ritningar.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: e5953617d5fa27098380f3f0e95843c69800f823
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458496"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Skapa dynamiska ritningar genom parametrar

En fullständigt definierad skiss med olika artefakter (till exempel resursgrupper, Resource Manager-mallar, principer eller rolltilldelningar) erbjuder snabb skapande och konsekvent skapande av objekt i Azure. Azure Blueprints stöder parametrar för att möjliggöra flexibel användning av dessa återanvändbara designmönster och behållare. Parametern skapar flexibilitet, både under definition och tilldelning, för att ändra egenskaper för artefakter som distribueras av skissen.

Ett enkelt exempel är resursgruppartefakten. När en resursgrupp skapas har den två obligatoriska värden som måste anges: namn och plats. När du lägger till en resursgrupp i skissen, om parametrar inte fanns, definierar du det namnet och platsen för varje användning av skissen. Den här upprepningen skulle orsaka all användning av skissen för att skapa artefakter i samma resursgrupp. Resurser i resursgruppen dupliceras och orsakar en konflikt.

> [!NOTE]
> Det är inte ett problem för två olika ritningar att inkludera en resursgrupp med samma namn.
> Om det redan finns en resursgrupp som ingår i en skiss fortsätter skissen att skapa relaterade artefakter i resursgruppen. Detta kan orsaka en konflikt eftersom två resurser med samma namn och resurstyp inte kan finnas i en prenumeration.

Lösningen på detta problem är parametrar. Med Azure Blueprints kan du definiera värdet för varje egenskap för artefakten under tilldelning till en prenumeration. Parametern gör det möjligt att återanvända en skiss som skapar en resursgrupp och andra resurser i en enda prenumeration utan att ha konflikt.

## <a name="blueprint-parameters"></a>Skissparametrar

Genom REST API kan parametrar skapas på själva skissen. Dessa parametrar skiljer sig från parametrarna på var och en av de artefakter som stöds. När en parameter skapas på skissen kan den användas av artefakterna i skissen. Ett exempel kan vara prefixet för namngivning av resursgruppen. Artefakten kan använda skissparametern för att skapa en "mestadels dynamisk" parameter. Eftersom parametern också kan definieras under tilldelningen tillåter det här mönstret en konsekvens som kan följa namngivningsregler. Steg för steg finns [i ange statiska parametrar - parameter för skissnivå](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Använda säkertSträngning och säkraObjektparametrar

Medan en Resource _Manager-mallartefakt_ stöder parametrar för typerna **secureString** och **secureObject,** kräver Azure Blueprints att var och en ska vara ansluten till ett Azure Key Vault. Denna säkerhetsåtgärd förhindrar den osäkra praxisen att lagra hemligheter tillsammans med blueprintn och uppmuntrar anställning av säkra mönster. Azure Blueprints stöder den här säkerhetsåtgärden och identifierar inkludering av en säker parameter i en _Resource Manager-mallartefakt_. Tjänsten frågar sedan under tilldelningen för följande Key Vault-egenskaper per identifierad säker parameter:

- Resurs-ID för Nyckelvalv
- Hemligt namn på Key Vault
- Key Vault hemlig version

Om skisstilldelningen använder en **systemtilldelad hanterad identitet** _måste_ det refererade Nyckelvalvet finnas i samma prenumeration som skissdefinitionen tilldelas.

Om skisstilldelningen använder en **användartilldelad hanterad identitet** _kan_ det refererade Nyckelvalvet finnas i en centraliserad prenumeration. Den hanterade identiteten måste beviljas lämpliga rättigheter i nyckelvalvet före skisstilldelning.

> [!IMPORTANT]
> I båda fallen måste Key Vault ha **aktivera åtkomst till Azure Resource Manager för malldistribution** konfigurerad på sidan **Åtkomstprinciper.** Anvisningar om hur du aktiverar den här funktionen finns i [Key Vault - Aktivera malldistribution](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Mer information om Azure Key Vault finns i [Översikt över Nyckelvalv](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Parametertyper

### <a name="static-parameters"></a>Statiska parametrar

Ett parametervärde som definieras i definitionen av en skiss kallas en **statisk parameter**, eftersom varje användning av skissen kommer att distribuera artefakten med det statiska värdet. I resursgruppsexemplet, även om det inte är meningsfullt för namnet på resursgruppen, kan det vara meningsfullt för platsen. Sedan skulle varje tilldelning av skissen skapa resursgruppen, vad den kallas under tilldelning, på samma plats. Med den här flexibiliteten kan du vara selektiv i det du definierar som obligatoriskt jämfört med vad som kan ändras under tilldelningen.

#### <a name="setting-static-parameters-in-the-portal"></a>Ställa in statiska parametrar i portalen

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Klicka på en befintlig skiss och klicka sedan på **Redigera skiss** eller klicka på + **Skapa skiss** och fyll i informationen på fliken **Grunderna.**

1. Klicka på **Nästa: Artefakter** ELLER klicka på fliken **Artefakter.**

1. Artefakter som läggs till i skissen som har parameteralternativ visar **X Y-parametrar som fylls** i kolumnen **Parametrar.** Klicka på artefaktraden för att redigera artefaktparametrarna.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Skissparametrar för en skissdefinition" border="false":::

1. På sidan **Redigera artefakt** visas de värdealternativ som är lämpliga för den artefakt som klickas på. Varje parameter på artefakten har en rubrik, en värderuta och en kryssruta. Ange att rutan ska avmarkeras så att den blir en **statisk parameter**. I exemplet nedan är endast _Plats_ en **statisk parameter** eftersom den är avmarkerad och _resursgruppsnamnet_ är markerat.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Skissastatiska parametrar på en skissartefakt" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Ange statiska parametrar från REST API

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourMG}` – Ersätt med namnet på din hanteringsgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

##### <a name="blueprint-level-parameter"></a>Parameter för skissnivå

När du skapar en skiss via REST API är det möjligt att skapa [skissparametrar](#blueprint-parameters). Det gör du genom att använda följande REST API URI och brödtextformat:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

När en parameter på skissnivå har skapats kan den användas på artefakter som läggs till i skissen.
I följande REST API-exempel skapas en rolltilldelningsartefakt på skissen och parametern för skissnivå används.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

I det här exemplet använder egenskapen **principalIds** **parametern** `[parameters('owners')]`för ägare skissnivå med hjälp av ett värde i . Att ange en parameter på en artefakt med hjälp av en parameter på skissnivå är fortfarande ett exempel på en **statisk parameter**. Parametern för skissnivå kan inte anges under skisstilldelning och kommer att vara samma värde för varje tilldelning.

##### <a name="artifact-level-parameter"></a>Parameter på artefaktnivå

Att skapa **statiska parametrar** på en artefakt är `parameters()` liknande, men tar ett rakt värde i stället för att använda funktionen. I följande exempel skapas två statiska parametrar, **tagName** och **tagValue**. Värdet på varje anges direkt och använder inte ett funktionsanrop.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dynamiska parametrar

Motsatsen till en statisk parameter är en **dynamisk parameter**. Den här parametern definieras inte i skissen, utan definieras i stället under varje tilldelning av skissen. I resursgruppsexemplet är det meningsfullt att använda en **dynamisk parameter** för resursgruppsnamnet. Det ger ett annat namn för varje tilldelning av ritningen. En lista över skissfunktioner finns i referensen för [skissfunktioner.](../reference/blueprint-functions.md)

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ställa in dynamiska parametrar i portalen

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på skissen som du vill tilldela. Välj **Tilldela skiss** eller klicka på skissen som du vill tilldela och klicka sedan på knappen Tilldela **skiss.**

1. Leta reda på avsnittet **Artefaktparametrar** på sidan **Tilldela skiss.** Varje artefakt med minst en **dynamisk parameter** visar artefakten och konfigurationsalternativen. Ange obligatoriska värden till parametrarna innan du tilldelar skissen. I exemplet nedan är _namn_ en **dynamisk parameter** som måste definieras för att slutföra skisstilldelning.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Dynamisk modellparameter för skisser under skisstilldelning" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ställa in dynamiska parametrar från REST API

Ställa in **dynamiska parametrar** under tilldelningen görs genom att ange värdet direkt. I stället för att använda en funktion, till exempel [parameters()](../reference/blueprint-functions.md#parameters), är det angivna värdet en lämplig sträng. Artefakter för en resursgrupp definieras med egenskaperna "mallnamn", **namn**och **plats.** Alla andra parametrar för inkluderad artefakt definieras under **parametrar** med ett ** \<namn-\> ** och **värdenyckelpar.** Om skissen är konfigurerad för en dynamisk parameter som inte anges under tilldelningen misslyckas tilldelningen.

- REST API-URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Nästa steg

- Se listan över [skissfunktioner](../reference/blueprint-functions.md).
- Mer information om [livscykeln för en skiss](lifecycle.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).