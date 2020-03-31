---
title: 'Snabbstart: Skapa en skiss i portalen'
description: I den här snabbstarten använder du Azure Blueprints för att skapa, definiera och distribuera artefakter via Azure-portalen.
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: 457f4f9c53f45077129b291c904bc1580b6d965e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282060"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Snabbstart: Definiera och tilldela en skiss i portalen

När du lär dig hur du skapar och tilldelar ritningar kan du definiera vanliga mönster för att utveckla återanvändbara och snabbt distribuerade konfigurationer baserat på Azure Resource Manager-mallar, principer, säkerhet med mera. I den här självstudien lär du dig att använda Azure Blueprints för att utföra några av de vanliga uppgifterna som är relaterade till att skapa, publicera och tilldela en skiss inom organisationen. Dessa uppgifter omfattar:

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. I det här exemplet skapar du en ny skiss med namnet **MyBlueprint** för att konfigurera roll- och principtilldelningar för prenumerationen. Lägg sedan till en ny resursgrupp och skapa en Resource Manager-mall och rolltilldelning i den nya resursgruppen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** från sidan till vänster och välj knappen **+ Skapa skiss** högst upp på sidan.

   Du kan också välja **Skapa** på sidan **Komma igång** för att gå direkt till att skapa en skiss.

   ![Skapa en skiss från sidan Skissdefinitioner](./media/create-blueprint-portal/create-blueprint-button.png)

1. Välj **Börja med tom skiss** från kortet högst upp i listan med inbyggda ritningar.

1. Ange ett **blueprint-namn** som **MyBlueprint**. (Använd upp till 48 bokstäver och siffror, men inga blanksteg eller specialtecken). Lämna **skissbeskrivningen** tom för tillfället.

1. I rutan **Definitionsplats** väljer du ellipsen till höger, väljer den [hanteringsgrupp](../management-groups/overview.md) eller prenumeration där du vill spara skissen och väljer **Välj**.

1. Kontrollera att informationen är korrekt. Det går inte att ändra fälten **Skissnamn** och **Definitionsplats** senare. Välj sedan **Nästa: Artefakter** längst ned på sidan eller fliken **Artefakter** högst upp på sidan.

