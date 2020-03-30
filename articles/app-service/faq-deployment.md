---
title: Vanliga frågor om distribution – Azure App Service | Microsoft-dokument
description: Få svar på vanliga frågor om distribution för webbappfunktionen i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671688"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Vanliga frågor om distribution för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (Vanliga frågor) om distributionsproblem för [webbappfunktionen](https://azure.microsoft.com/services/app-service/web/)i Azure App Service .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Jag har precis börjat med App Service webbappar. Hur publicerar jag min kod?

Här är några alternativ för att publicera webbappkoden:

*   Distribuera med Visual Studio. Om du har Visual Studio-lösningen högerklickar du på webbprogrammet och väljer sedan **Publicera**.
*   Distribuera med hjälp av en FTP-klient. I Azure-portalen laddar du ned publiceringsprofilen för webbappen som du vill distribuera koden till. Ladda sedan upp filerna till \site\wwwroot med samma FTP-autentiseringsuppgifter för publiceringsprofilen.

Mer information finns i [Distribuera din app till App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Jag ser ett felmeddelande när jag försöker distribuera från Visual Studio. Hur löser jag det här felet?

Om följande meddelande visas kanske du använder en äldre version av SDK: "Fel under distributionen för resursen YourResourceName' i resursgruppen YourResourceGroup: MissingRegistrationForLocation: Prenumerationen registreras inte för resurstypen "komponenter" på platsen "Centrala USA". Omregistrera sig för den här leverantören för att få tillgång till den här platsen." 

LÃ¶s problemet genom att uppgradera till den [senaste SDK](https://azure.microsoft.com/downloads/). Om du ser det här meddelandet och du har den senaste SDK skickar du en supportbegäran.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hur distribuerar jag ett ASP.NET program från Visual Studio till App Service?
<a id="deployasp"></a>

Självstudien [Skapa din första ASP.NET webbapp i Azure på fem minuter](app-service-web-get-started-dotnet.md) visar hur du distribuerar ett ASP.NET webbprogram till en webbapp i App Service med hjälp av Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Vilka olika typer av distributionsautentiseringsuppgifter finns?

App Service stöder två typer av autentiseringsuppgifter för lokal Git-distribution och FTP/S-distribution. Mer information om hur du konfigurerar distributionsautentiseringsuppgifter finns i [Konfigurera distributionsautentiseringsuppgifter för App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Vad är fil- eller katalogstrukturen i min App Service-webbapp?

Information om filstrukturen för apptjänstappen finns [i Filstruktur i Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hur löser jag "FTP Error 550 - Det finns inte tillräckligt med utrymme på disken" när jag försöker FTP mina filer?

Om det här meddelandet visas är det troligt att du stöter på en diskkvot i serviceplanen för webbappen. Du kan behöva skala upp till en högre tjänstnivå baserat på dina diskutrymmesbehov. Mer information om prisplaner och resursgränser finns i [Priser för App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hur konfigurerar jag kontinuerlig distribution för min App Service-webbapp?

Du kan konfigurera kontinuerlig distribution från flera resurser, inklusive Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox och andra Git-databaser. Dessa alternativ är tillgängliga i portalen. [Kontinuerlig distribution till App Service](deploy-continuous-deployment.md) är en användbar självstudiekurs som förklarar hur du konfigurerar kontinuerlig distribution.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hur felsöker jag problem med kontinuerlig distribution från GitHub och Bitbucket?

Mer information om hur du undersöker problem med kontinuerlig distribution från GitHub eller Bitbucket finns [i Undersöka kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Jag kan inte FTP till min webbplats och publicera min kod. Hur löser jag problemet?

Så här löser du FTP-problem:

1. Kontrollera att du anger rätt värdnamn och autentiseringsuppgifter. Detaljerad information om olika typer av autentiseringsuppgifter och hur du använder dem finns i [Distributionsautentiseringsuppgifter](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Kontrollera att FTP-portarna inte blockeras av en brandvägg. Portarna bör ha följande inställningar:
    * Anslutningsport för FTP-kontroll: 21
    * FTP-dataanslutningsport: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hur publicerar jag min kod i App Service?

Azure Quickstart är utformad för att hjälpa dig att distribuera din app med hjälp av distributionsstacken och valfri metod. Om du vill använda snabbstarten går du till apptjänsten i Azure-portalen under **Distribution**och väljer **Snabbstart**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Varför startar min app ibland om efter distribution till App Service?

Mer information om under vilka omständigheter en programdistribution kan leda till en omstart finns i [Problem med distribution jämfört med körning](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Som beskrivs i artikeln distribuerar App Service filer till mappen wwwroot. Den startar aldrig om din app direkt.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hur integrerar jag Azure DevOps-kod med App Service?

Du har två alternativ för att använda kontinuerlig distribution med Azure DevOps:

*   Använd ett Git-projekt. Anslut via App Service med hjälp av Distributionscenter.
*   Använd ett TFVC-projekt (Team Foundation Version Control). Distribuera med hjälp av byggagenten för App Service.

Kontinuerlig koddistribution för båda dessa alternativ beror på befintliga utvecklararbetsflöden och incheckningsprocedurer. Mer information finns i dessa artiklar: 

*   [Implementera kontinuerlig distribution av din app på en Azure-webbplats](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Konfigurera en Azure DevOps-organisation så att den kan distribueras till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hur använder jag FTP eller FTPS för att distribuera min app till App Service?

Information om hur du använder FTP eller FTPS för att distribuera webbappen till App Service finns i [Distribuera din app till App Service med FTP/S](deploy-ftp.md).
