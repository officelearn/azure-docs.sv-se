---
title: Aktivera granskning och hotidentifiering på SQL-servrar i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen **aktivera granskning på SQL-servrar**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 21d261fc82e7aec6d3d4a8ae0c98d71a686be201
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335970"
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Aktivera granskning och hotidentifiering på SQL-servrar i Azure Security Center
Azure Security Center rekommenderar att du aktiverar granskning och hotidentifiering för alla databaser på din Azure SQL-servrar om granskning inte redan är aktiverad. Granskning och identifiering kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

När du har aktiverat granskning kan du konfigurera inställningarna för Hotidentifiering och e-postmeddelanden om du vill få säkerhetsaviseringar. Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. På så sätt kan du identifiera och svara på potentiella hot allteftersom de sker.

Den här rekommendationen gäller för Azure SQL-tjänsten. de omfattar inte SQL Server som körs på dina virtuella datorer i Azure-infrastrukturtjänster (IaaS Azure).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera granskning på SQL-servrar**.  Då öppnas det **aktivera granskning på SQL-servrar** bladet.

   ![Aktivera granskning på SQL-servrar][1]
2. Välj en SQLServer för att aktivera granskning och hotidentifiering på. Då öppnas det **granskning och Hotidentifiering** bladet.

3. På den **granskning och Hotidentifiering** bladet väljer **på** under **granskning**.

   ![Aktivera granskning inställningar][2]
4. Följ stegen i [SQL database-granskning i Azure-portalen](../sql-database/sql-database-auditing-portal.md) konfigurerar du lagring där dina granskningsloggar kommer att lagras. Prenumerationens storage-konto för insamling av data är standardkontot för lagring.
5. Följ stegen i [Kom igång med SQL Database Threat Detection](../sql-database/sql-database-threat-detection.md) att aktivera och konfigurera identifiering av hot och konfigurera listan över e-postmeddelanden som ska få säkerhetsaviseringar när avvikande aktiviteter.

## <a name="see-also"></a>Se också
Den här artikeln visar dig hur du implementerar de Säkerhetscenter rekommendationen ”aktivera granskning och hotidentifiering på SQL-servrar”. Mer information om hur du skyddar din SQL-databas finns följande:

* [Säkra din SQL Database](../sql-database/sql-database-security-overview.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
