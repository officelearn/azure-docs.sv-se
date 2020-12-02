---
title: Publicera en webbapp till en virtuell Azure-dator från Visual Studio
description: Publicera ett ASP.NET-webbprogram på en virtuell Azure-dator från Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 7cf2474c9ca005d85cea28f2b8fa1e23836b191f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487566"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicera en ASP.NET-webbapp till en virtuell Azure-dator från Visual Studio

Det här dokumentet beskriver hur du publicerar ett ASP.NET-webbprogram på en virtuell Azure-dator med hjälp av funktionen för **Microsoft Azure Virtual Machines** publicering i Visual Studio 2019.  

## <a name="prerequisites"></a>Förutsättningar
För att kunna använda Visual Studio för att publicera ett ASP.NET-projekt till en virtuell Azure-dator måste den virtuella datorn konfigureras på rätt sätt.

- Datorn måste vara konfigurerad för att köra ett ASP.NET-webbprogram och ha WebDeploy installerat. Mer information finns i [skapa en virtuell ASP.net-dator med WebDeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- Den virtuella datorn måste ha ett konfigurerat DNS-namn. Mer information finns i [skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Windows-dator](../create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicera ASP.NET-webbappen på den virtuella Azure-datorn med Visual Studio
I följande avsnitt beskrivs hur du publicerar ett befintligt ASP.NET-webbprogram på en virtuell Azure-dator.

1. Öppna din Web App-lösning i Visual Studio 2019.
2. Högerklicka på projektet i Solution Explorer och välj **publicera...**
3. Använd pilen till höger på sidan för att bläddra igenom publicerings alternativen tills du hittar **Microsoft Azure Virtual Machines**.  

   ![Publicera sida-HÖGERPIL]

4. Välj ikonen **Microsoft Azure Virtual Machines** och välj **publicera**.

   ![Sidan publicera – Microsoft Azure ikon för virtuell dator]

5. Välj lämpligt konto (med Azure-prenumeration som är ansluten till den virtuella datorn).  
   - Om du är inloggad i Visual Studio fylls konto listan i med alla dina autentiserade konton.  
   - Om du inte är inloggad, eller om kontot du behöver inte finns med i listan, väljer du Lägg till ett konto... och följ anvisningarna för att logga in.  
   ![Konto väljare för Azure]  

6. Välj lämplig virtuell dator i listan över befintliga Virtual Machines.

   > [!Note]
   > Det kan ta en stund att fylla listan.

   ![VIRTUELL Azure-väljare]

7. Klicka på OK för att börja publicera.

8. När du uppmanas att ange autentiseringsuppgifter anger du användar namnet och lösen ordet för ett användar konto på den virtuella mål datorn som har kon figurer ATS med publicerings behörighet. Dessa autentiseringsuppgifter är vanligt vis administratörens användar namn och lösen ord som används när du skapar den virtuella datorn.  

   ![Inloggning för WebDeploy]

9. Godkänn säkerhetscertifikatet.

   ![Certifikat fel]

10. Se fönstret utdata för att kontrol lera förloppet för publicerings åtgärden.

    ![fönstret Utmatning]

11. Om publiceringen lyckas startas en webbläsare för att öppna URL: en för den nyligen publicerade platsen.

**Resultatet!**

Nu har du publicerat din webbapp på en virtuell Azure-dator.

## <a name="publish-page-options"></a>Alternativ för publicerings sida

När du har slutfört publicerings guiden öppnas publicerings sidan i dokumentets välbefinnande med den nya publicerings profilen vald.

### <a name="re-publish"></a>Publicera igen

Om du vill publicera uppdateringar i ditt webb program väljer du knappen **publicera** på sidan publicera.  
- Ange användar namn och lösen ord om du uppmanas att göra det.  
- Publiceringen börjar omedelbart.

![Sidan publicera – knappen publicera]

### <a name="modify-publish-profile-settings"></a>Ändra inställningar för publicerings profil

Om du vill visa och ändra inställningarna för publicerings profilen väljer du **Inställningar...**.  

![Sidan publicera – knappen Inställningar]

Inställningarna bör se ut ungefär så här:  

![Publicerings inställningar – anslutnings sida]

#### <a name="save-user-name-and-password"></a>Spara användar namn och lösen ord
- Undvik att tillhandahålla autentiseringsinformation varje gång du publicerar. Det gör du genom att fylla i fälten **användar namn** och **lösen ord** och markera rutan **Spara lösen ord** .
- Använd knappen **Verifiera anslutning** för att bekräfta att du har angett rätt information.

#### <a name="deploy-to-clean-web-server"></a>Distribuera till en ren webb server

- Om du vill kontrol lera att webb servern har en ren kopia av webb programmet efter varje uppladdning och att inga andra filer återstår från en tidigare distribution, kan du markera kryss rutan **ta bort ytterligare filer i målet** på fliken **Inställningar** .

- Varning: om du publicerar med den här inställningen tas alla filer som finns på webb servern (Wwwroot-katalogen) bort. Se till att du känner till datorns tillstånd innan du publicerar med det här alternativet aktiverat. 

![Publicerings inställningar – sidan Inställningar]

## <a name="next-steps"></a>Nästa steg

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Konfigurera CI/CD för automatisk distribution till virtuell Azure-dator

Om du vill konfigurera en pipeline för kontinuerlig leverans med Azure-pipelines, se [distribuera till en virtuell Windows-dator](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publicera sida-HÖGERPIL]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Sidan publicera – Microsoft Azure ikon för virtuell dator]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Konto väljare för Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[VIRTUELL Azure-väljare]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Inloggning för WebDeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Certifikat fel]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[fönstret Utmatning]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Sidan publicera – knappen publicera]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Sidan publicera – knappen Inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publicerings inställningar – anslutnings sida]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publicerings inställningar – sidan Inställningar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png