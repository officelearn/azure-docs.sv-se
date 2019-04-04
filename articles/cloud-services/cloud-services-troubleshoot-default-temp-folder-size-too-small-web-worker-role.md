---
title: TEMP-standardmappen är för liten för en roll | Microsoft Docs
description: En molntjänstroll har en begränsad mängd utrymme för TEMP-mappen. Den här artikeln innehåller några förslag på hur du undviker slut på utrymme.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7862e4d5c4dd603dacf5784df6c4194392ebc351
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918203"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>TEMP-standardmappen är för liten för en cloud service web/worker-roll
Tillfällig standardkatalogen för en worker eller web molntjänstroll har en maximal storlek på 100 MB, som kan bli fullständig vid en viss tidpunkt. Den här artikeln beskriver hur du undviker slut på utrymme för den tillfälliga katalogen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Varför kör slut på utrymme?
Standard Windows miljövariablerna TEMP och TMP är tillgängliga för kod som körs i ditt program. Både TEMP och TMP pekar på en enskild katalog som har en maximal storlek på 100 MB. Alla data som lagras i den här katalogen bevaras inte under hela livscykeln för Molntjänsten; Om rollinstanser i en molntjänst har återvunnits kan rensas katalogen.

## <a name="suggestion-to-fix-the-problem"></a>Förslag på problemet
Implementera en av följande alternativ:

* Konfigurera en resurs för lokal lagring och åtkomst till den direkt istället för att använda TEMP och TMP. Du kommer åt en resurs för lokal lagring från kod som körs i ditt program genom att anropa den [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metod.
* Konfigurera en resurs för lokal lagring och peka TEMP och TMP kataloger att den pekar på sökvägen till resursen för lokal lagring. Den här ändringen bör utföras inom den [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metod.

I följande kodexempel visas hur du ändrar mål-kataloger för TEMP och TMP från inom OnStart-metoden:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Läser en blogg som beskriver [så här ökar du storleken på Azure Web-roll ASP.NET tillfälliga mappen](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Visa mer [felsökningsartiklar](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) för molntjänster.

Om du vill lära dig mer om felsökning av problem med cloud service rollen genom att använda diagnostikdata för Azure PaaS-datorn, visa [Kevin Williamson bloggserie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
