---
title: Uppdateringar av windows virtual desktop-värdpooltjänst - Azure
description: Så här skapar du en verifieringsvärdpool för att övervaka tjänstuppdateringar innan du distribuerar uppdateringar till produktion.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365397"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Självstudiekurs: Skapa en värdpool för att validera tjänstuppdateringar

Värdpooler är en samling av en eller flera identiska virtuella datorer i Windows Virtual Desktop-klientmiljöer. Innan du distribuerar värdpooler till din produktionsmiljö rekommenderar vi starkt att du skapar en verifieringsvärdpool. Uppdateringar tillämpas först på verifieringsvärdpooler, så att du kan övervaka tjänstuppdateringar innan du distribuerar dem till produktionsmiljön. Utan en verifieringsvärdpool kan du inte upptäcka ändringar som medför fel, vilket kan resultera i driftstopp för användare i produktionsmiljön.

För att säkerställa att dina appar fungerar med de senaste uppdateringarna bör verifieringsvärdpoolen vara lika lik värdpooler i produktionsmiljön som möjligt. Användare bör ansluta så ofta som de till produktionsvärdpoolen. Om du har automatisk testning på värdpoolen bör du inkludera automatisk testning på verifieringsvärdpoolen.

Du kan felsöka problem i verifieringsvärdpoolen med antingen [diagnostikfunktionen](diagnostics-role-service.md) eller [felsökningsartiklarna](troubleshoot-set-up-overview.md)för Windows Virtual Desktop .

>[!NOTE]
> Vi rekommenderar att du lämnar verifieringsvärdpoolen på plats för att testa alla framtida uppdateringar.

Innan du börjar hämtar och importerar du [PowerShell-modulen för Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/), om du inte redan har gjort det. Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Skapa din värdpool

Du kan skapa en värdpool genom att följa instruktionerna i någon av dessa artiklar:
- [Självstudiekurs: Skapa en värdpool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värdpool med en Azure Resource Manager-mall](create-host-pools-arm-template.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiera värdpoolen som en verifieringsvärdpool

Kör följande PowerShell-cmdlets för att definiera den nya värdpoolen som en verifieringsvärdpool. Ersätt värdena inom citationstecken med de värden som är relevanta för din session:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Kör följande PowerShell-cmdlet för att bekräfta att valideringsegenskapen har angetts. Ersätt värdena inom citationstecken med de värden som är relevanta för sessionen.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Resultaten från cmdlet bör se ut ungefär som denna utgång:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Uppdatera schema

Serviceuppdateringar sker varje månad. Om det finns stora problem kommer viktiga uppdateringar att tillhandahållas i en mer frekvent takt.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en verifieringsvärdpool kan du läsa om hur du använder Azure Service Health för att övervaka distributionen av ditt Virtuella Windows-skrivbord. 

> [!div class="nextstepaction"]
> [Konfigurera tjänstaviseringar](./set-up-service-alerts.md)
