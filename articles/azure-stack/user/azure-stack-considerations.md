---
title: "Nyckeln skillnaderna mellan Azure och Azure Stack när du använder tjänster och skapa appar | Microsoft Docs"
description: "Vad du behöver veta när du använder tjänster eller skapa appar för Azure-stacken."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 695824ef2537a97ea0530f2c33ad24d5cd9e20f8
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Nyckeln överväganden: använda tjänster eller utveckla appar för Azure-Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

När du använder tjänster eller skapa appar för Azure-stacken måste du förstå att det finns skillnader mellan Azure-stacken och Azure. Den här artikeln innehåller en översikt över viktiga överväganden när du använder Azure Stack som din hybrid cloud-utvecklingsmiljö.

## <a name="overview"></a>Översikt

Med Azure Stack, som är en plattform för hybridmoln, kan du använda Azure-tjänster från företagets eller leverantörens datacenter. Du kan skapa appar som körs på Azure-stacken som en utvecklare. Du kan sedan distribuera dessa appar till Azure-stacken till Azure, eller du kan bygga verkligen hybrid-appar som utnyttjar anslutning mellan en Azure-stacken molnet och Azure.

Azure Stack-operatorn informerar dig om tjänster som är tillgängliga som du kan använda och hur du kan få support. De tillhandahåller dessa tjänster via anpassade planer och erbjudanden.

Azure tekniskt innehåll förutsätter att apparna utvecklats för en Azure-tjänst i stället för Azure-stacken. När du skapar och distribuerar appar till Azure-stacken måste du förstå några huvudsakliga skillnader som:

* Azure-stacken levererar en delmängd av tjänster och funktioner som är tillgängliga i Azure.
* Ditt företag eller service-providern kan välja vilka tjänster som de vill erbjuda. Detta inkluderar anpassade tjänster eller program. De kan erbjuda sina egna anpassade dokumentation.
* Du måste använda rätt Azure Stack-specifika slutpunkter (till exempel URL: er för Portaladress och Azure Resource Manager-slutpunkten).
* Du måste använda PowerShell och API-versioner som stöds av Azure-stacken. Detta garanterar att dina appar fungerar i både Azure-stacken och Azure.

## <a name="cheat-sheet-high-level-differences"></a>Cheat blad: övergripande skillnader

I följande tabell beskrivs utförligt skillnaderna mellan Azure-stacken och Azure. Tänk på följande när du utvecklar för Azure-stacken eller använder Azure Stack-tjänster.

| Område | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| Som fungerar det? | Microsoft | Din organisation eller service provider.|
| Som kontaktar du support? | Microsoft | Kontakta din Azure Stack-operatorn (på din organisation eller service provider) för stöd för ett integrerat system.<br><br>Azure-stacken Development Kit support finns i [Microsoft forum](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Eftersom development kit är en utvärderingsmiljö, stöds inte officiellt erbjuds via Microsoft kundservice (CSS).
| Tillgängliga tjänster | Visa en lista över [Azure produkter](https://azure.microsoft.com/services/?b=17.04b). Tillgängliga tjänster varierar beroende på Azure-region. | Azure-stacken stöder en delmängd av Azure-tjänster. Faktiska tjänster varierar beroende på vad din organisation eller service provider väljer att erbjuda.
| Azure Resource Manager-slutpunkten * | https://management.azure.com | Använd den slutpunkt som Azure Stack-operator har angetts för ett Azure-stacken integrerat system.<br><br>SDK-paket, Använd: https://management.local.azurestack.external
| Portal-URL * | [https://Portal.Azure.com](https://portal.azure.com) | Gå till den URL som tillhandahålls av Azure Stack-operatorn för ett Azure-stacken integrerat system.<br><br>SDK-paket, Använd: https://portal.local.azurestack.external
| Region | Du kan välja vilken region som du vill distribuera till. | Använd den region som är tillgänglig på din dator för ett Azure-stacken integrerat system.<br><br>I development kit region ska alltid vara **lokala**.
| Resursgrupper | En resursgrupp kan sträcka sig över regioner. | Det finns endast en region för både integrerade system och development kit.
|Namnområden som stöds, resurstyper och API-versioner | Senast (eller tidigare versioner som ännu inte har inaktuella). | Azure-stacken stöder specifika versioner. Se avsnittet ”Version krav” i den här artikeln.
| | |

* Om du är operatör Azure Stack [med hjälp av administratörsportalen](../azure-stack-manage-portals.md) och [Administration grunderna](../azure-stack-manage-basics.md) för mer information.

## <a name="helpful-tools-and-best-practices"></a>Användbara verktyg och bästa praxis
 
 Microsoft tillhandahåller flera olika verktyg och riktlinjer som hjälper dig att utveckla för Azure-stacken.

| Rekommendation | Referenser | 
| -------- | ------------- | 
| Installera rätt verktyg på arbetsstationen utvecklare. | - [Installera PowerShell](azure-stack-powershell-install.md)<br>- [Hämta verktyg](azure-stack-powershell-download.md)<br>- [Konfigurera PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installera Visual Studio](azure-stack-install-visual-studio.md) 
| Granska information om följande:<br>-Överväganden för azure Resource Manager<br>-Hur du söker efter snabbstartsmallar<br>-Använda en principmodul som hjälper dig att använda Azure för att utveckla för Azure-stacken | [Utveckla för Azure Stack](azure-stack-developer.md) | 
| Granska och följ rekommenderade metoder för mallar. | [Resource Manager-Snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Krav på version

Azure-stacken stöder specifika versioner av Azure PowerShell och Azure service API: er. Du måste använda versioner som stöds för att säkerställa att din app kan distribuera till både Azure-stacken och till Azure.

Om du vill kontrollera att du använder rätt version av Azure PowerShell, Använd [profiler för API-version](azure-stack-version-profiles.md). För att avgöra den senaste API-version profil som du kan använda, måste du veta vilket skapa Azure-protokollstacken som du använder. Du kan hämta den här informationen från administratören Azure Stack.

>[!NOTE]
 Om du använder Azure Stack Development Kit och du har administrativ åtkomst, se avsnittet ”avgöra den aktuella versionen” i [hantera uppdateringar](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) att fastställa Azure Stack-version.

För andra API: er, kör du följande PowerShell-kommando till utdata namnområden, resurstyper och API-versioner som stöds i din prenumeration på Azure-stacken. Obs det kan fortfarande finnas skillnader på en nivå för egenskapen. (Det här kommandot ska fungera måste du ha redan [installerat](azure-stack-powershell-install.md) och [konfigurerats](azure-stack-powershell-configure-user.md) PowerShell för en Azure-Stack-miljö. Du måste också ha en prenumeration på Azure-stacken erbjudanden.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exempel på utdata (trunkerad): ![exempel utdata från kommandot Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Nästa steg

Mer detaljerad information om skillnaderna i en tjänstenivå finns:

* [Överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md)
* [Överväganden för lagring i Azure-stacken](azure-stack-acs-differences.md)
* [Överväganden för Azure-stacken nätverk](azure-stack-network-differences.md)
