---
title: Kontinuerlig integrering i Azure DevOps-tjänster med hjälp av Azure-resursgruppsprojekt | Microsoft Docs
description: Beskriver hur du ställer in kontinuerlig integration i Azure DevOps-tjänster med hjälp av projekt för distribution av Azure-resursgrupp i Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: a3dfca48b52af39e7a536b3012a3f4cdac4e9a94
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955175"
---
# <a name="continuous-integration-in-azure-devops-services-using-azure-resource-group-deployment-projects"></a>Kontinuerlig integrering i Azure DevOps-tjänster med projekt för distribution av Azure-resursgrupp
För att distribuera en Azure-mall kan du utföra uppgifter i olika faser: Build-, Test, kopiera till Azure (kallas även ”mellanlagring”) och distribuera mallen. Det finns två olika sätt att distribuera mallar till Azure DevOps-tjänsterna. Båda metoderna ger samma resultat, så du väljer det alternativ som bäst passar ditt arbetsflöde.

1. Lägga till ett enda steg till din build-pipeline som kör PowerShell-skript som ingår i distributionsprojektet för Azure-resursgrupp (distribuera AzureResourceGroup.ps1). Skriptet kopierar artefakter och distribuerar sedan mallen.
2. Lägg till flera Azure DevOps-tjänsterna skapa steg, vart och ett utför en aktivitet för steg.

Den här artikeln visar båda alternativen. Det första alternativet har fördelen med att använda samma skript används av utvecklare i Visual Studio och ger konsekvens i hela livscykeln. Det andra alternativet är ett praktiskt alternativ till inbyggda skriptet. Båda procedurerna förutsätter att du redan har ett projekt för distribution av Visual Studio som kontrolleras i Azure DevOps-tjänsterna.

## <a name="copy-artifacts-to-azure"></a>Kopiera artefakter till Azure
Oavsett hur ditt scenario, om du har alla artefakter som behövs för malldistribution, måste du ge Azure Resource Manager-åtkomst till dem. Dessa artefakter kan innehålla filer som:

* Kapslade mallar
* Konfigurationsskript och DSC-skript
* Binärfilerna för programmet

