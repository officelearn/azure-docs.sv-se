---
title: Skapa och hantera principer för att använda kompatibilitet
description: Använd Azure Policy för att genomdriva standarder, efterleva krav på regelefterlevnad och granskning, kontrollera kostnader, behålla en konsekvent säkerhet och prestanda och tillämpa företagsomfattande principer.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1641a88a95d4c056cdd1be8d855482c80b1430cc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283621"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Skapa och hantera principer för att använda kompatibilitet

Det är viktigt att du förstår hur du skapar och hanterar principer i Azure för att du ska fortsätta att efterleva företagets standarder och servicenivåavtal. I den här självstudien lär du dig att använda Azure Policy för att utföra några av de vanligaste uppgifterna som att skapa, tilldela och hantera principer i organisationen, exempelvis:

> [!div class="checklist"]
> - Tilldela en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> - Skapa och tilldela en initiativdefinition för att spåra efterlevnad för flera resurser
> - Lösa en icke-kompatibel eller nekad resurs
> - Implementera en ny princip i en organisation

Om du vill tilldela en princip för att identifiera dina befintliga resursers efterlevnadstillstånd går artiklarna i snabbstarten igenom det. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="assign-a-policy"></a>Tilldela en princip

Det första steget för att tillämpa efterlevnad av Azure Policy är att tilldela en principdefinition. En principdefinition anger enligt vilket villkor en princip tillämpas och vilken åtgärd som ska vidtas. I det här exemplet ska du tilldela en inbyggd principdefinition som heter *Kräv SQL Server version 12.0*, för att tillämpa villkoret att alla SQL Server-databaser måste vara v12.0 för att vara kompatibla.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter princip i alla tjänster](../media/create-and-manage/search-policy.png)

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   ![Klicka på tilldelningar från principöversikt](../media/create-and-manage/select-assignments.png)

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   ![Tilldela en principdefinition från sidan tilldelningar](../media/create-and-manage/select-assign-policy.png)

1. På sidan **Tilldela princip** väljer du **Omfång** genom att klicka på ellipsen och antingen välja en hanteringsgrupp eller en prenumeration. Du kan även välja en resursgrupp. En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på.  Klicka sedan på **Välj** längst ned på sidan **Omfång**.

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**.  **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Du kan filtrera principdefinitionen **Typ** som *Inbyggt* om du vill se alla och läsa deras beskrivningar.

