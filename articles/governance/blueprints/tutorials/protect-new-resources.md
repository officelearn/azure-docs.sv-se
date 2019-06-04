---
title: Självstudie – skydda nya resurser med skissen resurslås
description: I de här självstudierna lär du dig att använda Azure skisser resource Lås alternativ skrivskyddad och ta inte bort för att skydda nyligen distribuerade resurser.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 274c437acd8df50d631727fc352c4b9ebecead18
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479969"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Självstudier: Skydda nya resurser med Azure skisser resurslås

Med Azure skisser [resurslås](../concepts/resource-locking.md), kan du skydda nyligen distribuerade resurser från att manipuleras, även med ett konto med den _ägare_ roll. Du kan lägga till det här skyddet i skissdefinitioner av resurser som skapats av en Resource Manager-mall-artefakt.

I de här självstudierna ska du utföra följande steg:

> [!div class="checklist"]
> - Skapa en skissdefinition
> - Markera din skissdefinitionen som **publicerad**
> - Tilldela en befintlig prenumeration din skissdefinition
> - Inspektera den nya resursgruppen
> - Ta bort tilldelning av skissen att ta bort låsen

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-blueprint-definition"></a>Skapa en skissdefinition

Börja med att skapa skissdefinitionen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På den **komma igång** sidan till vänster väljer **skapa** under **skapa en skiss**.

1. Hitta den **tom skiss** skissen exempel längst upp på sidan. Välj **starta med tom skiss**.

1. Ange den här informationen på den **grunderna** fliken:

   - **Namn på skiss**: Ange ett namn för din kopia av exemplet skissen. Den här självstudien använder vi namnet **låst storageaccount**.
   - **Skiss beskrivning**: Lägg till en beskrivning för skissdefinitionen. Använd **distribuerade resurser för testning skissen resource låsning på**.
   - **Definitionens plats**: Välj knappen med tre punkter (...) och välj sedan hanteringsgruppen eller prenumerationen för att spara din skissdefinitionen till.

1. Välj den **artefakter** fliken högst upp på sidan eller välj **nästa: Artefakter** längst ned på sidan.

1. Lägg till en resursgrupp på prenumerationsnivå:
   1. Välj den **Lägg till artefakt** rad **prenumeration**.
   1. Välj **resursgrupp** under **artefakttypen**.
   1. Ange den **artefakt visningsnamn** till **RGtoLock**.
   1. Lämna den **Resursgruppsnamn** och **plats** rutorna tom, men kontrollera att kryssrutan är markerad på varje egenskap så att de **dynamiska parametrar**.
   1. Välj **Lägg till** att lägga till artefakten i skissen.

1. Lägga till en mall under resursgrupp:
   1. Välj den **Lägg till artefakt** rad den **RGtoLock** posten. 
   1. Välj **Azure Resource Manager-mall** under **artefakttypen**anger **artefakt visningsnamn** till **StorageAccount**, och lämna  **Beskrivning av** tom. 
   1. På den **mall** och klistra in följande Resource Manager-mallen i rutan redigeraren. När du klistrar in i mallen, väljer **Lägg till** att lägga till artefakten i skissen.

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
           "location": "[resourceGroup().location]",
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

Efter den **sparar skissdefinitionen har tagits** portal-meddelande visas ska du gå till nästa steg.

## <a name="publish-the-blueprint-definition"></a>Publicera skissdefinitionen

Din skissdefinitionen har skapats i din miljö. Den skapas i **Draft** läge och måste publiceras innan den kan tilldelas och distribueras.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta den **låst storageaccount** skiss definition och markera den.

1. Välj **publicera skiss** överst på sidan. I den nya rutan till höger anger **1.0** som den **Version**. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändra anteckningar**, till exempel **första versionen som publicerats för låsning skissen distribuerade resurser**. Välj sedan **publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När skissdefinitionen har publicerats kan göra du fortfarande ändringar. Om du gör ändringar, måste du publicera definition med ett nytt versionsvärde att spåra skillnaderna mellan versioner av samma skissdefinitionen.

Efter den **publicering skiss definition lyckades** portal-meddelande visas ska du gå till nästa steg.

## <a name="assign-the-blueprint-definition"></a>Tilldela skissdefinition

När skissdefinitionen har publicerats kan tilldela du den till en prenumeration inom hanteringsgruppen där du sparade den. I det här steget ska ange du parametrar för att göra varje distribution av skissdefinitionen unikt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta den **låst storageaccount** skiss definition och markera den.

1. Välj **tilldela skissen** överst på sidan för skissen definition.

