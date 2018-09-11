---
title: Publicera en Webbapp till en Azure virtuell dator från Visual Studio
description: Publicera en ASP.NET Webbapp till Azure-datorer från Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d5fd6041f21700ffddd3b2c4755b7ce01691681e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304109"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicera en ASP.NET-Webbapp till en Azure virtuell dator från Visual Studio

Det här dokumentet beskriver hur du publicerar ett ASP.NET-webbprogram till en Azure-dator (VM) med hjälp av den **Microsoft Azure Virtual Machines** publiceringsfunktionen i Visual Studio 2017.  

## <a name="prerequisites"></a>Förutsättningar
För att kunna använda Visual Studio för att publicera ett ASP.NET-projekt till en Azure-dator, ställas den virtuella datorn ska in.

- Datorn måste konfigureras för att köra ASP.NET-webbprogram och låta WebDeploy installerad.

- Den virtuella datorn måste ha ett DNS-namn som har konfigurerats. Mer information finns i [skapa ett fullständigt kvalificerat domännamn i Azure portal för en virtuell Windows-dator](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicera din ASP.NET-webbapp med Visual Studio Azure-datorn
I följande avsnitt beskrivs hur du publicerar ett befintligt ASP.NET-webbprogram till en Azure-dator.

1. Öppna din web app-lösning i Visual Studio 2017.
2. Högerklicka på projektet i Solution Explorer och välj **publicera...**
3. Använd pilen till höger på sidan för att bläddra igenom publiceringsalternativ tills du hittar **Microsoft Azure Virtual Machines**.  

   ![Publicera sidan – HÖGERPIL]

4. Välj den **Microsoft Azure Virtual Machines** och välj **publicera**.

   ![Publicera sidan – Microsoft Azure-dator-ikon]

5. Välj lämpligt konto (med Azure-prenumeration är anslutna till den virtuella datorn).  
   - Om du har loggat in till Visual Studio, fylls kontolista med alla autentiserade konton.  
   - Om du inte är inloggad, eller om det konto som du behöver inte visas, välj ”Lägg till ett konto...” och följ anvisningarna för att logga in.  
   ![Väljare för Azure-konto]  

6. Välj den lämpliga Virtuellt enheten i listan över befintliga virtuella datorer.

   > [!Note]
   > Kan ta lite tid att fylla i den här listan.

   ![Azure VM-väljare]

7. Klicka på OK om du vill starta publiceringen.

8. När du tillfrågas om autentiseringsuppgifter, anger du användarnamnet och lösenordet för ett användarkonto på målet virtuell dator som är konfigurerad med att publicera rättigheter (vanligtvis administratörens användarnamn och lösenord som användes när du skapade den virtuella datorn).  

   ![WebDeploy-inloggning]

9. Acceptera säkerhetscertifikatet.

   ![Certifikatfel]

10. Se utdatafönstret om du vill kontrollera status för publiceringen.

    ![Utdatafönstret]

11. Om publiceringen har slutförts startar en webbläsare för att öppna URL: en för den nyligen publicerade webbplatsen.

**Lyckades!**

Du har nu publicerat din webbapp på Azure-datorer.

## <a name="publish-page-options"></a>Publicera sidan alternativ

När du har slutfört Publiceringsguiden öppnas på publiceringssidan i dokumentet bra med nya publiceringsprofilen som du har valt.

### <a name="re-publish"></a>Publicera på nytt

Om du vill publicera uppdateringar i ditt webbprogram, Välj den **publicera** på sidan Publicera.  
- Om du uppmanas, anger du användarnamn och lösenord.  
- Publiceringen börjar omedelbart.

![Publicera sidan – knappen Publicera]

### <a name="modify-publish-profile-settings"></a>Ändra publicera profilinställningar

Om du vill visa och ändra publiceringsinställningarna för profilen, Välj **inställningar...** .  

![Publicera sidan – knappen Inställningar]

Inställningarna bör se ut ungefär så här:  

![Publiceringsinställningar - anslutningssidan]

#### <a name="save-user-name-and-password"></a>Spara användarnamn och lösenord
- För att undvika att ange autentiseringsuppgifter varje gång du publicerar kan du fylla i den **användarnamn** och **lösenord** fält och välj den **spara lösenordet** box.
- Använd den **Validera anslutningen** för att bekräfta att du har angett rätt information.

#### <a name="deploy-to-clean-web-server"></a>Distribuera för att rensa webbserver

- Om du vill kontrollera att servern har en ren till webbprogrammet efter varje överföring (och att inga andra filer lämnas hängande från en tidigare distribution) du kan kontrollera den **ta bort extra filer från destinationen** kryssrutan i den **inställningar** fliken.

- Varning: Publicering med den här inställningen tar bort alla filer som finns på webbservern (Wwwroot-katalog). Måste du veta vilket tillstånd datorn innan du publicerar det här alternativet. 

![Publiceringsinställningar - sidan Inställningar]

## <a name="next-steps"></a>Nästa steg

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Konfigurera CI/CD för automatisk distribution för Azure VM

Om du vill konfigurera en pipeline för kontinuerlig leverans med Azure-Pipelines finns i [distribuera till en Windows-dator](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publicera sidan – HÖGERPIL]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publicera sidan – Microsoft Azure-dator-ikon]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Väljare för Azure-konto]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM-väljare]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy-inloggning]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Certifikatfel]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Utdatafönstret]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publicera sidan – knappen Publicera]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publicera sidan – knappen Inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publiceringsinställningar - anslutningssidan]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publiceringsinställningar - sidan Inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
