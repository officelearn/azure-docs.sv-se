---
title: Ansluta Microsoft Advanced Threat Analytics till Azure Security Center | Microsoft Docs
description: Lär dig hur Azure Security Center kan integreras med Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 49d6e2ded2ad4b2e5bb0ee30c323b1f459422437
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338404"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Ansluta Microsoft Advanced Threat Analytics till Azure Security Center
Det här dokumentet hjälper dig att konfigurera integration mellan Microsoft Advanced Threat Analytics och Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>Varför ska du lägga till data i Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) är en lokal plattform som hjälper dig att identifiera misstänkta användarbeteenden. När du är ansluten, kan du visa misstänkta åtgärder som identifieras av ATA i Security Center. Den här integrationen kan du visa, korrelera och undersöka alla säkerhetsaviseringar som är relaterade till dina hybridmolnarbetsbelastningar i Security Center.

## <a name="how-do-i-configure-this-integration"></a>Hur konfigurerar jag den här integreringen?
Om vi antar att du redan har ATA installerat och fungerar korrekt på plats, följer du stegen nedan för att konfigurera den här integreringen:

1. Logga in på ATA Center och få åtkomst till ATA-portalen.
2. Klicka på **Syslog-servern** i den vänstra rutan.

    ![Syslog-servern](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. I den **Syslog-serverslutpunkt** fältet, skriver du 127.0.0.7 (det måste vara den här adressen) och skriver 5114 på porten (rekommenderas). Portnumret är en rekommendation, bör valfri unik port fungera. Lämna övriga alternativ vara och klicka på **spara**.
4. Klicka på **meddelanden** i den vänstra rutan, och aktivera alla Syslog-aviseringar (rekommenderas) enligt följande bild:

    ![Meddelanden](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Klicka på **Spara**.
6. Öppna instrumentpanelen **Security Center**.
7. På den vänstra rutan klickar du på **säkerhetslösningar**.
8. Under **Advanced Threat Analytics**, klickar du på **lägga till**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)

9. Gå till den sista steg och klickar på **ladda ned agenten**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. I den **Lägg till nya Azure-datorn** väljer arbetsytan.

    ![Icke-Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. I den **Direktagent** sidan, Hämta lämplig Windows-agent och ner den **arbetsyte-ID** och **primärnyckel**.

    ![Direktagent](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Installera agenten i ATA Center. Under installationen, se till att välja alternativet **Anslut agenten till Azure Log Analytics**, och ange den *arbetsyte-ID*, och *primärnyckel* vid begäran.


När du är klar med installationen, integrationen har slutförts och du kommer att kunna se nya aviseringar som skickas från ATA till Security Center i den **Search** resultatet. Lösningen visas i den **säkerhetslösningar** sidan under **anslutna lösningar**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig att ansluta Microsoft ATA till Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Ansluta Azure Active Directory Identity Protection till Azure Security Center](security-center-aadip-integration.md)
* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md) – Lär dig hur data hanteras och skyddas i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.
