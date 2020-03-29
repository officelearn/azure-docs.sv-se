---
title: Behållarrekommendationer i Azure Security Center | Microsoft-dokument
description: I det här dokumentet beskrivs rekommendationerna för Azure Security Center för hur du skyddar dina behållare.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912370"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Förstå rekommendationer för Azure Security Center-container

När du migrerar dina monolitprogram för att köra verksamhetskritiska, containeriserade molnbaserade program i produktion kan du dra nytta av funktionerna i behållare, inklusive enkel och snabb distribution och uppdatering. I takt med att antalet distribuerade containrar fortsätter att öka måste säkerhetslösningar finnas på plats för att ge dig insyn i säkerhetstillståndet för dina behållare och skydda dem från hot.

Azure Security Center innehåller följande funktioner som hjälper dig att skydda dina behållare:

- **Insyn i behållare som finns på IaaS Linux-maskiner**<br>I Azure Security Center visar fliken Behållare alla virtuella datorer som distribueras med Docker. När du utforskar säkerhetsproblemen på en virtuell dator innehåller Security Center ytterligare information om behållarna på datorn, till exempel Docker-versionen och antalet avbildningar som körs på värden.

    ![fliken behållare](./media/security-center-container-recommendations/docker-recommendation.png)


- **Säkerhetsrekommendationer baserade på CIS-riktmärke för Docker**<br>Security Center söker igenom dina Docker-konfigurationer och ger dig insyn i felkonfigurationer genom att tillhandahålla en lista över alla misslyckade regler som har utvärderats. Security Center innehåller riktlinjer som hjälper dig att lösa dessa problem snabbt och spara tid. Security Center utvärderar Docker-konfigurationer och ger dig det senaste tillståndet kontinuerligt.

    ![fliken behållare](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Skydd mot hotskydd i realtid**<br> Security Center ger hotskydd i realtid för dina behållare på Linux-datorer med AuditD-komponent. Aviseringarna identifierar flera misstänkta Docker-aktiviteter, till exempel skapandet av en privilegierad behållare på värd, en indikation på Secure Shell (SSH) -server som körs inuti en Docker-behållare eller användning av kryptogruvarbetare. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

    ![fliken behållare](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Rekommendationer
Använd tabellerna nedan som en referens för att hjälpa dig att förstå de tillgängliga behållarna som finns på IaaS Linux-datorer och säkerhetsbedömning av deras Docker-konfigurationer.

| Rekommendation | Beskrivning | Åtgärder |
| --- | --- | --- |
|Åtgärda säkerhetsproblem i behållarens säkerhetskonfigurationer |Åtgärda säkerhetsproblem i behållarens säkerhetskonfigurationer baserat på metodtips för konfiguration.| Så här åtgärdar du säkerhetsproblem i behållarens säkerhetskonfigurationer:<br>1. Granska listan över misslyckade regler.<br>2. Fixera varje regel enligt de angivna instruktionerna.|


## <a name="next-steps"></a>Nästa steg
Mer information om rekommendationer som gäller för andra Azure-resurstyper finns i följande:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Läs om hur du hanterar och svarar på säkerhetsaviseringar.