---
title: Distribuera innehåll med FTP/S
description: Lär dig hur du distribuerar din app till Azure App Service med FTP eller FTPS. Förbättra webbplatsens säkerhet genom att inaktivera okrypterad FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fcc7c5b8fa182cace6e3dae0b1cae4cd41c5dcb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532603"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera din webbapp, mobilappsservering eller API-app till [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

FTP/S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

## <a name="open-ftp-dashboard"></a>Öppna FTP-instrumentpanelen

1. Sök efter och välj **App Services**i [Azure-portalen](https://portal.azure.com).

    ![Sök efter apptjänster.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Välj den webbapp som du vill distribuera.

    ![Välj din app.](media/app-service-continuous-deployment/select-your-app.png)

3. Välj**FTP-instrumentpanel** > **för** **Distributionscenter** > .

    ![Öppna FTP-instrumentpanelen](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Hämta information om FTP-anslutning

I FTP-instrumentpanelen väljer du **Kopiera** för att kopiera FTPS-slutpunkten och appautentiseringsuppgifterna.

![Kopiera FTP-information](./media/app-service-deploy-ftp/ftp-dashboard.png)

Vi rekommenderar att du använder **appautentiseringsuppgifter** för att distribuera till din app eftersom den är unik för varje app. Om du klickar på **Användarautentiseringsuppgifter**kan du dock ange autentiseringsuppgifter på användarnivå som du kan använda för FTP/S-inloggning till alla App Service-appar i prenumerationen.

> [!NOTE]
> Autentisering till en FTP/FTPS-slutpunkt med autentiseringsuppgifter på användarnivå kräver ett användarnamn i följande format: 
>
>`<app-name>\<user-name>`
>
> Eftersom autentiseringsuppgifter på användarnivå är länkade till användaren och inte en specifik resurs måste användarnamnet vara i det här formatet för att dirigera inloggningsåtgärden till rätt appslutpunkt.
>

## <a name="deploy-files-to-azure"></a>Distribuera filer till Azure

1. Från din FTP-klient (till exempel [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)eller [WinSCP](https://winscp.net/index.php)) använder du den anslutningsinformation som du har samlat in för att ansluta till din app.
2. Kopiera dina filer och deras respektive katalogstruktur till [katalogen **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller katalogen **/site/wwwroot/App_Data/Jobs/** för WebJobs).
3. Bläddra till appens webbadress för att kontrollera att appen fungerar som den ska. 

> [!NOTE] 
> Till skillnad från [Git-baserade distributioner](deploy-local-git.md)stöder FTP-distributionen inte följande automatiseringar av distributioner: 
>
> - beroendeåterställning (till exempel NuGet, NPM, PIP och Composer automations)
> - sammanställning av .NET-binärfiler
> - generering av web.config (här är ett [node.js exempel)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Generera dessa nödvändiga filer manuellt på din lokala dator och distribuera dem sedan tillsammans med din app.
>

## <a name="enforce-ftps"></a>Framtvinga FTPS

För ökad säkerhet bör du tillåta FTP endast över TLS/SSL. Du kan också inaktivera både FTP och FTPS om du inte använder FTP-distribution.

På appens resurssida i [Azure Portal](https://portal.azure.com)väljer du**Konfigurationsinställningar** **Configuration** > från vänster navigering.

Om du vill inaktivera okrypterad FTP väljer du **FTPS Only** i **FTP-tillstånd**. Om du vill inaktivera både FTP och FTPS helt väljer du **Inaktiverat**. När det är klart klickar du på **Spara**. Om du använder **FTPS Only**måste du framtvinga TLS 1.2 eller högre genom att navigera till **TLS/SSL-inställningsbladet** i din webbapp. TLS 1.0 och 1.1 stöds inte med **ENDAST FTPS**.

![Inaktivera FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatisera med skript

För FTP-distribution med [Azure CLI](/cli/azure)finns i [Skapa en webbapp och distribuera filer med FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

För FTP-distribution med [Azure PowerShell](/cli/azure)finns [ladda upp filer till en webbapp med FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Felsöka FTP-distribution

- [Hur felsöker jag FTP-distribution?](#how-can-i-troubleshoot-ftp-deployment)
- [Jag kan inte FTP och publicera min kod. Hur kan jag lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur felsöker jag FTP-distribution?

Det första steget för felsökning av FTP-distribution är att isolera ett distributionsproblem från ett körningsprogramproblem.

Ett distributionsproblem resulterar vanligtvis i inga filer eller felaktiga filer som distribueras till din app. Du kan felsöka genom att undersöka din FTP-distribution eller välja en alternativ distributionssökväg (till exempel källkontroll).

Ett körningsprogramproblem resulterar vanligtvis i rätt uppsättning filer som distribueras till din app men felaktigt appbeteende. Du kan felsöka genom att fokusera på kodbeteende vid körning och undersöka specifika felsökvägar.

Du kan fastställa ett distributions- eller körningsproblem i [Distribution kontra körningsproblem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Jag kan inte använda FTP och publicera min kod. Hur kan jag lösa problemet?
Kontrollera att du har angett rätt värdnamn och [autentiseringsuppgifter](#open-ftp-dashboard). Kontrollera också att följande FTP-portar på datorn inte blockeras av en brandvägg:

- Anslutningsport för FTP-kontroll: 21
- FTP-dataanslutningsport: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hur kan jag ansluta till FTP i Azure App Service via passivt läge?
Azure App Service stöder anslutning via både aktivt och passivt läge. Passivt läge är att föredra eftersom dina distributionsdatorer vanligtvis är bakom en brandvägg (i operativsystemet eller som en del av ett hem- eller affärsnätverk). Se ett [exempel från WinSCP-dokumentationen](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Nästa steg

Mer avancerade distributionsscenarier kan du prova [att distribuera till Azure med Git](deploy-local-git.md). Git-baserad distribution till Azure möjliggör versionskontroll, paketåterställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Autentiseringsuppgifter för Distribution av Azure App-tjänst](deploy-configure-credentials.md)
