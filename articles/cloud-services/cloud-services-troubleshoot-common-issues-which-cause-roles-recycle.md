---
title: Vanliga orsaker till åter användning av moln tjänst roller | Microsoft Docs
description: En moln tjänst roll som plötsligt återanvänds kan orsaka betydande stillestånds tid. Här följer några vanliga problem som gör att roller återvinns, vilket kan hjälpa dig att minska stillestånds tiden.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154980"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Vanliga problem som gör att roller återvinns
Den här artikeln beskriver några vanliga orsaker till distributions problem och innehåller fel söknings tips som hjälper dig att lösa problemen. En indikation på att ett problem har uppstått med ett program är när roll instansen inte startar, eller så växlar den mellan initierings-, upptaget-och stopp tillstånd.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Runtime-beroenden saknas
Om en roll i programmet är beroende av en sammansättning som inte är en del av .NET Framework eller det hanterade Azure-biblioteket, måste du uttryckligen ta med den sammansättningen i programpaketet. Tänk på att andra Microsoft-ramverk inte är tillgängliga på Azure som standard. Om rollen är beroende av ett sådant ramverk måste du lägga till dessa paket i programpaketet.

Innan du skapar och paketerar programmet kontrollerar du följande:

* Om du använder Visual Studio kontrollerar du att egenskapen **Kopiera lokal** har värdet **True** för varje refererad sammansättning i projektet som inte är en del av Azure SDK eller .NET Framework.
* Kontrol lera att filen Web. config inte refererar till oanvända sammansättningar i elementet Compilation.
* **Build-åtgärden** för varje. cshtml-fil har angetts till **Content**. Detta säkerställer att filerna visas korrekt i paketet och gör att andra refererade filer kan visas i paketet.

## <a name="assembly-targets-wrong-platform"></a>Monterings mål fel plattform
Azure är en 64-bitars miljö. Därför fungerar inte .NET-sammansättningar som kompileras för ett 32-bitars mål i Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rollen genererar ohanterade undantag vid initiering eller stopp
Undantag som har utlösts av metoderna i [RoleEntryPoint] -klassen, som innehåller metoderna [OnStart], [OnStop]och [Kör] , är ohanterade undantag. Om ett ohanterat undantag uppstår i någon av dessa metoder kommer rollen att återvinna. Om rollen åter användning görs upprepade gånger kan det leda till ett ohanterat undantag varje gång det försöker starta.

## <a name="role-returns-from-run-method"></a>Rollen returnerar från körnings metoden
[Kör] metoden är avsedd att köras oändligt. Om din kod åsidosätter [Kör] metoden bör den vila i låg tid. Om [Kör] metoden returnerar återvinner rollen.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Felaktig DiagnosticsConnectionString-inställning
Om programmet använder Azure-diagnostik måste tjänst konfigurations filen ange `DiagnosticsConnectionString` konfigurations inställningen. Den här inställningen ska ange en HTTPS-anslutning till ditt lagrings konto i Azure.

Kontrol lera följande för `DiagnosticsConnectionString` att se till att inställningen är korrekt innan du distribuerar ditt programpaket till Azure:  

* `DiagnosticsConnectionString` Inställningen pekar på ett giltigt lagrings konto i Azure.  
  Som standard pekar den här inställningen på det emulerade lagrings kontot, så du måste uttryckligen ändra den här inställningen innan du distribuerar ditt programpaket. Om du inte ändrar den här inställningen uppstår ett undantags fel när roll instansen försöker starta den diagnostiska övervakaren. Detta kan leda till att roll instansen omåtervinns oändligt.
* Anslutnings strängen har angetts i följande [format](../storage/common/storage-configure-connection-string.md). (Protokollet måste anges som HTTPS.) Ersätt *MyAccountName* med namnet på ditt lagrings konto och *MyAccountKey* med din åtkomst nyckel:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Om du utvecklar ditt program med hjälp av Azure Tools för Microsoft Visual Studio kan du använda egenskaps sidorna för att ange det här värdet.

## <a name="exported-certificate-does-not-include-private-key"></a>Det exporterade certifikatet innehåller inte privat nyckel
Om du vill köra en webbroll under SSL måste du se till att det exporterade hanterings certifikatet innehåller den privata nyckeln. Om du använder *Windows certifikat hanteraren* för att exportera certifikatet måste du välja **Ja** för alternativet **Exportera den privata nyckeln** . Certifikatet måste exporteras i PFX-format, vilket är det enda format som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg
Visa fler [fel söknings artiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för Cloud Services.

Visa fler scenarier för roll åter användning i [Kevin Williamsons blogg serie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Kör]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
