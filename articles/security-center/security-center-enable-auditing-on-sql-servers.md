---
title: "Aktivera granskning och hotidentifiering identifiering på SQL-servrar i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center ** aktivera Auditing & Threat detection på SQL-servrar **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 660b537aef8d175a478ff93d60b8391d55fc92ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Aktivera granskning och hotidentifiering identifiering på SQL-servrar i Azure Security Center
Azure Security Center rekommenderar att du aktiverar granskning och hotidentifiering för alla databaser på Azure SQL-servrar om granskning inte redan är aktiverad. Granskning och hotidentifiering identifiering kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

När du har aktiverat granskning kan du konfigurera Hotidentifiering inställningar och e-postmeddelanden för att ta emot säkerhetsaviseringar. Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. På så sätt kan du identifiera och svara på potentiella hot när de inträffar.

Den här rekommendationen gäller Azure SQL-tjänsten. Det innehåller inte SQL Server på virtuella datorer i Azure Infrastructure Services (Azure IaaS).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera Auditing & Threat detection på SQL-servrar**.  Då öppnas den **aktivera Auditing & Threat detection på SQL-servrar** bladet.

   ![Aktivera granskning på SQL-servrar][1]
2. Välj en SQL-server för att aktivera granskning och hotidentifiering på. Då öppnas den **granskning och Hotidentifiering** bladet.

3. På den **granskning och Hotidentifiering** bladet väljer **ON** under **granskning**.

   ![Aktivera granskningsinställningar][2]
4. Följ stegen i [SQL database auditing i Azure portal](../sql-database/sql-database-auditing-portal.md) du konfigurerar lagring där din granskningsloggar ska lagras. Prenumerationens lagringskonto för datainsamling är standardkontot för lagring.
5. Följ stegen i [Kom igång med SQL-databasen Hotidentifiering](../sql-database/sql-database-threat-detection.md) att aktivera och konfigurera Hotidentifiering och konfigurera en lista över e-postmeddelanden som ska ta emot säkerhetsaviseringar vid identifiering av avvikande aktiviteter.

## <a name="see-also"></a>Se även
Den här artikeln visar dig hur du implementerar Security Center rekommendation ”aktivera granskning och hotidentifiering identifieringen på SQL-servrar”. Mer information om att skydda din SQL-databas finns i:

* [Säkra din SQL Database](../sql-database/sql-database-security-overview.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --Azure security nyheter och information.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
