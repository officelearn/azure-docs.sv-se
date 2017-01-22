---
title: "Använda VS Code med Resource Manager-mallar | Microsoft Docs"
description: "Visar hur du konfigurerar Visual Studio Code för att skapa Azure Resource Manager-mallar."
services: azure-resource-manager
documentationcenter: na
author: cmatskas
manager: timlt
editor: tysonn
ms.assetid: 78f2aa22-df1d-41bd-92ec-dabd1175db88
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: chmatsk;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 10c7051c9b1218081d95cb10403006bfd95126ba
ms.openlocfilehash: 2ac1c2cce7a9e045990894b0bbaa045df3d48954


---
# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Arbeta med Azure Resource Manager-mallar i Visual Studio Code
Azure Resource Manager-mallar är JSON-filer som beskriver en resurs och relaterade beroenden. Eftersom dessa filer ibland kan vara stora och komplicerade är det viktigt att ha tillgång till bra verktyg. Visual Studio Code är en ny, enkel, plattformsoberoende kodredigerare med öppen källkod. Den har stöd för att skapa och redigera Resource Manager-mallar via ett [nytt tillägg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). VS Code kan köras var som helst och kräver bara Internetåtkomst när du vill distribuera dina Resource Manager-mallar till din Azure-prenumeration.

Om du inte redan har VS Code kan du installera verktyget från [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Installera Resource Manager-tillägget
Du måste installera ett tillägg för att kunna arbeta med JSON-mallar i VS Code. Följande steg beskriver hur du laddar ned och installerar språkstödet för JSON-mallar för Resource Manager:

1. Starta VS Code 
2. Öppna Snabböppning (Ctrl+P) 
3. Kör följande kommando: 
   
        ext install azurerm-vscode-tools
4. Starta om VS Code när du uppmanas att aktivera tillägget. 
   
   Klart!

## <a name="set-up-resource-manager-snippets"></a>Konfigurera Resource Manager-kodfragment
I föregående steg installerade vi verktygsstöd. Nu ska vi konfigurera VS Code att använda kodfragment i JSON-mallar.

1. Kopiera innehållet i filen från [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json)-databasen till Urklipp.
2. Starta VS Code 
3. I VS Code kan du öppna JSON-kodavsnittsfilen genom att antingen navigera till **Arkiv** -> **Inställningar** -> **Användar-kodavsnitt** -> **JSON**. Alternativt välj **F1** och skriv **inställningar** tills du kan välja **Inställningar: kodavsnitt**.
   
    ![inställningar: kodfragment](./media/resource-manager-vs-code/preferences-snippets.png)
   
    Välj **JSON** från alternativen.
   
    ![välj json](./media/resource-manager-vs-code/select-json.png)
4. Klistra in innehållet i filen i steg 1 i filen med kodfragment före filens sista ”}” 
5. Kontrollera att JSON-koden ser ut som den ska och att det inte finns några vågiga understrykningarna som indikerar fel. 
6. Spara och stäng filen med kodfragment.

Det är allt som behövs för att börja använda Resource Manager-kodfragment. Nu ska vi testa våra inställningar.

