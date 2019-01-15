---
title: Rekommendationer för behållare i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver Azure Security Center-rekommendationer för hur du kan skydda dina behållare.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: b5cb1fe623062816955278da7b0a9e63cbc19254
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258483"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Förstå rekommendationer för Azure Security Center-behållare

När du migrerar dina gammal program att köra verksamhetskritiska molnbaserade program i behållare i produktion, du kan dra nytta av funktionerna i behållare, inklusive enkel och snabb distribution och uppdatering. När antalet behållare som distribueras fortsätter att öka, måste säkerhetslösningar vara på plats för att ge dig insyn i säkerhetsläget för dina behållare och skydda dem mot hot.

Azure Security Center innehåller följande funktioner för att skydda dina behållare:

- **Insyn i behållare som finns på IaaS Linux-datorer**<br>I Azure Security Center visar fliken behållare alla virtuella datorer som distribueras med Docker. När du utforskar säkerhetsproblem på en virtuell dator innehåller ytterligare information som rör behållare på dator, till exempel Docker-versionen och antalet avbildningar som körs på värden i Security Center.

    ![behållare-fliken](./media/security-center-container-recommendations/docker-recommendation.png)


- **Säkerhetsrekommendationer baserat på tillgängligheten för CIS-benchmark för Docker**<br>Security Center söker igenom din Docker-konfigurationer och ger dig insyn i felkonfigurationer genom att tillhandahålla en lista över alla misslyckade regler som har utvärderats. Security Center innehåller riktlinjer för att hjälpa dig att lösa dessa problem snabbt och spara tid. Security Center utvärderar Docker-konfigurationer och ger dig det senaste tillståndet kontinuerligt.

    ![behållare-fliken](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Identifiering av hot i realtid behållare**<br> Security Center tillhandahåller i realtid hotidentifiering för dina behållare på Linux-datorer med AuditD-komponenten. Aviseringarna som identifierar flera misstänkta Docker-aktiviteter, till exempel att skapa en privilegierad behållare på en indikation på Secure Shell (SSH)-server som körs i en Docker-behållare eller användning av kryptografi datautvinnare-värden. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina behållare.

    ![behållare-fliken](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Rekommendationer
Använd tabellen nedan som referens för att förstå tillgängliga behållare på IaaS Linux-datorer och säkerhetsbedömning för sina Docker-konfigurationer.

| Rekommendation | Beskrivning | Åtgärd |
| --- | --- | --- |
|Åtgärda sårbarheter i containerns säkerhetskonfigurationer |Åtgärda sårbarheter i behållaren säkerhetskonfigurationer baserat på bästa metoderna för konfiguration.| Åtgärda sårbarheter i säkerhetskonfigurationer behållare:<br>1. Granska listan över misslyckade regler.<br>2. Åtgärda varje regel enligt angivna instruktionerna.|


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.

