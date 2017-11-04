---
title: "Distribution och svar för Azure-webbappar | Microsoft Docs"
description: "Få svar på vanliga frågor om distribution för funktionen Web Apps i Azure App Service."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 318a236652229c4e093ca33886ac1831686aed73
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Distribution och svar för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om distributionsproblem för den [funktionen Web Apps i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Det är bara komma igång med App Service web apps. Hur jag för att publicera min kod?

Här följer några alternativ för att publicera web app koden:

*   Distribuera med Visual Studio. Om du har Visual Studio-lösning, högerklicka på webbapprojektet och välj sedan **publicera**.
*   Distribuera med hjälp av en FTP-klient. Hämta publiceringsprofil för den webbapp som du vill distribuera din kod i Azure-portalen. Överför sedan filerna till \site\wwwroot med hjälp av samma Publicera profil FTP autentiseringsuppgifter.

Mer information finns i [distribuera din app till App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Jag ser ett felmeddelande när jag försöker distribuera från Visual Studio. Hur lösa problemet?

Om du ser följande meddelande du kanske använder en äldre version av SDK ”: fel under distributionen för resurs 'YourResourceName' i resursgruppen 'YourResourceGroup': MissingRegistrationForLocation: prenumerationen har inte registrerats för den resurstypen 'komponenter' på plats 'Centrala USA'. Registrera för den här providern för att få tillgång till den här platsen ”. 

Lös problemet genom att uppgradera till den [senaste SDK: N](https://azure.microsoft.com/downloads/). Om du ser det här meddelandet och du har den senaste SDK: N, begär support.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hur distribuerar ett ASP.NET-program från Visual Studio till App Service?
<a id="deployasp"></a>

Kursen [skapa din första ASP.NET-webbapp i Azure på fem minuter](app-service-web-get-started-dotnet.md) visar hur du distribuerar ett ASP.NET-webbprogram till en webbapp i App Service med Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Vilka är de olika typerna av autentiseringsuppgifter för distribution?

Apptjänst stöder två typer av autentiseringsuppgifter för lokal Git och FTP-/ S-distributionen. Mer information om hur du konfigurerar autentiseringsuppgifter för distribution finns [konfigurera autentiseringsuppgifter för distribution för Apptjänst](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Vad är filen eller katalogen strukturen för webbappen Apptjänst?

Information om filstruktur för din App Service-appen finns [filen strukturen i Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hur löser jag ”FTP fel 550 - det finns inte är tillräckligt med utrymme på disken” när jag försöker FTP-Mina filer?

Om du ser det här meddelandet är det troligt att du kör i en diskkvot i service-plan för webbappen. Du kan behöva skalas till en högre tjänstnivå baserat på dina behov av diskutrymme. Mer information om priser planer och gränserna finns [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hur ställer jag in kontinuerlig distribution för webbprogrammet min App Service?

Du kan ställa in kontinuerlig distribution från flera resurser, inklusive Visual Studio Team Services, OneDrive, GitHub, Bitbucket, Dropbox och andra Git-databaser. Dessa alternativ är tillgängliga i portalen. [Kontinuerlig distribution till App Service](app-service-continuous-deployment.md) en bra genomgång som förklarar hur du ställer in kontinuerlig distribution.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hur felsöker problem med kontinuerlig distribution från GitHub och Bitbucket?

Hur du undersöker problem med kontinuerlig distribution från GitHub eller Bitbucket finns [undersöker kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Jag kan inte FTP till min webbplats och publicera min kod. Hur lösa problemet?

Så här löser du problem med FTP:

1. Kontrollera att du anger rätt värdnamnet och autentiseringsuppgifterna. Detaljerad information om olika typer av autentiseringsuppgifter och hur de används finns [distributionsbehörigheterna](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Kontrollera att FTP-portarna inte blockeras av en brandvägg. Portarna som bör ha dessa inställningar:
    * Anslutningsport för FTP-kontroll: 21
    * FTP-dataporten för anslutning: 989 10001 10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hur publicera min kod till App Service?

Azure Quickstart är utformad för att distribuera din app med hjälp av distribution stack och metod du föredrar. Om du vill använda Snabbstart, i Azure portal, gå till **inställningar** > **Appdistribution**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Varför min app ibland starta om efter distribution till App Service?

Mer information om de omständigheter som en programdistribution kan resultera i en omstart, se [distribution kontra runtime problem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Eftersom artikeln, distribuerar Apptjänst filer i Wwwroot-mappen. Den startar aldrig direkt om din app.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Hur integreras Visual Studio Team Services kod med App Service?

Har du två alternativ för att använda kontinuerlig distribution med Visual Studio Team Services:

*   Använd ett Git-projekt. Anslut via App Service med hjälp av distributionsalternativ för den lagringsplatsen.
*   Använd ett Team Foundation Version kontrollen (TFVC)-projekt. Distribuera med build-agent för Apptjänst.

Koden kontinuerlig distribution för båda alternativen beror på befintliga developer arbetsflöden och checka in procedurer. Mer information finns i följande artiklar: 

*   [Implementera kontinuerlig distribution för din app till en Azure-webbplats](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Konfigurera ett Visual Studio Team Services-konto så att den kan distribuera till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hur använder FTP eller FTPS distribuera min app till App Service?

Information om hur du använder FTP eller FTPS för att distribuera ditt webbprogram till App Service finns [distribuera din app till App Service med hjälp av FTP/S](app-service-deploy-ftp.md).