## <a name="work-with-template-in-vs-code"></a>Arbeta med en mall i VS Code
Det enklaste sättet att börja arbeta med en mall är att hämta någon av snabbstartsmallarna som är tillgängliga på [Github](https://github.com/Azure/azure-quickstart-templates) eller att använda en egen mall. Du kan enkelt [exportera en mall](resource-manager-export-template.md) för valfria resursgrupper via portalen. 

1. Om du har exporterat en mall från en resursgrupp öppnar du de extraherade filerna i VS Code.
   
    ![visa filer](./media/resource-manager-vs-code/show-files.png)
2. Öppna filen template.json så att du kan redigera den och lägga till ytterligare resurser. Efter `"resources": [`, trycker du på Retur för att påbörja en ny rad. Om du skriver **arm** visas en lista med alternativ. Dessa alternativ är de mallkodfragment som du installerade. 
   
    ![visa kodfragment](./media/resource-manager-vs-code/type-snippets.png)
3. Välj önskat kodfragment. I den här artikeln väljer jag **arm-ip** för att skapa en ny offentlig IP-adress. Lägg till ett komma efter den avslutande hakparantesen `}` för den nya resursen för att kontrollera att din mallsyntax är giltig.
   
     ![lägg till komma](./media/resource-manager-vs-code/add-comma.png)
4. VS Code har inbyggt IntelliSense. När du redigerar mallarna föreslår VS Code tillgängliga värden. Om du till exempel vill lägga till ett variabelavsnitt i din mall, lägger du till `""` (två citattecken) och väljer **Ctrl+Space** mellan citattecknen. Nu visas olika alternativ, inklusive **variabler**.
   
    ![lägg till variabler](./media/resource-manager-vs-code/add-variables.png)
5. IntelliSense kan också föreslå tillgängliga värden eller funktioner. För att ställa in en egenskap till ett parametervärde, skapar du ett uttryck med `"[]"` och **Ctrl+Space**. Du kan börja skriva namnet på en funktion. Tryck på **Tabb** när du har hittat den funktion som du vill använda.
   
    ![lägg till parameter](./media/resource-manager-vs-code/select-parameters.png)
6. För att se en lista med tillgängliga parametrar inom din mall, väljer du **Ctrl+Space** igen inom funktionen.
   
    ![lägg till parameter](./media/resource-manager-vs-code/select-avail-parameters.png)
7. Om du har problem med schemavalidering i mallen, ser du de välbekanta vågiga understrykningarna i redigeraren. Du kan visa listan med fel och varningar genom att skriva **Ctrl+Skift+M** eller genom att välja tecknen i det nedre vänstra statusfältet.
   
    ![fel](./media/resource-manager-vs-code/errors.png)
   
    Genom att validera mallen kan du snabbt identifiera eventuella syntaxproblem. Du kan också se fel som du kan ignorera. I vissa fall jämför redigeraren din mall med ett schema som inte är uppdaterat och därför rapporterar ett fel, även om du vet att det är korrekt. Anta exempelvis att en funktion nyligen har lagts till i Resource Manager men att schemat inte har uppdaterats. Redigeraren rapporterar ett fel trots att funktionen fungerar korrekt under distributionen.
   
    ![felmeddelande](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Distribuera dina nya resurser
När mallen är redo kan du distribuera de nya resurserna genom att följa anvisningarna nedan: 

### <a name="windows"></a>Windows
1. Öppna en PowerShell-kommandotolk 
2. För att logga in skriver du: 
   
  ```powershell
  Login-AzureRmAccount
  ```

3. Om du har flera prenumerationer hämtar du en lista över prenumerationerna med:

  ```powershell 
  Get-AzureRmSubscription
  ```
   
    Och väljer den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId <Subscription Id>
  ```

4. Uppdatera parametrarna i filen parameters.json
5. Distribuera mallen i Azure genom att köra Deploy.ps1

### <a name="osxlinux"></a>OSX/Linux
1. Öppna ett terminalfönster 
2. För att logga in skriver du:

  ```azurecli
  azure login
  ```

3. Om du har flera prenumerationer väljer du rätt prenumeration med:

  ```azurecli
  azure account set <subscriptionNameOrId> 
  ```

4. Uppdatera parametrarna i filen parameters.json.
5. Distribuera mallen genom att köra:

  ```azurecli 
  azure group deployment create -f <PathToTemplate>
  ``` 

## <a name="next-steps"></a>Nästa steg
* Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Mer information om mallfunktioner finns i [Funktioner i en Azure Resource Manager-mall](resource-group-template-functions.md).
* Fler exempel på hur du arbetar med Visual Studio Code finns i [Skapa molnappar med Visual Studio Code](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) från [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect-[demonstrationen](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Fler snabbstartsguider från HealthClinic.biz-demonstrationen finns i [Snabbstartsguider för Azure Developer Tools](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).




<!--HONumber=Jan17_HO1-->


