---
title: Azure Stack-fysisk enhet och granskning
description: Lär dig hur du integrerar fysisk enhet åtkomstgranskning i Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/01/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 459cdf4e1a70ee02d818dd6abe101e4fc3475b68
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034802"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integrering med Azure Stack datacenter - granskning av fysisk enhet

Alla fysiska enheter i Azure Stack, som hanteringsstyrenheter för baskort (bmc) och nätverksväxlar, skickar granskningsloggar och de ska integreras i din lösning för granskning. Eftersom enheterna variera mellan olika maskinvaruleverantörer för Azure Stack OEM, kontakta leverantören i dokumentationen om hur du granskar integration. I avsnitten nedan innehåller några allmän information för fysisk enhet granskning i Azure Stack.  

## <a name="physical-device-access-auditing"></a>Fysisk enhet åtkomstgranskning

Alla fysiska enheter i Azure Stack stöd för användning av TACACS eller RADIUS. Detta inkluderar åtkomst till hanteringsstyrenheten för baskort (BMC) och nätverksväxlar.

Azure Stack-lösningar levereras inte med RADIUS eller TACACS inbyggda. Lösningarna har har godkänt för att stödja användning av befintliga RADIUS eller TACACS lösningar som är tillgängliga på marknaden.

För RADIUS, har MSCHAPv2 verifierats. Detta representerar den säkraste implementeringen med RADIUS.
Kontakta maskinvaruleverantören OEM för att aktivera TACAS eller RADIUS i de enheter som ingår i din Azure Stack-lösning.

## <a name="syslog-forwarding-for-network-devices"></a>Syslog-vidarebefordran för nätverksenheter

Alla fysiska nätverksenheter i Azure Stack stöd för syslog-meddelanden. Azure Stack-lösningar levereras inte med en syslog-server. Enheterna som har har godkänt för att stödja skicka meddelanden till befintliga syslog lösningar som är tillgängliga på marknaden.

Syslog-måladressen är en valfri parameter som samlats in för distribution, men den kan också läggas till efter distribution. Kontakta leverantören OEM och konfigurera syslog-vidarebefordran på dina nätverksenheter.

## <a name="next-steps"></a>Nästa steg

[Hanteringsprincip](azure-stack-servicing-policy.md)
