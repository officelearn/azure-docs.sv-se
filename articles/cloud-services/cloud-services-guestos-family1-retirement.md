---
title: "Lägg märke till gäst-OS-familjen 1 pensionering | Microsoft Docs"
description: "Innehåller information om när Azure gäst OS-familjen 1 pensionering har inträffat och hur man avgör om påverkas"
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 3178a09dab1cb972a3460d54dc9908fb95cce68b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="guest-os-family-1-retirement-notice"></a>Gäst-OS-familjen 1 pensionering meddelande
Tillbakadragning av OS-familjen 1 först har meddelat den 1 juni 2013.

**2 september 2014** i Azure gästoperativsystemet (gäst-OS) familj 1.x, som baseras på operativsystemet Windows Server 2008, officiellt har dragits tillbaka. Alla försök att distribuera nya tjänster eller uppgradera befintliga tjänster använder familj 1 misslyckas med felmeddelandet informerar dig om att gäst OS-familjen 1 har tagits bort.

**3 november 2014** utökat stöd för gäst-OS-familjen 1 avslutades och fullständigt har återkallats. Alla tjänster fortfarande på familj 1 påverkas. Vi kan stoppa tjänsterna när som helst. Det är inte säkert dina tjänster fortsätter att köras om du manuellt uppgradera dem själv.

Om du har fler frågor finns i [Cloud Services forum](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Påverkas du?
Dina molntjänster påverkas om något av följande gäller:

1. Du har värdet ”osFamily =” 1 ”uttryckligen anges i filen ServiceConfiguration.cscfg för Molntjänsten.
2. Du har inte ett värde för osFamily som uttryckligen anges i filen ServiceConfiguration.cscfg för Molntjänsten. För närvarande används standardvärdet ”1” i det här fallet.
3. Azure-portalen visar gästoperativsystemet family värdet som ”Windows Server 2008”.

För att hitta kör som dina molntjänster som OS-familjen, du kan köra följande skript i Azure PowerShell, även om du måste [konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) första. Mer information om skriptet finns [Azure gäst OS-familjen 1 slutet av livslängd: juni 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Dina molntjänster som påverkas av OS-familjen 1 pensionering om osFamily-kolumn i utdata från skriptet är tom eller innehåller en ”1”.

## <a name="recommendations-if-you-are-affected"></a>Rekommendationer om du påverkas
Vi rekommenderar att du migrerar Cloud Service-roller till en av de gäst-Operativsystemserier som stöds:

**Gästoperativsystem family 4.x** -Windows Server 2012 R2 *(rekommenderas)*

1. Se till att ditt program med .NET framework 4.0, 4.5 och 4.5.1 SDK 2.1 eller senare.
2. Ange osFamily-attributet till ”4” i filen ServiceConfiguration.cscfg och distribuera din tjänst i molnet.

**Gästoperativsystem family 3.x** -Windows Server 2012

1. Se till att ditt program med .NET framework 4.0 eller 4.5 SDK 1,8 eller senare.
2. Ange osFamily-attributet till ”3” i filen ServiceConfiguration.cscfg och distribuera din tjänst i molnet.

**Gästoperativsystem family 2.x** -Windows Server 2008 R2

1. Kontrollera att ditt program använder SDK 1.3 och senare med .NET framework 3.5 eller 4.0.
2. Ange osFamily-attributet till ”2” i filen ServiceConfiguration.cscfg och distribuera din tjänst i molnet.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Utökad Support för gäst-OS-familjen 1 avslutades 3 Nov 2014
Molntjänster på gäst-OS-familjen 1 stöds inte längre. Migrering av familj 1 så snart som möjligt för att undvika avbrott i tjänsten.  

## <a name="next-steps"></a>Nästa steg
Granska senast [Gästoperativsystem släpper](cloud-services-guestos-update-matrix.md).
