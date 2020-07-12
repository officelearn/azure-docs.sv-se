---
title: Felsök problem med Azure Arc for servers agent anslutning
description: Den här artikeln beskriver hur du felsöker och löser problem med den anslutna dator agent som uppstår med Azure Arc for Servers (för hands version) när du försöker ansluta till tjänsten.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262206"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Felsök problem med anslutning till anslutna dator agenter

Den här artikeln innehåller information om hur du felsöker och löser problem som kan uppstå vid försök att konfigurera Azure-bågen för servrar (för hands version) anslutna dator agenter för Windows eller Linux. Både interaktiva och storskaliga installations metoder när du konfigurerar anslutning till tjänsten ingår. Allmän information finns i [Översikt över båge för servrar](./overview.md).

## <a name="agent-verbose-log"></a>Utförlig logg för agent

Innan du följer fel söknings stegen som beskrivs längre fram i den här artikeln, är den minsta information som du behöver vara utförlig logg. Det innehåller utdata från kommandona för **azcmagent** , när argumentet verbose (-v) används. Loggfilerna skrivs till `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` för Windows och Linux till `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Windows när du utför en interaktiv installation.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Windows när du utför en storskalig installation med ett huvud namn för tjänsten.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Linux när du utför en interaktiv installation.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Linux när du utför en storskalig installation med ett huvud namn för tjänsten.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problem med agent anslutning till tjänsten

I följande tabell visas några kända fel och förslag på hur du felsöker och löser dem.

|Meddelande |Fel |Trolig orsak |Lösning |
|--------|------|---------------|---------|
|Det gick inte att erhålla enhets flödet för autentiseringstoken |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Det går inte att komma åt `login.windows.net` slut punkten | Verifiera anslutningen till slut punkten. |
|Det gick inte att erhålla enhets flödet för autentiseringstoken |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy eller brand vägg blockerar åtkomst till `login.windows.net` slut punkten. | Kontrol lera anslutningen till slut punkten och den blockeras inte av en brand vägg eller proxyserver. |
|Det gick inte att hämta autentiseringstoken från SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy eller brand vägg blockerar åtkomst till `login.windows.net` slut punkten. |Kontrol lera anslutningen till slut punkten och den blockeras inte av en brand vägg eller proxyserver. |
|Det gick inte att hämta autentiseringstoken från SPN |`Invalid client secret is provided` |Felaktig eller ogiltig hemlighet för tjänstens huvud namn. |Verifiera hemligheten för tjänstens huvud namn. |
| Det gick inte att hämta autentiseringstoken från SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Felaktigt tjänst huvud namn och/eller klient-ID. |Verifiera tjänstens huvud namn och/eller klient-ID: t.|
|Hämta resurs svar för ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Fel autentiseringsuppgifter och/eller behörigheter |Verifiera att du eller tjänstens huvud namn är medlem i rollen **Azure Connected Machine onboarding** . |
|Det gick inte att AzcmagentConnect ARM-resurs |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure Resource providers har inte registrerats. |Registrera [resurs leverantörer](./agent-overview.md#register-azure-resource-providers). |
|Det gick inte att AzcmagentConnect ARM-resurs |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxyservern eller brand väggen blockerar åtkomst till `management.azure.com` slut punkten. |Kontrol lera anslutningen till slut punkten och den blockeras inte av en brand vägg eller proxyserver. |

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.

* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.