1. Lägg till en rolltilldelning på prenumerationsnivå:

   1. Välj raden **+ Lägg till artefakt** under **Prenumeration**. **Fönstret Lägg till artefakt** öppnas till höger i webbläsaren.

   1. Välj **Rolltilldelning** för **artefakttyp**.

   1. Under **Roll**väljer du **Deltagare**. Lämna rutan **Lägg till användare, app eller grupp** med kryssrutan som anger en dynamisk parameter.

   1. Välj **Lägg till** om du vill lägga till den här artefakten i skissen.

   ![Rolltilldelning för en skissartefakt](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > De flesta artefakter stöder parametrar. En parameter som har tilldelats ett värde när skiss skapas är en *statisk parameter*. Om parametern tilldelas under skisstilldelningen är det en *dynamisk parameter*. Mer information finns i [Skissparametrar](./concepts/parameters.md).

1. Lägg till en principtilldelning på prenumerationsnivå:

   1. Välj raden **+ Lägg till artefakt** under rolltilldelningsartefakten.

   1. Välj **Principtilldelning** för **artefakttyp**.

   1. Ändra **typ** till **inbyggd**. Skriv **taggen**i **Sök**.

   1. Klicka på **Sök** om du vill genomföra filtrering. Välj **Lägg till-tagg och dess standardvärde i resursgrupper**.

   1. Välj **Lägg till** om du vill lägga till den här artefakten i skissen.

1. Markera raden för **taggen Lägg till tilläggsbelägger för principtilldelning och dess standardvärde för resursgrupper**.

1. Det fönster där du kan ange parametrar för att artefakten som en del av skissdefinitionen öppnas, så att du kan ange parametrar för alla tilldelningar (statiska parametrar) baserade på den här skissen istället för under tilldelningen (dynamiska parametrar). I det här exemplet används dynamiska parametrar under skisstilldelning, så lämna standardvärdena och välj **Avbryt**.

1. Lägg till en resursgrupp på prenumerationsnivå:

   1. Välj raden **+ Lägg till artefakt** under **Prenumeration**.

   1. Välj **resursgrupp** för **artefakttyp**.

   1. Lämna rutorna **Artefaktnamn,** **Resursgruppnamn**och **Plats** tomma, men kontrollera att kryssrutan är markerad för varje parameteregenskap för att göra dem dynamiska parametrar.

   1. Välj **Lägg till** om du vill lägga till den här artefakten i skissen.

1. Lägg till en mall under resursgruppen:

   1. Markera raden **+ Lägg till artefakt** under posten **ResourceGroup.**

   1. Välj **Azure Resource Manager-mall** för **artefakttyp,** ange **artefaktvisningsnamn** till **StorageAccount**och lämna **Beskrivning** tom.

   1. Klistra in följande Resource Manager-mall i redigeringsrutan på fliken **Mall**.
      När du har klistrat in mallen väljer du fliken **Parametrar** och noterar att mallparametrarna **storageAccountType** och **plats** har identifierats. Varje parameter har identifierades och fylldes i automatiskt, men konfigurerades som en dynamisk parameter.

      > [!IMPORTANT]
      > Om du importerar mallen kontrollerar du att filen bara är JSON och inte innehåller HTML. När du pekar på en URL på GitHub kontrollerar du att du har valt **RAW** för att få den rena JSON-filen och inte den som är insvept med HTML för visning på GitHub. Ett fel inträffar om den importerade mallen inte är ren JSON.

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
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
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

   1. Avmarkera kryssrutan **storageAccountType** och observera att listrutan endast innehåller värden som ingår i resource manager-mallen under **allowedValues**. Markera rutan om du vill ställa tillbaka den till en dynamisk parameter.

   1. Välj **Lägg till** om du vill lägga till den här artefakten i skissen.

   ![Resource Manager-mall för skissartefakten](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Din färdiga skiss bör se ut som i det följande. Observera att varje artefakt har ** _x_ av _y_ parametrar som fylls** i kolumnen **Parametrar.** De dynamiska parametrarna anges vid varje tilldelning av skissen.

   ![Slutförd skissdefinition](./media/create-blueprint-portal/completed-blueprint.png)

1. Nu när alla planerade artefakter har lagts till väljer du **Spara utkast** längst ned på sidan.

## <a name="edit-a-blueprint"></a>Redigera en skiss

I [Skapa en skiss](#create-a-blueprint)har du inte en beskrivning eller lagt till rolltilldelningen i den nya resursgruppen. Du kan åtgärda båda genom att följa dessa steg:

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den som du tidigare skapade i listan över ritningar och välj **Redigera skiss**.

1. I **Skissbeskrivning** anger du information om skissen och de artefakter som ingår i den. I det här fallet anger du något i stil med: **Den här skissen anger taggprincip och rolltilldelning för prenumerationen, skapar en ResourceGroup och distribuerar en resursmall och rolltilldelning till den ResourceGroup.**

1. Välj **Nästa: Artefakter** längst ned på sidan eller fliken **Artefakter** högst upp på sidan.

1. Lägg till en rolltilldelning under resursgruppen:

   1. Markera raden **+ Lägg till artefakt** direkt under **resursgruppsposten.**

   1. Välj **Rolltilldelning** för **artefakttyp**.

   1. Under **Roll**markerar du **Ägare**och avmarkerar kryssrutan under rutan Lägg **till användare, app eller grupp.**

   1. Sök efter och välj en användare, app eller grupp att lägga till. Den här artefakten använder en statisk parameter som ställs in på samma sätt i alla tilldelningar av den här skissen.

   e. Välj **Lägg till** om du vill lägga till den här artefakten i skissen.

   ![Andra rolltilldelningen för skissartefakten](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Din färdiga skiss bör se ut som i det följande. Observera att den nyligen tillagda rolltilldelningen visar **1 av 1 parametrar ifyllda**. Det betyder att det är en statisk parameter.

   ![Andra definitionen för den färdiga skissen](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Välj **Spara utkast** nu när det har uppdaterats.

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när alla de planerade artefakterna har lagts till i skissen är det dags att publicera den.
Publicering gör skissen tillgänglig för tilldelning till en prenumeration.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den som du tidigare har skapat i listan över ritningar och välj **Publicera skiss**.

1. I fönstret som öppnas anger du en **version** (bokstäver, siffror och bindestreck med en maximal längd på 20 tecken), till exempel **v1**. Du kan också ange text i **Ändra anteckningar,** till exempel **Publicera först**.

1. Välj **Publicera** längst ned på sidan.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

När en skiss har publicerats kan den tilldelas en prenumeration. Tilldela skissen som du skapade till en av prenumerationerna under hanteringsgrupphierarkin. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den som du tidigare har skapat (eller välj ellipsen) i listan över ritningar och välj **Tilldela skiss**.

1. På sidan **Tilldela skiss** i listrutan **Prenumeration** väljer du de prenumerationer som du vill distribuera skissen till.

   Om det finns Enterprise-erbjudanden som stöds tillgängliga från [Azure-fakturering](../../billing/index.md) aktiveras länken **Skapa nytt** under rutan **Prenumeration**. Följ de här stegen:

   1. Välj länken **Skapa nytt** för att skapa en ny prenumeration i stället för att välja befintliga.

   1. Ange ett **Visningsnamn** för den nya prenumerationen.

   1. Välj det tillgängliga **Erbjudandet** i listrutan.

   1. Använd ellipsen för att välja den [hanteringsgrupp](../management-groups/overview.md) som prenumerationen ska vara underordnad.

   1. Välj **Skapa** längst ned på sidan.

   ![Skapa en prenumeration för en prenumeration på skisstilldelning](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Den nya prenumerationen skapas direkt efter att du har valt **Skapa**.

   > [!NOTE]
   > En tilldelning skapas för varje prenumeration som du väljer. Du kan göra ändringar i en enskild prenumerationstilldelning vid ett senare tillfälle utan att tvinga fram ändringar på resten av de valda prenumerationerna.

1. För **tilldelningsnamn**anger du ett unikt namn för den här tilldelningen.

1. Välj en region för det hanterade identitets- och prenumerationsdistributionsobjekt som ska skapas i i **Plats.** Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

1. Lämna listrutan **Blueprint definition version** av **publicerade** versioner på **v1-posten.** (Standard är den senast publicerade versionen.)

1. Låt standardvärdet **Lås inte** vara för **Lås tilldelning**. Mer information finns i [Låsning av skisser resurs](./concepts/resource-locking.md).

   ![Låsa och hanterade identiteter för tilldelningen](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Lämna standardvärdet **system tilldelat**under **Hanterad identitet**.

1. För prenumerationsnivåns rolltilldelning **[Användargrupp eller programnamn]: Deltagare**, så sök efter och välj en användare, en app eller grupp.

1. För tilldelningen av prenumerationsnivåprincip anger du **Taggnamn** till **CostCenter** och **taggvärdet** till **ContosoIT**.

1. För **ResourceGroup**anger du ett **namn på** **StorageAccount** och en **plats för** östra **US 2** från listrutan.

   > [!NOTE]
   > För varje artefakt som du har lagt till under resursgruppen under skissdefinitionen är artefakten indragen för att justera med resursgruppen eller objektet som du ska distribuera den med.
   > Artefakter som antingen inte tar parametrar eller inte har några parametrar som ska definieras vid tilldelning visas endast för kontextuell information.

1. På Azure Resource Manager-mallen **StorageAccount**väljer du **Standard_GRS** för parametern **storageAccountType.**

1. Läs informationsrutan längst ned på sidan och välj sedan **Tilldela**.

## <a name="track-deployment-of-a-blueprint"></a>Spåra distributionen av en skiss

När en skiss har tilldelats till en eller flera prenumerationer händer två saker:

- Skissen läggs till på sidan **Tilldelade ritningar** för varje prenumeration.
- Processen att distribuera alla artefakter som definieras av skissen börjar.

Nu när skissen har tilldelats en prenumeration kontrollerar du hur distributionen fortskrider:

1. Välj **Tilldelade ritningar** från sidan till vänster.

1. Högerklicka på den som du tidigare har tilldelat i listan över ritningar och välj **Visa tilldelningsinformation**.

   ![Visa tilldelningsinformation från sidan Tilldelade skisser](./media/create-blueprint-portal/view-assignment-details.png)

1. På sidan **Skisstilldelning** verifierar du att alla artefakter har distribuerats och att det inte fanns några fel under distributionen. Om fel uppstod läser [du Felsöka ritningar](./troubleshoot/general.md) för steg för att avgöra vad som gick fel.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Om du inte längre behöver en skisstilldelning tar du bort den från en prenumeration. Skissen kan ha ersatts av en nyare skiss med uppdaterade mönster, principer och mönster. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Följ dessa steg om du vill ta bort en skisstilldelning:

1. Välj **Tilldelade ritningar** från sidan till vänster.

1. I listan över ritningar väljer du den skiss som du vill ta bort tilldelningen av. Välj sedan knappen **Ta bort tilldelning** av skiss högst upp på sidan.

1. Läs bekräftelsemeddelandet och välj sedan **OK**.

### <a name="delete-a-blueprint"></a>Ta bort en skiss

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på skissen som du vill ta bort och välj **Ta bort skiss**. Välj sedan **Ja** i bekräftelsedialogrutan.

> [!NOTE]
> Om du tar bort en skiss i den här metoden tas även alla publicerade versioner av den valda skissen bort.
> Om du vill ta bort en enda version öppnar du skissen, väljer fliken **Publicerade versioner,** markerar den version som du vill ta bort och väljer sedan Ta bort den **här versionen**. Du kan inte heller ta bort en skiss förrän du har tagit bort alla skisstilldelningar av skissdefinitionen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, tilldelat och tagit bort en skiss med Azure-portalen. Om du vill veta mer om Azure Blueprints fortsätter du till artikeln om skisslivscykeln.

> [!div class="nextstepaction"]
> [Läs mer om skisslivscykeln](./concepts/lifecycle.md)