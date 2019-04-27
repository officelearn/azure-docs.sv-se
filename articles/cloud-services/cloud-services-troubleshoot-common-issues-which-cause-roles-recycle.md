---
title: Vanliga orsaker till rollåteranvändning molntjänst | Microsoft Docs
description: En molntjänstroll som plötsligt återanvänds kan medföra betydande driftavbrott. Här följer några vanliga problem som gör att roller återvinns, som kan hjälpa dig att minska stilleståndstiden.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 2a9214b918883e493ebe5c93fc7f56e7ce9c77ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652221"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Vanliga problem som gör att roller återvinns
Den här artikeln beskriver några vanliga orsaker till distributionsproblem med samt felsökningstips som kan hjälpa dig att lösa dessa problem. En indikation på att det finns ett problem med ett program är när det inte går att starta rollinstansen eller den cykler emellan initierar, upptagen och stoppar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Saknar körningsberoenden
Om en roll i programmet som är beroende av någon sammansättning som inte är en del av .NET Framework eller Azure-hanterade biblioteket, måste du uttryckligen inkludera denna sammansättning i programpaketet. Tänk på att andra Microsoft-ramverk inte är tillgängliga på Azure som standard. Om din roll är beroende av sådan ram, måste du lägga till dessa sammansättningar till programpaketet.

Innan du bygga och paketera ditt program bör du kontrollera följande:

* Om du använder Visual studio, se till att den **kopiera lokala** är inställd på **SANT** för varje refererade sammansättningen i projektet som inte är en del av Azure SDK eller .NET Framework.
* Kontrollera att filen web.config inte refererar till några oanvända sammansättningar i elementet kompilering.
* Den **byggåtgärd** av varje .cshtml filen har angetts till **innehåll**. Detta säkerställer att filerna visas korrekt i paketet och gör att andra refererade filer ska visas i paketet.

## <a name="assembly-targets-wrong-platform"></a>Sammansättningen mål fel plattform
Azure är en 64-bitars-miljö. .NET-sammansättningar som kompilerats för ett 32-bitars mål fungerar därför inte på Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rollen utlöser ohanterade undantag vid initiering eller Stopp
Alla undantag som utlöses av-metoderna i den [RoleEntryPoint] klass som innehåller den [OnStart], [OnStop], och [kör] metoder, är ohanterade undantag. Om det inträffar ett ohanterat undantag i någon av följande metoder, kommer rollåtervinning Om rollen återanvänds upprepade gånger kan det utlöste ett ohanterat undantag varje gång den försöker starta.

## <a name="role-returns-from-run-method"></a>Rollen returneras från körningsmetoden
Den [kör] metoden är avsedd att köras på obestämd tid. Om din kod åsidosätter den [kör] metoden den bör vila på obestämd tid. Om den [kör] returnerar metoden rollen återanvänds.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Felaktig DiagnosticsConnectionString-inställning
Om programmet använder Azure Diagnostics, din tjänstekonfigurationsfil måste ange den `DiagnosticsConnectionString` konfigurationsinställning. Den här inställningen ska ange en HTTPS-anslutning till ditt lagringskonto i Azure.

Att säkerställa att din `DiagnosticsConnectionString` inställningen är korrekt innan du distribuerar ditt programpaket till Azure, kontrollerar du följande:  

* Den `DiagnosticsConnectionString` inställningen punkter till ett giltigt lagringskonto i Azure.  
  Som standard den här inställningen som pekar på emulerade storage-konto, så du måste uttryckligen ändrar den här inställningen innan du distribuerar dina programpaket. Om du inte ändrar den här inställningen genereras ett undantag när instance role försöker starta diagnostikövervakare. Detta kan orsaka rollinstansen återanvänds på obestämd tid.
* Anslutningssträngen har angetts i följande [format](../storage/common/storage-configure-connection-string.md). (Protokollet måste anges som HTTPS.) Ersätt *MyAccountName* med namnet på ditt lagringskonto och *MyAccountKey* med din åtkomstnyckel:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Om du utvecklar ditt program genom att använda Azure Tools för Microsoft Visual Studio, kan du använda egenskapssidorna för att ange värdet.

## <a name="exported-certificate-does-not-include-private-key"></a>Exporterade certifikatet saknar privat nyckel
Om du vill köra en webbroll i SSL, måste du kontrollera att din exporterade certifikatet innehåller den privata nyckeln. Om du använder den *Windows Certifikathanteraren* för att exportera certifikatet, måste du markera **Ja** för den **exportera den privata nyckeln** alternativet. Certifikatet måste exporteras i PFX-format, vilket är det enda format som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg
Visa mer [felsökningsartiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för molntjänster.

Visa mer återvinning scenarier vid [Kevin Williamson bloggserie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Kör]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
