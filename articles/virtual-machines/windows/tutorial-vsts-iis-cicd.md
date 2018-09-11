---
title: Självstudie – skapa en CI/CD-pipeline i Azure med Azure DevOps-tjänsterna | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en Azure-tjänster för DevOps-pipeline för kontinuerlig integrering och leverans som distribuerar en webbapp i IIS på en Windows-dator i Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d4edf0d22ce04eb2cb865d80c2b70f1bcc2169df
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301906"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-azure-devops-services-and-iis"></a>Självstudier: Skapa en pipeline för kontinuerlig integrering med Azure DevOps-tjänster och IIS
Du kan använda en kontinuerlig integrering och distribution (CI/CD) pipeline för att automatisera den att skapa, testa och distributionsfaser för programutveckling. I den här självstudien skapar du en CI/CD-pipeline med Azure DevOps-tjänsterna och en Windows-dator (VM) i Azure som kör IIS. Lär dig att:

> [!div class="checklist"]
> * Publicera ASP.NET-webbprogram till ett Azure-tjänster för DevOps-projekt
> * Skapa en build-pipeline som utlöses av kod incheckningar
> * Installera och konfigurera IIS på en virtuell dator i Azure
> * Lägga till IIS-instans i en distribution i Azure DevOps-tjänsterna
> * Skapa en pipeline för versionen för att publicera nya web distribuera paket till IIS
> * Testa CI/CD-pipeline

