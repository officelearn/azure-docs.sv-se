---
title: Uppdateringar av tjänsten Host pool i Windows virtuella skriv bord – Azure
description: Så här skapar du en pool för validering av värdar för övervakning av tjänst uppdateringar innan uppdateringar distribueras till produktion.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fe6b047d10b8aef8faf81365cae940b05b3d798c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117745"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Självstudie: skapa en adresspool för att verifiera tjänst uppdateringar

>[!IMPORTANT]
>Det här innehållet gäller för hösten 2019-versionen som inte stöder Azure Resource Manager virtuella Windows Desktop-objekt. Om du försöker hantera Azure Resource Manager virtuella Windows-skrivbordet som introduceras i våren 2020-uppdateringen, se [den här artikeln](../create-validation-host-pool.md).

Värdbaserade pooler är en samling av en eller flera identiska virtuella datorer i Windows-miljöer för virtuella Skriv bords klienter. Innan du distribuerar lagringspooler till produktions miljön rekommenderar vi starkt att du skapar en pool för validerings värdar. Uppdateringar tillämpas först på verifiering av värdbaserade pooler, så att du kan övervaka tjänst uppdateringar innan du återställer dem till produktions miljön. Utan en verifierings värd kan du inte identifiera ändringar som innehåller fel, vilket kan leda till stillestånds tid för användare i produktions miljön.

För att se till att dina appar fungerar med de senaste uppdateringarna bör verifierings värddatorn vara lika likadan som lagringspooler i produktions miljön som möjligt. Användarna bör ansluta så ofta som de använder för att köra en pool för värden för autentisering. Om du har automatiserat tester på din värd-pool bör du inkludera automatiserad testning på verifierings värds poolen.

Du kan felsöka problem i poolen för validering av värdar med hjälp [av en diagnostisk funktion](diagnostics-role-service-2019.md) eller [fel söknings artiklarna för Windows Virtual Desktop](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Vi rekommenderar att du lämnar validerings värd för poolen på plats för att testa alla framtida uppdateringar.

Innan du börjar [hämtar och importerar du Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/), om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Skapa din värd bassäng

Du kan skapa en adresspool genom att följa anvisningarna i någon av följande artiklar:
- [Självstudie: skapa en värdbaserad pool med Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Skapa en värdpool med en Azure Resource Manager-mall](create-host-pools-arm-template.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiera poolen som värd för en pool för validerings värdar

Kör följande PowerShell-cmdlets för att definiera den nya poolen som en validerings värd pool. Ersätt värdena i citat tecken med de värden som är relevanta för din session:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Kör följande PowerShell-cmdlet för att bekräfta att validerings egenskapen har angetts. Ersätt värdena i citationstecken med de värden som är relevanta för din session.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Resultatet från cmdleten bör likna följande utdata:

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

## <a name="update-schedule"></a>Uppdaterings schema

Tjänst uppdateringar sker varje månad. Om det uppstår allvarliga problem kommer kritiska uppdateringar att tillhandahållas oftare.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en pool för validerings värdar kan du lära dig hur du använder Azure Service Health för att övervaka distributionen av virtuella Windows-datorer. 

> [!div class="nextstepaction"]
> [Konfigurera tjänstaviseringar](set-up-service-alerts-2019.md)
