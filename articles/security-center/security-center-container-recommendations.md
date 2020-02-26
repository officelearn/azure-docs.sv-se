---
title: Rekommendationer för behållare i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver Azure Security Center rekommendationer för hur du kan skydda dina behållare.
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
ms.openlocfilehash: b8b8b05f703a3eb05936ca95e2047a13650914cf
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604262"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Förstå rekommendationer för Azure Security Center container

När du migrerar dina monolit-program till att köra verksamhets kritiska, molnbaserad molnbaserade program i produktion, kan du dra nytta av funktionerna i behållare, inklusive enkel och snabb distribution och uppdatering. När antalet behållare som distribueras fortsätter att öka måste säkerhetslösningarna vara på plats för att ge dig insyn i säkerhets läget för dina behållare och hjälpa till att skydda dem mot hot.

Azure Security Center tillhandahåller följande funktioner som hjälper dig att skydda dina behållare:

- **Insyn i behållare som finns på IaaS Linux-datorer**<br>I Azure Security Center visar fliken behållare alla virtuella datorer som har distribuerats med Docker. När du utforskar säkerhets problemen på en virtuell dator innehåller Security Center ytterligare information som är relaterad till behållarna på datorn, till exempel Docker-versionen och antalet avbildningar som körs på värden.

    ![fliken container](./media/security-center-container-recommendations/docker-recommendation.png)


- **Säkerhets rekommendationer baserat på CIS-benchmark för Docker**<br>Security Center söker igenom dina Docker-konfigurationer och ger dig insyn i felkonfigurationer genom att tillhandahålla en lista över alla misslyckade regler som har utvärderats. Security Center innehåller rikt linjer som hjälper dig att snabbt lösa problemen och spara tid. Security Center utvärderar Docker-konfigurationer och ger dig det senaste tillståndet kontinuerligt.

    ![fliken container](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Hot identifiering för behållare i real tid**<br> Security Center tillhandahåller hot identifiering i real tid för dina behållare på Linux-datorer med granskad komponent. Aviseringarna identifierar flera misstänkta Docker-aktiviteter, till exempel skapandet av en privilegie rad behållare på värden, en indikation på SSH-servern (Secure Shell) som körs i en Docker-behållare eller användning av kryptografiska gruv arbetare. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

    ![fliken container](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Rekommendationer
Använd tabellerna nedan som referens för att hjälpa dig att förstå de tillgängliga behållare som finns på IaaS Linux-datorer och säkerhets utvärdering av sina Docker-konfigurationer.

| Rekommendation | Beskrivning | Åtgärd |
| --- | --- | --- |
|Åtgärda sårbarheter i säkerhets konfigurationerna för behållare |Åtgärda sårbarheter i säkerhets konfigurationerna för behållare baserat på rekommenderade konfigurations metoder.| Så här åtgärdar du säkerhets risker i säkerhets konfigurationerna för behållare:<br>1. Granska listan över misslyckade regler.<br>2. korrigera varje regel enligt de instruktioner som anges.|


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.