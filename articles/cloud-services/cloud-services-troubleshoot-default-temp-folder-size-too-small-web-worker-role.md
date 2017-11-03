---
title: "Standard TEMP-mappen är för litet för en roll | Microsoft Docs"
description: "En rolltjänst för molnet har en begränsad mängd utrymme för TEMP-mappen. Den här artikeln innehåller några förslag på hur du undviker utrymmet tar slut."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 7/26/2017
ms.author: v-six
ms.openlocfilehash: 02a185fbe3603aa7f033ea3d50dc6ad36dd7f8f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Standardstorleken TEMP-mappen är för liten för en cloud service web/worker roll
Den tillfälliga standardkatalogen av en cloud service worker eller webbplats roll har en maximal storlek på 100 MB, som kan bli fullständig vid en viss tidpunkt. Den här artikeln beskriver hur du undviker slut på diskutrymme för den temporära katalogen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Varför kör slut på diskutrymme?
Standard Windows miljövariablerna TEMP och TMP är tillgängliga för kod som körs i ditt program. Både TEMP och TMP pekar på en katalog som har en maximal storlek på 100 MB. Alla data som lagras i den här katalogen är inte beständiga över livscykeln för Molntjänsten; Om rollinstanser i en molnbaserad tjänst har återvunnits kan rensa katalogen.

## <a name="suggestion-to-fix-the-problem"></a>Förslag på problemet
Implementera ett av följande alternativ:

* Konfigurera en resurs för lokal lagring och åtkomst till den direkt i stället för TEMP eller TMP. Om du vill komma åt en resurs för lokal lagring från kod som körs i ditt program måste anropa den [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metod.
* Konfigurera en resurs för lokal lagring och peka TEMP och TMP kataloger att peka till sökvägen till resursen för lokal lagring. Den här ändringen ska utföras i den [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metod.

Följande kodexempel visar hur du ändrar katalogerna som mål för TEMP och TMP från inom OnStart-metoden:

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
Läser en blogg som beskriver [så här ökar du storleken på Azure Web rollen ASP.NET tillfällig mapp](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Visa mer [felsökning artiklar](/?tag=top-support-issue&product=cloud-services) för molntjänster.

Lär du dig hur du felsöker cloud service rollen problem med hjälp av Azure PaaS datorn diagnostikdata [Kevin Williamson bloggserie](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
