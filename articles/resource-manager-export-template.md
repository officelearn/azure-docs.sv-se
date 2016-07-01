<properties
    pageTitle="Exportera en Azure Resource Manager-mall | Microsoft Azure"
    description="Använd Azure Resource Manager om du vill exportera en mall från en befintlig resursgrupp."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="tomfitz"/>

# Exportera en Azure Resource Manager-mall från befintliga resurser

Att skapa Azure Resource Manager-mallar kan kännas avskräckande. Som tur är hjälper Resource Manager dig med uppgiften och låter dig exportera en mall från befintliga resurser i din prenumeration. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs.

I den här självstudiekursen ska du logga in på Azure Portal, skapa ett lagringskonto och exportera mallen för det lagringskontot. Du ska lägga till ett virtuellt nätverk för att ändra resursgruppen. Slutligen ska du exportera en ny mall som representerar det aktuella tillståndet. Även om den här artikeln fokuserar på en förenklad infrastruktur kan du följa samma steg för att exportera en mall för en mer komplicerad lösning.

## Skapa ett lagringskonto

1. På [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Data + Lagring** > **Lagringskonto**.

      ![Skapa lagring](./media/resource-manager-export-template/create-storage.png)

2. Skapa ett lagringskonto med namnet **lagring**, din initialer och datumet. Namnet på lagringskontot måste vara unikt i Azure. Om du försöker med ett namn som redan används kan du pröva en variant. För resursgruppen använder du **ExportGroup**. Du kan använda standardvärden för de andra egenskaperna. Välj **Skapa**.

      ![Ange värden för lagring](./media/resource-manager-export-template/provide-storage-values.png)

När distributionen är klar innehåller din prenumeration lagringskontot.

## Exportera mallen för distribution

1. Gå till resursgruppsbladet för din nya resursgrupp. Som du ser visas resultatet från den senaste distributionen. Välj den här länken.

      ![blad för resursgrupp](./media/resource-manager-export-template/resource-group-blade.png)

2. Du ser distributionshistoriken för gruppen. I ditt fall visas antagligen bara en distribution. Välj den här distributionen.

     ![den senaste distributionen](./media/resource-manager-export-template/last-deployment.png)

3. En sammanfattning av distributionen visas. Sammanfattningen innehåller statusen för distributionen och dess åtgärder samt de värden som du angav för parametrarna. Om du vill visa mallen som användes för distributionen väljer du **Visa mall**.

     ![visa distributionssammanfattning](./media/resource-manager-export-template/deployment-summary.png)

4. Resource Manager hämtar följande fem filer åt dig:

   - Mallen som definierar infrastrukturen för lösningen. När du skapade lagringskontot på portalen använde Resource Manager en mall för att distribuera det och sparade mallen för framtida bruk.

   - En parameterfil som du kan använda för att skicka in värden under distributionen. Den innehåller de värden som du angav under den första distributionen, men du kan ändra dessa värden när du distribuerar om mallen.

   - En Azure PowerShell-skriptfil som du kan använda för att distribuera mallen.

   - En Azure CLI-skriptfil (Command-Line-Interface) som du kan använda för att distribuera mallen.

   - En .NET-klass som du kan använda för att distribuera mallen.

     Filerna är tillgängliga via länkar i bladet. Mallen är vald som standard.

       ![Visa mall](./media/resource-manager-export-template/view-template.png)

     Vi ska titta närmare på mallen. Mallen bör se ut ungefär så här:

        {      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",      "contentVersion": "1.0.0.0",      "parameters": {        "name": {          "type": "String"        },        "accountType": {          "type": "String"        },        "location": {          "type": "String"        },        "encryptionEnabled": {          "defaultValue": false,          "type": "Bool"        }      },      "resources": [        {          "type": "Microsoft.Storage/storageAccounts",          "sku": {            "name": "[parameters('accountType')]"          },          "kind": "Storage",          "name": "[parameters('name')]",          "apiVersion": "2016-01-01",          "location": "[parameters('location')]",          "properties": {            "encryption": {              "services": {                "blob": {                  "enabled": "[parameters('encryptionEnabled')]"                }              },              "keySource": "Microsoft.Storage"            }          }        }      ]    }

     Observera att mallen definierar parametrar för lagringskontots namn, typ och plats. En parameter anger även om kryptering har aktiverats. Standardvärdet är **false**. I avsnittet **resources** ser du definitionen för lagringskontot som ska distribueras.

Hakparenteserna innehåller uttryck som utvärderas under distributionen. Uttrycken inom hakparenteser i mallen används för att hämta parametervärden under distributionen. Du kan använda många fler uttryck och du ser exempel på andra uttryck senare i den här artikeln. Den fullständiga listan finns i [Funktioner för Azure Resource Manager-mall](resource-group-template-functions.md).

Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

## Lägga till ett virtuellt nätverk

Mallen som du hämtade i det förra avsnittet representerar infrastrukturen för den ursprungliga distributionen, men tar inte hänsyn till eventuella ändringar som du gör efter distributionen.
För att illustrera detta ska vi ändra resursgruppen genom att lägga till ett virtuellt nätverk via portalen.

1. I bladet för resursgruppen väljer du **Lägg till** och väljer sedan **virtuellt nätverk** från de tillgängliga resurserna.

2. Ge det virtuella nätverket namnet **VNET** och använd standardvärdena för de andra egenskaperna. Välj **Skapa**.

      ![ange varning](./media/resource-manager-export-template/create-vnet.png)

3. När det virtuella nätverket har distribuerats till resursgruppen titta du på distributionshistoriken igen. Nu visas två distributioner. Välj den nyare distributionen.

      ![distributionshistorik](./media/resource-manager-export-template/deployment-history.png)

4. Titta på mallen för den distributionen. Observera att den bara definierar de ändringar som du har gjort för att lägga till det virtuella nätverket.

Det är ofta en bra idé att arbeta med en mall som distribuerar hela infrastrukturen för lösningen i samma åtgärd i stället för att komma ihåg flera olika mallar som ska distribueras.


## Exportera mallen för resursgruppen

Även om varje distribution endast visar de ändringar som du har gjort i resursgruppen kan du när som helst exportera en mall för att visa attributen för hela resursgruppen.  

1. Om du vill visa mallen för en resursgrupp väljer du **Export mall**.

      ![Exportera resursgrupp](./media/resource-manager-export-template/export-resource-group.png)

2. Återigen ser du de fem filer som du kan använda för att distribuera lösningen igen, men nu är mallen lite annorlunda. Den här mallen har bara två parametrar: en för lagringskontots namn och en för det virtuella nätverkets namn.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Resource Manager hämtade inte mallarna som användes under distributionen. I stället skapades en ny mall som baseras på resursernas aktuella konfiguration. Resource Manager vet inte vilka värden du vill skicka in som parametrar och hårdkodar därför de flesta värden baserat på värdena i resursgruppen. Exempelvis konfigureras värdet för lagringskontots plats och replikering till:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Ladda ned mallen så att du kan arbeta med den lokalt.

      ![Ladda ned mall](./media/resource-manager-export-template/download-template.png)

4. Leta upp ZIP-filen som du laddade ned och extrahera innehållet. Du kan använda den nedladdade mallen för att distribuera om din infrastruktur.

## Nästa steg

Grattis! Nu vet du hur du exporterar en mall från resurser som du har skapat på portalen.

- I den andra delen av den här kursen ska du anpassa mallen som du precis har laddat ned genom att lägga till fler parametrar och distribuera om den med hjälp av ett skript. Mer information finns i [Anpassa och distribuera om en exporterad mall](resource-manager-customize-template.md).
- Information om hur du exporterar en mall med PowerShell finns i [Använda Azure PowerShell med Azure Resource Manager](powershell-azure-resource-manager.md).
- Information om hur du exporterar en mall med Azure CLI finns i [Använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).



<!--HONumber=Jun16_HO2-->