1. Ange parametervärden för skisstilldelningen:

   - **Grundläggande inställningar**

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som är i hanteringsgruppen där du sparade din skissdefinition. Om du väljer mer än en prenumeration skapas en uppgift för varje prenumeration med parametrar som du anger.
     - **Tilldelningsnamn**: Namnet är förifyllda baserat på namnet på skissdefinitionen. Vi vill att tilldelningen för att representera låser den nya resursgruppen, så ändra namnet på tilldelningen till **tilldelning-låst-storageaccount-TestingBPLocks**.
     - **Plats**: Välj en region där du vill skapa den hanterade identitet. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       Den här självstudien väljer **östra USA 2**.
     - **Definitionsversion för skissen**: Välj den publicerade versionen **1.0** av skissdefinition.

   - **Tilldelning av Lås**

     Välj den **skrivskyddad** skissen lock-läge. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - **Hanterad identitet**

     Använd alternativet: **Systemtilldelad**. Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Artefakten parametrar**

     De parametrar som definierats i det här avsnittet gäller för artefakten som definieras. De här parametrarna kan [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definierades vid tilldelning av skissen. För varje artefakt anger du parametervärdet till det som visas i den **värdet** kolumn.

     |Artefaktnamn|Typ av artefakt|Parameternamn|Värde|Beskrivning|
     |-|-|-|-|-|
     |RGtoLock resursgrupp|Resursgrupp|Namn|TestingBPLocks|Definierar namnet på den nya resursgruppen för att tillämpa skissen Lås till.|
     |RGtoLock resursgrupp|Resursgrupp|Location|Västra USA 2|Definierar platsen för den nya resursgruppen för att tillämpa skissen Lås till.|
     |StorageAccount|Resource Manager-mall|storageAccountType (StorageAccount)|Standard_GRS|Lagrings-SKU. Standardvärdet är _Standard_LRS_.|

1. När du har angett alla parametrar, Välj **tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **Lås tilldelning**. Det kan ta upp till 30 minuter att tillämpa skissen Lås.

Efter den **tilldela skissdefinitionen har tagits** portal-meddelande visas ska du gå till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrollera resurser som distribueras av tilldelningen

Tilldelningen skapar resursgruppen _TestingBPLocks_ och storage-kontot som distribueras av artefakt för Resource Manager-mall. Den nya resursgruppen och status för valda fjärrlås visas på sidan med tilldelningen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **tilldelade skisser** sidan till vänster. Använd filtren för att hitta den **tilldelning-låst-storageaccount-TestingBPLocks** skiss tilldelning och markera den.

   Vi kan se att tilldelningen lyckades och att resurserna som har distribuerats med den nya skissen låser upp från den här sidan. Om tilldelningen har uppdaterats kan den **tilldelningsåtgärd** listrutan visar information om distributionen av varje definitionsversion. Du kan välja resursgruppen för att öppna egenskapssidan.

1. Välj den **TestingBPLocks** resursgrupp.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster. Välj sedan den **rolltilldelningar** fliken.

   Här ser vi att den _tilldelning-låst-storageaccount-TestingBPLocks_ skisstilldelningen har den _ägare_ roll. Den har den här rollen eftersom den här rollen har använts för att distribuera och lås resursgruppen.

1. Välj den **neka tilldelningar** fliken.

   Skisstilldelningen skapas en [neka tilldelning](../../../role-based-access-control/deny-assignments.md) på den distribuerade resursgruppen för att tvinga den **skrivskyddad** skissen lock-läge. Neka tilldelningen förhindrar att någon med rätt behörighet på den **rolltilldelningar** fliken från att utföra specifika åtgärder. Neka tilldelningen påverkar _alla huvudkonton_.

   Läs om hur exkludera ett huvudnamn för från en tilldelning av neka [skisser resource låsning](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Välj neka tilldelningen och välj sedan den **nekad behörighet** sidan till vänster.

   Neka tilldelningen förhindrar alla åtgärder med den **\*** och **åtgärd** konfiguration, men det ger läsbehörighet genom att exkludera  **\* /läsa**via **NotActions**.

1. I Azure portal länken Välj **TestingBPLocks - åtkomstkontroll (IAM)** . Välj sedan den **översikt** sidan till vänster och sedan den **ta bort resursgrupp** knappen. Ange namnet **TestingBPLocks** att bekräfta ta bort och välj sedan **ta bort** längst ned i fönstret.

   Portal-meddelande **ta bort resursgrupp TestingBPLocks misslyckades** visas. Felet anger att även om ditt konto har behörighet att ta bort resursgruppen, nekas åtkomst av skisstilldelningen. Kom ihåg att vi har valt den **skrivskyddad** skissen låsläge under skisstilldelningen. Skissen låset förhindrar att ett konto med behörighet, även _ägare_, tar bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

De här stegen visar våra distribuerade resurser är nu skyddade med skissen lås som förhindrar oönskad borttagning, även från ett konto som har behörighet att ta bort resurserna.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelning av skissen

Det sista steget är att ta bort tilldelning av skissdefinitionen. Ta bort tilldelningen tas inte bort relaterade artefakter.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **tilldelade skisser** sidan till vänster. Använd filtren för att hitta den **tilldelning-låst-storageaccount-TestingBPLocks** skiss tilldelning och markera den.

1. Välj **otilldelad skissen** överst på sidan. Läs varningen i den bekräftande dialogrutan och välj sedan **OK**.

   När skisstilldelningen tas bort tas skissen låsen också bort. Resurserna kan återigen tas bort med ett konto med rätt behörighet.

1. Välj **resursgrupper** från Azure-menyn och välj sedan **TestingBPLocks**.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster och välj sedan den **rolltilldelningar** fliken.

Säkerhet för resursgruppen visar att det inte längre har skisstilldelningen _ägare_ åtkomst.

Efter den **ta bort skisstilldelningen har tagits** portal-meddelande visas ska du gå till nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de här resurserna när du är klar med den här självstudien:

- Resursgrupp _TestingBPLocks_
- Skiss definition _låst storageaccount_

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [skiss livscykel](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Ta reda på hur du använder [skiss resource låsning](../concepts/resource-locking.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- [Felsöka problem med](../troubleshoot/general.md) under tilldelningen av en skiss.
