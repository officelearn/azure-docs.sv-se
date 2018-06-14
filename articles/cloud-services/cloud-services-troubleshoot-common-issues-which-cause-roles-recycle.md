---
title: Vanliga orsaker till Molntjänsten roller återvinning | Microsoft Docs
description: En rolltjänst för molnet plötsligt återanvänds kan medföra betydande driftstopp. Här följer några vanliga problem som kan medföra att roller som ska återvinnas, som kan hjälpa dig att minska driftstopp.
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
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 7f513a7d3fe44cbbf06855059c87c10ddceef7c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984294"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Vanliga problem som gör att roller återvinns
Den här artikeln beskrivs några vanliga orsaker till problem med distribution och ger felsökningstips som kan hjälpa dig att lösa dessa problem. En indikation på att det finns ett problem med ett program är när instansen inte kan startas eller den cykler emellan initiera upptagen och stoppas.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Runtime-beroenden som saknas
Om en roll i ditt program är beroende av någon sammansättning som inte är en del av .NET Framework eller Azure hanteras bibliotek, måste du uttryckligen inkludera sammansättningen i programpaketet. Tänk på att andra Microsoft-ramverk inte är tillgängliga på Azure som standard. Om din roll är beroende av sådan ram, måste du lägga till dessa sammansättningar programpaketet.

Innan du skapar och paketera programmet bör du kontrollera följande:

* Om du använder Visual studio, se till att den **kopiera lokala** egenskap är inställd på **SANT** för varje refererade sammansättning i projektet som inte är en del av Azure SDK eller .NET Framework.
* Kontrollera att filen web.config inte refererar till några oanvända sammansättningar i elementet kompilering.
* Den **Skapa åtgärd** av varje .cshtml fil har angetts till **innehåll**. Detta säkerställer att filerna visas korrekt i paketet och aktiverar andra refererade filer ska visas i paketet.

## <a name="assembly-targets-wrong-platform"></a>Sammansättningen mål fel plattform
Azure är en 64-bitars-miljö. Därför fungerar inte .NET-sammansättningar som kompilerats för ett 32-bitars mål på Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rollen utlöser ett ohanterat undantag vid initiering eller stoppar
Alla undantag som utlöses av den [RoleEntryPoint] -klassen, som innehåller den [OnStart], [OnStop], och [kör] metoder, är ohanterade undantag. Om det uppstår ett ohanterat undantag i någon av dessa metoder kan rollen kommer att återanvändas. Om rollen återvinning upprepade gånger kan det utlöser ett undantag när den försöker starta.

## <a name="role-returns-from-run-method"></a>Rollen som returneras från metoden
Den [kör] metoden är avsedd att köras under obestämd tid. Om din kod åsidosätter den [kör] metod det ska vila på obestämd tid. Om den [kör] metoden returnerar rollen återanvänds.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Felaktig DiagnosticsConnectionString-inställning
Om programmet använder Azure-diagnostik, din tjänstekonfigurationsfil måste ange den `DiagnosticsConnectionString` konfigurationsinställning. Den här inställningen bör ange en HTTPS-anslutning till ditt lagringskonto i Azure.

Att säkerställa att din `DiagnosticsConnectionString` är korrekt innan du distribuerar ditt programpaket till Azure, verifiera följande:  

* Den `DiagnosticsConnectionString` ställa pekar på ett giltigt lagringskonto i Azure.  
  Som standard den här inställningen som pekar på emulerade storage-konto så att du måste uttryckligen ändra den här inställningen innan du distribuerar ditt programpaket. Om du inte ändrar den här inställningen genereras ett undantag när instansen försöker starta diagnostik övervakaren. Detta kan orsaka rollinstansen att återvinna på obestämd tid.
* Anslutningssträngen har angetts i följande [format](../storage/common/storage-configure-connection-string.md). (Protokollet måste anges som HTTPS). Ersätt *MyAccountName* med namnet på ditt lagringskonto och *MyAccountKey* med din åtkomstnyckel:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Om du utvecklar programmet med hjälp av Azure-verktyg för Microsoft Visual Studio kan du använda egenskapssidor för att ange värdet.

## <a name="exported-certificate-does-not-include-private-key"></a>Exporterade certifikatet innehåller ingen privat nyckel
Om du vill köra en webbroll under SSL, måste du kontrollera att din exporterade certifikatet innehåller den privata nyckeln. Om du använder den *Windows Certificate Manager* för att exportera certifikatet, måste du markera **Ja** för den **exportera den privata nyckeln** alternativet. Certifikatet måste exporteras i PFX-format, vilket är det enda format som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg
Visa mer [felsökning artiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för molntjänster.

Visa mer rollåtervinning scenarier vid [Kevin Williamson bloggserie](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[kör]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
