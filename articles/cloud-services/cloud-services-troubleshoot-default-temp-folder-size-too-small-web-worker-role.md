---
title: Standard storleken för TEMP-mappar är för liten för en roll | Microsoft Docs
description: En moln tjänst roll har en begränsad mängd utrymme för TEMP-mappen. Den här artikeln innehåller några förslag på hur du undviker att ta slut på utrymme.
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
ms.openlocfilehash: 77c4f627668b91c4bc21ad73b3511c1775943229
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460209"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Standard storleken för TEMP-mappar är för liten för en webb-eller arbets roll i moln tjänsten
Den tillfälliga standard katalogen för en moln tjänst arbetare eller webb roll har en maximal storlek på 100 MB, vilket kan bli en fullständig tidpunkt. I den här artikeln beskrivs hur du undviker att använda utrymme för den tillfälliga katalogen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Varför får jag slut på utrymme?
Standard Windows-miljövariablerna TEMP och TMP är tillgängliga för kod som körs i ditt program. Både TEMP-och TMP-platser till en enda katalog med en maximal storlek på 100 MB. Alla data som lagras i den här katalogen sparas inte i moln tjänstens livs cykel. om roll instanserna i en moln tjänst återvinns rensas katalogen.

## <a name="suggestion-to-fix-the-problem"></a>Förslag för att åtgärda problemet
Implementera något av följande alternativ:

* Konfigurera en lokal lagrings resurs och få åtkomst till den direkt i stället för att använda TEMP eller TMP. Om du vill komma åt en lokal lagrings resurs från kod som körs i ditt program anropar du metoden [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Konfigurera en lokal lagrings resurs och peka TEMP-och TMP-katalogerna för att peka på sökvägen till den lokala lagrings resursen. Den här ändringen ska utföras i metoden [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

I följande kod exempel visas hur du ändrar mål katalogerna för TEMP och TMP inifrån metoden OnStart:

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
Läs en blogg som beskriver [hur du ökar storleken på den temporära mappen för Azure-Webbrollens ASP.net](https://docs.microsoft.com/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Visa fler [fel söknings artiklar](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) för Cloud Services.

Om du vill veta mer om hur du felsöker problem med Cloud Service-roller med hjälp av Azure PaaS Computer Diagnostics-data kan du Visa [Kevin Williamsons blogg serie](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)
