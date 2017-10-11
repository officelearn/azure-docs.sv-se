---
title: Kontinuerlig integration i VS Team Services med Azure-resursgruppsprojekt | Microsoft Docs
description: "Beskriver hur du ställer in kontinuerlig integration i Visual Studio Team Services med hjälp av projekt för distribution av Azure-resursgrupp i Visual Studio."
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: 
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: e7d98ca3fa281a136595c37ed9b7e71de0cf7bff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Kontinuerlig integration i Visual Studio Team Services med hjälp av projekt för distribution av Azure-resursgrupp
För att distribuera en Azure-mall måste du utföra uppgifter i olika faser: skapa, testa, kopiera till Azure (kallas även ”mellanlagring”) och distribuerar mallen. Det finns två olika sätt att distribuera mallar till Visual Studio Team Services (VS Team Services). Båda metoderna ger samma resultat, så Välj det alternativ som passar bäst för ditt arbetsflöde.

1. Lägga till ett enda steg i din build-definition som kör PowerShell-skript som ingår i Azure-resursgrupp distributionsprojekt (distribuera AzureResourceGroup.ps1). Skriptet kopierar artefakter och distribuerar mallen.
2. Lägga till flera VS Team Services skapa steg, var och en utförs steget.

Den här artikeln visar båda alternativen. Det första alternativet har fördelen med att använda samma skript används av utvecklare i Visual Studio och ger konsekvens i hela livscykeln. Det andra alternativet är ett praktiskt alternativ till det inbyggda skriptet. Båda procedurer förutsätter att du redan har ett projekt för distribution av Visual Studio checkas in VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Kopiera artefakter till Azure
Oavsett scenario, om du har alla artefakter som behövs för malldistribution, måste du ge Azure Resource Manager åtkomst till dem. Dessa artefakter kan innehålla filer som:

* Kapslade mallar
* Konfigurationsskript och DSC-skript
* Binärfiler

