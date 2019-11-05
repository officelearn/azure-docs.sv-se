---
title: Introduktion till Azure Web Application-brandvägg
description: Den här artikeln innehåller en översikt över brand väggen för Azure Web Application (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502046"
---
# <a name="what-is-azure-web-application-firewall"></a>Vad är Azure-brandväggen för webbaserade program?

Brand vägg för webbaserade program (WAF) tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. SQL-inmatning och Cross-Site-skript är bland de vanligaste angrepp.

![Översikt över WAF](media/overview/wafoverview.png)

Det är svårt att förhindra sådana angrepp i program koden. Det kan kräva rigoröst underhåll, uppdatering och övervakning på flera skikt i programtopologin. En centraliserad brand vägg för webbaserade program gör säkerhets hanteringen mycket enklare. En WAF ger också program administratörer bättre garantier för skydd mot hot och intrång.

En WAF-lösning kan reagera på ett säkerhetshot snabbare genom att centralt korrigera en känd sårbarhet, i stället för att skydda varje enskilt webb program.

WAF kan distribueras med Azure Application Gateway och Azures frontend-tjänst. För närvarande har WAF funktioner som är anpassade för varje enskild tjänst. Mer information om WAF-funktioner för varje tjänst finns i översikten för varje tjänst.

## <a name="next-steps"></a>Nästa steg

- Mer information om brand vägg för webbaserade program på Application Gateway finns i [brand vägg för webbaserade program på Azure Application Gateway](./ag/ag-overview.md).
- Mer information om brand vägg för webbaserade program på Azures frontend-tjänst finns i [brand vägg för webbaserade program i Azures frontend-tjänst](./afds/afds-overview.md).
