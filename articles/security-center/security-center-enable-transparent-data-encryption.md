---
title: Aktivera transparent datakryptering i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du implementerar Azure Security Center rekommendation **aktivera Transparent datakryptering**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704040"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Aktivera transparent datakryptering i Azure Security Center
Azure Security Center rekommenderar att du aktiverar transparent datakryptering (TDE) på SQL-databaser om TDE inte redan är aktiverat. TDE skyddar dina data och hjälper dig att uppfylla kraven för efterlevnad genom att kryptera din databas, tillhör ande säkerhets kopior och transaktionsloggfiler i vila, utan att kräva ändringar i programmet. Läs mer i [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Den här rekommendationen gäller endast för Azure SQL-tjänsten. inkluderar inte SQL som körs på dina virtuella datorer.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. På bladet **rekommendationer** väljer du **Aktivera Transparent datakryptering**.
   ![Aktivera transparent datakryptering][1]
2. Då öppnas bladet **aktivera Transparent datakryptering på SQL-databaser** . Välj en SQL-databas för att aktivera TDE.
   ![Välj SQL-databas för att aktivera TDE på][2]
3. På bladet **transparent data kryptering** väljer du under data kryptering och väljer **Spara** i det övre menyfliksområdet på bladet.
   ![Aktivera TDE][3]

   När TDE har Aktiver ATS för den valda SQL-databasen ändras **krypterings statusen** till **krypterad**.    

   ![Krypteringsstatus][4]

## <a name="see-also"></a>Se också
I den här artikeln visar vi hur du implementerar Security Center rekommendation "Aktivera transparent datakryptering". Mer information om SQL-TDE finns i följande avsnitt:

* [transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Kom igång med transparent datakryptering (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hämta de senaste nyheterna och informationen om Azure-säkerhet.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
