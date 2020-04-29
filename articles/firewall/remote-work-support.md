---
title: Support för Azure Firewall Remote Work
description: Den här artikeln visar hur Azure-brandväggen kan ge support för ditt krav på fjärranslutna arbets kraft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289646"
---
# <a name="azure-firewall-remote-work-support"></a>Support för Azure Firewall Remote Work

Azure-brandväggen är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. 

## <a name="firewall-rules"></a>Brandväggsregler

Du kan använda Azure-brandväggen för att skydda din VDI-inkommande RDP-åtkomst (Virtual Desktop Infrastructure) till ditt virtuella Azure-nätverk med hjälp av Azure Firewall [DNAt-regler](rule-processing.md). Windows Virtual Desktop (WVD) kräver inte att du öppnar någon inkommande åtkomst till det virtuella nätverket. Du måste dock tillåta en uppsättning utgående nätverks anslutningar för virtuella WVD-datorer som körs i det virtuella nätverket. Mer information finns i [Vad är Windows Virtual Desktop?](../virtual-desktop/overview.md#requirements)

Du kan konfigurera den här utgående åtkomsten med hjälp av regler för Azure brand Väggs program. Mer information finns i [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-desktop/).