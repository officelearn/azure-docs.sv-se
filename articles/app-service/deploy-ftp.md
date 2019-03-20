---
title: Distribuera innehåll med FTP/S - Azure App Service | Microsoft Docs
description: Lär dig mer om att distribuera din app till Azure App Service med FTP eller FTPS.
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
ms.date: 11/30/2018
ms.author: cephalin;dariac
ms.custom: seodec18
ms.openlocfilehash: db8445ec2b3dd8bdefa661d7f186e720c6fada09
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858885"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera din webbapp, mobilappserverdel eller API-app till [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

FTP/S-slutpunkten för din app är redan aktiv. Det krävs ingen konfiguration för FTP/S ska distribueras.

## <a name="open-ftp-dashboard"></a>Öppna FTP-instrumentpanelen

I den [Azure-portalen](https://portal.azure.com), öppna appens [resurssida](../azure-resource-manager/manage-resources-portal.md#manage-resources).

För att öppna FTP-instrumentpanelen, klickar du på **Deployment Center** > **FTP** > **instrumentpanelen**.

![Öppna FTP-instrumentpanelen](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Anslutningsinformation för FTP

I FTP-instrumentpanelen klickar du på **kopia** att kopiera FTPS slutpunkt och autentiseringsuppgifter.

![Kopiera FTP-information](./media/app-service-deploy-ftp/ftp-dashboard.png)

Vi rekommenderar att du använder **App autentiseringsuppgifter** att distribuera till din app eftersom den är unik för varje app. Men om du klickar på **användarautentiseringsuppgifter**, du kan ange användarnivå autentiseringsuppgifter som du kan använda för FTP/S logga in på alla App Service-appar i din prenumeration.

> [!NOTE]
> Autentisering till en FTP/FTPS-slutpunkt som använder användarnivå autentiseringsuppgifter requirers ett användarnamn i formatet: 
>
>`<app-name>\<user-name>`
>
> Eftersom användarnivå autentiseringsuppgifter är länkade till användaren och inte en specifik resurs, måste användarnamnet vara i formatet att dirigera åtgärden logga in till rätt app-slutpunkten.
>

## <a name="deploy-files-to-azure"></a>Distribuera filer till Azure

1. Från FTP-klienten (till exempel [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/), eller [WinSCP](https://winscp.net/index.php)), Använd den information du samlade in för att ansluta till din app.
2. Kopiera filerna med respektive katalogstruktur till den [ **/site/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/site/wwwroot/App_Data/jobb/** katalogen för WebJobs).
3. Gå till appens URL för att verifiera appen körs korrekt. 

> [!NOTE] 
> Till skillnad från [Git-baserade distributioner](deploy-local-git.md), FTP-distribution stöder inte följande distribution automatiseringar: 
>
> - beroende återställer (till exempel NuGet, NPM, PIP och Composer automatiseringar)
> - .NET-binärfiler för
> - generering av web.config (här är en [Node.js-exempel](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Skapa dessa nödvändiga filer manuellt på den lokala datorn och sedan distribuera dem tillsammans med din app.
>

## <a name="enforce-ftps"></a>Enforce FTPS

För ökad säkerhet bör du tillåta FTP över SSL endast. Du kan också inaktivera både FTP och FTPS om du inte använder FTP-distribution.

I din app resurssida i [Azure-portalen](https://portal.azure.com)väljer **appinställningar** i det vänstra navigeringsfönstret.

Om du vill inaktivera okrypterade FTP, Välj **FTPS endast**. För att inaktivera både FTP och FTPS helt och hållet, Välj **inaktivera**. När det är klart klickar du på **Spara**. Om du använder **FTPS endast** du måste använda TLS 1.2 eller senare genom att navigera till den **SSL-inställningar** bladet för webbappen. TLS 1.0 och 1.1 stöds inte med **FTPS endast**.

![Inaktivera FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatisera med skript

För FTP-distribution med [Azure CLI](/cli/azure), se [skapa en webbapp och distribuera filer med FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

För FTP-distribution med [Azure PowerShell](/cli/azure), se [ladda upp filer till en webbapp med FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Felsöka FTP-distribution

- [Hur felsöker jag FTP-distribution?](#how-can-i-troubleshoot-ftp-deployment)
- [Jag kan inte FTP och publicera min kod. Hur kan jag för att lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur felsöker jag FTP-distribution?

Det första steget för felsökning av FTP-distribution är att isolera ett distributionsproblem från en runtime programproblem.

Ett distributionsproblem ger vanligtvis inga filer eller fel filer som har distribuerats till din app. Du kan felsöka genom att undersöka din FTP-distribution eller välja en annan distributionssökväg (till exempel källkontroll).

Ett problem med programmet runtime uppstår vanligen i rätt uppsättning filer som distribueras till din app men felaktigt appbeteendet. Du kan felsöka genom att fokusera på koden beteende vid körning och undersöka specifika felet sökvägar.

Information om ett problem med distributionen eller runtime finns [distributionen jämfört med körningsproblem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Jag kan inte FTP och publicera min kod. Hur kan jag för att lösa problemet?
Kontrollera att du har angett rätt värdnamn och [autentiseringsuppgifter](#open-ftp-dashboard). Kontrollera också att följande FTP-portar på din dator inte blockeras av en brandvägg:

- Anslutningsport för FTP-kontroll: 21
- Anslutningsport för FTP-data: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hur kan jag ansluta till FTP i Azure App Service via passivt läge?
Azure App Service stöder anslutning via både aktiva och passivt läge. Passivt läge är att föredra eftersom din distribution. virtuella datorer är vanligtvis bakom en brandvägg (i operativsystemet eller som en del av ett hem- eller företagsnätverk). Se en [exempel WinSCP-dokumentationen](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Nästa steg

Mer avancerade distributionsscenarier för, försök [distribuera till Azure med Git](deploy-local-git.md). Git-baserade distributionen till Azure gör det möjligt för versionskontroll, paketåterställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Autentiseringsuppgifter för distribution av Azure App Service](deploy-configure-credentials.md)
