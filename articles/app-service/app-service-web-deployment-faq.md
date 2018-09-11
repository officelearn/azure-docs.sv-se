---
title: Distribution av vanliga frågor och svar för Azure web apps | Microsoft Docs
description: Få svar på vanliga frågor om distributionen för funktionen Web Apps i Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ab8750e5824cf9f7635d11a6b2be332b2f9a761c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302596"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Distribution av vanliga frågor och svar för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om distributionsproblem för den [Web Apps-funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Jag är bara början med App Service web apps. Hur publicerar jag min kod?

Här följer några alternativ för att publicera webbappens kod:

*   Distribuera med hjälp av Visual Studio. Om du har Visual Studio-lösningen, högerklicka på programmet webbprojektet och välj sedan **publicera**.
*   Distribuera med hjälp av en FTP-klient. Ladda ned publiceringsprofil för den webbapp som du vill distribuera din kod till Azure-portalen. Ladda sedan upp filer till \site\wwwroot med hjälp av samma Publicera profil FTP autentiseringsuppgifter.

Mer information finns i [distribuera din app till App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Jag får ett felmeddelande när jag försöker distribuera från Visual Studio. Hur löser jag det?

Om du ser följande meddelande du kanske använder en äldre version av SDK ”: ett fel uppstod vid distribution för resursen” YourResourceName ”i resursgruppen” YourResourceGroup ”: MissingRegistrationForLocation: prenumerationen har inte registrerats för den Resurstyp komponenter om du på plats ”USA, centrala”. Registrera den här leverantören för att få tillgång till den här platsen ”. 

Lös felet genom att uppgradera till den [senaste SDK: N](https://azure.microsoft.com/downloads/). Om du ser det här meddelandet och du har den senaste SDK kan skicka en supportförfrågan.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hur jag för att distribuera ett ASP.NET-program från Visual Studio till App Service?
<a id="deployasp"></a>

Självstudien [skapa din första ASP.NET-webbapp i Azure på fem minuter](app-service-web-get-started-dotnet.md) visar hur du distribuerar en ASP.NET-webbprogram till en webbapp i App Service med Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Vilka är de olika typerna av autentiseringsuppgifter för distribution?

App Service stöder två typer av autentiseringsuppgifter för lokal Git-distribution och distribution för FTP/S. Mer information om hur du konfigurerar autentiseringsuppgifter för distribution finns i [konfigurera autentiseringsuppgifter för distribution för App Service](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Vad är filen eller katalogen strukturen för min App Service-webbapp?

Information om filen strukturen för din App Service-app finns i [filen strukturen i Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hur löser jag ”FTP fel 550 - där inte är tillräckligt med utrymme på disken” när jag försöker FTP-Mina filer?

Om du ser det här meddelandet är det troligt att du kör i en diskkvot i service-plan för webbappen. Du kan behöva skala upp till en högre tjänstnivå baserat på dina behov av diskutrymme. Läs mer om priserna för planer och resursbegränsningar [prissättning för App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hur ställer jag in kontinuerlig distribution för min App Service webbapp?

Du kan ställa in kontinuerlig distribution från flera resurser, inklusive Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox och andra Git-lagringsplatser. Dessa alternativ är tillgängliga i portalen. [Kontinuerlig distribution till App Service](app-service-continuous-deployment.md) är en bra genomgång som förklarar hur du ställer in kontinuerlig distribution.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hur felsöker jag problem med kontinuerlig distribution från GitHub och Bitbucket?

Hjälp med att undersöka problem med kontinuerlig distribution från GitHub eller Bitbucket finns i [undersöka kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Jag kan inte FTP till min webbplats och publicera min kod. Hur löser jag det?

Lösa problem som FTP:

1. Kontrollera att du angett rätt värdnamn och autentiseringsuppgifter. Detaljerad information om olika typer av autentiseringsuppgifter och hur de används finns i [distributionsbehörigheterna](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Kontrollera att FTP-portarna inte blockeras av en brandvägg. Portarna som ska ha de här inställningarna:
    * Anslutningsport för FTP-kontroll: 21
    * Anslutningsport för FTP-data: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hur publicerar jag min kod till App Service?

Snabbstart för Azure är utformat för att distribuera din app med hjälp av distributionsstacken och metod för ditt val. Om du vill använda snabbstarten i Azure-portalen, gå till **inställningar** > **Appdistribution**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Varför startar ibland min app efter distributionen till App Service?

Läs mer om omständigheter som en programdistribution kan resultera i att en omstart, i [distributionen jämfört med körningsproblem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Eftersom artikeln beskriver distribuerar filer till Wwwroot-mappen i App Service. Den startar aldrig direkt om din app.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hur jag för att integrera Azure DevOps-kod med App Service?

Har du två alternativ för att använda kontinuerlig distribution med Azure DevOps:

*   Använd ett Git-projekt. Anslut via App Service med hjälp av distributionsalternativen för den lagringsplatsen.
*   Använd ett Team Foundation Version Control (TFVC)-projekt. Distribuera med hjälp av build-agenten för App Service.

Kontinuerlig koddistribution för båda alternativen är beroende av befintliga arbetsflöden och checka in procedurer. Mer information finns i dessa artiklar: 

*   [Implementera kontinuerlig distribution av programmet till en Azure-webbplats](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Ställ in en Azure DevOps-organisation så att den kan distribuera till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hur använder jag FTP eller FTPS för att distribuera min app till App Service?

Information om hur du använder FTP eller FTPS för att distribuera din webbapp till App Service finns i [distribuera din app till App Service med FTP/S](app-service-deploy-ftp.md).
