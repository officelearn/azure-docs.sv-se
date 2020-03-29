---
title: Publicera en webbapp till en virtuell Azure-dator från Visual Studio
description: Publicera ett ASP.NET webbprogram på en virtuell Azure-dator från Visual Studio
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970870"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicera en ASP.NET web app till en virtuell Azure-dator från Visual Studio

I det här dokumentet beskrivs hur du publicerar ett ASP.NET webbprogram till en virtuell Azure-dator (VM) med hjälp av publiceringsfunktionen **för Microsoft Azure Virtual Machines** i Visual Studio 2019.  

## <a name="prerequisites"></a>Krav
För att kunna använda Visual Studio för att publicera ett ASP.NET projekt till en virtuell Azure-dator måste den virtuella datorn vara korrekt konfigurerad.

- Datorn måste vara konfigurerad för att köra ett ASP.NET webbprogram och ha WebDeploy installerat.

- Den virtuella datorn måste ha ett DNS-namn konfigurerat. Mer information finns [i Skapa ett fullständigt kvalificerat domännamn i Azure-portalen för en Windows VM](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicera din ASP.NET webbapp till Den virtuella Azure-datorn med Visual Studio
I följande avsnitt beskrivs hur du publicerar ett befintligt ASP.NET webbprogram till en virtuell Azure-dator.

1. Öppna webbapplösningen i Visual Studio 2019.
2. Högerklicka på projektet i Lösningsutforskaren och välj **Publicera...**
3. Använd pilen till höger på sidan för att bläddra igenom publiceringsalternativen tills du hittar **Microsoft Azure Virtual Machines**.  

   ![Publicera sida - högerpil]

4. Välj ikonen **Microsoft Azure Virtual Machines** och välj **Publicera**.

   ![Publiceringssida – ikon för Microsoft Azure Virtual Machine]

5. Välj lämpligt konto (med Azure-prenumeration ansluten till din virtuella dator).  
   - Om du är inloggad i Visual Studio fylls kontolistan i med alla dina autentiserade konton.  
   - Om du inte är inloggad, eller om kontot du behöver inte finns med i listan, väljer du "Lägg till ett konto..." och följ anvisningarna för att logga in.  
   ![Azure-kontoväljare]  

6. Välj lämplig virtuell dator i listan över befintliga virtuella datorer.

   > [!Note]
   > Det kan ta lite tid att fylla i den här listan.

   ![Azure VM-väljare]

7. Klicka på OK för att börja publicera.

8. När du uppmanas att ange autentiseringsuppgifter anger du användarnamn och lösenord för ett användarkonto på måldatorn som har konfigurerats med publiceringsrättigheter. Dessa autentiseringsuppgifter är vanligtvis administratörens användarnamn och lösenord som används när den virtuella datorn skapas.  

   ![Webbdeploy-inloggning]

9. Acceptera säkerhetscertifikatet.

   ![Certifikatfel]

10. Titta på utdatafönstret för att kontrollera förloppet för publiceringsåtgärden.

    ![Utdatafönster]

11. Om publiceringen lyckas startar en webbläsare för att öppna webbadressen till den nyligen publicerade webbplatsen.

**Framgång!**

Du har nu publicerat din webbapp till en virtuell Azure-dator.

## <a name="publish-page-options"></a>Alternativ för publicering av sidor

När publiceringsguiden har slutförts öppnas sidan Publicera i dokumentet väl med den nya publiceringsprofilen markerad.

### <a name="re-publish"></a>Publicera på för en om

Om du vill publicera uppdateringar i webbprogrammet väljer du knappen **Publicera** på sidan Publicera.  
- Om du uppmanas till det anger du användarnamn och lösenord.  
- Publiceringen börjar omedelbart.

![Knappen Publicera sida - knappen Publicera]

### <a name="modify-publish-profile-settings"></a>Ändra inställningar för publiceringsprofil

Om du vill visa och ändra inställningarna för publicera profil väljer du **Inställningar...**.  

![Knappen Publicera sida – knappen Inställningar]

Dina inställningar bör se ut ungefär så här:  

![Publiceringsinställningar - Sidan Anslutning]

#### <a name="save-user-name-and-password"></a>Spara användarnamn och lösenord
- Undvik att tillhandahålla autentiseringsinformation varje gång du publicerar. Om du vill göra det fyller du i fälten **Användarnamn** och **Lösenord** och väljer rutan **Spara lösenord.**
- Använd knappen **Validera anslutning** för att bekräfta att du har angett rätt information.

#### <a name="deploy-to-clean-web-server"></a>Distribuera för att rensa webbservern

- Om du vill vara medveten om att webbservern har en ren kopia av webbprogrammet efter varje överföring och att inga andra filer finns kvar från en tidigare distribution kan du markera kryssrutan **Ta bort ytterligare filer vid mål på** fliken **Inställningar.**

- Varning: Om du publicerar med den här inställningen tas alla filer som finns på webbservern (wwwroot-katalogen bort). Kontrollera att du känner till maskinens tillstånd innan du publicerar det här alternativet. 

![Sidan Publiceringsinställningar – sidan Inställningar]

## <a name="next-steps"></a>Nästa steg

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Konfigurera CI/CD för automatiserad distribution till Azure VM

Information om hur du konfigurerar en kontinuerlig leveranspipeline med Azure Pipelines finns i [Distribuera till en Virtuell Windows-dator](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publicera sida - högerpil]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publiceringssida – ikon för Microsoft Azure Virtual Machine]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-kontoväljare]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM-väljare]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Webbdeploy-inloggning]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Certifikatfel]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Utdatafönster]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Knappen Publicera sida - knappen Publicera]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Knappen Publicera sida – knappen Inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publiceringsinställningar - Sidan Anslutning]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Sidan Publiceringsinställningar – sidan Inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
