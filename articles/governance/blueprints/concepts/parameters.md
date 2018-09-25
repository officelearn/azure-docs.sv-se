---
title: Skapa dynamiska skisser genom parametrar i Azure skisser
description: Läs mer om statiska och dynamiska parametrar och hur använder dem skapar dynamiska skisser.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993586"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Skapa dynamiska skisser genom parametrar

En skiss som är fullständigt definierat med olika artefakter (till exempel resursgrupper, Resource Manager-mallar, principer eller rolltilldelningar) erbjuder snabb skapandet och konsekvent etablering av objekt i Azure. Om du vill aktivera flexibel användning av dessa återanvändbara designmönster och behållare, stöder Azure skisser parametrar. Parametern skapar flexibilitet, både under definition och tilldelning för att ändra egenskaperna på artefakter som distribueras av skissen.

Ett enkelt exempel är resource group artefakten. När det skapas en resursgrupp, har två värden som krävs som måste anges: namn och plats. När du lägger till en resursgrupp din skiss om parametrar inte fanns, skulle du definiera den namn och plats för varje användning av skissen. Detta innebär att varje användning av skissen att skapa artefakter i samma resursgrupp. Även om inte ett problem med resursgruppen själva resurserna i resursgruppen skulle bli en dubblett och orsaka en konflikt.

> [!NOTE]
> Det är inte ett problem för två olika skisser att inkludera en resursgrupp med samma namn.
> Om en resursgrupp som ingår i en skiss redan finns, fortsätter skissen att skapa relaterade artefakter i den resursgruppen. Detta kan orsaka en konflikt som två resurser med samma namn och resurstypen kan inte finnas inom en prenumeration.

Det här är där parametrar kommer att få plats i. Värdet för dessa egenskaper, när det gäller resursgruppen namnet och Platsegenskapen skisser kan du inte definiera dem under definitionen av skissen, men i stället definierar du deras värden under tilldelning till en prenumeration. Detta gör det möjligt att återanvända en skiss som skapar en resursgrupp och andra resurser i en enskild prenumeration utan konflikter.

## <a name="blueprint-parameters"></a>Skissparametrar

