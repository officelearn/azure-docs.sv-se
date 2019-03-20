---
title: Skydda nya resurser med skissresurslås
description: Lär dig att använda Azure skisser resurslås skrivskyddad och ta inte bort för att skydda nyligen distribuerade resurser.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2018
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e3a05329ea247dbf5baa23ae9b3d32f909c0d1bb
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57855770"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Skydda nya resurser med Azure skisser resurslås

Azure skisser [resurslås](../concepts/resource-locking.md) gör det möjligt att skydda nyligen distribuerade resurser från intrång, även med ett konto med den _ägare_ roll. Det här skyddet kan läggas till resurser som skapats av en Resource Manager mall artefakter i skissdefinitionen.

Följande steg beskrivs:

> [!div class="checklist"]
> - Skapa en ny skissdefinition
> - Markera din skissdefinitionen som **publicerad**
> - Tilldela en befintlig prenumeration din skissdefinition
> - Inspektera den nya resursgruppen
> - Ta bort tilldelning av skissen att ta bort låsen

## <a name="prerequisites"></a>Förutsättningar

Den här kursen krävs en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-new-blueprint-definition"></a>Skapa ny skissdefinition

Börja med att skapa nya skissdefinitionen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från den **komma igång** sidan till vänster, Välj den **skapa** knappen _skapa en skiss_.

1. Hitta den **tom exempel** skissen exempel överst på sidan och väljer **använda det här exemplet**.

1. Ange den _grunderna_ av skiss exemplet:

   - **Namn på skiss**: Ange ett namn för din kopia av exemplet skissen. Den här självstudien använder vi namnet _låst storageaccount_.
   - **Skiss beskrivning**: Beskriver skissdefinitionen. Använd ”för skissen testningsresurs låsning på distribuerade resurser”.
   - **Definitionens plats**: Använd de tre punkterna och välj hanteringsgruppen eller prenumerationen för att spara din skissdefinitionen till.

1. Välj den _artefakter_ fliken högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Lägg till resursgrupp för prenumeration: Välj den **+ Lägg till artefakt...**  rad **prenumeration**.
   Välj ”Resursgrupp” som _Artefakttyp_. Ange den _artefakt visningsnamn_ till **RGtoLock**.
   Lämna fälten _Resursgruppsnamn_ och _Plats_ tomma, men se till att kryssrutorna för varje egenskap har markerats så att de blir **dynamiska parametrar**. Lägg till den här artefakten till skissen genom att klicka på **Lägg till**.

1. Lägg till en mall under resursgruppen: Välj den **+ Lägg till artefakt...** rad den **RGtoLock** posten. Välj Azure Resource Manager-mall som _Artefakttyp_, ställ in _Artefaktvisningsnamn_ på StorageAccount och lämna _Beskrivning_ tomt. Klistra in följande Resource Manager-mall i redigeringsrutan på fliken **Mall**. När du klistrar in mallen, väljer **Lägg till** att lägga till den här artefakten i skissen.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroups('RGtoLock').location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Välj **spara utkast** längst ned på sidan.

Det här steget skapar skissdefinitionen i den valda hanteringsgruppen eller prenumerationen.

När den **sparar skissdefinitionen har tagits** portal-meddelande visas, gå vidare till nästa steg.

## <a name="publish-the-blueprint-definition"></a>Publicera skissdefinitionen

Din skissdefinitionen har skapats i din miljö. Den skapas i **Draft** läge och måste vara **publicerad** innan den kan tilldelas och distribueras.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta den _låst storageaccount_ skiss definition och markera den.

1. Välj **publicera skiss** överst på sidan. I den nya rutan till höger anger **Version** som _1.0_. Den här egenskapen är användbar för om du gör en ändring av senare. Ange **ändra anteckningar** som ”första versionen som publicerats för låsning skissen distribuerade resurser”. Välj sedan **publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När publicerats kan fortfarande ändras. Ytterligare ändringar måste publiceras med en ny **Version** värde att spåra skillnaderna mellan olika versioner av samma skissdefinitionen.

När den **publicering skiss definition lyckades** portal-meddelande visas, gå vidare till nästa steg.

## <a name="assign-the-blueprint-definition"></a>Tilldela skissdefinition

När skissdefinitionen har installerats utan **publicerad**, så kan de tilldelas en prenumeration inom den sparades till hanteringsgruppen. Det här steget är där parametrar har angetts så att varje distribution av skissdefinitionen unikt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta den _låst storageaccount_ skiss definition och markera den.