### <a name="nested-templates-and-configuration-scripts"></a>Kapslade mallar och konfigurationsskript
När du använder mallar som skapas av Visual Studio (eller skapats med Visual Studio-kodfragment) PowerShell-skript skapar inte bara nivåer artefakter, den också funktionsfråga URI för resurser för olika distributioner. Skriptet och sedan kopierar artefakterna till en säker behållare i Azure, skapar en SaS-token för behållaren och skickar sedan informationen in malldistributionen. Se [skapar en för malldistribution](https://msdn.microsoft.com/library/azure/dn790564.aspx) mer information om kapslade mallar.  När du använder uppgifter i Azure DevOps-tjänsterna, måste du välja lämpliga uppgifter för malldistributionen och om det behövs, skickar parametervärden från mellanlagring steg till malldistributionen.

## <a name="set-up-continuous-deployment-in-azure-pipelines"></a>Konfigurera kontinuerlig distribution i Azure-Pipelines
För att anropa PowerShell-skriptet i Azure Pipelines, måste du uppdatera din build-pipeline. Enkelt uttryckt är stegen: 

1. Redigera build-pipeline.
2. Konfigurera auktorisering i Azure-Pipelines med Azure.
3. Lägg till en Azure PowerShell-byggsteg som refererar till PowerShell-skriptet i distributionsprojektet för Azure-resursgrupp.
4. Ange värdet för den *- ArtifactsStagingDirectory* parametern för att arbeta med ett projekt som skapats i Azure-Pipelines.

### <a name="detailed-walkthrough-for-option-1"></a>Detaljerad genomgång av alternativ 1
Följande procedurer för att gå igenom stegen för att konfigurera kontinuerlig distribution i Azure DevOps-tjänster med hjälp av en aktivitet som kör PowerShell-skript i projektet. 

1. Redigera din Azure DevOps-tjänsterna build-pipeline och lägga till ett byggsteg för Azure PowerShell. Välj build-pipeline under den **skapa pipeliner** kategori och välj sedan den **redigera** länk.
   
   ![Redigera build pipeline][0]
2. Lägga till en ny **Azure PowerShell** skapa steg för att skapa pipelinen och välj sedan den **Lägg till byggsteg...** till.
   
   ![Lägg till byggsteg][1]
3. Välj den **distribuera uppgift** kategori, väljer den **Azure PowerShell** uppgift, och välj sedan dess **Lägg till** knappen.
   
   ![Lägga till aktiviteter][2]
4. Välj den **Azure PowerShell** byggsteg och fyller sedan i dess värden.
   
   1. Om du redan har en Azure service-slutpunkt som lagts till i Azure DevOps-tjänster, Välj prenumerationen som den **Azure-prenumeration** nedrullningsbara listrutan och sedan hoppar du till nästa avsnitt. 
      
      Om du inte har en Azure service-slutpunkt i Azure DevOps-tjänsterna kan behöva du lägga till en. Detta avsnitt tar dig igenom processen. Om ditt Azure-konto använder ett Microsoft-konto (till exempel Hotmail), måste du vidta följande steg för att få en autentisering med tjänstens huvudnamn.

   2. Välj den **hantera** länka bredvid den **Azure-prenumeration** nedrullningsbara listrutan.
      
      ![Hantera Azure-prenumerationer][3]
   3. Välj **Azure** i den **nya tjänstslutpunkt** nedrullningsbara listrutan.
      
      ![Ny tjänstslutpunkt][4]
   4. I den **Lägg till Azure-prenumeration** dialogrutan den **tjänstens huvudnamn** alternativet.
      
      ![Alternativet för tjänstens huvudnamn][5]
   5. Lägg till din Azure-prenumerationsinformation till den **Lägg till Azure-prenumeration** dialogrutan. Du måste ange följande:
      
      * Prenumerations-ID
      * Prenumerationsnamn
      * Id för tjänstens huvudnamn
      * Nyckel för tjänstens huvudnamn
      * Klient-ID
   6. Lägga till ett namn för att den **prenumeration** namnrutan. Det här värdet senare visas i den **Azure-prenumeration** listrutan i Azure DevOps-tjänsterna. 

   7. Om du inte vet ditt Azure-prenumerations-ID, kan du använda något av följande kommandon för att hämta den.
      
      Använd för PowerShell-skript:
      
      `Get-AzureRmSubscription`
      
      Om du använder Azure CLI använder du:
      
      `azure account show`
   8. Att hämta ett ID för tjänstens huvudnamn, tjänstens huvudnamn nyckel och klient-ID, Följ proceduren i [Skapa Active Directory-program och tjänstobjekt med portalen](active-directory/develop/howto-create-service-principal-portal.md) eller [autentisera ett huvudnamn för tjänsten med Azure Resource Manager](active-directory/develop/howto-authenticate-service-principal-powershell.md).
   9. Lägg till värden för ID för tjänstens huvudnamn, tjänstens huvudnamn nyckel och klient-ID i den **Lägg till Azure-prenumeration** dialogrutan rutan och välj sedan den **OK** knappen.
      
      Nu har du ett giltigt huvudnamn för tjänsten för att köra Azure PowerShell-skript med.
5. Redigerar build-pipeline och välj den **Azure PowerShell** byggsteg. Välj prenumerationen de **Azure-prenumeration** nedrullningsbara listrutan. (Om prenumerationen inte visas, väljer du den **uppdatera** knappen bredvid den **hantera** länk.) 
   
   ![Konfigurera Azure PowerShell build-uppgift][8]
6. Ange en sökväg till distribuera AzureResourceGroup.ps1 PowerShell-skript. Gör detta genom att välja knappen med tre punkter (...) bredvid den **skriptets sökväg** , bläddrar till distribuera AzureResourceGroup.ps1 PowerShell-skriptet i den **skript** mapp i ditt projekt, markera den och sedan Välj den **OK** knappen.    
   
   ![Välj sökväg till skript][9]
7. När du väljer du skriptet uppdaterar du sökvägen till skriptet så att den körs från Build.StagingDirectory (samma katalog som *ArtifactsLocation* har angetts till). Du kan göra detta genom att lägga till ”$(Build.StagingDirectory)/” till början av skriptets sökväg.
   
    ![Redigera sökvägen till skript][10]
8. I den **skriptargument** , ange följande parametrar (i en enda rad). När du kör skriptet i Visual Studio kan du se hur VS använder parametrarna i den **utdata** fönster. Du kan använda detta som en startpunkt för att ange parametervärden i din byggsteg.
   
   | Parameter | Beskrivning |
   | --- | --- |
   | -ResourceGroupLocation |Värdet geografiska plats där resursgruppen finns, till exempel **eastus** eller **”östra USA”**. (Lägg till enkla citattecken om det finns ett blanksteg i namnet.) Se [Azure-regionerna](https://azure.microsoft.com/regions/) för mer information. |
   | -ResourceGroupName |Namnet på resursgruppen som används för den här distributionen. |
   | -UploadArtifacts |Den här parametern när det finns, anger att artefakter som behöver överföras till Azure från det lokala systemet. Du behöver bara ange den här växeln om malldistributionen av kräver extra artefakter som du vill att mellanlagra med hjälp av PowerShell-skript (exempelvis konfigurationsskript eller kapslade mallar). |
   | -StorageAccountName |Namnet på det lagringskonto som används för att steget artefakter för den här distributionen. Den här parametern används bara om du organiserar artefakter för distribution. Om den här parametern anges, skapas ett nytt lagringskonto om skriptet inte har skapat någon under en tidigare distribution. Om parametern har angetts måste till storage-kontot redan finnas. |
   | -StorageAccountResourceGroupName |Namnet på resursgruppen som är associerade med lagringskontot. Den här parametern krävs endast om du anger ett värde för parametern StorageAccountName. |
   | -TemplateFile |Sökväg till mallfilen i distributionsprojektet för Azure-resursgrupp. För att förbättra flexibiliteten att använda en sökväg för den här parametern som är relativ till platsen för PowerShell-skriptet i stället för en absolut sökväg. |
   | -TemplateParametersFile |Sökvägen till filen parametrar i distributionsprojektet för Azure-resursgrupp. För att förbättra flexibiliteten att använda en sökväg för den här parametern som är relativ till platsen för PowerShell-skriptet i stället för en absolut sökväg. |
   | -ArtifactStagingDirectory |Den här parametern kan PowerShell skript vet mappen från där projektets binära filer ska kopieras. Det här värdet åsidosätter standardvärdet används av PowerShell-skriptet. För att använda Azure DevOps-tjänsterna, anger du värdet till: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Här är ett exempel på skript argument (rad bruten för läsbarhet):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   När du är klar, den **skriptargument** rutan bör likna följande lista:
   
   ![Skriptargument][11]
9. När du har lagt till alla nödvändiga objekt till ett byggsteg för Azure PowerShell, väljer den **kö** skapa knappen för att skapa projektet. Den **skapa** skärmen visar utdata från PowerShell-skriptet.

### <a name="detailed-walkthrough-for-option-2"></a>Detaljerad genomgång av alternativ 2
Följande procedurer för att gå igenom stegen för att konfigurera kontinuerlig distribution i Azure DevOps-tjänster med hjälp av inbyggda uppgifter.

1. Redigera din Azure DevOps-tjänsterna build-pipeline för att lägga till två nya skapa steg. Välj build-pipeline under den **Byggesdefinitioner** kategori och välj sedan den **redigera** länk.
   
   ![Redigera build-definitionslänken][12]
2. Lägg till ny byggsteg i build pipeline med hjälp av den **Lägg till byggsteg...** till.
   
   ![Lägg till byggsteg][13]
3. Välj den **distribuera** aktivitetskategori, väljer den **Azure File Copy** uppgift, och välj sedan dess **Lägg till** knappen.
   
   ![Lägg till Azure File Copy-aktivitet][14]
4. Välj den **Azure Resursgruppsdistribution** uppgift, och välj sedan dess **Lägg till** knappen och sedan **Stäng** den **uppgift Catalog**.
   
   ![Lägg till aktivitet för Azure Resursgruppsdistribution][15]
5. Välj den **Azure File Copy** uppgift och fylla i dess värden.
   
   Om du redan har en Azure service-slutpunkt som lagts till i Azure DevOps-tjänster, Välj prenumerationen som den **Azure-prenumeration** nedrullningsbara listrutan. Om du inte har en prenumeration, se [alternativ 1](#detailed-walkthrough-for-option-1) anvisningar för att konfigurera en i Azure DevOps-tjänsterna.
   
   * Käll - ange **$(Build.StagingDirectory)**
   * Azure anslutningstypen - Välj **Azure Resource Manager**
   * Azure RM-prenumeration – Välj den prenumeration för det lagringskonto som du vill använda i den **Azure-prenumeration** nedrullningsbara listrutan. Om prenumerationen inte visas, väljer du den **uppdatera** knappen bredvid den **hantera** länk.
   * Typ av mål - Välj **Azure Blob**
   * RM Lagringskonto – Välj det lagringskonto du vill använda för att mellanlagra artefakter
   * Behållarens namn – Ange namnet på behållaren som du vill använda för mellanlagring; Det kan vara vilket namn som giltig behållare, men använder en dedikerad för den här build-pipeline
   
   För utdatavärden:
   
   * Anger du lagringsbehållare URI - **artifactsLocation**
   * Storage-behållare SAS-Token - ange **artifactsLocationSasToken**
   
   ![Konfigurera Azure File Copy-uppgift][16]
6. Välj den **Azure Resursgruppsdistribution** byggsteg och fyller sedan i dess värden.
   
   * Azure anslutningstypen - Välj **Azure Resource Manager**
   * Azure RM-prenumeration – Välj prenumerationen för distribution i den **Azure-prenumeration** nedrullningsbara listrutan. Det här är vanligtvis samma prenumeration som används i föregående steg
   * Åtgärd - väljer **skapa eller uppdatera resursgrupp**
   * Resursgrupp – Välj en resursgrupp eller ange namnet på en ny resursgrupp för distributionen
   * Plats – Välj plats för resursgruppen.
   * Mall – ange sökvägen och namnet på mallen som ska distribueras prepending **$(Build.StagingDirectory)**, till exempel: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Mallparametrar – ange sökvägen och namnet på parametrarna som ska användas, prepending **$(Build.StagingDirectory)**, till exempel: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Åsidosätt mallparametrar – ange eller kopiera och klistra in följande kod:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Konfigurera Distributionsuppgift för Azure-resursgrupp][17]
7. När du har lagt till alla nödvändiga objekt, spara build-pipeline och välj **ny version i kö** högst upp.

## <a name="next-steps"></a>Nästa steg
Läs [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md) vill veta mer om Azure Resource Manager och Azure-resursgrupper.

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
