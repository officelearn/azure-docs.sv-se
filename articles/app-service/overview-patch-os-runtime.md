---
title: OS och runtime patching kadens
description: Lär dig hur Azure App Service uppdaterar operativsystemet och runtimes, vilka körningar och korrigeringsnivå dina appar har och hur du kan få uppdateringsmeddelanden.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 597964914f4022899ab027b735ec6932105497b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273625"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Os- och körningskorrigering i Azure App Service

Den här artikeln visar hur du får viss versionsinformation om operativsystemet eller programvaran i [App Service](overview.md). 

App-tjänsten är en plattform-som-en-tjänst, vilket innebär att OS och programstacken hanteras åt dig av Azure. du bara hantera ditt program och dess data. Mer kontroll över operativsystemet och programstacken är tillgänglig i [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/). Med detta i åtanke är det ändå bra för dig som apptjänstanvändare att veta mer information, till exempel:

-   Hur och när tillämpas OS-uppdateringar?
-   Hur korrigeras App Service mot betydande sårbarheter (t.ex. nolldag)?
-   Vilka operativsystem- och körningsversioner kör dina appar?

Av säkerhetsskäl publiceras inte vissa säkerhetsinformationsdetaljer. Artikeln syftar dock till att lindra problem genom att maximera transparensen i processen och hur du kan hålla dig uppdaterad om säkerhetsrelaterade meddelanden eller körningsuppdateringar.

## <a name="how-and-when-are-os-updates-applied"></a>Hur och när tillämpas OS-uppdateringar?

Azure hanterar OS-korrigering på två nivåer, de fysiska servrarna och gästdatorer (VIRTUELLA datorer) som kör App Service-resurserna. Båda uppdateras varje månad, vilket anpassar sig till den månatliga [Patch tisdag](https://technet.microsoft.com/security/bulletins.aspx) schema. Dessa uppdateringar tillämpas automatiskt, på ett sätt som garanterar sla-avtalet med hög tillgänglighet för Azure-tjänster. 

Detaljerad information om hur uppdateringar tillämpas finns i [Demystifiera magin bakom App Service OS-uppdateringar](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hur hanterar Azure betydande sårbarheter?

När allvarliga sårbarheter kräver omedelbar korrigering, till exempel [nolldagssårbarheter,](https://wikipedia.org/wiki/Zero-day_(computing))hanteras uppdateringar med hög prioritet från fall till fall.

Håll dig uppdaterad med kritiska säkerhetsmeddelanden i Azure genom att besöka [Azure Security Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>När uppdateras, läggs eller är inaktuella språkkörningar som stöds?

Nya stabila versioner av språkkörningar som stöds (huvud-, moll- eller korrigeringsfiler) läggs regelbundet till i App Service-instanser. Vissa uppdateringar skriver över den befintliga installationen, medan andra installeras sida vid sida med befintliga versioner. En överskrivningsinstallation innebär att appen körs automatiskt på den uppdaterade körningen. En sida vid sida-installation innebär att du manuellt måste migrera appen för att kunna dra nytta av en ny körningsversion. Mer information finns i ett av underavsnitten.

Runtime uppdateringar och utfasningar tillkännages här:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informationen gäller för språkkörningar som är inbyggda i en App Service-app. En anpassad körning som du laddar upp till App Service, till exempel, förblir oförändrad om du inte uppgraderar den manuellt.
>
>

### <a name="new-patch-updates"></a>Nya korrigeringsuppdateringar

Patch uppdateringar till .NET, PHP, Java SDK, eller Tomcat / Brygga version tillämpas automatiskt genom att skriva över den befintliga installationen med den nya versionen. Node.js patch uppdateringar installeras sida vid sida med befintliga versioner (liknande större och delversioner i nästa avsnitt). Nya Python patch versioner kan installeras manuellt via [platstillägg](https://azure.microsoft.com/blog/azure-web-sites-extensions/), sida vid sida med den inbyggda Python installationer.

### <a name="new-major-and-minor-versions"></a>Nya huvud- och delversioner

När en ny huvud- eller delversion läggs till installeras den sida vid sida med de befintliga versionerna. Du kan uppgradera appen manuellt till den nya versionen. Om du har konfigurerat körningsversionen i `web.config` en `package.json`konfigurationsfil (till exempel och ) måste du uppgradera med samma metod. Om du använde en apptjänstinställning för att konfigurera din körningsversion kan du ändra den i [Azure-portalen](https://portal.azure.com) eller genom att köra ett [Azure CLI-kommando](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) i [Cloud Shell](../cloud-shell/overview.md), som visas i följande exempel:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Inaktuella versioner  

När en äldre version är inaktuell meddelas borttagningsdatumet så att du kan planera uppgraderingen av körningsversionen i enlighet med detta. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hur kan jag fråga OS och körtidsuppdateringsstatus på mina instanser?  

Medan viktig OS-information är låst från åtkomst (se [operativsystemfunktioner på Azure App Service),](operating-system-functionality.md)kan [Kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) du fråga din App Service-instans om OS-versionen och körningsversioner. 

I följande tabell visas hur du använder versioner av Windows och de språkkörningar som kör dina appar:

| Information | Var hittar man den | 
|-|-|
| Windows-version | Se `https://<appname>.scm.azurewebsites.net/Env.cshtml` (under Systeminfo) |
| .NET-version | Kör `https://<appname>.scm.azurewebsites.net/DebugConsole`följande kommando i kommandotolken: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET Core-version | Kör `https://<appname>.scm.azurewebsites.net/DebugConsole`följande kommando i kommandotolken: <br> `dotnet --version` |
| PHP-version | Kör `https://<appname>.scm.azurewebsites.net/DebugConsole`följande kommando i kommandotolken: <br> `php --version` |
| Standardversion av nod.js | Kör följande kommando i [Cloud Shell:](../cloud-shell/overview.md) <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-version | Kör `https://<appname>.scm.azurewebsites.net/DebugConsole`följande kommando i kommandotolken: <br> `python --version` |  
| Java-version | Kör `https://<appname>.scm.azurewebsites.net/DebugConsole`följande kommando i kommandotolken: <br> `java -version` |  

> [!NOTE]  
> Åtkomsten till `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`registerplatsen , där information om [KB-korrigeringsfiler](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) lagras, är låst.
>
>

## <a name="more-resources"></a>Fler resurser

[Säkerhetscenter: Säkerhet](https://www.microsoft.com/en-us/trustcenter/security)  
[64-bitars ASP.NET Core på Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
