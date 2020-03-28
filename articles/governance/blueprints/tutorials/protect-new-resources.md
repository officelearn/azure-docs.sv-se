---
title: 'Självstudiekurs: Skydda nya resurser med lås'
description: I den här självstudien använder du azure blueprints-resurslåsalternativen Skrivskyddad och Ta inte bort för att skydda nyligen distribuerade resurser.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327447"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Självstudiekurs: Skydda nya resurser med Azure Blueprints-resurslås

Med Azure [Blueprints-resurslås](../concepts/resource-locking.md)kan du skydda nyligen distribuerade resurser från att manipuleras, även av ett konto med _ägarrollen._ Du kan lägga till det här skyddet i skissdefinitionerna för resurser som skapats av en Resource Manager-mallartefakt.

I den här självstudien ska du utföra följande steg:

> [!div class="checklist"]
> - Skapa en skissdefinition
> - Markera ritningsdefinitionen som **publicerad**
> - Tilldela skissdefinitionen till en befintlig prenumeration
> - Kontrollera den nya resursgruppen
> - Ta bort tilldelning av skissen för att ta bort låsen

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-a-blueprint-definition"></a>Skapa en skissdefinition

Skapa först ritningsdefinitionen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du **Skapa** under Skapa **en skiss**.

1. Leta reda på exemplet **med skissen Tom skiss** högst upp på sidan. Välj **Börja med tom skiss**.

1. Ange den här informationen på fliken **Grunderna:**

   - **Skissnamn:** Ange ett namn för ditt exemplar av skissexemplet. För den här självstudien använder vi namnet **locked-storageaccount**.
   - **Skissbeskrivning**: Lägg till en beskrivning av skissdefinitionen. Används **för att testa skissresurslåsning på distribuerade resurser**.
   - **Definitionsplats**: Välj ellipsknappen (...) och välj sedan hanteringsgruppen eller prenumerationen för att spara ritningsdefinitionen på.

1. Välj fliken **Artefakter** högst upp på sidan eller välj **Nästa: Artefakter** längst ned på sidan.

1. Lägg till en resursgrupp på prenumerationsnivå:
   1. Välj raden **Lägg till artefakt** under **Prenumeration**.
   1. Välj **Resursgrupp** under **Artefakttyp**.
   1. Ange **artefaktvisningsnamnet** till **RGtoLock**.
   1. Lämna rutorna **Resursgruppnamn** och **plats** tomma, men kontrollera att kryssrutan är markerad på varje egenskap för att göra dem **dynamiska parametrar**.
   1. Välj **Lägg till** om du vill lägga till artefakten i skissen.

1. Lägg till en mall under resursgruppen:
   1. Markera raden **Lägg till artefakt** under **RGtoLock-posten.**
   1. Välj **Azure Resource Manager-mall** under **Artefakttyp,** ange **Artefaktvisningsnamn** till **StorageAccount**och lämna **Beskrivning** tom.
   1. Klistra in följande Resource Manager-mall på fliken **Mall** i redigeringsrutan.
      När du har klistrat in mallen väljer du **Lägg till** för att lägga till artefakten i skissen.

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

1. Välj **Spara utkast** längst ned på sidan.

Det här steget skapar skissdefinitionen i den valda hanteringsgruppen eller prenumerationen.

När **portalmeddelandet Spara skissdefinitionen har slutförts** går du till nästa steg.

## <a name="publish-the-blueprint-definition"></a>Publicera skissdefinitionen

Din skissdefinition har nu skapats i din miljö. Den skapas i **utkastläge** och måste publiceras innan den kan tilldelas och distribueras.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta skissdefinitionen **för låst lagringskonto** och välj den sedan.

1. Välj **Publicera skiss** högst upp på sidan. I den nya rutan till höger anger du **1.0** som **version**. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar,** till exempel Den första versionen som **publicerats för att låsa distribuerade resurser**för skiss. Välj sedan **Publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När skissdefinitionen har publicerats kan du fortfarande göra ändringar. Om du gör ändringar måste du publicera definitionen med ett nytt versionsvärde för att spåra skillnader mellan versioner av samma skissdefinition.

När **portalmeddelandet För publiceringsriting har lyckats** visas går du till nästa steg.

## <a name="assign-the-blueprint-definition"></a>Tilldela skissdefinitionen

När skissdefinitionen har publicerats kan du tilldela den till en prenumeration inom hanteringsgruppen där du sparade den. I det här steget anger du parametrar för att göra varje distribution av skissdefinitionen unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta skissdefinitionen **för låst lagringskonto** och välj den sedan.

1. Välj **Tilldela skiss** högst upp på skissdefinitionssidan.

