---
services: virtual-machines
title: Konfigurera PowerShell
author: JoeDavies-MSFT
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-powershell"></a>Konfigurera PowerShell
Följ dessa steg innan du kan använda Azure PowerShell.

### <a name="verify-powershell-versions"></a>Kontrollera att PowerShell-versioner
Innan du kan använda Windows PowerShell, måste du ha Windows PowerShell, Version 3.0 eller 4.0. Skriv följande kommando i Kommandotolken för Windows PowerShell för att hitta versionen av Windows PowerShell.

    $PSVersionTable

Du bör se ut så här.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Kontrollera att värdet för **PSVersion** 3.0 eller 4.0. Om du vill installera en kompatibel version [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Du bör också ha Azure PowerShell version 0.8.0 eller senare. Du kan kontrollera versionen av Azure PowerShell som du har installerat med det här kommandot på Azure PowerShell-Kommandotolken.

    Get-Module azure | format-table version

Du bör se ut så här.

    Version
    -------
    0.8.16.1

Instruktioner och en länk till den senaste versionen finns [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Konfigurera Azure-kontot och Azure-prenumerationen
Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

Öppna ett Azure PowerShell-Kommandotolken och logga in på Azure med det här kommandot.

    Add-AzureAccount

Om du har flera Azure-prenumerationer, kan du visa din Azure-prenumerationer med det här kommandot.

    Get-AzureSubscription

Du får följande typ av information:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Du kan ange den aktuella Azure-prenumerationen genom att köra dessa kommandon i Kommandotolken Azure PowerShell. Ersätt allt inom citattecken, inklusive den < och > tecken, med rätt namn.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Läs mer om Azure-prenumerationer och konton [så här: ansluta till din prenumeration](/powershell/azureps-cmdlets-docs#Connect).

