---
title: Introduktion till Azure Web Application-brandvägg
description: Den här artikeln innehåller en översikt över brand väggen för Azure Web Application (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851184"
---
# <a name="what-is-azure-web-application-firewall"></a>Vad är Azure-brandväggen för webbaserade program?

Brandväggen för webbaserade program (WAF) tillhandahåller ett centraliserat skydd för dina webbprogram mot vanliga sårbarheter och hot. Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. SQL-inmatning och Cross-Site-skript är bland de vanligaste angrepp.

![Översikt över WAF](media/overview/wafoverview.png)

Det är svårt att förhindra sådana angrepp i program koden. Det kan kräva rigoröst underhåll, uppdatering och övervakning på flera skikt i programtopologin. En centraliserad brand vägg för webbaserade program gör säkerhets hanteringen mycket enklare. En WAF ger också program administratörer bättre garantier för skydd mot hot och intrång.

En WAF-lösning kan reagera på ett säkerhetshot snabbare genom att centralt korrigera en känd sårbarhet, i stället för att skydda varje enskilt webb program.

## <a name="supported-services"></a>Tjänster som stöds

WAF kan distribueras med [Azure Application Gateway](../application-gateway/overview.md) och [Azures frontend-tjänst](../frontdoor/front-door-overview.md). Båda tjänsterna är belastningsutjämnare för Layer-7 (HTTP/S), men Application Gateway är en regional tjänst och en front dörr är en global tjänst. WAF har funktioner som är anpassade för varje enskild tjänst.

Mer information finns i Översikt över WAF för varje tjänst.

## <a name="next-steps"></a>Nästa steg

- Mer information om brand vägg för webbaserade program på Application Gateway finns i [brand vägg för webbaserade program på Azure Application Gateway](./ag/ag-overview.md).
- Mer information om brand vägg för webbaserade program i Azures frontend-tjänst finns i [brand vägg för webbaserade program i Azures frontend-tjänst](./afds/afds-overview.md).