1. Ange parametervärdena för skisstilldelningen:

   - **Grundläggande inställningar**

     - **Prenumerationer**: Välj en eller flera av prenumerationerna i hanteringsgruppen där du sparade ritningsdefinitionen. Om du väljer mer än en prenumeration skapas en tilldelning för varje prenumeration med hjälp av de parametrar du anger.
     - **Tilldelningsnamn**: Namnet är förifyllt baserat på namnet på skissdefinitionen. Vi vill att den här tilldelningen ska representera låsning av den nya resursgruppen, så ändra tilldelningsnamnet till **tilldelning-låst-storageaccount-TestingBPLocks**.
     - **Plats**: Välj en region där den hanterade identiteten ska skapas. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       För den här självstudien väljer du **Östra USA 2**.
     - **Blueprint definition version**: Välj den publicerade versionen **1.0** av skissdefinitionen.

   - **Lås tilldelning**

     Välj **skrivskyddat** skisslåsläge. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - **Hanterad identitet**

     Använd standardalternativet: **Systemtilldelade**. Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Artefaktparametrar**

     De parametrar som definieras i det här avsnittet gäller för den artefakt som de definieras under. Dessa parametrar är [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definieras under tilldelningen av skissen. För varje artefakt anger du parametervärdet till det du ser i kolumnen **Värde.**

     |Artefaktnamn|Artefakttyp|Parameternamn|Värde|Beskrivning|
     |-|-|-|-|-|
     |Resursgrupp för RGtoLock|Resursgrupp|Namn|TestaBPLocks|Definierar namnet på den nya resursgruppen som skisslås ska användas på.|
     |Resursgrupp för RGtoLock|Resursgrupp|Location|USA, västra 2|Definierar platsen för den nya resursgruppen som skisslås ska användas på.|
     |StorageAccount|Resource Manager-mall|storageAccountType (StorageAccount)|Standard_GRS|Lagrings-SKU. Standardvärdet är _Standard_LRS_.|

1. När du har angett alla parametrar väljer du **Tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **låstilldelningen**. Det kan ta upp till 30 minuter att använda skisslås.

När **portalmeddelandet Tilldela skissdefinitionen visas** går du till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspektera resurser som distribueras av tilldelningen

Tilldelningen skapar resursgruppen _TestingBPLocks_ och lagringskontot som distribueras av Resource Manager-mallartefakten. Den nya resursgruppen och det valda låsläget visas på sidan tilldelningsinformation.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Tilldelade ritningar** till vänster. Använd filtren för att hitta **den tilldelning-låst-storageaccount-TestingBPLocks** skisstilldelning och välj den sedan.

   Från den här sidan kan vi se att tilldelningen lyckades och att resurserna har distribuerats med det nya skisslåstillståndet. Om tilldelningen uppdateras visar listrutan **Tilldelningsåtgärd** information om distributionen av varje definitionsversion. Du kan välja den resursgrupp som ska öppnas för egenskapssidan.

1. Välj resursgruppen **TestingBPLocks.**

1. Välj sidan **Access control (IAM)** till vänster. Välj sedan fliken **Rolltilldelningar.**

   Här ser vi att _tilldelning-låst-storageaccount-TestingBPLocks_ skisstilldelning har _ägarrollen._ Den har den här rollen eftersom den här rollen användes för att distribuera och låsa resursgruppen.

1. Välj fliken **Neka tilldelningar.**

   Skisstilldelningen skapade en [neka tilldelning](../../../role-based-access-control/deny-assignments.md) för den distribuerade resursgruppen för att framtvinga skisslåsläget **skrivskydd.** Neka tilldelning förhindrar att någon med lämpliga rättigheter på fliken **Rolltilldelningar** vidtar specifika åtgärder. Neka-tilldelningen påverkar _Alla huvudnamn_.

   Information om hur du utesluter ett huvudnamn från en neka tilldelning finns i [skisser resurslåsning](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Markera neka tilldelningen och välj sedan sidan **Nekad behörighet till** vänster.

   Neka tilldelning förhindrar alla åtgärder **\*** med och **Åtgärd** konfiguration, men det tillåter läsåtkomst genom att utesluta ** \*/läs** via **NotActions**.

1. I Azure-portalens brödsmula väljer du **TestingBPLocks - Access control (IAM)**. Välj sedan sidan **Översikt** till vänster och sedan knappen **Ta bort resursgrupp.** Ange namnet **TestingBPLocks** för att bekräfta borttagningen och välj sedan **Ta bort** längst ned i fönstret.

   Det gick inte att **ta bort resursgruppen TestingBPLocks i portalmeddelandet.** Felet anger att även om ditt konto har behörighet att ta bort resursgruppen nekas åtkomst av skisstilldelningen. Kom ihåg att vi valde **skrivskyddat** skisslåsläge under skisstilldelning. Skisslåset hindrar ett konto med behörighet, även _Ägare,_ från att ta bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

Dessa steg visar att våra distribuerade resurser nu skyddas med skisslås som förhindrar oönskad borttagning, även från ett konto som har behörighet att ta bort resurserna.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelning av skissen

Det sista steget är att ta bort tilldelningen av skissdefinitionen. Om du tar bort tilldelningen tas inte de associerade artefakterna bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Tilldelade ritningar** till vänster. Använd filtren för att hitta **den tilldelning-låst-storageaccount-TestingBPLocks** skisstilldelning och välj den sedan.

1. Välj **Ta bort skissen** överst på sidan. Läs varningen i bekräftelsedialogrutan och välj sedan **OK**.

   När skisstilldelningen tas bort tas även skisslåsen bort. Resurserna kan återigen tas bort av ett konto med rätt behörighet.

1. Välj **Resursgrupper** på Azure-menyn och välj sedan **TestingBPLocks**.

1. Välj sidan **Access control (IAM)** till vänster och välj sedan fliken **Rolltilldelningar.**

Säkerheten för resursgruppen visar att skisstilldelningen inte längre har _ägaråtkomst._

När **portalmeddelandet Ta bort skisstilldelningen** visas går du till nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort följande resurser:

- Testning _av resursgruppSBPLocks_
- Konto _för låst lagringskonto för skissdefinition_

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skyddar nya resurser som distribueras med Azure Blueprints. Om du vill veta mer om Azure Blueprints fortsätter du till artikeln om skisslivscykeln.

> [!div class="nextstepaction"]
> [Läs mer om skisslivscykeln](../concepts/lifecycle.md)