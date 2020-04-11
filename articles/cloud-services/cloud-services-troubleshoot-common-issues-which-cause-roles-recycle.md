---
title: Vanliga orsaker till återvinning av molntjänstroller | Microsoft-dokument
description: En molntjänstroll som plötsligt återvinner kan orsaka betydande driftstopp. Här är några vanliga problem som gör att roller återvinns, vilket kan hjälpa dig att minska driftstopp.
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
ms.openlocfilehash: a644e211cc933ca686f0bd6a13b0d2ba8ae20162
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114100"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Vanliga problem som gör att roller återvinns
I den här artikeln beskrivs några av de vanligaste orsakerna till distributionsproblem och felsökningstips som hjälper dig att lösa dessa problem. En indikation på att ett problem finns med ett program är när rollinstansen inte startar, eller om den växlar mellan initierings-, upptagen- och stopptillstånden.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Saknar körningsberoenden
Om en roll i ditt program är beroende av någon sammansättning som inte ingår i .NET Framework eller Det Azure-hanterade biblioteket, måste du uttryckligen inkludera den sammansättningen i programpaketet. Tänk på att andra Microsoft-ramverk inte är tillgängliga på Azure som standard. Om din roll är beroende av ett sådant ramverk måste du lägga till dessa sammansättningar i programpaketet.

Kontrollera följande innan du skapar och paketerar programmet:

* Om du använder Visual Studio kontrollerar du att egenskapen **Kopiera lokal** är inställd på **True** för varje refererad sammansättning i projektet som inte ingår i Azure SDK eller .NET Framework.
* Kontrollera att filen web.config inte refererar till oanvända sammansättningar i kompileringselementet.
* **Byggåtgärden** för varje Cshtml-fil är inställd på **Innehåll**. Detta säkerställer att filerna visas korrekt i paketet och gör att andra refererade filer kan visas i paketet.

## <a name="assembly-targets-wrong-platform"></a>Monteringsmål fel plattform
Azure är en 64-bitars miljö. Därför fungerar inte .NET-sammansättningar som kompileras för ett 32-bitarsmål på Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Roll kastar ohanterade undantag vid initiering eller stopp
Alla undantag som genereras av metoderna i klassen [RoleEntryPoint,] som innehåller metoderna [OnStart,] [OnStop]och [Run,] är ohanterade undantag. Om ett ohanterat undantag inträffar i någon av dessa metoder kommer rollen att återvinnas. Om rollen är återvinning upprepade gånger, kan det kasta ett ohanterat undantag varje gång den försöker starta.

## <a name="role-returns-from-run-method"></a>Rollreturer från körmetod
Metoden [Kör] är avsedd att köras på obestämd tid. Om koden åsidosätter metoden [Kör] ska den vilas på obestämd tid. Om metoden [Kör] returneras återvinns rollen.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Felaktig inställning för Diagnostikanslutningssträng
Om programmet använder Azure Diagnostics måste tjänstkonfigurationsfilen ange konfigurationsinställningen. `DiagnosticsConnectionString` Den här inställningen bör ange en HTTPS-anslutning till ditt lagringskonto i Azure.

Kontrollera följande `DiagnosticsConnectionString` för att säkerställa att din inställning är korrekt innan du distribuerar programpaketet till Azure:  

* Inställningen `DiagnosticsConnectionString` pekar på ett giltigt lagringskonto i Azure.  
  Som standard pekar den här inställningen på det emulerade lagringskontot, så du måste uttryckligen ändra den här inställningen innan du distribuerar programpaketet. Om du inte ändrar den här inställningen genereras ett undantag när rollinstansen försöker starta diagnostikövervakaren. Detta kan leda till att rollinstansen återvinns på obestämd tid.
* Anslutningssträngen anges i följande [format](../storage/common/storage-configure-connection-string.md). (Protokollet måste anges som HTTPS.) Ersätt *MyAccountName* med namnet på ditt lagringskonto och *MyAccountKey* med åtkomstnyckeln:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Om du utvecklar ditt program med hjälp av Azure Tools för Microsoft Visual Studio kan du använda egenskapssidorna för att ange det här värdet.

## <a name="exported-certificate-does-not-include-private-key"></a>Exporterat certifikat innehåller inte privat nyckel
Om du vill köra en webbroll under TLS måste du se till att det exporterade hanteringscertifikatet innehåller den privata nyckeln. Om du använder *Windows-certifikathanteraren* för att exportera certifikatet måste du välja **Ja** för alternativet **Exportera den privata nyckeln.** Certifikatet måste exporteras i PFX-format, vilket är det enda format som för närvarande stöds.

## <a name="next-steps"></a>Nästa steg
Visa fler [felsökningsartiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för molntjänster.

Visa fler roll återvinning scenarier på [Kevin Williamson blogg serie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Påstart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop (på)]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Köra]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
