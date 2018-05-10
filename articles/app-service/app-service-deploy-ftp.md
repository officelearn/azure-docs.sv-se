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
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera ditt webbprogram, mobilappsserverdel eller API-appen [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

FTP-/ S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Steg 1: Ange autentiseringsuppgifter för distribution

För att komma åt FTP-servern för din app måste du först autentiseringsuppgifter för distribution. 

Om du vill ange eller återställa dina autentiseringsuppgifter för distribution, se [autentiseringsuppgifter för distribution av Azure App Service](app-service-deployment-credentials.md). Den här kursen visar hur du använder användarnivå autentiseringsuppgifter.

## <a name="step-2-get-ftp-connection-information"></a>Steg 2: Hämta information om FTP-anslutning

1. I den [Azure-portalen](https://portal.azure.com), öppna appens [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Välj **översikt** i det vänstra navigeringsfönstret anteckna värdena för **FTP/distribution användaren**, **värdnamn för FTP-**, och **FTPS värdnamn**. 

    ![Information om FTP-anslutning](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Att ge rätt kontext för FTP-servern i **FTP/distribution användaren** värde visas i Azure portal innehåller namnet på appen.
    > Du kan hitta samma information när du väljer **egenskaper** i det vänstra navigeringsfönstret. 
    >
    > Dessutom visas aldrig lösenord för distribution. Om du glömmer lösenordet distribution kan gå tillbaka till [steg 1](#step1) och återställa lösenordet distribution.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Steg 3: Distribuera filer till Azure

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

## <a name="troubleshoot-ftp-deployment"></a>Felsökning av FTP-distribution

- [Hur felsöker FTP-distributionen?](#how-can-i-troubleshoot-ftp-deployment)
- [Jag kan inte FTP och publicera min kod. Hur kan jag för att lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur felsöker FTP-distributionen?

Det första steget för felsökning av FTP-distribution är att isolera ett distributionsproblem med från en runtime problem med programmet.

Ett distributionsproblem med innebär vanligtvis inga filer eller felaktiga filer som distribueras till din app. Det kan åtgärdas genom att undersöka din FTP-distribution eller välja en annan distributionssökväg (till exempel källkontrollen).

Ett problem med programmet runtime vanligtvis resulterar i rätt uppsättning filer som distribueras till din app men felaktigt Apps beteende. Det kan åtgärdas genom att fokusera på koden beteende under körning och undersöka specifika felet sökvägar.

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
