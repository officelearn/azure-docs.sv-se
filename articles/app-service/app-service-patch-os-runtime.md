---
title: OS- och runtime korrigering i Azure App Service | Microsoft Docs
description: Beskriver hur Azure App Service uppdateringarna OS-körningar, och hur du får uppdatera meddelanden.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.openlocfilehash: 0626b958a9b822569f4d3b6d27f3395bed853174
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030061"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>OS- och runtime korrigering i Azure App Service

Den här artikeln visar hur du hämtar vissa versionsinformation om Operativsystemet och programvara i [Apptjänst](app-service-web-overview.md). 

Apptjänst är en plattform som en-tjänst, vilket innebär att operativsystem och program stacken hanteras av Azure; du bara hantera ditt program och dess data. Mer kontroll över Operativsystemet och programmet stacken är tillgängliga i [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/). Med detta i åtanke är det dock användbara för dig som en App Service-användare har mer information som:

-   Hur och när OS-uppdateringar tillämpas?
-   Hur är Apptjänst korrigeras mot betydande säkerhetsproblem (till exempel noll-dag)?
-   Vilka versioner av operativsystem och runtime kör dina appar?

Av säkerhetsskäl publiceras inte vissa specifika säkerhetsinformation. Dock syftar artikeln till att minska problem med maximera transparens i processen och hur du kan hålla dig uppdaterad på säkerhetsrelaterade meddelanden eller runtime-uppdateringar.

## <a name="how-and-when-are-os-updates-applied"></a>Hur och när OS-uppdateringar tillämpas?

Azure hanterar OS-korrigering på två nivåer, fysiska servrar och virtuella gästdatorer (VM) som kör App Service-resurser. Både uppdateras varje månad, som justeras mot månatliga [korrigeringstisdag](https://technet.microsoft.com/security/bulletins.aspx) schema. Uppdateringarna tillämpas automatiskt på ett sätt som garanterar SLA för Azure-tjänster för hög tillgänglighet. 

Detaljerad information om hur uppdateringar tillämpas finns [Avmystifiering magin bakom App Service operativsystemuppdateringar](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hur Azure behandlar betydande säkerhetsproblem?

När allvarliga problem kräver omedelbar uppdatering, t.ex [noll-dagars säkerhetsrisker](https://wikipedia.org/wiki/Zero-day_(computing)), viktiga uppdateringar hanteras på grund av fall.

Håll dig uppdaterad med kritiska säkerhetsmeddelanden i Azure genom att besöka [Azure-Säkerhetsblogg](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>När språk som stöds körningar uppdateras, läggs till, eller föråldrad?

Nya stabil versioner av körningar språk som stöds (större, mindre eller korrigering) med jämna mellanrum läggs till i App Service-instanser. Vissa uppdateringar skriva över den befintliga installationen, medan andra är installerade sida vid sida med befintliga versioner. En installation av överskrivning innebär att din app automatiskt körs på den uppdaterade körningsmiljön. En sida-vid-sida-installation innebär manuellt måste du migrera din app för att dra nytta av en ny version av körningsmiljön. Mer information finns i något av underavsnitt.

Runtime uppdateringar och deprecations annonseras här:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informationen här gäller för språket körningar som är inbyggda i en Apptjänst-app. En anpassad runtime som du överför till App Service, till exempel ändras inte om du uppgraderar den manuellt.
>
>

### <a name="new-patch-updates"></a>Ny korrigering uppdateringar

Korrigering uppdateringar för .NET, PHP, Java SDK eller Tomcat/Jetty version tillämpas automatiskt genom att skriva över den befintliga installationen med den nya versionen. Node.js korrigering uppdateringar installeras bredvid de befintliga versionerna (liknar högre och lägre versioner i nästa avsnitt). Ny Python versioner kan installeras manuellt via [plats tillägg](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), sida vid sida med inbyggda Python-installationer.

### <a name="new-major-and-minor-versions"></a>Nya versioner av högre och lägre

När en ny högre eller lägre version läggs installeras den bredvid de befintliga versionerna. Du kan manuellt uppgradera din app till den nya versionen. Om du har konfigurerat versionen av körningsmiljön i en konfigurationsfil (som `web.config` och `package.json`), måste du uppgradera med samma metod. Om du använde en Apptjänst till Konfigurera runtime-versionen kan du ändra den i den [Azure-portalen](https://portal.azure.com) eller genom att köra en [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) i den [moln Shell](../cloud-shell/overview.md), som visas i följande exempel:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Föråldrad versioner  

När en äldre version är föråldrad har profilens Borttagningsdatum meddelats så att du kan planera runtime versionsuppgraderingen i enlighet med detta. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hur kan fråga OS och körning uppdateringsstatus på min instanser?  

När kritiska OS-information är låst ned från åtkomst (se [operativsystemet funktioner i Azure App Service](web-sites-available-operating-system-functionality.md)), [Kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) kan du fråga din App Service-instans om Operativsystemet versionen och runtime-versioner. 

I följande tabell visas hur de versioner av Windows och language runtime som kör dina appar:

| Information | Var den | 
|-|-|
| Windows-version | Se `https://<appname>.scm.azurewebsites.net/Env.cshtml` (under SYSTEMINFO) |
| .NET-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör följande kommando i Kommandotolken: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET core-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör följande kommando i Kommandotolken: <br> `dotnet --version` |
| PHP-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör följande kommando i Kommandotolken: <br> `php --version` |
| Node.js-standardversion | I den [moln Shell](../cloud-shell/overview.md), kör du följande kommando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör följande kommando i Kommandotolken: <br> `python --version` |  

> [!NOTE]  
> Åtkomst till registerplats `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, där information om [”KB” korrigeringsfiler]((https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins)) lagras, är låst.
>
>

## <a name="more-resources"></a>Fler resurser

[Säkerhetscenter: säkerhet](https://www.microsoft.com/en-us/trustcenter/security)  
[64-bitars ASP.NET Core på Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
