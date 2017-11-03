---
title: Aktivera Transparent datakryptering i Azure Security Center | Microsoft Docs
description: "Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center ** aktivera Transparent Data kryptering **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Aktivera Transparent datakryptering i Azure Security Center
Azure Security Center kommer rekommenderar att du aktiverar Transparent Data kryptering (TDE) på SQL-databaser om TDE inte redan är aktiverad. TDE skyddar dina data och hjälper dig att uppfylla efterlevnadskrav genom att kryptera din databas, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att ändra ditt program. Läs mer finns [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Den här rekommendationen gäller Azure SQL-tjänsten. innehåller SQL som körs på virtuella datorer.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera Transparent datakryptering**.
   ![Aktivera transparent datakryptering][1]
2. Då öppnas den **aktivera Transparent datakryptering på SQL-databaser** bladet. Välj en SQL-databas för att aktivera TDE på.
   ![Välj SQL-databas för att aktivera TDE på][2]
3. På den **Transparent datakryptering** bladet väljer **ON** under datakryptering och välj **spara** på menyfliken överst på bladet.
   ![Aktivera TDE][3]

   När TDE har aktiverats på den valda SQL-databasen i **krypteringsstatus** ändras till **krypterade**.    

   ![Krypteringsstatus][4]

## <a name="see-also"></a>Se även
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”aktivera Transparent datakryptering”. Mer information om SQL TDE finns i:

* [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Kom igång med Transparent Data kryptering (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --Azure security nyheter och information.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