Den här självstudiekursen kräver Azure PowerShell-modulen version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="create-a-project-in-azure-devops-services"></a>Skapa ett projekt i Azure DevOps-tjänsterna
Azure DevOps-tjänster kan enkelt samarbete och utveckling utan att behålla en hanteringslösning för lokal kod. Azure DevOps-tjänsterna tillhandahåller moln kod testning, version och application insights. Du kan välja en version kontroll lagringsplatsen och IDE som bäst passar utvecklingen av din kod. Du kan använda en kostnadsfri organisation för att skapa en grundläggande ASP.NET-webbapp och CI/CD-pipeline i den här självstudien. Om du inte redan har en organisation med Azure DevOps-tjänsterna, [skapar ett](http://go.microsoft.com/fwlink/?LinkId=307137).

Om du vill hantera allokeringsprocessen kod, skapa pipeliner och släppa pipelines, skapar du ett projekt i Azure DevOps-tjänsterna på följande sätt:

1. Öppna instrumentpanelen i Azure DevOps-tjänsterna i en webbläsare och välj **nytt projekt**.
2. Ange *myWebApp* för den **projektnamn**. Lämna alla övriga standardvärden att använda *Git* versionskontroll och *Agile* arbetsobjektet bearbeta.
3. Välj alternativet att **dela med** *teammedlemmar*och välj sedan **skapa**.
5. När projektet har skapats, kan du välja alternativet att **initieras med en Viktigt-filen eller gitignore**, sedan **initiera**.
6. I det nya projektet, väljer **instrumentpaneler** överst, Välj **öppna i Visual Studio**.


## <a name="create-aspnet-web-application"></a>Skapa ASP.NET-webbprogram
I det föregående steget skapade du ett projekt i Azure DevOps-tjänsterna. Det sista steget öppnas det nya projektet i Visual Studio. Du hanterar din kod incheckningar i den **Team Explorer** fönster. Skapa en lokal kopia av det nya projektet och sedan skapa en ASP.NET-webbapp från en mall på följande sätt:

1. Välj **klona** att skapa en lokal git-lagringsplats för projektet Azure DevOps-tjänsterna.
    
    ![Klona lagringsplatsen från Services för Azure DevOps-projekt](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Under **lösningar**väljer **New**.

    ![Skapa webbprogramslösning](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Välj **Web** mallar, och välj sedan den **ASP.NET-webbprogram** mall.
    1. Ange ett namn för ditt program, till exempel *myWebApp*, och avmarkera kryssrutan **Skapa katalog för lösningen**.
    2. Om alternativet är tillgängligt, avmarkerar du kryssrutan till **Lägg till Application Insights i projekt**. Application Insights måste du godkänna ditt webbprogram med Azure Application Insights. Hoppa över den här processen för att den enkelt i den här självstudien.
    3. Välj **OK**.
4. Välj **MVC** från lista.
    1. Välj **ändra autentisering**, Välj **ingen autentisering**och välj sedan **OK**.
    2. Välj **OK** att skapa din lösning.
5. I den **Team Explorer** fönstret Välj **ändringar**.

    ![Genomför lokala ändringar av Azure-lagringsplatser Git-lagringsplats](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. I textrutan commit ange ett meddelande som *första commit*. Välj **genomför alla och synkronisera** från den nedrullningsbara menyn.


## <a name="create-build-pipeline"></a>Skapa build-pipeline
I Azure DevOps-tjänsterna använder du en build-pipeline för att beskriva hur ditt program bör skapas. I den här självstudiekursen skapar vi en grundläggande pipeline som tar vår källkod, bygger lösningen och sedan skapar web distribuera paket som vi kan använda för att köra webbappen på en IIS-server.

1. I projektet Azure DevOps-tjänsterna väljer **Build & Release** överst, Välj **bygger**.
3. Välj **+ ny definition**.
4. Välj den **ASP.NET (FÖRHANDSVERSION)** mallen och välj **tillämpa**.
5. Låt alla standardvärdet uppgift värden. Under **hämta källor**, se till att den *myWebApp* databasen och *master* grenen har valts.

    ![Skapa build pipeline i Azure DevOps-Services-projekt](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. På den **utlösare** fliken, flytta skjutreglaget för **aktivera den här utlösaren** till *aktiverad*.
7. Spara build pipeline och kön en ny version genom att välja **spara och köa** , sedan **spara och köa** igen. Lämna standardvärdena och välj **kö**.

Titta på börjar när versionen har schemalagts för en värdbaserad agent sedan skapa. Utdata ser ut ungefär så här:

![Version av Services för Azure DevOps-projekt](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
För att tillhandahålla en plattform för att köra ASP.NET-webbappen behöver du en virtuell Windows-dator som kör IIS. Azure DevOps-tjänster använder en agent för att interagera med IIS-instansen som du kopplar kod och versioner utlöses.

Skapa en Windows Server 2016 VM med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVM* i den *USA, östra* plats. Resursgruppen *myResourceGroupAzureDevOpsServices* och stödjande nätverksresurser skapas också. Att tillåta webbtrafik, TCP-port *80* öppnas till den virtuella datorn. När du uppmanas, anger du ett användarnamn och lösenord som ska användas som autentiseringsuppgifter för inloggning för den virtuella datorn:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupAzureDevOpsServices" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Hämta den offentliga IP-adressen med för att ansluta till den virtuella datorn, [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) på följande sätt:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Skapa en fjärrskrivbordssession till den virtuella datorn:

```cmd
mstsc /v:<publicIpAddress>
```

På den virtuella datorn, öppna en **administratör PowerShell** Kommandotolken. Installera IIS och nödvändiga .NET-funktioner på följande sätt:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Skapa distributionsgrupp

För att skicka ut webbdistributionspaket till IIS-servern, du definierar en grupp i Azure DevOps-tjänsterna. Den här gruppen kan du ange vilka servrar som är mål för nya versioner som du kan lägga in kod i Azure DevOps-tjänsterna och versioner har slutförts.

1. I Azure DevOps-tjänster, väljer **Build & Release** och välj sedan **distributionsgrupper**.
2. Välj **Lägg till grupp**.
3. Ange ett namn för gruppen, till exempel *myIIS*och välj sedan **skapa**.
4. I den **registrera datorer** Kontrollera *Windows* har valts och sedan markerar kryssrutan **använder en personlig åtkomsttoken i skriptet för autentisering**.
5. Välj **kopiera skriptet till Urklipp**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Lägg till IIS VM i distributionsgruppen
Lägg till varje IIS-instans i gruppen med distributionsgruppen skapas. Azure DevOps-tjänster genererar ett skript som laddar ned och konfigurerar en agent på den virtuella datorn som tar emot nya web distribuera paket och använder den sedan till IIS.

1. I den **administratör PowerShell** session på den virtuella datorn, klistra in och kör skriptet kopieras från Azure DevOps-tjänsterna.
2. När du uppmanas att konfigurera taggar för agenten, välja *Y* och ange *web*.
3. När du tillfrågas om användarkontot, trycker du på *returnera* att acceptera standardinställningarna.
4. Vänta tills skriptet som ska slutföras med ett meddelande *vstsagent.account.computername Service har startats*.
5. I den **distributionsgrupper** för den **Build & Release** menyn och öppna den *myIIS* distributionsgrupp. På den **datorer** fliken, kontrollerar du att den virtuella datorn visas.

    ![Virtuell dator lagts har till i Azure DevOps-tjänsterna distributionsgrupp](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-pipeline"></a>Skapa releasepipeline
Om du vill publicera dina versioner, skapar du en pipeline för versionen i Azure DevOps-tjänsterna. Denna pipeline utlöses automatiskt av en version av ditt program. Du väljer distributionsgrupp att skicka ditt webb-distribuera paketet till och definiera IIS-inställningarna.

1. Välj **Build & Release**och välj sedan **bygger**. Välj den build-pipeline som skapade i föregående steg.
2. Under **nyss blev klara**, Välj den senaste versionen och välj **versionen**.
3. Välj **Ja** att skapa en releasepipeline.
4. Välj den **tom** mall, välj sedan **nästa**.
5. Kontrollera projekt och källa build pipelinen fylls med ditt projekt.
6. Välj den **kontinuerlig distribution** kryssrutan och välj sedan **skapa**.
7. Välj den nedrullningsbara listrutan bredvid **+ Lägg till aktiviteter** och välj *lägger du till gruppen distributionsfasen*.
    
    ![Lägg till aktivitet om du vill frigöra pipeline i Azure DevOps-tjänsterna](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Välj **Lägg till** bredvid **IIS Web App Deploy(Preview)** och välj sedan **Stäng**.
9. Välj den **körs på distributionsgrupp** överordnade uppgiften.
    1. För **distributionsgrupp**, Välj en distributionsgrupp som du skapade tidigare, till exempel *myIIS*.
    2. I den **Machine taggar** väljer **Lägg till** och välj den *web* tagg.
    
    ![Versionen pipeline distributionsuppgift grupp för IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Välj den **distribuera: IIS Web App Deploy** uppgift att konfigurera inställningarna för IIS-instans enligt följande:
    1. För **webbplatsnamn**, ange *standardwebbplats*.
    2. Lämna alla andra standardinställningar.
12. Välj **spara**och välj sedan **OK** två gånger.


## <a name="create-a-release-and-publish"></a>Skapa en version och publicera
Du kan nu skicka ditt webb-distribuera paketet som en ny version. Det här steget kommunicerar med agenten på varje instans som ingår i distributionsgruppen, push-meddelanden på webben distribuera paketet och sedan konfigurerar IIS att köra det uppdaterade webbprogrammet.

1. I din releasepipeline väljer **+ Release**, välj sedan **skapa viktig**.
2. Kontrollera att den senaste versionen har valts i listrutan, tillsammans med **automatisk distribution: när versionen har skapats**. Välj **Skapa**.
3. En liten banderoll överst på din releasepipeline som *versionen ”Release-1” har skapats*. Välj länken versionen.
4. Öppna den **loggar** flik för att se förloppet för versionen.
    
    ![Lyckade Azure DevOps-tjänsterna versionen och web distribuera paketet push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. När versionen har slutförts, öppna en webbläsare och ange den offentliga PIA-adressen för den virtuella datorn. ASP.NET-webbapp körs.

    ![ASP.NET-webbapp som körs på IIS VM](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testa hela CI/CD-pipeline
Med ditt webbprogram som körs på IIS, prova hela CI/CD-pipeline. När du har gjort en ändring i Visual Studio och bekräfta din kod, ett build har utlösts som utlöser en version av webbplatsen uppdaterade distribuera paketet till IIS:

1. Visual Studio, öppna den **Solution Explorer** fönster.
2. Navigera till och öppna *myWebApp | Vyer | Start | Index.cshtml*
3. Redigera rad 6 att läsa:

    `<h1>ASP.NET with Azure DevOps Services and CI/CD!</h1>`

4. Spara filen.
5. Öppna den **Team Explorer** väljer den *myWebApp* projektet och välj sedan **ändringar**.
6. Ange ett meddelande, till exempel *testning CI/CD-pipeline*, välj sedan **Commit alla och synkronisera** från den nedrullningsbara menyn.
7. Azure DevOps-tjänsterna arbetsytan utlöses en ny version från kodbekräftelse. 
    - Välj **Build & Release**och välj sedan **bygger**. 
    - Välj din build-pipeline och välj den **i kö & körs** build du säkert titta när skapa fortskrider.
9. När versionen har genomförts utlöses en ny version.
    - Välj **Build & Release**, sedan **versioner** att se webben distribuera paket som skickas till din IIS-VM. 
    - Välj den **uppdatera** ikon för att uppdatera statusen. När den *miljöer* kolumnen visar en grön bock, versionen har distribuerats till IIS.
11. Uppdatera din IIS-webbplats i en webbläsare om du vill se ändringarna tillämpas.

    ![ASP.NET-webbapp som körs på IIS VM från CI/CD-pipeline](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien får du skapat ASP.NET-webbprogram i Azure DevOps-tjänsterna och konfigurerat build och releaser att distribuera nya web distribuera paket till IIS på varje kodgenomförande. Du har lärt dig att:

> [!div class="checklist"]
> * Publicera ASP.NET-webbprogram till ett Azure-tjänster för DevOps-projekt
> * Skapa en build-pipeline som utlöses av kod incheckningar
> * Installera och konfigurera IIS på en virtuell dator i Azure
> * Lägga till IIS-instans i en distribution i Azure DevOps-tjänsterna
> * Skapa en pipeline för versionen för att publicera nya web distribuera paket till IIS
> * Testa CI/CD-pipeline

Fortsätt till nästa självstudie och lär dig att installera en SQL&#92;IIS&#92;.NET-stack på ett par med virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET-stack](tutorial-iis-sql.md)
