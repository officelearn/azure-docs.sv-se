---
title: Uppdateringar av tjänsten Host pool i Windows virtuella skriv bord – Azure
description: Så här skapar du en pool för validering av värdar för övervakning av tjänst uppdateringar innan uppdateringar distribueras till produktion.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8eb8378480b8784939e3ab63327e73e3705c74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526518"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Självstudie: skapa en adresspool för att verifiera tjänst uppdateringar

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Värdbaserade pooler är en samling av en eller flera identiska virtuella datorer i Windows Virtual Desktop-miljö. Vi rekommenderar starkt att du skapar en pool för validering av värdar där tjänst uppdateringar tillämpas först. På så sätt kan du övervaka tjänst uppdateringar innan tjänsten tillämpar dem i din standard miljö eller icke-validerings miljö. Utan en verifierings värd kan du inte identifiera ändringar som innehåller fel, vilket kan leda till stillestånds tid för användare i din standard miljö.

För att se till att dina appar fungerar med de senaste uppdateringarna bör verifierings värd poolen vara lika likadan som värdar i miljön som inte verifieras som möjligt. Användarna bör ansluta så ofta som de använder sig av en standardpool för validering av värden. Om du har automatiserat tester på din värd-pool bör du inkludera automatiserad testning på verifierings värds poolen.

Du kan felsöka problem i poolen för validering av värdar med hjälp [av en diagnostisk funktion](diagnostics-role-service.md) eller [fel söknings artiklarna för Windows Virtual Desktop](troubleshoot-set-up-overview.md).

>[!NOTE]
> Vi rekommenderar att du lämnar validerings värd för poolen på plats för att testa alla framtida uppdateringar.

>[!IMPORTANT]
>Windows Virtual Desktop med Azure Resource Management-integrering har för närvarande problem med att aktivera och inaktivera validerings miljöer. Vi uppdaterar den här artikeln när vi har löst problemet.

## <a name="prerequisites"></a>Krav

Innan du börjar följer du anvisningarna i [Konfigurera Windows Virtual Desktop PowerShell-modulen](powershell-module.md) för att konfigurera din PowerShell-modul och logga in på Azure.

## <a name="create-your-host-pool"></a>Skapa din värd bassäng

Du kan skapa en adresspool genom att följa anvisningarna i någon av följande artiklar:
- [Självstudie: skapa en värdbaserad pool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiera poolen som värd för en pool för validerings värdar

Kör följande PowerShell-cmdlets för att definiera den nya poolen som en validerings värd pool. Ersätt värdena inom hak paren tes med de värden som är relevanta för din session:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Kör följande PowerShell-cmdlet för att bekräfta att validerings egenskapen har angetts. Ersätt värdena inom hak paren tes med de värden som är relevanta för din session.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Resultatet från cmdleten bör likna följande utdata:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Uppdaterings schema

Tjänst uppdateringar sker varje månad. Om det uppstår allvarliga problem kommer kritiska uppdateringar att tillhandahållas oftare.

Om det finns några tjänst uppdateringar kontrollerar du att du har minst en liten grupp användare som loggar in varje dag för att validera miljön. Vi rekommenderar att du regelbundet besöker vår [TechCommunity-webbplats](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) och följer eventuella inlägg med WVDUPdate för att hålla dig informerad om tjänst uppdateringar.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en pool för validerings värdar kan du lära dig hur du använder Azure Service Health för att övervaka distributionen av virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Konfigurera tjänstaviseringar](./set-up-service-alerts.md)
