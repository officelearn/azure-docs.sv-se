---
title: Skapa en skiss i portalen
description: Använd Azure Blueprints för att skapa, definiera och distribuera artefakter via Azure-portalen.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0a41f038595524a9ffaa5134ca2fe53fc0ae83af
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338384"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>Definiera och tilldela en Azure-skiss i portalen

När du skapar och tilldelar skisser kan definitionen av vanliga mönster utveckla återanvändbara och snabbt distribuerbara konfigurationer baserade på Resource Manager-mallar, principer, säkerhet med mera. I den här självstudien får du lära dig att använda Azure Blueprint för att utföra några av de vanliga uppgifter som rör generering, publicering och tilldelning av en skiss i din organisation. Du lär dig till exempel att:

> [!div class="checklist"]
> - Skapa en ny skiss och lägga till olika artefakter som stöds
> - Göra ändringar i en befintlig skiss som fortfarande har **utkaststatus**
> - Markera en skiss som redo att tilldelas med **Publicerad**
> - Tilldela en skiss till en befintlig prenumeration
> - Kontrollera statusen och förloppet för en tilldelad skiss
> - Ta bort en skiss som en prenumeration har tilldelats

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. I det här exemplet skapar du en ny skiss med namnet ”MyBlueprint” för att konfigurera roll- och principtilldelningar för prenumerationen, lägger till en ny resursgrupp och skapar en Resource Manager-mall och rolltilldelning för den nya resursgruppen.

1. Klicka på **Alla tjänster** och sök efter och välj **Princip** i den vänstra fönsterrutan. Klicka på **Skisser** på sidan **Princip**.

1. Välj **Skissdefinitioner** från sidan till vänster och klicka på knappen **+ Skapa skiss** högst upp på sidan.

   - Du kan också klicka på **Skapa** på sidan **Komma igång** och gå direkt till att skapa en skiss.

   ![Skapa skiss](./media/create-blueprint-portal/create-blueprint-button.png)

1. Ange ett **Skissnamn**, t.ex. MyBlueprint (bokstäver och siffror – upp till 48 tecken, men inga blanksteg eller specialtecken) för skissen, men lämna **Skissbeskrivning** tomt tills vidare.  Klicka på de tre punkterna till höger i rutan **Definitionsplats**, välj den [hanteringsgrupp](../management-groups/overview.md) eller prenumeration där du vill spara skissen och klicka på **Välj**.

1. Kontrollera att informationen är korrekt (fälten **Skissnamn** och **Definitionsplats** kan inte ändras senare) och klicka på **Nästa: Artefakter** längst ned på sidan eller på fliken **Artefakter** högst upp på sidan.

