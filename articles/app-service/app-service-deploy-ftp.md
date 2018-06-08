---
title: Distribuera din app till Azure App Service med FTP/S | Microsoft Docs
description: Lär dig mer om att distribuera din app till Azure App Service med hjälp av FTP- eller FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariac
ms.openlocfilehash: 7e05e06a5abd02dd67f58a8e01bb246e318f51de
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850244"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera ditt webbprogram, mobilappsserverdel eller API-appen [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

FTP-/ S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

## <a name="open-ftp-dashboard"></a>Öppna FTP-instrumentpanelen

I den [Azure-portalen](https://portal.azure.com), öppna appens [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources).

Klicka för att öppna instrumentpanelen FTP **kontinuerlig leverans (förhandsgranskning)** > **FTP** > **instrumentpanelen**.

![Öppna FTP-instrumentpanelen](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Hämta information om FTP-anslutning

I FTP-instrumentpanelen, klickar du på **kopiera** att kopiera FTPS slutpunkt och app autentiseringsuppgifter.

![Kopiera FTP-information](./media/app-service-deploy-ftp/ftp-dashboard.png)

Det rekommenderas att du använder **App autentiseringsuppgifter** att distribuera din app eftersom det är unikt för varje app. Men om du klickar på **användarautentiseringsuppgifter**, du kan ange användarnivå autentiseringsuppgifter som du kan använda för FTP-/ S inloggning i alla Apptjänst-appar i din prenumeration.

## <a name="deploy-files-to-azure"></a>Distribuera filer till Azure

1. Från FTP-klient (till exempel [Visual Studio](https://www.visualstudio.com/vs/community/) eller [FileZilla](https://filezilla-project.org/download.php?type=client)), använder informationen som samlats in för att ansluta till din app.
3. Kopiera filerna och deras respektive katalogstrukturen till den [ **/platsen/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/platsen/wwwroot/App_Data/jobb/** katalogen för WebJobs).
4. Gå till din app-URL att verifiera appen körs korrekt. 

> [!NOTE] 
> Till skillnad från [Git-baserade distributioner](app-service-deploy-local-git.md), FTP-distributionen inte stöder följande distribution automatiseringar: 
>
> - beroende återställer (till exempel NuGet och NPM, PIP. Composer automatiseringar)
> - .NET-binärfiler för
> - generering av web.config (det här är en [Node.js-exempel](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Skapa dessa nödvändiga filer manuellt på den lokala datorn och sedan distribuera dem tillsammans med din app.
>
>

## <a name="enforce-ftps"></a>Framtvinga FTPS

För ökad säkerhet ska du tillåta FTP över SSL endast. Du kan också inaktivera både FTP och FTPS om du inte använder FTP-distribution.

I din app resurssida i [Azure-portalen](https://portal.azure.com)väljer **appinställningar** i det vänstra navigeringsfönstret.

Om du vill inaktivera okrypterad FTP, Välj **FTPS endast**. För att inaktivera både FTP och FTPS helt väljer **inaktivera**. När du är klar klickar du på **spara**.

![Inaktivera FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatisera med skript

För FTP-distribution med [Azure CLI](/cli/azure), se [skapa en webbapp och distribuera filer med FTP (Azure CLI)](./scripts/app-service-cli-deploy-ftp.md).

För FTP-distribution med [Azure PowerShell](/cli/azure), se [ladda upp filer till ett webbprogram med hjälp av FTP (PowerShell)](./scripts/app-service-powershell-deploy-ftp.md).

## <a name="troubleshoot-ftp-deployment"></a>Felsökning av FTP-distribution

- [Hur felsöker FTP-distributionen?](#how-can-i-troubleshoot-ftp-deployment)
- [Jag kan inte FTP och publicera min kod. Hur kan jag för att lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur felsöker FTP-distributionen?

Det första steget för felsökning av FTP-distribution är att isolera ett distributionsproblem med från en runtime problem med programmet.

Ett distributionsproblem med innebär vanligtvis inga filer eller felaktiga filer som distribueras till din app. Du kan felsöka genom att undersöka din FTP-distribution eller välja en annan distributionssökväg (till exempel källkontrollen).

Ett problem med programmet runtime vanligtvis resulterar i rätt uppsättning filer som distribueras till din app men felaktigt Apps beteende. Du kan felsöka genom att fokusera på koden beteende under körning och undersöka specifika felet sökvägar.

Information om ett problem med distributionen eller runtime finns [distribution kontra runtime problem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Jag kan inte FTP och publicera min kod. Hur kan jag för att lösa problemet?
Kontrollera att du har angett rätt värdnamnet och [autentiseringsuppgifter](#step-1--set-deployment-credentials). Kontrollera också att följande FTP-portar på din dator inte blockeras av en brandvägg:

- Anslutningsport för FTP-kontroll: 21
- FTP-dataporten för anslutning: 989 10001 10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hur kan jag ansluta till FTP i Azure App Service via passivt läge?
Azure Apptjänst stöder anslutning via både aktivt och passivt läge. Passivt läge är att föredra eftersom dina datorer för distributionen är vanligtvis bakom en brandvägg (i operativsystemet eller som en del av ett hem- eller företagsnätverk). Se en [exempel i dokumentationen för WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Nästa steg

Mer avancerade scenarier för distribution, försök [distribution till Azure med Git](app-service-deploy-local-git.md). Git-baserad distribution till Azure kan versionskontroll, paketet återställning, MSBuild och mycket mer.

## <a name="more-resources"></a>Fler resurser

* [Autentiseringsuppgifter för distribution av Azure App Service](app-service-deploy-ftp.md)
