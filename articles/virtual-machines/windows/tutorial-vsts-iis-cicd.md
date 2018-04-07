---
title: Skapa en CI/CD-pipeline i Azure med Team Services | Microsoft Docs
description: Lär dig hur du skapar en Visual Studio Team Services pipeline för kontinuerlig integration och som distribuerar ett webbprogram till IIS på en Windows VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cf6e3013d4dfc7e18d96a717a76b591cde939139
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Skapa en pipeline för kontinuerlig integrering med Visual Studio Team Services och IIS
Du kan använda en kontinuerlig integrering och distribution (CI/CD) pipeline för att automatisera bygga, testa och distribution faser för programutveckling. I den här självstudiekursen skapar du en CI/CD-pipeline med hjälp av Visual Studio Team Services och en Windows-dator (VM) i Azure som kör IIS. Lär dig att:

> [!div class="checklist"]
> * Publicera ett ASP.NET-webbprogram till ett Team Services-projekt
> * Skapa en definition av build som utlöses av koden incheckningar
> * Installera och konfigurera IIS på en virtuell dator i Azure
> * Lägga till IIS-instans i en distribution i Team Services
> * Skapa en version definitionen för att publicera ny webbplats distribuerar paket till IIS
> * Testa CI/CD-pipelinen

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).


