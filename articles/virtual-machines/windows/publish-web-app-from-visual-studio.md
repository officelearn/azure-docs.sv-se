---
title: Publicera ett webbprogram till en virtuell dator i Azure från Visual Studio
description: Publicera ett ASP.NET-webbprogram till en virtuell Azure-dator från Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: aspnet
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d9e2c1a941c25aedd9048f2784350106b8739dba
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "31797204"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicera en ASP.NET-Webbapp till en virtuell dator i Azure från Visual Studio

Det här dokumentet beskrivs hur du publicerar ett ASP.NET-webbprogram till en virtuell Azure-dator (VM) med hjälp av den **Microsoft Azure Virtual Machines** publiceringsfunktionen i Visual Studio-2017.  

## <a name="prerequisites"></a>Förutsättningar
För att kunna använda Visual Studio för att publicera en ASP.NET-projekt till en Azure VM, måste den virtuella datorn vara korrekt inställt.

- Datorn måste konfigureras för att köra ASP.NET-webbprogram och ha WebDeploy installerad.

- Den virtuella datorn måste ha ett DNS-namn som har konfigurerats. Mer information finns i [skapa ett fullständigt kvalificerat domännamn i Azure-portalen för en Windows VM](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicera din ASP.NET-webbapp på Azure-dator med hjälp av Visual Studio
I följande avsnitt beskrivs hur du publicerar ett befintligt ASP.NET-webbprogram till en virtuell Azure-dator.

1. Öppna din web app lösning i Visual Studio-2017.
2. Högerklicka på projektet i Solution Explorer och välj **publicera...**
3. Använd pilen till höger på sidan för att rulla igenom publiceringsalternativ tills du hittar **Microsoft Azure Virtual Machines**.  

   ![Publicera sida - HÖGERPIL]

4. Välj den **Microsoft Azure Virtual Machines** och välj **publicera**.

   ![Publicera sida - ikon för Microsoft Azure-dator]

5. Välj lämpligt konto (med Azure-prenumeration ansluten till den virtuella datorn).  
   - Om du är inloggad på Visual Studio, fylls på listan över med alla autentiserade konton.  
   - Om du inte är inloggad, eller om det konto som du behöver inte visas, välja ”Lägg till ett konto...” och följ anvisningarna för att logga in.  
   ![Selector Azure-konto]  

6. Välj lämplig virtuell dator i listan på befintliga virtuella datorer.

   > [!Note]
   > Kan ta lite tid att fylla i den här listan.

   ![Azure VM väljare]

7. Klicka på OK börjar publicera.

8. När du tillfrågas om autentiseringsuppgifter, ange användarnamn och lösenord för ett användarkonto på målet virtuell dator som är konfigurerad med att publicera rättigheter (vanligtvis admin-användarnamn och lösenord som används när du skapar den virtuella datorn).  

   ![WebDeploy-inloggning]

9. Acceptera säkerhetscertifikatet.

   ![Certifikatfel]

10. Titta på utdatafönstret för att kontrollera status för publiceringen.

    ![Utdatafönstret]

11. Om publiceringen har lyckats, startar en webbläsare för att öppna URL för den nyligen publicerade webbplatsen.

**Lyckades!**

Du har nu publicerat din webbapp till en virtuell Azure-dator.

## <a name="publish-page-options"></a>Publicera sidor

När du har slutfört guiden Publicera öppnas publicera sidan i dokumentet med nya publiceringsprofilen som du valt.

### <a name="re-publish"></a>Publicera

Om du vill publicera uppdateringar till ditt webbprogram, Välj den **publicera** på sidan Publicera.  
- Om du uppmanas ange användarnamn och lösenord.  
- Publicering börjar omedelbart.

![Publicera sida - knapp publicera]

### <a name="modify-publish-profile-settings"></a>Ändra publicera profilinställningar

Om du vill visa och ändra publiceringsinställningarna för profil, Välj **inställningar...** .  

![Publicera sida - inställningar]

Inställningarna bör se ut ungefär så här:  

![Publiceringsinställningar - anslutningssidan]

#### <a name="save-user-name-and-password"></a>Spara användarnamn och lösenord
- För att undvika att tillhandahålla autentiseringsuppgifter varje gång du publicerar kan du fylla i den **användarnamn** och **lösenord** fält och markera den **spara lösenordet** rutan.
- Använd den **Validera anslutningen** för att bekräfta att du har angett rätt information.

#### <a name="deploy-to-clean-web-server"></a>Distribuera om du vill rensa webbserver

- Om du vill kontrollera att webbservern har en ren till webbprogrammet efter varje överför (och att inga andra filer lämnas hängande från en tidigare distribution) kan du kontrollera den **ta bort extra filer från destinationen** kryssrutan i den **inställningar** fliken.

- Varning: Publicering med den här inställningen tar bort alla filer som finns på webbservern (wwwroot directory). Se till att du vet tillståndet för datorn innan du publicerar med det här alternativet aktiverat. 

![Publiceringsinställningar - inställningssidan]

## <a name="next-steps"></a>Nästa steg

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Ställ in CI/CD för automatisk distribution till Azure VM

Om du vill konfigurera en kontinuerlig leverans pipeline med Visual Studio Team-tjänsten finns [distribuera till en virtuell Windows-dator](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publicera sida - HÖGERPIL]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publicera sida - ikon för Microsoft Azure-dator]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selector Azure-konto]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM väljare]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy-inloggning]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Certifikatfel]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Utdatafönstret]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publicera sida - knapp publicera]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publicera sida - inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publiceringsinställningar - anslutningssidan]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publiceringsinställningar - inställningssidan]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
