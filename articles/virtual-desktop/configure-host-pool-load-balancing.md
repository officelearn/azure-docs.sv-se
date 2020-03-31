---
title: Konfigurera belastningsutjämning för Windows Virtual Desktop - Azure
description: Konfigurerar belastningsutjämningsmetoden för en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128303"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop

Genom att konfigurera belastningsutjämningsmetoden för en värdpool kan du justera Windows Virtual Desktop-miljön så att den passar dina behov bättre.

>[!NOTE]
> Detta gäller inte för en beständig skrivbordsvärdpool eftersom användarna alltid har en 1:1-mappning till en sessionsvärd i värdpoolen.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurera bredd-första belastningsutjämning

Bredd-första belastningsutjämning är standardkonfigurationen för nya icke-beständiga värdpooler. Bredd-första belastningsutjämning distribuerar nya användarsessioner över alla tillgängliga sessionsvärdar i värdpoolen. När du konfigurerar bredd-första belastningsutjämning kan du ange en maximal sessionsgräns per sessionsvärd i värdpoolen.

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det. Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Om du vill konfigurera en värdpool för att utföra bredd-första belastningsutjämning utan att justera den maximala sessionsgränsen kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Om du vill konfigurera en värdpool för att utföra bredd-första belastningsutjämning och använda en ny maximal sessionsgräns kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurera djup-första belastningsutjämning

Djup-första belastningsutjämning distribuerar nya användarsessioner till en tillgänglig sessionsvärd med det högsta antalet anslutningar men har inte nått sitt maximala tröskelvärde för sessionsgräns. När du konfigurerar djup-första belastningsutjämning **måste** du ange en maximal sessionsgräns per sessionsvärd i värdpoolen.

Om du vill konfigurera en värdpool för att utföra djup-första belastningsutjämning kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