1. Välj **Kräv SQL Server version 12.0**. Om du inte hittar det genast skriver du **require sql server** (kräv sql server) i sökrutan och trycker sedan på RETUR eller klickar utanför sökrutan. Klicka på **Välj** längst ned på sidan **Tillgängliga definitioner** när du har hittat och valt principdefinitionen.

   ![Använd sökrutan filtrera för att hitta en princip](../media/create-and-manage/select-available-definition.png)

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet låter du *Kräv SQL Server version 12.0* vara kvar. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.  **Tilldelad av** fylls automatiskt i baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när den princip eller det initiativ som tilldelas omfattar en princip med effekten [deployIfNotExists](../concepts/effects.md#deployifnotexists). Eftersom den princip som används för den här självstudien inte gör det kan du lämna den tom. Mer information finns i avsnitten [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Klicka på **Tilldela**.

## <a name="implement-a-new-custom-policy"></a>Implementera en ny anpassad princip

Nu när du har tilldelat en inbyggd principdefinition kan du göra mer med Azure Policy. Sedan skapar du en ny anpassad princip för att spara kostnader genom att kontrollera att de virtuella datorer som skapats i din miljö inte kan vara i G-serien. På så sätt nekas varje gång en begäran från en användare i organisationen som försöker skapa en virtuell dator i G-serien.

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

   ![Sidan definition under redigering grupp](../media/create-and-manage/definition-under-authoring.png)

1. Välj **+ Principdefinition** överst på sidan. Den här knappen öppnar sidan **Principdefinition**.

1. Ange följande information:

   - Hanteringsgruppen eller prenumerationen där principdefinitionen sparas. Välj **Definitionens plats** med ellipsen.

     > [!NOTE]
     > Om du tänker tillämpa denna principdefinition på flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller de prenumerationer som du tilldelar principen. Detsamma gäller för en initiativdefinition.

   - Namnet på principdefinitionen – *Kräv VM SKU:er som är mindre än G-serien*
   - Beskrivning av vad principdefinitionen är avsedd att göra – *Den här principdefinitionen tvingar alla virtuella datorer som är skapade i omfånget att ha SKU:er som är mindre än G-serien för att minska kostnaderna.*
   - Välj mellan befintliga alternativ (som _Compute_), eller skapa en ny kategori för den här principdefinitionen.
   - Kopiera följande JSON-kod och uppdatera den sedan efter dina behov med:
      - Principparametrarna.
      - Principens regler/villkor, i det här fallet – VM SKU-storlek som motsvarar G-serien
      - Principens effekt, i det här fallet – **Neka**.

   Så här ska din JSON se ut. Klistra in den reviderade koden i Azure-portalen.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   Egenskapen *fält* i principregeln måste vara något av följande värden: Namn, Typ, Plats, Taggar eller ett alias. Ett exempel på ett alias kan vara `"Microsoft.Compute/VirtualMachines/Size"`.

   Fler Azure-principexempel finns i [Azure Policy-mallar](../samples/index.md).

1. Välj **Spara**.

## <a name="create-a-policy-definition-with-rest-api"></a>Skapa en principdefinition med REST API

Du kan skapa en princip med REST API för principdefinitioner. Med REST API kan du skapa och ta bort principdefinitioner och få information om befintliga definitioner. Om du vill skapa en principdefinition använder du följande exempel:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Inkludera en begärantext som liknar följande exempel:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Skapa en principdefinition med PowerShell

Innan du fortsätter med PowerShell-exemplet ska du kontrollera att du har installerat den senaste versionen av Azure PowerShell Az-modulen. 

Du kan skapa en principdefinition med cmdleten `New-AzPolicyDefinition`.

Om du vill skapa en principdefinition från en fil skickar du sökvägen till filen. Använd följande exempel för en extern fil:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Använd följande exempel för en lokal fil:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Utdata lagras i ett `$definition`-objekt som används under principtilldelningen. I följande exempel skapas en principdefinition som innehåller parametrarna:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Visa principdefinitioner med PowerShell

Om du vill visa alla principdefinitioner i din prenumeration använder du följande kommando:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Skapa en principdefinition med Azure CLI

Du kan skapa en principdefinition med Azure CLI med kommandot för principdefinition. Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Visa principdefinitioner med Azure CLI

Om du vill visa alla principdefinitioner i din prenumeration använder du följande kommando:

```azurecli-interactive
az policy definition list
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Skapa och tilldela en initiativdefinition

Med en initiativdefinition kan du gruppera flera principdefinitioner för att uppnå ett övergripande mål. Ett initiativ utvärderar resurser inom omfånget för tilldelningen för efterlevnad med tillhörande principer. Se [Översikt över Azure Policy](../overview.md) för mer information om initiativdefinitioner.

### <a name="create-an-initiative-definition"></a>Skapa en initiativdefinition

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

   ![Välj definition från sidan definitioner](../media/create-and-manage/definition-under-authoring.png)

1. Välj **+ Initiativdefinition** överst på sidan för att öppna sidan **Initiativdefinition**.

   ![Initiativdefinitionen granskningssidan](../media/create-and-manage/initiative-definition.png)

1. Använd ellipsen **Definitionens plats**  och välj en hanteringsgrupp eller en prenumeration där definitionen ska lagras. Om föregående sida begränsades till en enskild hanteringsgrupp eller prenumeration så fylls **definitionsplats** i automatiskt.

1. Ange **Namn** och **Beskrivning** för initiativet.

   Det här exemplet kontrollerar att resurser efterlever principdefinitioner om säkerhet. Namnge initiativet **Get Secure** och ange beskrivningen som: **Det här initiativet har skapats för att hantera principdefinitioner associerade med att skydda resurser**.

1. Välj bland befintliga alternativ eller skapa en ny kategori i **Kategori**.

1. Bläddra igenom listan **Tillgängliga definitioner** (högre halvan av sidan **Initiativdefinition**) och välj de principdefinitioner som du vill lägga till i initiativet. För initiativet **Get Secure** lägger du till följande inbyggda principdefinitioner genom att klicka på **+** bredvid principdefinitionens information eller klicka på en rad för principdefinitionen och sedan på alternativet **+ Lägg till** på informationssidan:

   - Kräv SQL Server version 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   När du har valt principdefinitionen i listan läggs den till under **Principer och parametrar**.

   ![Granska initiativdefinitionen parametrar](../media/create-and-manage/initiative-definition-2.png)

1. Om en principdefinition som läggs till i initiativet har parametrar visas de under namnet på principen i området **Principer och parametrar**. _Värdet_ kan anges till antingen Ange värde (hårdkodat för alla tilldelningar i initiativet) eller Använd initiativparametern (ställs in under varje initiativtilldelning). Om du väljer "Ange värde" kan du via listrutan till höger om _Värden_ ange eller välja värden. Om du väljer Använd initiativparametern så visas ett nytt avsnitt **Initiativparameter** där du kan definiera den parameter som ställs in under initiativtilldelningen. Tillåtna värden för den här initiativparametern kan ytterligare begränsa vad du kan ange under initiativtilldelningen.

   ![Ändra initiativdefinitionen parametrar från tillåtna värden](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > När det gäller vissa parametrar av typen `strongType` går det inte att automatiskt fastställa listan med värden. I de här fallen visas en ellips till höger om parameterraden. Om du klickar på den så öppnas sidan Parameteromfång (&lt;Parameternamn&gt;). På den här sidan väljer du den prenumeration som ska användas för att tillhandahålla värdealternativen. Det här parameterområdet används bara när initiativdefinitionen skapas. Den påverkar inte principutvärderingen eller initiativets omfattning efter tilldelningen.

1. Klicka på **Spara**.

### <a name="assign-an-initiative-definition"></a>Tilldela en initiativdefinition

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

1. Leta upp initiativdefinitionen **Bli säker** som du skapade tidigare och markera den. Välj **Tilldela** överst på sidan för att öppna sidan **Get Secure: Assign initiative** (Tilldela initiativ).

   ![Tilldela en definition från sidan för definition av initiativ](../media/create-and-manage/assign-definition.png)

   Du kan även högerklicka på den markerade raden eller vänsterklicka på ellipsen i slutet av raden för att få upp en snabbmeny.  Välj sedan **Tilldela**.

   ![Alternativ för ett initiativ](../media/create-and-manage/select-right-click.png)

1. Fyll i sidan **Get Secure: Assign Initiative** genom att ange följande exempelinformation. Du kan använda din egen information.

   - Omfång: Den hanteringsgrupp eller prenumeration som du sparade initiativet till blir standard.  Du kan ändra omfånget för att tilldela initiativet till en prenumeration eller resursgrupp inom lagringsplatsen.
   - Undantag: Konfigurera några resurser inom omfånget för att förhindra att initiativtilldelningen tillämpas på dem.
   - Initiativdefinition och tilldelningsnamn: Get Secure (förifylld som namnet på det initiativ som tilldelas).
   - Beskrivning: Initiativtilldelningen är skräddarsydd för att tillämpa den här gruppen med principdefinitioner.
   - Tilldelad av: Anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när den princip eller det initiativ som tilldelas omfattar en princip med effekten [deployIfNotExists](../concepts/effects.md#deployifnotexists). Eftersom den princip som används för den här självstudien inte gör det kan du lämna den tom. Mer information finns i avsnitten [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Klicka på **Tilldela**.

## <a name="check-initial-compliance"></a>Kontrollera inledande efterlevnad

1. Välj **Efterlevnad** till vänster på Azure Policy-sidan.

1. Leta upp initiativet **Hämta källan**. Det är sannolikt fortfarande i _Efterlevnadstillståndet_ **Inte startat**. Klicka på initiativet att få fullständig information om förloppet för tilldelningen.

   ![Initiativkompatibilitet sidan – utvärderingar som inte har startats](../media/create-and-manage/compliance-status-not-started.png)

1. När initiativtilldelningen har slutförts, uppdateras sidan för efterlevnad med den _Efterlevnadstillståndet_ **Efterlever**.

   ![Initiativkompatibilitet sidan-resurser kompatibel](../media/create-and-manage/compliance-status-compliant.png)

1. När du klickar på en princip på sidan initiativefterlevnad så öppnas sidan med efterlevnadsinformation för principen. Den här sidan ger information på resursnivån för efterlevnad.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Undanta en icke-kompatibel eller nekad resurs med Exkludering

När du har angett att principdefinitionen kräver version 12.0 av SQL Server skulle en SQL-server som har skapats med en annan version än 12.0 nekas, precis som i exemplet ovan. I det här avsnittet går vi igenom hur du hanterar en nekad begäran att skapa en SQL-server genom att skapa ett undantag på en enskild resursgrupp. Undantaget förhindrar att principen (eller initiativet) tillämpas på resursen.
Följande exempel tillåter alla SQL-serverversioner i en enskild resursgrupp. Ett undantag kan gälla för en prenumeration, resursgrupp eller så kan du begränsa undantaget till enskilda resurser.

En distribution som har förhindrats av en tilldelad princip eller ett initiativ kan visas på två platser:

- I den resursgrupp som är mål för distributionen: Välj **Distributioner** till vänster på sidan och klicka på **Distributionsnamn** för den misslyckade distributionen. Resursen som nekades visas med statusen _Förbjuden_. För att fastställa den princip, det initiativ och den tilldelning som har nekat resursen, klickar du på **Misslyckades. Klicka här för information ->** på sidan Distributionsöversikt. Ett fönster öppnas till höger om sidan med felinformationen. Under **Felinformation** finns GUID för de relaterade principobjekten.

  ![Distributionen nekas av principtilldelningen](../media/create-and-manage/rg-deployment-denied.png)

- På sidan Azure Policy: Välj **Efterlevnad** till vänster på sidan och klicka på principen **Kräv SQL Server version 12.0**. På sidan som öppnas visas en ökning i antalet för **Neka**. På fliken **Händelser** visas även vem som försökte utföra den distribution som nekades av principen.

  ![Efterlevnadsöversikt för en tilldelad princip](../media/create-and-manage/compliance-overview.png)

I det här exemplet utförde Trent Baker, en av Contosos seniora virtualiseringsspecialister, obligatoriskt arbete. Vi behöver bevilja Trent ett undantag, men vi vill inte ha de SQL-servrar som inte är av version 12.0 i godtyckliga resursgrupper. Vi har skapat den nya resursgruppen **SQLServers_Excluded** och kommer nu att bevilja ett undantag till den för principtilldelningen.

### <a name="update-assignment-with-exclusion"></a>Uppdatera tilldelning med undantag

1. Välj **Tilldelningar** under **Redigering** till vänster om Azure Policy-sidan.

1. Bläddra igenom alla principtilldelningar och öppna tilldelningen *Kräv SQL Server version 12.0*.

1. Ange **Undantag** genom att klicka på ellipsen och välj den resursgrupp som ska exkluderas, vilket är *SQLServers_Excluded* i det här exemplet.

   ![Lägga till en exkluderade resursgrupp i principtilldelningen](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Beroende på principen och dess effekt skulle undantaget också kunna beviljas till specifika resurser i en resursgrupp inom omfånget för tilldelningen. Eftersom **Neka** har använts i den här självstudien, gör det ingen skillnad att ange undantag för en specifik resurs som redan finns.

1. Klicka på **Välj** och sedan på **Spara**.

I det här avsnittet löste du den nekade begäran genom att skapa ett undantag på en enskild resursgrupp.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med att arbeta med resurser i den här självstudien kan du använda följande steg för att ta bort tilldelningar eller definitioner som skapades ovan:

1. Välj **Definitioner** (eller **Tilldelningar** om du ska ta bort en tilldelning) under **Redigering** till vänster på sidan Azure Policy.

1. Sök efter den nya initiativ- eller principdefinition (eller tilldelning) som du vill ta bort.

1. Högerklicka på raden eller välj ellipserna i slutet av definitionen (eller tilldelningen) och välj **Ta bort definition** (eller **Ta bort tilldelning**).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du genomfört följande uppgifter:

> [!div class="checklist"]
> - Tilldelat en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> - Skapat och tilldelat en initiativdefinition för att spåra efterlevnad för flera resurser
> - Löst en icke-kompatibel eller nekad resurs
> - Implementerat en ny princip i en organisation

Mer information om principdefinitionernas strukturer finns i den här artikeln:

> [!div class="nextstepaction"]
> [Azure Policy-definitionsstruktur](../concepts/definition-structure.md)