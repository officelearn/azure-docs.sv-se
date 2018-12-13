---
title: OS- och runtime korrigeringar takt – Azure App Service | Microsoft Docs
description: Beskriver hur Azure App Service-uppdateringar Operativsystemet och körningar och hur du skaffar uppdaterar meddelanden.
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
ms.custom: seodec18
ms.openlocfilehash: a0fdab06606a88fafa803a9a112c05452578cfeb
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252872"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>OS- och runtime korrigeringar i Azure App Service

Den här artikeln visar hur du hämtar vissa versionsinformation om Operativsystemet eller programvara i [Apptjänst](app-service-web-overview.md). 

App Service är en Platform-as-a-Service, vilket innebär att Operativsystemet och programmet stack hanteras åt dig av Azure; du bara hantera ditt program och dess data. Mer kontroll över Operativsystemet och programmet stack är tillgängligt i [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/). Med det i åtanke är det dock användbara för dig som en App Service-användare att veta mer information, till exempel:

-   Hur och när OS-uppdateringar tillämpas?
-   Hur är App Service uppdaterad mot betydande säkerhetsrisker (till exempel noll dagar)?
-   Vilka versioner av operativsystem och runtime kör dina appar?

Av säkerhetsskäl publiceras inte vissa ärendets natur säkerhetsinformation. Artikeln är dock att lösa problem genom att maximera transparens i processen och hur du kan hålla dig uppdaterad om säkerhetsrelaterade meddelanden eller runtime-uppdateringar.

## <a name="how-and-when-are-os-updates-applied"></a>Hur och när OS-uppdateringar tillämpas?

Azure hanterar operativsystemsuppdateringar på två nivåer, fysiska servrar och de virtuella gästdatorer (virtuella datorer) som kör App Service-resurser. Båda uppdateras varje månad, som stämmer överens med månatliga [korrigeringstisdag](https://technet.microsoft.com/security/bulletins.aspx) schema. De här uppdateringarna tillämpas automatiskt på ett sätt som garanterar SLA för Azure-tjänster med hög tillgänglighet. 

Detaljerad information om hur uppdateringar tillämpas finns [Avmystifiera magic bakom App Service-OS-uppdateringar](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hur Azure behandlar betydande säkerhetsproblem?

När allvarliga problem kräver omedelbar uppdatering, till exempel [nolldagsattacker sårbarheter](https://wikipedia.org/wiki/Zero-day_(computing)), viktiga uppdateringar hanteras på en-fall till fall.

Håll dig uppdaterad med kritiska säkerhetsmeddelanden i Azure genom att besöka [Azure Security-bloggen](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>När stöds språkkörningar uppdateras, läggs till, eller inaktuella?

Nya stabil versioner av stöds språkkörningar (större, mindre eller korrigera) läggs regelbundet till i App Service-instanser. Vissa uppdateringar skriva över den befintliga installationen, medan andra installeras sida vid sida med befintliga versioner. En installation av överskrivning innebär att din app automatiskt körs på en uppdaterad. En sida-vid-sida-installation innebär manuellt måste du migrera din app för att dra nytta av en ny runtime-version. Mer information finns i någon av underavsnitt.

Runtime-uppdateringar och utfasningar tillkännages här:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informationen här gäller för språkkörningar som är inbyggda i en App Service-app. En anpassad runtime som du överför till App Service, till exempel ändras inte om du uppgraderar den manuellt.
>
>

### <a name="new-patch-updates"></a>Nya patchuppdateringar

Patch-uppdateringar av .NET, PHP, Java SDK eller Tomcat/Jetty version tillämpas automatiskt genom att skriva över den befintliga installationen med den nya versionen. Node.js patchuppdateringar installeras sida vid sida med de befintliga versionerna (liknar högre och den lägre versionen i nästa avsnitt). Nya Python versioner kan installeras manuellt via [platstillägg](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), sida vid sida med de inbyggda Python-installationerna.

### <a name="new-major-and-minor-versions"></a>Nya versioner av högre och lägre

När en ny högre eller lägre version läggs till, installeras den sida vid sida med de befintliga versionerna. Du kan manuellt uppgradera din app till den nya versionen. Om du har konfigurerat runtime-versionen i en konfigurationsfil (till exempel `web.config` och `package.json`), måste du uppgradera med samma metod. Om du har använt en App Service till Konfigurera runtime-versionen kan du ändra den i den [Azure-portalen](https://portal.azure.com) eller genom att köra en [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) i den [Cloud Shell](../cloud-shell/overview.md), som i följande exempel visas:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Föråldrad versioner  

När en äldre version är inaktuell har i Borttagningsdatum meddelats så att du kan planera din runtime-versionsuppgraderingen på lämpligt sätt. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hur kan jag fråga uppdateringsstatus för operativsystem och runtime på min instanser?  

När kritiska OS-information är låst nedåt från åtkomst (se [funktioner för operativsystemet på Azure App Service](web-sites-available-operating-system-functionality.md)), [Kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) kan du fråga din App Service-instans om Operativsystemet versionen och runtime-versioner. 

I följande tabell visas de versioner av Windows och språk-runtime som kör dina appar så här:

| Information | Var du hittar den | 
|-|-|
| Windows-version | Se `https://<appname>.scm.azurewebsites.net/Env.cshtml` (under systeminformation) |
| .NET-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör du följande kommando i Kommandotolken: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET core-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör du följande kommando i Kommandotolken: <br> `dotnet --version` |
| PHP-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör du följande kommando i Kommandotolken: <br> `php --version` |
| Node.js-standardversion | I den [Cloud Shell](../cloud-shell/overview.md), kör du följande kommando: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-version | Vid `https://<appname>.scm.azurewebsites.net/DebugConsole`, kör du följande kommando i Kommandotolken: <br> `python --version` |  

> [!NOTE]  
> Åtkomst till registerplatsen `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, där information om [”KB” korrigeringar](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) lagras, är låsta.
>
>

## <a name="more-resources"></a>Fler resurser

[Säkerhetscenter: Säkerhet](https://www.microsoft.com/en-us/trustcenter/security)  
[64-bitars ASP.NET Core på Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
