---
title: "Skydda SQL Azure-tjänsten och data i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet behandlar rekommendationerna i Azure Security Center som hjälper dig skydda dina data och Azure SQL-tjänsten och vara kompatibla med säkerhetsprinciper."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Skydda SQL Azure-tjänsten och data i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsrisker, skapar rekommendationer som hjälper dig att konfigurera nödvändiga kontroller.  Rekommendationer gäller för Azure resurstyper: virtuella datorer (VM), nätverk, SQL och data och program.

Den här artikeln tar rekommendationer som gäller för Azure SQL-tjänsten och data. Rekommendationer center runt aktivera granskning för Azure SQL-servrar och databaser, aktivera kryptering för SQL-databaser och aktivera kryptering av Azure storage-konto.  Använd tabellen nedan som referens för att hjälpa dig att förstå de tillgängliga SQL-tjänst och data rekommendationerna och det var och en gör om du använder den.

## <a name="available-sql-service-and-data-recommendations"></a>Tillgängliga rekommendationer för SQL-tjänsten och data
| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera granskning och hotidentifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hotidentifiering identifiering för Azure SQL-servrar (Azure SQL-tjänsten; inte omfattar SQL som körs på virtuella datorer). |
| [Aktivera granskning och hotidentifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hotidentifiering identifiering för Azure SQL-databaser (Azure SQL-tjänsten; inte omfattar SQL som körs på virtuella datorer). |
| [Aktivera Transparent datakryptering på SQL-databaser](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser (endast Azure SQL-tjänsten). |

## <a name="see-also"></a>Se även
Mer information om rekommendationer som gäller för andra typer av Azure finns i:

* [Skydda dina virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda dina program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda ditt nätverk i Azure Security Center](security-center-network-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
