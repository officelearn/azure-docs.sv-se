---
title: Delat ansvar i molnet – Microsoft Azure
description: Förstå modellen med delat ansvar och vilka säkerhetsuppgifter som hanteras av molnleverantören och vilka uppgifter som hanteras av dig.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518374"
---
# <a name="shared-responsibility-in-the-cloud"></a>Delat ansvar i molnet

När du överväger och utvärderar offentliga molntjänster är det viktigt att förstå modellen med delat ansvar och vilka säkerhetsuppgifter som hanteras av molnleverantören och vilka uppgifter som hanteras av dig. Arbetsbelastningsansvaret varierar beroende på om arbetsbelastningen finns på Software as a Service (SaaS), Platform as a Service (PaaS), Infrastructure as a Service (IaaS) eller i ett lokalt datacenter

## <a name="division-of-responsibility"></a>Ansvarsfördelning
I ett lokalt datacenter äger du hela stacken. När du flyttar till molnet överförs vissa ansvarsområden till Microsoft. Följande diagram illustrerar ansvarsområdena mellan dig och Microsoft, beroende på vilken typ av distribution av din stack.

![Ansvarszoner](./media/shared-responsibility/shared-responsibility.png)

Du äger dina data och identiteter oavsett typen av molndistribution. Du är ansvarig för att skydda säkerheten för dina data och identiteter, lokala resurser och de molnkomponenter du styr (som varierar beroende på tjänsttyp).

Oavsett typ av distribution behålls alltid följande ansvarsområden av dig:

- Data
- Slutpunkter
- Konto
- Åtkomsthantering

## <a name="cloud-security-advantages"></a>Fördelar med molnsäkerhet
Molnet erbjuder betydande fördelar för att lösa långvariga informationssäkerhetsutmaningar. I en lokal miljö har organisationer sannolikt ouppfyllda ansvarsområden och begränsade resurser tillgängliga för att investera i säkerhet, vilket skapar en miljö där angripare kan utnyttja sårbarheter på alla lager.

Följande diagram visar en traditionell metod där många säkerhetsansvar inte uppfylls på grund av begränsade resurser. I den molnaktiverade metoden kan du flytta dagliga säkerhetsansvar till din molnleverantör och omfördela dina resurser.

![Säkerhetsfördelar med molneran](./media/shared-responsibility/cloud-enabled-security.png)

I den molnaktiverade metoden kan du också utnyttja molnbaserade säkerhetsfunktioner för mer effektivitet och använda molninformation för att förbättra din hotidentifiering och svarstid. Genom att flytta ansvar till molnleverantören kan organisationer få mer säkerhetstäckning, vilket gör att de kan omfördela säkerhetsresurser och budget till andra affärsprioriteringar.

## <a name="next-steps"></a>Nästa steg
Mer information om ansvarsfördelningen mellan dig och Microsoft i en SaaS-, PaaS- och IaaS-distribution finns i [Delat ansvar för molnbaserad databehandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
