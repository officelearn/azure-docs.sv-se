---
title: Använda parametrar för att skapa dynamiska skisser
description: Läs mer om statiska och dynamiska parametrar och hur använder dem skapar dynamiska skisser.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 9166d5d552df4854a4d00c2211a273a06198877a
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567493"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Skapa dynamiska skisser genom parametrar

En skiss som är fullständigt definierat med olika artefakter (till exempel resursgrupper, Resource Manager-mallar, principer eller rolltilldelningar) erbjuder att snabbt kunna skapa och konsekvent skapa för objekt inom Azure. Om du vill aktivera flexibel användning av dessa återanvändbara designmönster och behållare, stöder Azure skisser parametrar. Parametern skapar flexibilitet, både under definition och tilldelning för att ändra egenskaperna på artefakter som distribueras av skissen.

Ett enkelt exempel är resource group artefakten. När det skapas en resursgrupp, har två värden som krävs som måste anges: namn och plats. När du lägger till en resursgrupp din skiss om parametrar inte fanns, skulle du definiera den namn och plats för varje användning av skissen. Repetition skulle orsaka varje användning av skissen att skapa artefakter i samma resursgrupp. Resurser i resursgruppen skulle bli en dubblett och orsaka en konflikt.

> [!NOTE]
> Det är inte ett problem för två olika skisser att inkludera en resursgrupp med samma namn.
> Om en resursgrupp som ingår i en skiss redan finns, fortsätter skissen att skapa relaterade artefakter i den resursgruppen. Detta kan orsaka en konflikt som två resurser med samma namn och resurstypen kan inte finnas inom en prenumeration.

Lösningen på det här problemet är parametrar. Skisser kan du definiera värdet för varje egenskap för artefakten under tilldelning till en prenumeration. Parametern gör det möjligt att återanvända en skiss som skapar en resursgrupp och andra resurser i en enskild prenumeration utan konflikter.

## <a name="blueprint-parameters"></a>Skissparametrar

