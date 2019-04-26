---
title: Lägg märke till gästoperativsystemfamilj 1 försvinner | Microsoft Docs
description: Innehåller information om när Azure Guest OS-familjen 1 indragningen har hänt och hur du avgör om du påverkas
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: d6429766b6aac547fd99279659acb1067298e77c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518957"
---
# <a name="guest-os-family-1-retirement-notice"></a>Gäst-OS-familjen 1 tillbakadragande meddelande
Tillbakadragning av OS-familjen 1 först lanserades på den 1 juni 2013.

**2 september 2014** The Azure gästoperativsystemet (gäst-OS) familj 1.x, som baseras på operativsystemet Windows Server 2008, drogs officiellt. Alla försök att distribuera nya tjänster eller uppgradera befintliga tjänster som använder familj 1 misslyckas med ett felmeddelande som informerar dig om att Guest OS-familjen 1 har tagits bort.

**3 november 2014** utökat stöd för gäst-OS-familjen 1 avslutats och den fullständigt dras tillbaka. Alla tjänster som är på familj 1 påverkas. Vi kan stoppa tjänsterna när som helst. Det finns ingen garanti dina tjänster fortsätter att köras om du inte manuellt uppgraderar dem själv.

Om du har fler frågor kan du gå till den [Cloud Services-forum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Påverkas du?
Cloud Services berörs om något av följande gäller:

1. Du har värdet ”osFamily =” 1 ”uttryckligen anges i filen ServiceConfiguration.cscfg för din molntjänst.
2. Du har inte ett värde för osFamily som uttryckligen anges i filen ServiceConfiguration.cscfg för din molntjänst. För närvarande används standardvärdet ”1” i det här fallet.
3. Azure-portalen visas ditt family gästoperativsystemet-värde som ”Windows Server 2008”.

För att hitta vilka av dina molntjänster kör vilka OS-familj, du kan köra följande skript i Azure PowerShell, även om du måste [konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) första. Mer information om skriptet finns i [Azure Guest OS-familjen 1 slutet av liv: Juni 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Cloud services påverkas av OS-familjen 1 tillbakadragande om osFamily-kolumnen i utdata från skriptet är tom eller innehåller en ”1”.

## <a name="recommendations-if-you-are-affected"></a>Rekommendationer om du påverkas
Vi rekommenderar att du migrerar din Cloud Service-roller till en av de gäst-Operativsystemserier som stöds:

**Gäst-OS family 4.x** -Windows Server 2012 R2 *(rekommenderas)*

1. Se till att ditt program använder SDK 2.1 eller senare med .NET framework 4.0, 4.5 och 4.5.1.
2. Ange osFamily-attributet till ”4” i filen ServiceConfiguration.cscfg och distribuera om din molntjänst.

**Gäst-OS family 3.x** -Windows Server 2012

1. Se till att ditt program använder SDK 1.8 eller senare med .NET framework 4.0 eller 4.5.
2. Ange osFamily-attributet till ”3” i filen ServiceConfiguration.cscfg och distribuera om din molntjänst.

**Gäst-OS family 2.x** -Windows Server 2008 R2

1. Kontrollera att ditt program använder SDK 1.3 och senare med .NET framework 3.5 eller 4.0.
2. Ange osFamily-attributet till ”2” i filen ServiceConfiguration.cscfg och distribuera om din molntjänst.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Utökat stöd för gäst-OS-familjen 1 avslutades 3 november 2014
Molntjänster på gästoperativsystemfamilj 1 stöds inte längre. Migrering av familj 1 så snart som möjligt för att undvika avbrott i tjänsten.  

## <a name="next-steps"></a>Nästa steg
Granska senast [Guest OS-versionerna](cloud-services-guestos-update-matrix.md).
