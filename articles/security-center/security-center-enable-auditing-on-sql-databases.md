---
title: Aktivera granskning och hotidentifiering på SQL-databaser i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen **aktivera granskning och hotidentifiering på SQL-databaser**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1108265101f37433860d0112e4e80aee0002ab5c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127233"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Aktivera granskning och hotidentifiering på SQL-databaser i Azure Security Center
Azure Security Center rekommenderar att du aktiverar granskning och hotidentifiering för alla SQL-databaser om granskning och hotidentifiering inte redan är aktiverad. Granskning och identifiering kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

När du har aktiverat granskning kan du konfigurera inställningarna för Hotidentifiering och e-postmeddelanden om du vill få säkerhetsaviseringar. Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. På så sätt kan du identifiera och svara på potentiella hot allteftersom de sker.

Den här rekommendationen gäller för Azure SQL-tjänsten. de omfattar inte SQL som körs på dina virtuella datorer.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera granskning på SQL-databaser**.  Då öppnas det **aktivera granskning på SQL-databaser** bladet.

   ![Aktivera granskning på SQL-databaser][1]
2. Välj en SQL-databas för att aktivera granskning på. Då öppnas det **granskning och Hotidentifiering** bladet.

3. På den **granskning och Hotidentifiering** bladet väljer **på** under **granskning**.

   ![Aktivera granskning och hotidentifiering][2]
4. Följ stegen i [Hotidentifiering för SQL Database i Azure-portalen](../sql-database/sql-database-threat-detection-portal.md) att aktivera och konfigurera identifiering av hot och konfigurera listan över e-postmeddelanden som ska få säkerhetsaviseringar när avvikande aktiviteter.

## <a name="see-also"></a>Se också
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”aktivera granskning och Hotidentifiering identifiering på SQL-databaser”. Mer information om hur du skyddar din SQL-databas finns följande:

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
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
