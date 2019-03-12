---
title: Introduktion till nätverk i Azure Stack | Microsoft Docs
description: Lär dig mer om nätverk i Azure Stack
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 67465a64f38ce4767b384cf5395179c0dbcb99fe
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769910"
---
# <a name="introduction-to-azure-stack-networking"></a>Introduktion till nätverksfunktioner i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack finns massor av nätverksfunktioner som kan användas tillsammans eller separat:

- **Anslutningar mellan Azure Stack-resurser**  
    Ansluta Azure-resurser tillsammans i en säker och privat virtuellt nätverk i molnet.
- **Internetanslutning**  
    Meddela och från Azure Stack-resurser via Internet.
- **Lokala anslutningsmöjligheter**  
    Ansluta ett lokalt nätverk till Azure Stack-resurser via ett virtuellt privat nätverk (VPN) via Internet eller via en dedikerad anslutning till Azure Stack.
- **Läsa in belastningsutjämning och traffic riktning**  
    Belastningsutjämna trafik till servrar i samma plats och dirigera trafik till servrar på olika platser.
- **Säkerhet**  
    Filtrera nätverkstrafik mellan undernät eller enskilda virtuella datorer (VM).
- **Routning**  
    Använd standardroutning eller helt och hållet styr routning mellan ditt Azure Stack och lokala resurser.
- **Hanterbarhet**  
    Övervaka och hantera dina Azure Stack nätverksresurser.
- **Verktyg för distribution och konfiguration**  
    Använda en webbaserad portal eller plattformsoberoende kommandoradsverktyg för att distribuera och konfigurera nätverksresurser.


## <a name="next-steps"></a>Nästa steg

* [Överväganden för nätverk i Azure Stack](azure-stack-network-differences.md)