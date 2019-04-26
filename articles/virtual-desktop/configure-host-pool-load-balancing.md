---
title: Konfigurera förhandsversion för virtuella skrivbord i Windows-belastningsutjämningsmetod - Azure
description: Så här konfigurerar du metoden belastningsutjämning för en miljö för virtuella Windows-skrivbordet.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328892"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Konfigurera förhandsversion för virtuella skrivbord i Windows-belastningsutjämningsmetod

Konfigurera belastningsutjämningsmetoden för en värd-pool kan du justera Windows Virtual Desktop förhandsversionsmiljön att bättre passa dina behov.

>[!NOTE]
> Detta gäller inte till poolen permanent skrivbord värd eftersom användarna alltid har en 1:1-mappning till en värd för fjärrskrivbordssession i poolen för värden.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurera belastningsutjämning med bredden först

Bredden först av nätverksbelastning är standardkonfigurationen för den nya värden för icke-beständiga pooler. Bredden första nätverksbelastning distribuerar nya användarsessioner över alla tillgängliga session värdar i poolen för värden. När du konfigurerar bredden först av nätverksbelastning, kan du ange en längsta sessionsgräns per värd för fjärrskrivbordssession i poolen för värden.

Först [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

Om du vill konfigurera en värd-pool för att utföra bredden första belastningsutjämning utan att justera maximal sessionens tidsgräns, kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Om du vill konfigurera en pool för värden att utföra bredden först av nätverksbelastning och använda en ny längsta sessionsgräns, kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurera belastningsutjämning med djup först

Djup först av nätverksbelastning distribuerar nya användarsessioner till en tillgänglig fjärrskrivbordssessioner med det högsta antalet anslutningar, men inte har nått det längsta session gräns tröskelvärdet. När du konfigurerar belastningsutjämning av djup först du **måste** ange en gräns för längsta session per värd för fjärrskrivbordssession i poolen värden.

Om du vill konfigurera en värd-pool för att utföra djup först av nätverksbelastning, kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
