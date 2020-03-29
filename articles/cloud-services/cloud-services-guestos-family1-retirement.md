---
title: Gäst OS familj 1 pension meddelande | Microsoft-dokument
description: Ger information om när Azure Guest OS Family 1-pensioneringen inträffade och hur du tar reda på om du påverkas
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359579"
---
# <a name="guest-os-family-1-retirement-notice"></a>Gäst OS Familj 1 pensionsvarning
Pensioneringen av OS Family 1 tillkännagavs först den 1 juni 2013.

**den 2 september 2014** Azure Guest operativsystem (Guest OS) Family 1.x, som är baserat på operativsystemet Windows Server 2008, har officiellt dragits tillbaka. Alla försök att distribuera nya tjänster eller uppgradera befintliga tjänster med Family 1 misslyckas med ett felmeddelande som informerar dig om att Gästoperativsystemet Family 1 har dragits tillbaka.

**den 3 november 2014** Utökat stöd för Guest OS Family 1 slutade och det är helt pensionerad. Alla tjänster som fortfarande finns på Familj 1 kommer att påverkas. Vi kan stoppa dessa tjänster när som helst. Det finns ingen garanti för att dina tjänster kommer att fortsätta att köras om du inte uppgraderar dem manuellt själv.

Om du har ytterligare frågor kan du besöka [Cloud Services-forumen](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Är du påverkad?
Dina molntjänster påverkas om något av följande gäller:

1. Du har värdet "osFamily = "1" som uttryckligen anges i filen ServiceConfiguration.cscfg för molntjänsten.
2. Du har inget värde för osFamily som uttryckligen anges i filen ServiceConfiguration.cscfg för molntjänsten. För närvarande använder systemet standardvärdet "1" i det här fallet.
3. Azure-portalen listar ditt familj för gästoperativsystem som "Windows Server 2008".

För att ta reda på vilka av dina molntjänster som körs vilken OS-familj du kan köra följande skript i Azure PowerShell, men du måste [konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) först. Mer information om skriptet finns i [Azure Guest OS Family 1 End of Life: juni 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Dina molntjänster påverkas av OS Family 1-avgång om osFamily-kolumnen i skriptutdatan är tom eller innehåller ett "1".

## <a name="recommendations-if-you-are-affected"></a>Rekommendationer om du påverkas
Vi rekommenderar att du migrerar dina Cloud Service-roller till en av de gäst-OS-familjer som stöds:

**Gäst-OS-familjen 4.x** – Windows Server 2012 R2 *(rekommenderas)*

1. Kontrollera att ditt program använder SDK 2.1 eller senare med .NET framework 4.0, 4.5 eller 4.5.1.
2. Ange attributet osFamily till "4" i filen ServiceConfiguration.cscfg och distribuera om molntjänsten.

**Gäst-OS-familjen 3.x** – Windows Server 2012

1. Kontrollera att ditt program använder SDK 1.8 eller senare med .NET framework 4.0 eller 4.5.
2. Ange attributet osFamily till "3" i filen ServiceConfiguration.cscfg och distribuera om molntjänsten.

**Gäst-OS-familjen 2.x** - Windows Server 2008 R2

1. Kontrollera att ditt program använder SDK 1.3 och högre med .NET framework 3.5 eller 4.0.
2. Ange attributet osFamily till "2" i filen ServiceConfiguration.cscfg och distribuera om molntjänsten.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Utökad support för Gäst OS Family 1 avslutades 3 november 2014
Molntjänster på Gäst OS-familjen 1 stöds inte längre. Migrera av familj 1 så snart som möjligt för att undvika avbrott i tjänsten.  

## <a name="next-steps"></a>Nästa steg
Granska de senaste [Gäst-OS-utgåvorna](cloud-services-guestos-update-matrix.md).
