---
title: Support för Fjärrarbete i Azure Firewall
description: Den här artikeln visar hur Azure Firewall kan stödja dina krav på fjärrarbetskraft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289646"
---
# <a name="azure-firewall-remote-work-support"></a>Support för Fjärrarbete i Azure Firewall

Azure Firewall är en hanterad, molnbaserad nätverkssäkerhetstjänst som skyddar dina virtuella Azure-nätverksresurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. 

## <a name="firewall-rules"></a>Brandväggsregler

Du kan använda Azure Firewall för att skydda din virtuella skrivbordsinfrastruktur (VDI) inkommande RDP-åtkomst till ditt virtuella Azure-nätverk med hjälp av Azure Firewall [DNAT-regler](rule-processing.md). Windows Virtual Desktop (WVD) kräver inte att du öppnar någon inkommande åtkomst till det virtuella nätverket. Du måste dock tillåta en uppsättning utgående nätverksanslutningar för virtuella WVD-datorer som körs i det virtuella nätverket. Mer information finns i [Vad är Windows Virtual Desktop?](../virtual-desktop/overview.md#requirements)

Du kan konfigurera den här utgående åtkomsten med hjälp av Programregler för Azure Firewall. Mer information finns i [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen med Azure-portalen](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).