1. Välj **tilldela skissen** överst på sidan för skissen definition.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som är i hanteringsgruppen som du sparade din skissdefinitionen till. Om du väljer mer än en prenumeration skapas en tilldelning för att använda de parametrar som anges.
     - **Tilldelningsnamn**: Namnet är förifyllda baserat på namnet på skissdefinitionen. Vi vill att tilldelningen för att representera låser den nya resursgruppen, så ändra namnet på tilldelningen till _tilldelning-låst-storageaccount-TestingBPLocks_.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       Den här självstudien väljer _östra USA 2_.
     - **Definitionsversion för skissen**: Välj den **publicerad** version _1.0_ av skissdefinition.

   - Lås tilldelning

     Välj den _skrivskyddad_ skissen lock-läge. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - Hanterad identitet

     Låt standardvärdet _systemtilldelad_ alternativet. Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Artefaktparametrar

     De parametrar som definierats i det här avsnittet gäller för artefakten som den definieras. De här parametrarna kan [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definierades vid tilldelning av skissen. Ange parametervärdet för varje artefakt enligt definitionen i den **värdet** kolumn.

     |Namn på artefakt|Artefakttyp|Parameternamn|Värde|Beskrivning|
     |-|-|-|-|-|
     |RGtoLock resursgrupp|Resursgrupp|Namn|TestingBPLocks|Definierar namnet på den nya resursgruppen för att tillämpa skissen Lås till.|
     |RGtoLock resursgrupp|Resursgrupp|Plats|Västra USA 2|Definierar platsen för den nya resursgruppen för att tillämpa skissen Lås till.|
     |StorageAccount|Resource Manager-mall|storageAccountType (StorageAccount)|Standard_GRS|Välj lagrings-SKU. Standardvärdet är _Standard_LRS_.|

1. När alla parametrar har angetts, väljer **tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **Lås tilldelning**. Skissen Lås kan ta upp till 30 minuter att tillämpa.

När den **tilldela skissdefinitionen har tagits** portal-meddelande visas, gå vidare till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrollera resurser som distribueras av tilldelningen

Tilldelningen skapade resursgruppen _TestingBPLocks_ och storage-kontot som distribueras av artefakt för Resource Manager-mall. Den nya resursgruppen och status för valda fjärrlås visas på sidan med tilldelningen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **tilldelade skisser** sidan till vänster. Använd filtren för att hitta den _tilldelning-låst-storageaccount-TestingBPLocks_ skiss tilldelning och markera den.

   Vi kan se tilldelningen lyckades och resurserna som har distribuerats med den nya skissen låser upp från den här sidan. Om tilldelningen har uppdaterats kan den **tilldelningsåtgärd** listrutan visar information om distributionen av varje definitionsversion. Klicka på resursgruppen för att öppna egenskapssidan direkt.

1. Välj den **TestingBPLocks** resursgrupp.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster och sedan den **rolltilldelningar** fliken.

   Här ser vi att den _tilldelning-låst-storageaccount-TestingBPLocks_ skisstilldelningen har den _ägare_ rollen eftersom den har använts för att distribuera och lås resursgruppen.

1. Välj den **neka tilldelningar** fliken.

   Skisstilldelningen skapas en [neka tilldelning](../../../role-based-access-control/deny-assignments.md) på den distribuerade resursgruppen för att tvinga den _skrivskyddad_ skissen lock-läge. Neka tilldelningen förhindrar att någon med rätt behörighet på den _rolltilldelningar_ fliken från att utföra specifika åtgärder. Neka tilldelningen påverkar _alla huvudkonton_.

1. Välj neka tilldelningen och välj sedan den **nekad behörighet** sidan till vänster.

   Neka tilldelningen förhindrar alla åtgärder med den **\*** och **åtgärd** konfiguration, men tillåter läsbehörighet genom att exkludera  **\* /läsa**via **NotActions**.

1. Azure portal länken Välj **TestingBPLocks - åtkomstkontroll (IAM)**. Välj sedan den **översikt** sidan till vänster och sedan den **ta bort resursgrupp** knappen. Ange namnet _TestingBPLocks_ att bekräfta ta bort och välj **ta bort** längst ned i fönstret.

   Portal-meddelande **ta bort resursgrupp TestingBPLocks misslyckades** visas. Felet anger att även om ditt konto har behörighet att ta bort resursgruppen, nekas åtkomst av skisstilldelningen. Kom ihåg att vi har valt den _skrivskyddad_ skissen låsläge under skisstilldelningen. Skissen låset förhindrar att ett konto med behörighet, även _ägare_, tar bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

De här stegen visar våra distribuerade resurser är nu skyddade med skissen lås som förhindrade oönskad borttagning, även från ett konto med behörighet.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelning av skissen

Det sista steget är att ta bort tilldelning av skissdefinitionen. Ta bort tilldelningen tas inte bort vidröras artefakter.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **tilldelade skisser** sidan till vänster. Använd filtren för att hitta den _tilldelning-låst-storageaccount-TestingBPLocks_ skiss tilldelning och markera den.

1. Välj den **otilldelad skissen** längst upp på sidan. Läs varningen i bekräftelsedialogrutan och välj sedan **OK**.

   Med skisstilldelningen har tagits bort, tas skissen låsen också bort. De skapade resurserna kan återigen tas bort av ett konto med behörighet.

1. Välj **resursgrupper** från Azure-menyn, Välj **TestingBPLocks**.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster och sedan den **rolltilldelningar** fliken.

Säkerhet för resursgruppen visar att det inte längre har skisstilldelningen _ägare_ åtkomst.

När den **ta bort skisstilldelningen har tagits** portal-meddelande visas, gå vidare till nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort följande resurser:

- Resursgrupp _TestingBPLocks_
- Skiss definition _låst storageaccount_

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](../concepts/lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md)
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)