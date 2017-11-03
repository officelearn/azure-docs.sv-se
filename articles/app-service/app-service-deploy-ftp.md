---
title: Distribuera din app till Azure App Service med FTP/S | Microsoft Docs
description: "Lär dig mer om att distribuera din app till Azure App Service med hjälp av FTP- eller FTPS."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.openlocfilehash: e3ac2f2156719ad975049b0c2b4cbca81d88e779
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera ditt webbprogram, mobilappsserverdel eller API-appen [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

FTP-/ S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

> [!IMPORTANT]
> Vi vidtar kontinuerligt åtgärder för att förbättra säkerheten för Microsoft Azure-plattformen. Som en del av den här pågående arbete planerad uppgradering av webbprogram för Tyskland Central och Tyskland nordöst regioner. Web Apps inaktiverar under denna användning av klartext FTP-protokollet för distributioner. Vår rekommendation för våra kunder är att växla till FTPS för distributioner. Vi förväntar sig inte några avbrott i tjänsten under den här uppgraderingen som är planerat till 9/5. Vi uppskattar du stöd för i den här aktiviteten.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Steg 1: Ange autentiseringsuppgifter för distribution

För att komma åt FTP-servern för din app måste du först autentiseringsuppgifter för distribution. 

Om du vill ange eller återställa dina autentiseringsuppgifter för distribution, se [autentiseringsuppgifter för distribution av Azure App Service](app-service-deployment-credentials.md). Den här kursen visar hur du använder användarnivå autentiseringsuppgifter.

## <a name="step-2-get-ftp-connection-information"></a>Steg 2: Hämta information om FTP-anslutning

1. I den [Azure-portalen](https://portal.azure.com), öppna appens [resursbladet](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Välj **översikt** i den vänstra menyn anteckna värdena för **FTP/distribution användaren**, **värdnamn för FTP-**, och **FTPS värdnamn**. 

    ![Information om FTP-anslutning](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Den **FTP/distribution användaren** användaren värdet som visas i Azure Portal inklusive namnet på appen för att ge rätt kontext för FTP-servern.
    > Du kan hitta samma information när du väljer **egenskaper** i den vänstra menyn. 
    >
    > Dessutom visas aldrig lösenord för distribution. Om du glömmer lösenordet distribution kan gå tillbaka till [steg 1](#step1) och återställa lösenordet distribution.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Steg 3: Distribuera filer till Azure

1. Från FTP-klient ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client), osv.), använder informationen som samlats in för att ansluta till din app.
3. Kopiera filerna och deras respektive katalogstrukturen till den [ **/platsen/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/platsen/wwwroot/App_Data/jobb/** katalogen för WebJobs).
4. Gå till din app-URL att verifiera appen körs korrekt. 

> [!NOTE] 
> Till skillnad från [Git-baserade distributioner](app-service-deploy-local-git.md), FTP-distributionen inte stöder följande distribution automatiseringar: 
>
> - beroende återställning (till exempel NuGet och NPM, PIP. Composer automatiseringar)
> - .NET-binärfiler för
> - generering av web.config (det här är en [Node.js-exempel](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Du måste återställa, skapa, skapa dessa nödvändiga filer manuellt på den lokala datorn och distribuera dem tillsammans med din app.
>
>

## <a name="next-steps"></a>Nästa steg

Mer avancerade scenarier för distribution, försök [distribution till Azure med Git](app-service-deploy-local-git.md). Git-baserad distribution till Azure kan versionskontroll, paketet återställning, MSBuild och mycket mer.

## <a name="more-resources"></a>Fler resurser

* [Autentiseringsuppgifter för distribution av Azure App Service](app-service-deploy-ftp.md)