1. Lägg till rolltilldelningen för prenumerationen: Vänsterklicka på raden **+ Lägg till artefakt...** under **Prenumeration**, varvid fönstret ”Lägg till artefakt” öppnas till höger i webbläsaren. Välj ”Rolltilldelning” som _Artefakttyp_. Välj Deltagare under _Roll_ och lämna fältet _Lägg till användare, app eller grupp_ fältet med kryssrutan som anger en **dynamisk parameter**. Lägg till den här artefakten till skissen genom att klicka på **Lägg till**.

   ![Artefakt – rolltilldelning](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > De flesta _artefakter_ stöder parametrar. En parameter som tilldelas ett värde när skissen skapas är en **statisk parameter**. Om parametern tilldelas under skisstilldelningen är den en **dynamisk parameter**. Mer information finns [Skissparametrar](./concepts/parameters.md).

1. Lägg till principtilldelning för prenumerationen: Vänsterklicka på raden **+ Lägg till artefakt...** direkt under **Prenumeration**. Välj ”Policytilldelning” som _Artefakttyp_. Ändra _Typ_ till Inbyggd och ange ”tagg” i _Sök_. Klicka på _Sök_ om du vill genomföra filtrering. Välj alternativet ”Använd tagg och dess standardvärde i resursgrupper” genom att klicka på det. Lägg till den här artefakten till skissen genom att klicka på **Lägg till**.

1. Klicka på raden för principtilldelningen ”Använd tagg och dess standardvärde i resursgrupper”. Det fönster där du kan ange parametrar för att artefakten som en del av skissdefinitionen öppnas, så att du kan ange parametrar för alla tilldelningar (**statiska parametrar**) baserade på den här skissen istället för under tilldelningen (**dynamiska parametrar**). Det här exemplet använder **dynamiska parametrar** under skisstilldelningen, så låt standardinställningarna vara kvar och klicka på **Avbryt**.

1. Lägg till resursgrupp för prenumeration: Vänsterklicka på raden **+ Lägg till artefakt...** under **Prenumeration**. Välj ”Resursgrupp” som _Artefakttyp_. Lämna fälten _Resursgruppsnamn_ och _Plats_ tomma, men se till att kryssrutorna för varje egenskap har markerats så att de blir **dynamiska parametrar**. Lägg till den här artefakten till skissen genom att klicka på **Lägg till**.

1. Lägg till en mall under resursgruppen: Vänsterklicka på **+ Lägg till artefakt...** direkt under posten **ResourceGroup**. Välj Azure Resource Manager-mall som _Artefakttyp_, ställ in _Artefaktvisningsnamn_ på StorageAccount och lämna _Beskrivning_ tomt. Klistra in följande Resource Manager-mall i redigeringsrutan på fliken **Mall**. När du har klistrart in mallen klickar du på fliken **Parametrar** och kan se att mallparametern **storageAccountType** och standardvärdet **Standard_LRS** har identifierats automatiskt och fyllts i, men konfigurerats som en **dynamisk parameter**. Avmarkera kryssrutan och se att listan endast innehåller värden som inkluderats i Resource Manager-mallen under **allowedValues**. Markera kryssrutan så att den åter indikerar **dynamisk parameter**. Lägg till den här artefakten till skissen genom att klicka på **Lägg till**.

   > [!IMPORTANT]
   > Om du importerar mallen, så kontrollera att filen enbart är JSON och inte innehåller HTML. När du pekar på en URL på GitHub, så se till att du har klickat på **RAW** så att du får en ren JSON-fil och inte en som är paketerad med HTML för att visas på GitHub. Ett fel inträffar om den importerade mallen inte är ren JSON.

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
           "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "apiVersion": "2016-01-01",
           "location": "[resourceGroup().location]",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "Storage",
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

   ![Artefakt – Azure Resource Manager-mall](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Din färdiga skiss bör se ut som i det följande. Observera att för varje artefakt anges ”_x_ av _y_ parametrar har fyllts i” under kolumnen _Parametrar_. De **dynamiska parametrarna** anges vid varje tilldelning av skissen.

   ![Slutförd skiss](./media/create-blueprint-portal/completed-blueprint.png)

1. Nu när alla planerade artefakter har lagts till klickar du på **Spara utkast** längst ned på sidan.

## <a name="edit-a-blueprint"></a>Redigera en skiss

I [Skapa en skiss](#create-a-blueprint) angavs ingen beskrivning, rolltilldelningen lades inte till i den nya resursgruppen. Båda kan åtgärdas med dessa steg:

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den skiss som du skapade tidigare i listan över skisser och välj **Redigera skiss**.

1. Tillhandahåll information om skissen och de artefakter som ingår i den i **Skissbeskrivning**.  I det här fallet kan du skriva något som liknar följande: ”Den här skissen anger prenumerationens taggprincip och rolltilldelning, skapar en resursgrupp och distribuerar en resursmall och en resurstilldelning till denna ResourceGroup”.

1. Klicka på **Nästa: Artefakter** längst ned på sidan eller på fliken **Artefakter** högst upp på sidan.

1. Lägg till rolltilldelning under resursgruppen: Vänsterklicka på raden **+ Lägg till artefakt...** direkt under posten **ResourceGroup**. Välj ”Rolltilldelning” som _Artefakttyp_. Under _Roll_ väljer du ”Ägare”, avmarkerar kryssrutan för fältet _Lägg till användare, app eller grupp_ och söker efter och väljer en användare, app eller grupp som ska läggas till. Den här artefakten använder en **statisk parameter** som ställs in på samma sätt i alla tilldelningar av den här skissen. Lägg till den här artefakten till skissen genom att klicka på **Lägg till**.

   ![Artefakt – rolltilldelning nr 2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Din färdiga skiss bör se ut som i det följande. Lägg märke till att den nyligen tillagda rolltilldelningen visar att **1 av 1 parametrar har fyllts i**, vilket innebär att det är en **statisk parameter**.

   ![Slutförd skiss nr 2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Klicka på **Spara utkast** nu när den har uppdaterats.

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när alla de planerade artefakterna har lagts till i skissen är det dags att publicera den.
När den har publicerats kan den tilldelas till en prenumeration.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den skiss som du skapade tidigare i listan över skisser och välj **Publicera skiss**.

1. I den dialogruta som öppnas anger du en **Version** (bokstäver, siffror och bindestreck med en maxlängd på 20 tecken), t.ex. ”v1” och **Ändra anteckningar** (valfritt), t.ex. ”Första publiceringen”.

1. Klicka på **Publicera** längst ned på sidan.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

När en skiss har publicerats kan den tilldelas till en prenumeration. Tilldela skissen som du skapade till någon av prenumerationerna i din hierarki med hanteringsgrupper. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den skiss som du skapade tidigare i listan över skisser (eller vänsterklicka på ellipsen) och välj **Tilldela skiss**.

1. Välj den eller de prenumerationer till vilka du vill distribuera den här skissen i listrutan **Prenumeration** på sidan **Tilldela skiss**.

   > [!NOTE]
   > En tilldelning skapas för varje prenumeration som väljs, vilket tillåter ändringar i enskilda prenumerationstilldelningar senare utan att resten av de valda prenumerationerna påtvingas dessa ändringar.

1. Ge den här tilldelningen ett unikt namn i **Tilldelat namn**.

1. Ange i vilken region den hanterade identiteten ska skapas i **Plats**. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

1. Lämna listrutan **Skissdefinitionsversion** listrutan för **Publicerade** versioner för posten ”v1” (som standard den allra senast **publicerade** versionen).

1. Låt standardvärdet **Lås inte** vara för **Lås tilldelning**. Mer information finns i [Låsa skissresurser](./concepts/resource-locking.md).

1. Rolltilldelningen på prenumerationsnivå **[Användargrupp eller programnamn]: Deltagare**, sök efter och välj en användare, en app eller en grupp.

1. För prenumerationsnivåns principtilldelning ställer du in **Taggnamn** på CostCenter och **Taggvärde** på ContosoIT.

1. För ResourceGroup anger du ett **Namn** för StorageAccount och hämtar ”USA, östra 2” från listrutan som värde för **Plats**.

   > [!NOTE]
   > Varje artefakt som lades till under resursgruppen under skissdefinitionen dras in så att den överensstämmer med den resursgrupp eller det objekt som den ska distribueras med. Artefakter som antingen inte accepterar parametrar och inte har några parametrar att definiera vid tilldelningen visas enbart för att ge kontextuell information.

1. I Azure Resource Manager-mallen StorageAccount väljer du Standard_GRS för parametern **storageAccountType**.

1. Läs informationsrutan längst ned på sidan och klicka på **Tilldela**.

## <a name="track-deployment-of-a-blueprint"></a>Spåra distributionen av en skiss

När en skiss har tilldelats till en eller flera prenumerationer händer två saker:

- Skissen läggs till på sidan **Tilldelade skisser** för den tilldelade prenumerationen
- Processen att distribuera alla artefakter som definierats av skissen börjar

Nu när skissen har tilldelats till en prenumeration kan du kontrollera distributionens förlopp.

1. Välj **Tilldelade skisser** på sidan till vänster.

1. Högerklicka på den skiss som du tilldelade tidigare i listan över skisser och välj **Visa tilldelningsinformation**.

   ![Visa tilldelningsinformation](./media/create-blueprint-portal/view-assignment-details.png)

1. Verifiera på sidan **Distributionsinformation** att alla artefakter har distribuerats och att det inte har uppstått några fel under distributionen. Om det inträffade fel kan du läsa stegen i avsnittet om att [felsöka skiss](./troubleshoot/general.md) för att avgöra vad som gick fel.

## <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Om en skisstilldelning inte längre behövs kan du ta bort den från prenumeration. Skissen har kanske ersatts av en nyare skiss med uppdaterade mönster, principer och designer. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Följ dessa steg om du vill ta bort en skisstilldelning:

1. Välj **Tilldelade skisser** på sidan till vänster.

1. Välj den skiss för vilken du vill ta bort tilldelningen i listan över skisser och klicka sedan på knappen **Ta bort skisstilldelning** högst upp på sidan.

1. Läs bekräftelsemeddelandet och klicka på **OK**.

## <a name="delete-a-blueprint"></a>Ta bort en skiss

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den skiss som du vill ta bort, välj **Ta bort skiss** och klicka sedan på **Ja** i bekräftelsedialogrutan.

> [!NOTE]
> Om du tar bort en skiss med den här metoden raderas även alla **publicerade versioner** av den valda skissen. Om du vill ta bort en enskild version öppnar du skissen, klickar på fliken **Publicerade versioner**, väljer och klickar på den version som du vill ta bort och klickar sedan på **Ta bort den här versionen**. Dessutom går det inte att ta bort en skiss med tilldelningar förrän alla tilldelningar för den skissen har tagits bort.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](./concepts/lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](./concepts/parameters.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](./concepts/sequencing-order.md)
- Lär dig hur du använder [resurslåsning för en skiss](./concepts/resource-locking.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](./how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](./troubleshoot/general.md)