Parametrar kan skapas på själva skissen förutom var och en av artefakterna som stöds via REST-API. När en parameter skapas i skissen, kan du använda den med artefakter i den skissen. Ett exempel kan vara prefixet för namngivning av resursgruppen. Artefakten kan sedan använda skissparametern för att skapa en ”huvudsakligen dynamisk” parameter som parametern fortfarande kan definieras under tilldelning, men har en konsekvens som kan följa regler för namngivning av organisationen. Anvisningar finns i [inställningen statisk parametrar – skiss nivåparametern](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Med hjälp av parametrarna secureString och secureObject

När en Resource Manager-mall _artefakt_ har stöd för parametrarna för den **secureString** och **secureObject** typer, Azure skisser kräver var och en vara ansluten med ett Azure Key Vault.
Detta förhindrar att den osäkra metoden för att lagra hemligheter tillsammans med skissen och uppmuntrar anställning av säker mönster. Azure skisser förenklar detta med hjälp av inkludering av antingen säker parameter i Resource Manager-mall _artefakt_ och fråga under skisstilldelningen för följande Key Vault egenskaper per upptäcks säker parameter:

- Resurs-ID för Key Vault
- Namn på hemlighet för Key Vault
- Version av hemlighet för Key Vault

Den refererade Key Vault måste finnas i samma prenumeration som skissen tilldelas till och måste också ha **Aktivera åtkomst till Azure Resource Manager för malldistribution** konfigurerats för Key Vault **åtkomst principer** sidan. Läs anvisningarna om hur du aktiverar den här funktionen [Key Vault - aktivera malldistributionen](../../../managed-applications/key-vault-access.md#enable-template-deployment).
Läs mer om Azure Key Vault, [översikt över Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Parametertyper

### <a name="static-parameters"></a>Statisk parametrar

Ett parametervärde som definierats i definitionen av en skiss kallas en **Statiska parametern**. Det beror på att varje användning av skissen distribuerar artefakten med hjälp av det statiska värdet. I exemplet resource group även om detta inte passar för namnet på resursgruppen och kan det vara klokt för platsen. Sedan varje tilldelning av skissen skulle skapa resursgruppen, oavsett den anropas under tilldelningen på samma plats. Detta kan du vara noggrann i vad du definierar som krävs vs vad kan ändras under tilldelning.

#### <a name="setting-static-parameters-in-the-portal"></a>Ställa in statisk parametrar i portalen

1. Starta tjänsten Azure skisser i Azure portal genom att klicka på **alla tjänster** och söka efter och välja **princip** i den vänstra rutan. På den **princip** klickar du på **skisser**.

1. Välj **Skissdefinitioner** från sidan till vänster.

1. Klicka på en befintlig skissen och klicka sedan på **redigera skiss** eller klicka på **+ skapa skiss** och Fyll i informationen på den **grunderna** fliken.

1. Klicka på **nästa: artefakter** eller klicka på den **artefakter** fliken.

1. Artefakter som lagts till i skissen och som har parameteralternativ visa **X Y parametrar har fyllts i** i den **parametrar** kolumn. Klicka på raden artefakt som ska redigera artefakt-parametrar.

   ![Skissparametrar](../media/parameters/parameter-column.png)

1. Den **redigera artefakt** sidan visar värdealternativ som är lämpligt att artefakten klickat på. Varje parameter på artefakten har en rubrik och en värderuta en kryssruta. Inställd rutan avmarkerad så att de blir en **Statiska parametern**. I exemplet nedan, endast _plats_ är en **statiska parametern** eftersom det är avmarkerat och _resursgruppens namn_ kontrolleras.

   ![Statisk skissparametrar](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Ställa in statisk parametrar från REST API

Det finns variabler som används för att du måste ersätta med dina egna värden i varje REST API-URI:

- `{YourMG}` – Ersätt med namnet på hanteringsgruppen
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

##### <a name="blueprint-level-parameter"></a>Nivån skissparametern

När du skapar en skiss via REST-API, det är möjligt att skapa [skiss parametrar](#blueprint-parameters). Gör du genom att använda följande REST API-URI och brödtexten format:

- URI FÖR REST-API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
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

- URI FÖR REST-API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
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

I det här exemplet på **principalIds** egenskapen gjort användning av den **ägare** skiss nivåparametern genom att tillhandahålla ett värde av `[parameters('owners')]`. Ange en parameter för en artefakt som använder en skiss nivåparametern är fortfarande ett exempel på en **Statiska parametern** som det går inte att ange under skisstilldelningen och kommer att det här värdet vid varje uppgift.

##### <a name="artifact-level-parameter"></a>Artefakten nivåparametern

Skapa **Statiska parametrar** på en artefakt är liknande, men tar en rak värde istället för att använda den `parameters()` funktion. I följande exempel skapas två statiska parametrar, **tagName** och **matchning**. Värdet på varje ges direkt och använder inte ett funktionsanrop.

- URI FÖR REST-API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
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

Motsatsen till en statisk parameter är en **dynamisk parameter**. Det här är en parameter som inte har definierats för skissen, men i stället definieras under varje tilldelning av skissen. I exemplet resource group passar det dig för resursgruppens namn, vilket ger ett annat namn för varje tilldelning av skissen.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ange dynamiska parametrar i portalen

1. Starta tjänsten Azure skisser i Azure portal genom att klicka på **alla tjänster** och söka efter och välja **princip** i den vänstra rutan. På den **princip** klickar du på **skisser**.

1. Välj **Skissdefinitioner** från sidan till vänster.

1. Högerklicka på det som du vill tilldela och väljer **tilldela skissen** eller klicka på den modell som du vill tilldela och sedan klicka på den **tilldela skissen** knappen.

1. På den **tilldela skissen** sidan, hitta den **artefakt parametrar** avsnittet. Varje artefakt med minst en **dynamisk parameter** visar artefakten och konfigurationsalternativen. Ange obligatoriska värden för parametrarna innan du tilldelar skissen. I exemplet nedan, _namn_ är en **dynamisk parameter** som måste definieras för att slutföra skisstilldelningen.

   ![Dynamisk skissparametern](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ange dynamiska parametrar från REST API

Ange **dynamiska parametrar** under tilldelningen utförs genom att ange det önskade värdet direkt. Istället för att använda en funktion som `parameters()`, det tillhandahållna värdet är en lämplig sträng. Artefakter för en resursgrupp definieras med en ”namn”, och **namn** och **plats** egenskaper. Alla andra parametrar för varje inkluderade artefakten har definierats under **parametrar** med en **\<namn\>** och **värdet** nyckelpar. Om skissen har konfigurerats för en dynamisk parameter som inte har angetts under tilldelning, misslyckas tilldelningen.

- URI FÖR REST-API

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
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

- Lär dig mer om den [skiss livscykel](lifecycle.md)
- Lär dig att anpassa den [skiss ordningsföljd](sequencing-order.md)
- Ta reda på hur du får använda [skiss resource låsning](resource-locking.md)
- Lär dig hur du [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)