---
title: Aviseringar om hot identifiering från Azures säkerhets produkter i Azure Security Center
description: I det här avsnittet presenteras de Azure-säkerhetsprodukter från vilka Azure Security Center kan visa hot aviseringar
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913028"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Aviseringar om hot identifiering från Azure WAF och Azure DDoS Protection

Azure Security Center kan visa och samla in hot identifierings aviseringar som genererats av följande Azure-säkerhetsprodukter, (en separat licens för varje produkt krävs):

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure-WAF<a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway-WAF baseras på kärn regel uppsättningen 3,0 eller 2.2.9 från det öppna säkerhets projektet för webb program. WAF uppdateras automatiskt för att skydda mot nya sårbarheter, utan ytterligare konfiguration som behövs. WAF-aviseringar strömmas till Security Center. Mer information om de aviseringar som genererats av WAF finns i [regel grupper och regler för webb programs brand vägg](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-attacker (distributed denial of Service) är kända för att vara lätta att köra. De har blivit ett bra säkerhets problem, särskilt om du flyttar dina program till molnet. 

En DDoS-attack syftar till att göra slut på ett programs resurser, så att programmet blir otillgängligt för behöriga användare. DDoS-attacker kan rikta in sig på alla slut punkter som kan nås via Internet.

Azure DDoS Protection kombineras med bästa praxis för program design, vilket ger ett försvar mot DDoS-attacker. DDoS Protection tillhandahåller olika tjänst nivåer. Mer information finns i [Azure DDoS Protection översikt](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

En lista över Azure DDoS Protection aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureddos).