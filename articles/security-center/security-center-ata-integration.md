---
title: Ansluta Microsoft Advanced Threat Analytics till Azure Security Center | Microsoft Docs
description: "Lär dig hur Azure Security Center kan integreras med Microsoft Advanced Threat Analytics."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: e1b9e598af3b55c1d9591e5c1e529a80ae3319ca
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Ansluta Microsoft Advanced Threat Analytics till Azure Security Center
Det här dokumentet hjälper dig att konfigurera integration mellan Microsoft Advanced Threat Analytics och Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>Varför ska du lägga till data för Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) är en lokal plattform som hjälper dig att identifiera misstänkta användarbeteenden. När du är ansluten, kan du visa misstänkta åtgärder som identifieras av ATA i Security Center. Den här integreringen kan du visa, korrelera och undersök alla säkerhetsaviseringar som rör hybrid cloud arbetsbelastningar i Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Hur konfigurerar jag den här integreringen?
Under förutsättning att du redan har ATA installerat och fungerar korrekt lokalt, Följ dessa steg om du vill konfigurera den här integreringen:

1. Logga in på ATA Center och åtkomst till ATA-portalen.
2. Klicka på **Syslog-servern** i den vänstra rutan.

    ![Syslog-servern](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. I den **Syslog-serverslutpunkt** fältet 127.0.0.7 (det måste vara den här adressen) och Skriv skriver 5114 på port (rekommenderas). Portnumret är en rekommendation, ska alla unika portar fungera. Lämna övriga alternativ som är och på **spara**.
4. Klicka på **meddelanden** i den vänstra rutan, och aktivera alla Syslog-meddelanden (rekommenderas) enligt följande bild:

    ![Meddelanden](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Klicka på **Spara**.
6. Öppna instrumentpanelen **Security Center**.
7. I den vänstra rutan klickar du på **säkerhetslösningar**.
8. Under **Advanced Threat Analytics**, klickar du på **lägga till**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Gå till den sista steg och klickar på **Download agent**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. I den **Lägg till nya Azure-datorn** väljer arbetsytan.

    ![Azure-](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. I den **direkt Agent** sidan, Hämta lämplig Windows-agent och anteckningar för den **arbetsyte-ID** och **primärnyckel**.

    ![Styr agent](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Installera agenten i ATA Center. Under installationen, se till att välja alternativet **ansluta agenten till Azure logganalys (OMS)**, och ange den *arbetsyte-ID*, och *primärnyckel* begäran .


När du slutför installationen integrationen har slutförts och du kommer att kunna se nya aviseringar som skickas från ATA till Security Center i **säkerhetsaviseringar** och **Sök**. Lösningen visas i den **säkerhetslösningar** sidan under **anslutna lösningar**. 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet du har lärt dig hur du ansluter Microsoft ATA till Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Ansluta Azure Active Directory Identity Protection till Azure Security Center](security-center-aadip-integration.md)
* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md) – Lär dig hur data hanteras och garanteras i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Azure security nyheter och information.