### <a name="nested-templates-and-configuration-scripts"></a>Kapslade mallar och konfigurationsskript
När du använder mallar som ingår i Visual Studio (eller skapats med Visual Studio kodavsnitt) PowerShell-skriptet skapar inte bara artefakter, den också parameterizes URI för resurser för olika distributioner. Skriptet och sedan kopierar artefakter till en säker behållare i Azure, skapas en SaS-token för behållaren och skickar sedan informationen in malldistributionen. Se [skapar en för malldistribution](https://msdn.microsoft.com/library/azure/dn790564.aspx) lära dig mer om kapslade mallar.  När du använder uppgifter i VS Team Services, måste du väljer lämpliga uppgifter för din för malldistribution och eventuellt ange parametervärden från fristående steg mall-distributionen.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Ställ in kontinuerlig distribution i VS Team Services
För att anropa PowerShell-skriptet i VS Team Services, som du behöver uppdatera din build-definition. I korthet består av följande steg: 

1. Redigera build-definition.
2. Ställ in Azure auktorisering VS Team Services.
3. Lägg till ett Azure PowerShell build steg som refererar till PowerShell-skript i distributionsprojektet för Azure-resursgrupp.
4. Ange värdet för den *- ArtifactsStagingDirectory* parametern för att arbeta med ett projekt som skapats i VS Team Services.

### <a name="detailed-walkthrough-for-option-1"></a>Detaljerad genomgång av alternativ 1
Följande procedurer för att gå igenom stegen för att konfigurera kontinuerlig distribution i VS Team Services med hjälp av en enskild aktivitet som kör PowerShell-skriptet i projektet. 

1. Redigera din VS Team Services build-definition och lägger till en Azure PowerShell build-steget. Välj build-definition under den **skapa definitioner** kategori och välj sedan den **redigera** länk.
   
   ![Redigera definition av build][0]
2. Lägg till en ny **Azure PowerShell** skapa steg för att skapa definition och välj sedan den **Lägg till build steg...** till.
   
   ![Lägg till build steg][1]
3. Välj den **distribuera aktiviteten** kategori, Välj den **Azure PowerShell** uppgift och välj sedan dess **Lägg till** knappen.
   
   ![Lägg till aktiviteter][2]
4. Välj den **Azure PowerShell** skapa steg och fyll sedan i dess värden.
   
   1. Om du redan har en Azure-tjänst-slutpunkt som lagts till i VS Team Services väljer du prenumerationen som den **Azure-prenumeration** nedrullningsbara listrutan och sedan hoppar du till nästa avsnitt. 
      
      Om du inte har en Azure-tjänsteslutpunkt i VS Team Services, måste du lägga till en. Detta underavsnitt tar dig igenom processen. Om ditt Azure-konto använder ett microsoftkonto (till exempel Hotmail), måste du utföra följande steg för att hämta ett huvudnamn för tjänsten-autentisering.
   2. Välj den **hantera** länka bredvid den **Azure-prenumeration** nedrullningsbara listrutan.
      
      ![Hantera Azure-prenumerationer][3]
   3. Välj **Azure** i den **nya tjänstslutpunkten** nedrullningsbara listrutan.
      
      ![Nya tjänstslutpunkten][4]
   4. I den **Lägg till Azure-prenumeration** dialogrutan markerar du den **tjänstens huvudnamn** alternativet.
      
      ![Tjänstens huvudnamn alternativet][5]
   5. Lägga till din Azure-prenumerationsinformation på den **Lägg till Azure-prenumeration** dialogrutan. Du måste ange följande:
      
      * Prenumerations-Id
      * Prenumerationsnamn
      * Tjänstens huvudnamn Id
      * Tjänstens huvudnamn nyckel
      * Klient-Id
   6. Lägga till ett namn för ditt val att den **prenumeration** namnrutan. Det här värdet senare visas i den **Azure-prenumeration** listrutan i VS Team Services. 
   7. Om du inte vet Azure prenumerations-ID, kan du använda något av följande kommandon för att hämta den.
      
      Använd för PowerShell-skript:
      
      `Get-AzureRmSubscription`
      
      Om du använder Azure CLI använder du:
      
      `azure account show`
   8. Hämta ett huvudnamn för tjänsten ID, Service Principal Key och klient-ID, Följ proceduren i [Skapa Active Directory-program och tjänstens huvudnamn med hjälp av portalen](resource-group-create-service-principal-portal.md) eller [autentiserar ett huvudnamn för tjänsten med Azure Hanteraren för filserverresurser](resource-group-authenticate-service-principal.md).
   9. Lägg till värden för tjänstens huvudnamn ID, Service Principal Key och klient-ID i den **Lägg till Azure-prenumeration** dialogrutan rutan och välj sedan den **OK** knappen.
      
      Nu har du ett giltigt huvudnamn för tjänsten för att köra Azure PowerShell-skript med.
5. Redigera build-definition och väljer den **Azure PowerShell** skapa steg. Välj prenumerationen som den **Azure-prenumeration** nedrullningsbara listrutan. (Om prenumerationen inte visas, väljer du den **uppdatera** knappen bredvid den **hantera** länken.) 
   
   ![Konfigurera Azure PowerShell build-aktivitet][8]
6. Ange en sökväg till distribuera AzureResourceGroup.ps1 PowerShell-skript. Om du vill göra det, väljer du på ellipsknappen (...) bredvid den **skriptsökvägen** går du till distribuera AzureResourceGroup.ps1 PowerShell-skriptet i den **skript** mapp i ditt projekt, markerar du den, och sedan Välj den **OK** knappen.    
   
   ![Välj en sökväg till skript][9]
7. När du väljer du skriptet uppdaterar du sökvägen till skriptet så att den körs från Build.StagingDirectory (samma katalog som *ArtifactsLocation* har angetts till). Du kan göra detta genom att lägga till ”$(Build.StagingDirectory)/” till början av sökvägen för skriptet.
   
    ![Redigera sökvägen till skriptet][10]
8. I den **skriptargument** ange följande parametrar (i en enda rad). När du kör skriptet i Visual Studio kan du se hur VS använder parametrar i den **utdata** fönster. Du kan använda den som en startpunkt för att ange parametervärden i build-steget.
   
   | Parameter | Beskrivning |
   | --- | --- |
   | -ResourceGroupLocation |Värdet geografiska plats där resursgruppen finns, som **eastus** eller **'Östra USA'**. (Lägga till enkla citattecken om namnet är ett blanksteg.) Se [Azure-regioner](https://azure.microsoft.com/en-us/regions/) för mer information. |
   | -ResourceGroupName |Namnet på resursgruppen som används för den här distributionen. |
   | -UploadArtifacts |Den här parametern när anger att artefakter som behöver överföras till Azure från det lokala systemet. Du behöver bara ange den här växeln om mallen distributionen kräver extra artefakter som du vill att mellanlagra med hjälp av PowerShell-skript (exempelvis konfigurationsskript eller kapslade mallar). |
   | -StorageAccountName |Namnet på lagringskontot används för att mellanlagra artefakter för den här distributionen. Den här parametern används bara om du Förproduktion artefakter för distribution. Om den här parametern anges skapas ett nytt lagringskonto om skriptet inte har skapat något under en tidigare distribution. Om parametern anges finnas lagringskontot redan. |
   | -StorageAccountResourceGroupName |Namnet på resursgruppen som är kopplad till lagringskontot. Den här parametern krävs endast om du anger ett värde för parametern StorageAccountName. |
   | -TemplateFile |Sökväg till mallfilen i distributionsprojektet för Azure-resursgrupp. Om du vill förbättra flexibilitet att använda en sökväg för den här parametern är i förhållande till platsen för PowerShell-skript i stället för en absolut sökväg. |
   | -TemplateParametersFile |Sökvägen till filen parametrar i distributionsprojektet för Azure-resursgrupp. Om du vill förbättra flexibilitet att använda en sökväg för den här parametern är i förhållande till platsen för PowerShell-skript i stället för en absolut sökväg. |
   | -ArtifactStagingDirectory |Den här parametern kan PowerShell script vet vilken mapp var projektets binära filer ska kopieras. Det här värdet åsidosätter standardvärdet som används av PowerShell-skript. För att använda VS Team Services, anger du värdet till: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Här är ett exempel på skript argument (bruten för att läsa rad):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   När du är klar, den **skriptargument** rutan bör likna följande lista:
   
   ![Skriptargument][11]
9. När du har lagt till alla obligatoriska objekt till Azure PowerShell build steg, väljer du den **kön** skapa om du vill skapa projektet. Den **skapa** skärmen visar utdata från PowerShell-skript.

### <a name="detailed-walkthrough-for-option-2"></a>Detaljerad genomgång av alternativ 2
Följande procedurer för att gå igenom stegen för att konfigurera kontinuerlig distribution i VS Team Services med hjälp av inbyggda aktiviteter.

1. Redigera din VS Team Services build-definition för att lägga till två nya skapa steg. Välj build-definition under den **skapa definitioner** kategori och välj sedan den **redigera** länk.
   
   ![Redigera build attributdefinitionstabellen][12]
2. Lägg till hur build till build definition med den **Lägg till build steg...** till.
   
   ![Lägg till build steg][13]
3. Välj den **distribuera** aktivitetskategori, Välj den **Azure filkopiering** uppgift och välj sedan dess **Lägg till** knappen.
   
   ![Lägg till Azure filkopiering aktivitet][14]
4. Välj den **Azure Resource distribution** uppgift och välj sedan dess **Lägg till** knappen och sedan **Stäng** den **aktivitet katalogen**.
   
   ![Lägg till Azure-resursgrupp distribution aktivitet][15]
5. Välj den **Azure filkopiering** uppgift och Fyll i dess värden.
   
   Om du redan har en Azure-tjänst-slutpunkt som lagts till i VS Team Services väljer du prenumerationen som den **Azure-prenumeration** nedrullningsbara listrutan. Om du inte har en prenumeration, se [alternativ 1](#detailed-walkthrough-for-option-1) anvisningar för att konfigurera en i VS Team Services.
   
   * Käll - ange **$(Build.StagingDirectory)**
   * Azure anslutningstypen - Välj **Azure Resource Manager**
   * Azure RM-prenumeration – Välj prenumerationen för storage-konto som du vill använda i den **Azure-prenumeration** nedrullningsbara listrutan. Om prenumerationen inte visas, väljer du den **uppdatera** knappen bredvid den **hantera** länk.
   * Måltypen - Välj **Azure Blob**
   * RM Lagringskonto - Välj lagring konto du vill använda för mellanlagring artefakter
   * Behållarens namn – Ange namnet på den behållare som du vill använda för mellanlagring; Det kan vara vilket behållarnamn som giltiga, men använder en dedikerad till denna build-definition
   
   För utdatavärden:
   
   * Ange lagringsbehållaren URI - **artifactsLocation**
   * Storage-behållare SAS-Token - ange **artifactsLocationSasToken**
   
   ![Konfigurera Azure filkopiering aktivitet][16]
6. Välj den **Azure Resource distribution** skapa steg och fyll sedan i dess värden.
   
   * Azure anslutningstypen - Välj **Azure Resource Manager**
   * Azure RM-prenumeration – Välj prenumerationen för distribution i den **Azure-prenumeration** nedrullningsbara listrutan. Det här är vanligtvis samma prenumeration som används i föregående steg
   * Åtgärd - väljer **skapa eller uppdatera resursgruppen.**
   * Resursgrupp – Välj en resursgrupp eller ange namnet på en ny resursgrupp för distributionen
   * Plats – Välj plats för resursgruppen
   * Mall – ange sökvägen och namnet på mallen som ska distribueras tillagt **$(Build.StagingDirectory)**, till exempel: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Mallparametrar – ange sökvägen och namnet på parametrarna som ska användas, tillagt **$(Build.StagingDirectory)**, till exempel: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Åsidosätt mallparametrar – ange eller kopiera och klistra in följande kod:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Konfigurera Azure-resursgrupp distribution aktivitet][17]
7. När du har lagt till alla objekt, spara build-definition och välj **kö nya build** längst upp.

## <a name="next-steps"></a>Nästa steg
Läs [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md) lära dig mer om Azure Resource Manager och Azure-resursgrupper.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