## <a name="create-project-in-team-services"></a>Skapa projekt i Team Services
Visual Studio Team Services möjliggör enkel samarbete och utveckling utan att behålla en hanteringslösning för lokal kod. Team Services innehåller molnet kod testning, bygga och application insights. Du kan välja en version kontrollen lagringsplatsen och IDE som bäst passar din kod utveckling. Du kan använda ett kostnadsfritt konto för den här självstudiekursen för att skapa en grundläggande ASP.NET-webbprogram och CI/CD-pipeline. Om du inte redan har ett Team Services-konto [skapar du en](http://go.microsoft.com/fwlink/?LinkId=307137).

Om du vill hantera commit koden, skapa definitioner och släpper definitioner, skapar du ett projekt i Team Services:

1. Öppna instrumentpanelen Team Services i en webbläsare och välj **nytt projekt**.
2. Ange *myWebApp* för den **projektnamn**. Lämna övriga standardvärdena att använda *Git* versionskontroll och *Agile* process för arbetsobjekt.
3. Välj alternativet för att **dela med** *gruppmedlemmar*och välj **skapa**.
5. När projektet har skapats kan du välja alternativet att **initieras med en viktigt- eller gitignore**, sedan **initiera**.
6. I det nya projektet, Välj **instrumentpaneler** högst upp på sidan, Välj **öppna i Visual Studio**.


## <a name="create-aspnet-web-application"></a>Skapa ASP.NET-webbprogram
I det föregående steget Skapa ett projekt i Team Services. Det sista steget öppnar det nya projektet i Visual Studio. Du hanterar din kod incheckningar i den **Team Explorer** fönster. Skapa en lokal kopia av det nya projektet och sedan skapa en ASP.NET-webbprogram från en mall på följande sätt:

1. Välj **klona** att skapa en lokal git-lagringsplatsen i ditt Team Services-projekt.
    
    ![Klona lagringsplatsen från Team Services-projekt](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Under **lösningar**väljer **ny**.

    ![Skapa-webbprogramslösning](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Välj **Web** mallar, och välj sedan den **ASP.NET-webbprogram** mall.
    1. Ange ett namn för programmet, till exempel *myWebApp*, och avmarkera kryssrutan **Skapa katalog för lösning**.
    2. Om alternativet är tillgängligt, avmarkerar du kryssrutan för att **Lägg till Application Insights i projekt**. Application Insights måste du godkänna ditt webbprogram med Azure Application Insights. Om du vill göra det enkelt i den här självstudiekursen, hoppar du över den här processen.
    3. Välj **OK**.
4. Välj **MVC** mall-listan.
    1. Välj **ändra autentisering**, Välj **ingen autentisering**och välj **OK**.
    2. Välj **OK** att skapa din lösning.
5. I den **Team Explorer** fönstret Välj **ändringar**.

    ![Utföra lokala ändringar i Team Services git repo](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. I textrutan Bekräfta att ange ett meddelande som *inledande commit*. Välj **genomför alla och Sync** från den nedrullningsbara menyn.


## <a name="create-build-definition"></a>Skapa build-definition
I Team Services använder du en build-definition för att beskriva hur programmet ska byggas. I den här självstudiekursen skapar vi en grundläggande definition tar våra källkoden bygger lösningen och sedan skapar web distribuera paket som vi kan använda för att köra webbprogrammet på en IIS-server.

1. I projektet Team Services väljer **Skapa & släpper** högst upp på sidan, Välj **bygger**.
3. Välj **+ ny definition**.
4. Välj den **ASP.NET (FÖRHANDSGRANSKNING)** mall och välj **tillämpa**.
5. Låt alla standardvärdet uppgiften värden. Under **hämta källor**, se till att den *myWebApp* databasen och *master* grenen väljs.

    ![Skapa build-definition i Team Services-projekt](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. På den **utlösare** fliken, flytta skjutreglaget för **aktivera den här utlösaren** till *aktiverad*.
7. Spara build definitionen och kön en ny version genom att välja **Spara & kö** , sedan **Spara & kö** igen. Lämna standardinställningarna och välj **kön**.

Titta på börjar som bygga schemaläggs på en värdbaserad agent sedan skapa. Utdata ser ut ungefär så här:

![Lyckad version av Team Services-projekt](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
För att ge en plattform för att köra ditt webbprogram för ASP.NET, behöver du en virtuell Windows-dator som kör IIS. Team Services använder en agent för att interagera med IIS-instans som du kopplar kod och versioner har utlösts.

Skapa en virtuell Windows Server 2016-dator med hjälp av [detta skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). Det tar några minuter för skriptet att köra och skapa den virtuella datorn. När den virtuella datorn har skapats kan du öppna port 80 för webbtrafik med [Lägg till AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) på följande sätt:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Hämta den offentliga IP-adressen för att ansluta till den virtuella datorn, [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) på följande sätt:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Skapa en fjärrskrivbordssession till den virtuella datorn:

```cmd
mstsc /v:<publicIpAddress>
```

På den virtuella datorn, öppna ett **administratör PowerShell** kommandotolk. Installera IIS och nödvändiga .NET-funktioner på följande sätt:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Skapa distributionsgrupp
För att skicka webbdistributionspaket till IIS-servern, du definierar en distributionsgrupp i Team Services. Den här gruppen kan du ange vilka servrar som är mål för nya versioner som du kopplar kod till Team Services och versioner är slutförda.

1. I Team Services väljer **Skapa & släpper** och välj sedan **distributionsgrupper**.
2. Välj **lägga till distributionsgruppen**.
3. Ange ett namn för gruppen, som *myIIS*och välj **skapa**.
4. I den **registrera datorer** Kontrollera *Windows* har valts och sedan markera kryssrutan för **använder en personlig åtkomsttoken i skriptet för autentisering**.
5. Välj **kopiera skriptet till Urklipp**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Lägg till IIS VM i distributionsgruppen
Lägg till varje IIS-instans i gruppen med distributionsgruppen skapas. Team Services genererar ett skript som hämtar och konfigurerar en agent på den virtuella datorn som tar emot nya distribuera paket och sedan gäller för IIS.

1. I den **administratör PowerShell** session på den virtuella datorn, klistra in och kör skriptet kopieras från Team Services.
2. När du uppmanas att konfigurera taggar för agenten, välja *Y* och ange *web*.
3. När du uppmanas att ange användarkontot trycker du på *returnera* att acceptera standardinställningarna.
4. Vänta tills skriptet avslutas med ett meddelande *vstsagent.account.computername för tjänsten har startats*.
5. I den **distributionsgrupper** sida av den **Skapa & släpper** menyn och öppna den *myIIS* distributionsgruppen. På den **datorer** kontrollerar du att den virtuella datorn visas.

    ![VM lagts har till Team Services distributionsgruppen](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Skapa en definition för versionen
För att publicera dina versioner, skapar du en definition av versionen i Team Services. Den här definitionen utlöses automatiskt av en lyckad version av programmet. Distributionsgruppen att push-webbplatsen du distribuerar paketet till och definiera IIS-inställningarna.

1. Välj **Skapa & släpper**och välj **bygger**. Välj build-definitionen skapades i föregående steg.
2. Under **nyligen slutförts**, välja den senaste versionen och markerar sedan **versionen**.
3. Välj **Ja** att skapa en definition av versionen.
4. Välj den **tom** mall, välj sedan **nästa**.
5. Kontrollera att bygga projektet och källa definitionen fylls med projektet.
6. Välj den **kontinuerlig distribution** kryssrutan och välj sedan **skapa**.
7. Välj i listrutan bredvid **+ Lägg till aktiviteter** och välj *lägga till en distribution grupp fas*.
    
    ![Lägg till aktivitet om du vill frigöra definition i Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Välj **Lägg till** bredvid **IIS Web App Deploy(Preview)**och välj **Stäng**.
9. Välj den **körs på distributionsgruppen** överordnad aktivitet.
    1. För **distributionsgruppen**, Välj distributionsgruppen som du skapade tidigare, t.ex *myIIS*.
    2. I den **datorn taggar** väljer **Lägg till** och välj den *web* tagg.
    
    ![Släpp definition distribution grupp uppgiften för IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Välj den **distribuera: IIS Web App Deploy** uppgift att konfigurera inställningarna för IIS-instans enligt följande:
    1. För **webbplatsnamn**, ange *standardwebbplats*.
    2. Lämna alla andra standardinställningar.
12. Välj **spara**och välj **OK** två gånger.


## <a name="create-a-release-and-publish"></a>Skapa en version och publicera
Du kan nu push webbplatsen distribuera paket som en ny version. Det här steget kommunicerar med agenten på varje instans som ingår i distributionsgruppen, skickar Internet distribuera paket och konfigurerar IIS att köra webbprogrammet uppdaterade.

1. Markera i definitionen för versionen **+ släpper**, Välj **skapa släpper**.
2. Kontrollera att den senaste versionen är markerad i listrutan, tillsammans med **automatisk distribution: efter att versionen har skapats**. Välj **Skapa**.
3. En liten banderoll visas överst i definitionen för versionen som *version 'Version 1' har skapats*. Välj länken versionen.
4. Öppna den **loggar** att övervaka status för versionen.
    
    ![Lyckad Team Services versionen och web distribuera paketet push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. När versionen har slutförts, öppna en webbläsare och ange den offentliga PIA-adressen för den virtuella datorn. ASP.NET-webbprogram körs.

    ![ASP.NET-webbprogram som körs på IIS VM](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testa hela CI/CD-pipelinen
Med ditt webbprogram som körs på IIS, försök hela CI/CD-pipeline. När du har gjort en ändring i Visual Studio och spara utlöses koden, en version som utlöser en version av den uppdaterade webbplatsen kan du distribuera paketet till IIS:

1. Öppna i Visual Studio den **Solution Explorer** fönster.
2. Navigera till och öppna *myWebApp | Vyer | Start | Index.cshtml*
3. Redigera rad 6 att läsa:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Spara filen.
5. Öppna den **Team Explorer** väljer den *myWebApp* projektet och välj sedan **ändringar**.
6. Ange ett commit-meddelande som *testning CI/CD-pipeline*, Välj **alla genomförande och Sync** från den nedrullningsbara menyn.
7. I arbetsytan Team Services utlöses en ny version från kod genomförande. 
    - Välj **Skapa & släpper**och välj **bygger**. 
    - Välj build-definition, och välj sedan den **i kö & körs** build kan du titta på medan build pågår.
9. När bygga lyckas, utlöses en ny version.
    - Välj **Skapa & släpper**, sedan **versioner** att se på webben distribuera paket som skickas till din IIS-VM. 
    - Välj den **uppdatera** ikon för att uppdatera statusen. När den *miljöer* kolumnen visas en grön bock, versionen har distribuerats till IIS.
11. Uppdatera din IIS-webbplats i en webbläsare om du vill se dina ändringar tillämpas.

    ![ASP.NET-webbprogram som körs på IIS VM från CI/CD-pipeline](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Nästa steg

I kursen får du skapat en ASP.NET-webbprogram i Team Services och konfigurerat build och versionen definitioner för att distribuera nya distribuera paket till IIS på varje kod genomförande. Du har lärt dig att:

> [!div class="checklist"]
> * Publicera ett ASP.NET-webbprogram till ett Team Services-projekt
> * Skapa en definition av build som utlöses av koden incheckningar
> * Installera och konfigurera IIS på en virtuell dator i Azure
> * Lägga till IIS-instans i en distribution i Team Services
> * Skapa en version definitionen för att publicera ny webbplats distribuerar paket till IIS
> * Testa CI/CD-pipelinen

Gå till nästa kurs att lära dig hur du installerar en SQL&#92;IIS&#92;.NET-stacken på ett par med virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET stack](tutorial-iis-sql.md)