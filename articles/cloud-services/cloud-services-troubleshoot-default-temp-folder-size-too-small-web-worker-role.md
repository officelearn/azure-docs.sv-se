---
title: Standardmappstorleken för TEMP är för liten för en roll | Microsoft-dokument
description: En molntjänstroll har ett begränsat utrymme för TEMP-mappen. Den här artikeln innehåller några förslag på hur du undviker att få på utrymme.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154987"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Standardmappstorleken för TEMP är för liten på en molntjänstwebb/arbetsroll
Standardkatalogen för en molntjänstarbetare eller webbroll har en maximal storlek på 100 MB, vilket kan bli fullt någon gång. I den här artikeln beskrivs hur du undviker att ta på utrymme för den tillfälliga katalogen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Varför får jag på utrymme?
Standardvariablerna för Windows-miljön TEMP och TMP är tillgängliga för kod som körs i ditt program. Både TEMP och TMP pekar på en enda katalog som har en maximal storlek på 100 MB. Data som lagras i den här katalogen sparas inte under molntjänstens livscykel. Om rollinstanserna i en molntjänst återvinns rensas katalogen.

## <a name="suggestion-to-fix-the-problem"></a>Förslag för att åtgärda problemet
Implementera något av följande alternativ:

* Konfigurera en lokal lagringsresurs och komma åt den direkt i stället för att använda TEMP eller TMP. Om du vill komma åt en lokal lagringsresurs från kod som körs i ditt program anropar du metoden [RoleEnvironment.GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))
* Konfigurera en lokal lagringsresurs och peka på TEMP- och TMP-katalogerna så att de pekar på sökvägen till den lokala lagringsresursen. Den här ändringen bör utföras i metoden [RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))

I följande kodexempel visas hur du ändrar målkatalogerna för TEMP och TMP från metoden OnStart:

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
Läs en blogg som beskriver [hur du ökar storleken på Azure Web Role ASP.NET tillfällig mapp](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Visa fler [felsökningsartiklar](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) för molntjänster.

Om du vill veta hur du felsöker problem med molntjänstroll med hjälp av Azure PaaS datordiagnostikdata kan du läsa [Kevin Williamsons bloggserie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
