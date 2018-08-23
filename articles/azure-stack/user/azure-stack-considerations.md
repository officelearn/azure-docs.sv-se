---
title: Förstå de viktigaste skillnaderna mellan Azure och Azure Stack när du använder tjänster och skapa appar | Microsoft Docs
description: Vad du behöver veta för att använda tjänster eller skapa appar för Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: a8d211992f52c9719cad76f16133e23eba24d422
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139669"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Viktiga överväganden: med hjälp av tjänster eller att skapa appar för Azure Stack

Innan du använder tjänster eller skapa appar för Azure Stack, måste du förstå skillnaderna mellan Azure Stack och Azure. Den här artikeln identifierar viktiga överväganden när du använder Azure Stack som utveckling för hybridmolnmiljön.

## <a name="overview"></a>Översikt

Med Azure Stack, som är en plattform för hybridmoln, kan du använda Azure-tjänster från företagets eller leverantörens datacenter. Du kan skapa en app på Azure Stack och sedan distribuera den till Azure Stack, till Azure eller till ditt Azure hybridmoln.

Azure Stack-operatör kan du veta vilka tjänster som är tillgängliga som du kan använda och få support. De ger dessa tjänster via sina anpassade planer och erbjudanden.

Azure tekniskt innehåll förutsätter att appar håller på att utvecklas för en Azure-tjänst i stället för Azure Stack. När du skapar och distribuerar appar till Azure Stack, måste du förstå några huvudsakliga skillnader som:

* Azure Stack får du en delmängd av de tjänster och funktioner som är tillgängliga i Azure.
* Ditt företag eller en tjänst-providern kan välja vilka tjänster som de vill erbjuda. De tillgängliga alternativen kan innehålla anpassade tjänster eller program. De kan erbjuda sina egna anpassade dokumentation.
* Du måste använda rätt Azure Stack-specifika slutpunkter (t.ex, URL: er för Portaladress och Azure Resource Manager-slutpunkt).
* Du måste använda PowerShell och API-versioner som stöds av Azure Stack. Användning av versioner som stöds garanterar att dina appar fungerar i både Azure Stack och Azure.

## <a name="cheat-sheet-high-level-differences"></a>Lathund: övergripande skillnader

I följande tabell beskrivs de övergripande skillnaderna mellan Azure Stack och Azure. Behåll skillnaderna i åtanke när du utvecklar för Azure Stack eller använder Azure Stack-tjänster.
*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

| Område | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| Som fungerar det? | Microsoft | Din organisation eller service provider.|
| Vem du kontaktar support? | Microsoft | Kontakta din Azure Stack-operatör (i din organisation eller service provider) för support för ett integrerat system.<br><br>Support för Azure Stack Development Kit, finns det [Microsofts forum](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Eftersom i development kit är en utvecklingsmiljö, det finns inga officiella stöd som erbjuds via Microsofts kundsupport (CSS).
| Tillgängliga tjänster | Se en lista över [Azure-produkter](https://azure.microsoft.com/services/?b=17.04b). Tillgängliga tjänster varierar i Azure-region. | Azure Stack har stöd för en delmängd av Azure-tjänster. Faktiska tjänster kan variera beroende på vad din organisation eller service provider väljer att erbjuda.
| Azure Resource Manager-slutpunkten * | https://management.azure.com | Använd den slutpunkt som tillhandahålls av Azure Stack-operatör för ett integrerat Azure Stack-system.<br><br>För SDK-paket, använder du: https://management.local.azurestack.external
| Portal-URL * | [https://portal.azure.com](https://portal.azure.com) | Gå till den URL som tillhandahålls av Azure Stack-operatör för ett integrerat Azure Stack-system.<br><br>För SDK-paket, använder du: https://portal.local.azurestack.external
| Region | Du kan välja vilken region som du vill distribuera till. | För ett integrerat Azure Stack-system, använder du den region som är tillgängliga på datorn.<br><br>För SDK-paket, region kommer alltid att **lokala**.
| Resursgrupper | En resursgrupp kan sträcka sig över regioner. | Det finns endast en region för både integrerade system och i development kit.
|Stöds namnområden, resurstyper och API-versioner | Senast (eller tidigare versioner som ännu inte har inaktuella). | Azure Stack har stöd för specifika versioner. Se avsnittet ”krav för Version” i den här artikeln.
| | |

* Om du är Azure Stack-operatör kan du läsa [med hjälp av administratörsportalen](../azure-stack-manage-portals.md) och [grundläggande om Administration](../azure-stack-manage-basics.md) för mer information.

## <a name="helpful-tools-and-best-practices"></a>Användbara verktyg och bästa praxis
 
 Microsoft tillhandahåller flera olika verktyg och vägledning som hjälper dig att utveckla för Azure Stack.

| Rekommendation | Referenser | 
| -------- | ------------- | 
| Installera rätt verktyg på din utvecklararbetsstation. | - [Installera PowerShell](azure-stack-powershell-install.md)<br>- [Hämta verktyg](azure-stack-powershell-download.md)<br>- [Konfigurera PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installera Visual Studio](azure-stack-install-visual-studio.md) 
| Granska information om följande:<br>– Överväganden för azure Resource Manager-mall<br>– Hur du hittar snabbstartsmallar<br>– Använd en principmodul när du använder Azure för att utveckla för Azure Stack | [Utveckla för Azure Stack](azure-stack-developer.md) | 
| Granska och följ de rekommenderade säkerhetsmetoderna för mallar. | [Resource Manager Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Krav på version

Azure Stack har stöd för specifika versioner av Azure PowerShell och Azure service API: er. Använda versioner som stöds för att säkerställa att din app kan distribuera till både Azure Stack och Azure.

För att säkerställa att du använder rätt version av Azure PowerShell, Använd [API-versionsprofiler](azure-stack-version-profiles.md). Ta reda på versionen av Azure Stack som du använder för att fastställa den senaste version profil för API som du kan använda. Du kan hämta den här informationen från din Azure Stack-administratör.

>[!NOTE]
 Om du använder Azure Stack Development Kit och du har administratörsbehörighet, se avsnittet ”avgöra den aktuella versionen” i [hantera uppdateringar](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) fastställa Azure Stack-versionen.

För andra API: er, kör du följande PowerShell-kommando för att mata ut de namnområden, resurstyper och API-versioner som stöds i Azure Stack-prenumerationen. Obs det kan fortfarande finnas skillnader på en nivå för egenskapen. (För det här kommandot ska fungera måste du ha redan [installerat](azure-stack-powershell-install.md) och [konfigurerats](azure-stack-powershell-configure-user.md) PowerShell för Azure Stack-miljön. Du måste också ha en prenumeration på ett Azure Stack-erbjudande.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exempel på utdata (trunkerad): ![exempel på utdata från kommandot Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Nästa steg

Mer detaljerad information om skillnader på en servicenivå finns:

* [Överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md)
* [Överväganden för lagring i Azure Stack](azure-stack-acs-differences.md)
* [Överväganden för nätverk i Azure Stack](azure-stack-network-differences.md)
