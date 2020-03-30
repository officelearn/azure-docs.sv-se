---
title: Introduktion till Brandvägg för Azure Web Application
description: Den här artikeln innehåller en översikt över Brandvägg för Azure Web Application (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475795"
---
# <a name="what-is-azure-web-application-firewall"></a>Vad är Azure-brandväggen för webbaserade program?

Brandväggen för webbaserade program (WAF) tillhandahåller ett centraliserat skydd för dina webbprogram mot vanliga sårbarheter och hot. Webbprogram blir alltmer inriktade på skadliga attacker som utnyttjar allmänt kända sårbarheter. SQL-injektion och skript över flera webbplatser är några av de vanligaste attackerna.

![WAF översikt](media/overview/wafoverview.png)

Att förhindra sådana attacker i programkoden är en utmaning. Det kan kräva rigoröst underhåll, korrigering och övervakning vid flera lager av programtopologin. En centraliserad brandvägg för webbprogram hjälper till att göra säkerhetshanteringen mycket enklare. En WAF ger också programadministratörer bättre garantier för skydd mot hot och intrång.

En WAF-lösning kan reagera snabbare på ett säkerhetshot genom att centralt korrigera ett känt säkerhetsproblem, i stället för att skydda varje enskilt webbprogram.

## <a name="supported-service"></a>Service som stöds

WAF kan distribueras med Azure Application Gateway, Azure Front Door och Azure Content Delivery Network (CDN) från Microsoft. WAF på Azure CDN är för närvarande under offentlig förhandsversion.  WAF har funktioner som är anpassade för varje specifik tjänst. Mer information om WAF-funktioner för varje tjänst finns i översikten för varje tjänst.

## <a name="next-steps"></a>Nästa steg

- Mer information om brandvägg för webbprogram i Application Gateway finns i [brandväggen för webbprogram på Azure Application Gateway](./ag/ag-overview.md).
- Mer information om brandvägg för webbprogram i Azure Front Door Service finns i [brandväggen för webbprogram i Azure Front Door Service](./afds/afds-overview.md).
- Mer information om brandvägg för webbprogram på Azure CDN Service finns i [brandväggen för webbprogram på Azure CDN-tjänsten](./cdn/cdn-overview.md)
