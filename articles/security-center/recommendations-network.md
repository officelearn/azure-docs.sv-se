---
title: Azure Security Center rekommendationer för nätverk
description: Den här artikeln innehåller Azure Security Center säkerhets rekommendationer som hjälper dig att skydda dina nätverks resurser.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781974"
---
# <a name="network-recommendations---reference-guide"></a>Nätverks rekommendationer – referens guide

Den här artikeln innehåller en fullständig lista över de rekommendationer som du kan se i Azure Security Center om nätverkets topologi och slut punkter mot Internet.

En förklaring av hur du hittar dessa och hur du löser dem finns [här](security-center-network-recommendations.md).

## <a name="network-recommendations"></a>Nätverksrekommendationer

|Rekommendations namn|Beskrivning|Allvarsgrad|Säkerhetspoäng|Resurstyp|
|----|----|----|----|----|----|
|Nätverks säkerhets grupper på under näts nivån måste vara aktiverade|Aktivera nätverks säkerhets grupper för att kontrol lera nätverks åtkomst till resurser som har distribuerats i dina undernät.|Hög/medel|30|Undernät|
|Virtuella datorer ska associeras med en nätverks säkerhets grupp|Aktivera nätverks säkerhets grupper för att kontrol lera nätverks åtkomst för dina virtuella datorer.|Hög/medel|30|Virtuell dator|
|Åtkomsten ska begränsas för tillåtade nätverks säkerhets grupper med virtuella datorer som riktas mot Internet|Skärp nätverks säkerhets grupperna för dina Internet-riktade virtuella datorer genom att begränsa åtkomsten till dina befintliga Tillåt-regler.|Hög|20|Virtuell dator|
|Reglerna för webb program på IaaS NSG: er bör vara härdade|Skärp nätverks säkerhets gruppen (NSG) för dina virtuella datorer som kör webb program, med NSG-regler som kan tillåtas med avseende på webb program portar.|Hög|20|Virtuell dator|
|Åtkomst till App Services bör vara begränsad|Begränsa åtkomsten till din App Services genom att ändra nätverks konfigurationen för att neka inkommande trafik från intervall som är för breda.|Hög|10|App Service|
|Hanterings portar bör stängas på dina virtuella datorer|Skärp nätverks säkerhets gruppen för dina virtuella datorer för att begränsa åtkomsten till hanterings portar.|Hög|10|Virtuell dator|
DDoS Protection standard ska vara aktive rad|Skydda virtuella nätverk som innehåller program med offentliga IP-adresser genom att aktivera DDoS Protection Service standard. DDoS Protection möjliggör minskning av nätverks-och protokoll attacker.|Hög|10|Virtuellt nätverk|
|IP-vidarebefordran på den virtuella datorn bör inaktive ras|Inaktivera IP-vidarebefordring. När IP-vidarebefordring är aktiverat på en virtuell dators nätverkskort kan datorn ta emot trafik som är adresserad till andra mål. IP-vidarebefordran krävs sällan (till exempel när du använder den virtuella datorn som en virtuell nätverks installation) och därför bör detta granskas av nätverks säkerhets teamet.|Medium|10|Virtuell dator|
|Webb program bör endast vara tillgängliga via HTTPS|Aktivera "endast HTTPS"-åtkomst för webb program. Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå.|Medium|20|Webbprogram|
|Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer|Använd just-in-Time (JIT)-åtkomst kontroll för att permanent låsa åtkomsten till valda portar och aktivera behöriga användare för att öppna dem via JIT, under en begränsad tid.|Hög|20|Virtuell dator|
|Function-appar bör endast vara tillgängliga via HTTPS|Aktivera "endast HTTPS"-åtkomst för Function Apps. Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå.|Medium|20|Funktionsapp|
|Säker överföring till lagrings konton ska vara aktiverat|Aktivera säker överföring till lagrings konton. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från angrepp på nätverks nivå, till exempel man-in-the-Middle, avlyssning och session-kapning.|Hög|20|Lagringskonto|


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande avsnitt:

* [Övervaka identitet och åtkomst](security-center-identity-access.md)
* [Skydda dina datorer och program](security-center-virtual-machine-protection.md)
* [Skydda din Azure SQL-tjänst](security-center-sql-service-recommendations.md)

