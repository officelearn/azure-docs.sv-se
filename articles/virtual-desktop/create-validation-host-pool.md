---
title: Skapa en pool virtuella Windows-skrivbordet förhandsversion värden för att verifiera uppdateringar av tjänsten - Azure
description: Hur du skapar en pool med verifiering värden för att övervaka uppdateringar av tjänsten innan du distribuerar uppdateringar till produktion.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c9b2a593a6943fe2e9577acc61b1d5a7bcd98607
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070667"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Självstudier: Skapa en värdpool för att verifiera tjänstuppdateringar

Värd-pooler är en samling av en eller flera identiska virtuella datorer i förhandsversion för virtuella skrivbord i Windows klient-miljöer. Vi rekommenderar du skapar en pool för verifiering värden innan du distribuerar värden pooler i din produktionsmiljö. Uppdateringar tillämpas först verifiering värd pooler, så att du kan övervaka uppdateringar av tjänsten innan du distribuerar dem till produktionsmiljön. Du kan inte identifiera ändringar som orsakar fel, vilket kan leda till stilleståndstid för användare i din produktionsmiljö utan poolen verifiering värden.

Om du vill se till att dina appar fungerar med de senaste uppdateringarna, bör verifiering värd poolen likna som värd-pooler i produktionsmiljön som möjligt. Användare bör ansluta så ofta till poolen verifiering värden som de gör i produktion värd-poolen. Om du har automatiserad testning på din värd-pool, bör du inkludera automatiserade tester för verifiering värd-poolen.

Du kan felsöka problem i poolen verifiering värden med antingen [funktionen diagnostik](diagnostics-role-service.md) eller [Windows virtuellt skrivbord felsökningsartiklar](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Vi rekommenderar att du lämnar poolen verifiering värden att testa alla framtida uppdateringar.

Innan du börjar [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), om du inte redan har gjort.

## <a name="create-your-host-pool"></a>Skapa din värd-pool

Du kan skapa en pool med värden genom att följa anvisningarna i någon av följande artiklar:
- [Självstudie: Skapa en värd-pool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värd-pool med en Azure Resource Manager-mall](create-host-pools-arm-template.md)
- [Skapa en värd-pool med PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiera din värd-pool som en verifiering värd-pool

Kör följande PowerShell-cmdletar för att definiera den nya poolen för värden som en värd pool för verifiering. Ersätt värden inom citattecken efter värdena som är relevanta för din session:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Kör följande PowerShell-cmdlet för att bekräfta att egenskapen verifiering har ställts in. Ersätt värden inom citattecken med värdena som är relevanta för din session.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Resultatet från cmdleten bör likna den här utdata:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Uppdatera schema

I förhandsversion inträffar tjänstuppdateringar på cirka en månatlig takt. Om det finns allvarliga problem, kommer kritiska uppdateringar att finnas på en mer frekventa takt.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat poolen verifiering värd kan du lära dig hur du distribuerar och ansluta till ett hanteringsverktyg för att hantera virtuella skrivbord för Microsoft-resurser.

> [!div class="nextstepaction"]
> [Distribuera en självstudiekurs om enhetshantering verktyget](./manage-resources-using-ui.md)
