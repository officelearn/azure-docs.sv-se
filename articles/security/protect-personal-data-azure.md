---
title: Skydda personliga data i Microsoft Azure | Microsoft Docs
description: "Den här artikeln hjälper dig att använda Azure att skydda personliga data och överensstämma med allmänna Data Protection förordning (BNPR)"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 741fb17be315faacef6483cbaaa565136622cb45
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Skydda personliga data i Microsoft Azure

Den här artikeln introducerar ett antal artiklar som hjälper dig använda Azure säkerhetstekniker och tjänster för att skydda personliga data. Detta är ett krav för många företag och industrin efterlevnad och styrning initiativ. Exempelvis kan du använda informationen i den här serien med artiklar i enlighet med allmänna Data Protection förordning (BNPR). Scenario, problem och företagets mål ingår.

## <a name="scenario-and-problem-statement"></a>Scenariot och problembeskrivningen

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavet, Adriatiska havet, baltiska havet samt Förenta staterna. För att stödja dessa ansträngningar genererade den flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien

Företaget använder Microsoft Azure för att lagra företagets data i molnet. Detta kan inkludera kund- och information som:

- adresser
- telefonnummer
- skatt ID-nummer
- kreditkortsinformation

Företaget måste skydda sekretessen för kund- och data när du gör data tillgängliga för de avdelningar som behöver den. (till exempel löneuppgifter och -reservationer avdelningar)

## <a name="company-goals"></a>Företagets mål 

- Datakällor som innehåller personuppgifter krypteras när som finns i molnet.

- Personliga data som överförs från en plats till en annan är krypterad under överföringen. Detta är SANT om data färdas över det virtuella nätverket eller via Internet mellan företagets datacenter och Azure-molnet.

- Sekretess och integriteten för personliga data är skyddat från obehörig åtkomst av starka Identitetshantering och tekniker för åtkomstkontroll.

- Personliga data skyddas mot exponering via data intrång via övervakning för säkerhetsrisker och hot.

- Säkerhetstillståndet hos Azure-tjänster som lagrar eller skickar personliga data används för att utvärdera om du vill identifiera möjligheterna till att bättre skydda personliga data.

## <a name="data-protection-guidance"></a>Data protection vägledning

I följande artiklar innehåller teknisk vägledning som hjälper dig uppnå personliga data protection mål i listan ovan:

- [Azure krypteringstekniker](protect-personal-data-at-rest.md)

- [Azure krypteringstekniker](protect-personal-data-in-transit-encryption.md)

- [Azure identitets- och -tekniker](protect-personal-data-identity-access-controls.md)

- [Säkerhetstekniker i Azure-nätverk](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Nästa steg

- [Azure-säkerhet Information plats](https://aka.ms/AzureSecInfo)

- [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Azure-säkerhetsteamets blogg](https://www.azuresecurityorg)

- [Azure.com blogg - säkerhet](https://azure.microsoft.com/blog/topics/security/)