Parametrar kan skapas på skissen själva via REST-API. Dessa parametrar skiljer sig från parametrarna på var och en av artefakterna som stöds. När en parameter skapas i skissen, kan du använda den med artefakter i den skissen. Ett exempel kan vara prefixet för namngivning av resursgruppen. Artefakten kan använda skissparametern för att skapa en ”huvudsakligen dynamisk” parameter. Eftersom parametern kan också definieras under tilldelning, kan det här mönstret för en konsekvens som kan följa regler för namngivning. Anvisningar finns i [inställningen statisk parametrar – skiss nivåparametern](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Med hjälp av parametrarna secureString och secureObject

När en Resource Manager-mall _artefakt_ har stöd för parametrarna för den **secureString** och **secureObject** typer, Azure skisser kräver var och en vara ansluten med ett Azure Key Vault.
Den här säkerhetsåtgärd förhindrar den osäkra metoden för att lagra hemligheter tillsammans med skissen och uppmuntrar anställning av säker mönster. Azure skisser stöder denna säkerhetsåtgärd identifierar inkludering av antingen säker parameter i en Resource Manager-mall _artefakt_. Tjänsten sedan uppmanas under tilldelning för följande egenskaper för Key Vault per identifierade säker parameter:

- Resurs-ID för Key Vault
- Namn på hemlighet för Key Vault
- Version av hemlighet för Key Vault

Den refererade Key Vault måste finnas i samma prenumeration som skissen tilldelas till.
Det måste också ha **Aktivera åtkomst till Azure Resource Manager för malldistribution** konfigurerats för Key Vault **åtkomstprinciper** sidan. Läs anvisningarna om hur du aktiverar den här funktionen [Key Vault - aktivera malldistributionen](../../../managed-applications/key-vault-access.md#enable-template-deployment). Läs mer om Azure Key Vault, [översikt över Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Parametertyper

### <a name="static-parameters"></a>Statisk parametrar

Ett parametervärde som definierats i definitionen av en skiss kallas en **Statiska parametern**, eftersom varje användning av skissen distribuerar artefakten med hjälp av det statiska värdet. I exemplet resource group även om det inte meningsfullt för namnet på resursgruppen och kan det vara klokt för platsen. Sedan varje tilldelning av skissen skulle skapa resursgruppen, oavsett den anropas under tilldelningen på samma plats. Den här flexibiliteten gör att du ska vara selektiv i vad du definierar som krävs vs vad kan ändras under tilldelning.

#### <a name="setting-static-parameters-in-the-portal"></a>Ställa in statisk parametrar i portalen

1. Klicka på **Alla tjänster** och sök efter och välj **Princip** i den vänstra fönsterrutan. Klicka på **Skisser** på sidan **Princip**.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Klicka på en befintlig skissen och klicka sedan på **redigera skiss** eller klicka på **+ skapa skiss** och Fyll i informationen på den **grunderna** fliken.

1. Klicka på **Nästa: Artefakter** eller klicka på den **artefakter** fliken.

1. Artefakter som lagts till i skissen och som har parameteralternativ visa **X Y parametrar har fyllts i** i den **parametrar** kolumn. Klicka på raden artefakt som ska redigera artefakt-parametrar.

   ![Skissparametrar](../media/parameters/parameter-column.png)

1. Den **redigera artefakt** sidan visar värdealternativ som är lämpligt att artefakten klickat på. Varje parameter på artefakten har en rubrik och en värderuta en kryssruta. Inställd rutan avmarkerad så att de blir en **Statiska parametern**. I exemplet nedan, endast _plats_ är en **statiska parametern** eftersom den har alternativet är avmarkerat och _resursgruppens namn_ kontrolleras.

   ![Statisk skissparametrar](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Ställa in statisk parametrar från REST API

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourMG}` – Ersätt med namnet på din hanteringsgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

##### <a name="blueprint-level-parameter"></a>Nivån skissparametern

När du skapar en skiss via REST-API, det är möjligt att skapa [skiss parametrar](#blueprint-parameters). Använd följande format för REST API-URI och brödtexten gör du:

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

När en nivå skissparametern har skapats kan den användas på artefakter som lagts till i den skissen.
I följande REST API-exempel skapar en rolltilldelningsartefakten på skissen och använder parametern för skissen.

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

I det här exemplet på **principalIds** egenskapen använder den **ägare** skiss nivåparametern med hjälp av värdet `[parameters('owners')]`. Ange en parameter för en artefakt som använder en skiss nivåparametern är fortfarande ett exempel på en **Statiska parametern**. Parametern för skissen kan inte anges under skisstilldelningen och kommer att samma värde för varje tilldelning.

##### <a name="artifact-level-parameter"></a>Artefakten nivåparametern

Skapa **Statiska parametrar** på en artefakt är liknande, men tar en rak värde istället för att använda den `parameters()` funktion. I följande exempel skapas två statiska parametrar, **tagName** och **matchning**. Värdet på varje ges direkt och använder inte ett funktionsanrop.

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

Motsatsen till en statisk parameter är en **dynamisk parameter**. Den här parametern inte är definierad i skissen, men i stället definieras under varje tilldelning av skissen. I exemplet resource group användning av en **dynamisk parameter** passar för resursgruppens namn. Det ger ett annat namn för varje tilldelning av skissen.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ange dynamiska parametrar i portalen

1. Klicka på **Alla tjänster** och sök efter och välj **Princip** i den vänstra fönsterrutan. Klicka på **Skisser** på sidan **Princip**.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på det som du vill tilldela. Välj **tilldela skissen** eller klicka på den modell som du vill tilldela och sedan klicka på den **tilldela skissen** knappen.

1. På den **tilldela skissen** sidan, hitta den **artefakt parametrar** avsnittet. Varje artefakt med minst en **dynamisk parameter** visar artefakten och konfigurationsalternativen. Ange obligatoriska värden för parametrarna innan du tilldelar skissen. I exemplet nedan, _namn_ är en **dynamisk parameter** som måste definieras för att slutföra skisstilldelningen.

   ![Dynamisk skissparametern](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ange dynamiska parametrar från REST API

Ange **dynamiska parametrar** under tilldelningen utförs genom att ange värdet direkt.
Istället för att använda en funktion som `parameters()`, det tillhandahållna värdet är en lämplig sträng.
Artefakter för en resursgrupp definieras med ett ”mallnamn”, **namn**, och **plats** egenskaper. Alla andra parametrar för inkluderade artefakten definieras under **parametrar** med en **\<namn\>** och **värdet** nyckelpar. Om skissen har konfigurerats för en dynamisk parameter som inte är tillgängliga under tilldelning, misslyckas tilldelningen.

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

- Lär dig mer om [livscykeln för en skiss](lifecycle.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md)
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)