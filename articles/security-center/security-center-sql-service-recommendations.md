---
title: Skydda Azure SQL-tjänst och data i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänst och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0b3b8082412b12a0fffbaea04409a8bbb3f4ac15
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295385"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Skydda Azure SQL-tjänst och data i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs.  Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM), nätverk, SQL och data och program.

Den här artikeln belyser rekommendationer som gäller för Azure SQL-tjänst och data. Rekommendationer center för att aktivera granskning för Azure SQL-servrar och databaser, att aktivera kryptering för SQL-databaser och aktivering av kryptering för Azure storage-kontot.  Använd tabellen nedan som referens för att förstå de tillgängliga rekommendationerna för SQL-tjänsten och data och vad var och en gör om du använder den.

## <a name="available-sql-service-and-data-recommendations"></a>Tillgängliga rekommendationer för SQL-tjänsten och data
| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera granskning och hotidentifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hotidentifiering för Azure SQL-servrar (Azure SQL-tjänsten inte omfattar SQL som körs på dina virtuella datorer). |
| [Aktivera granskning och hotidentifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hotidentifiering för Azure SQL-databaser (Azure SQL-tjänsten inte omfattar SQL som körs på dina virtuella datorer). |
| [Aktivera Transparent datakryptering på SQL-databaser](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser (endast Azure SQL-tjänst). |

## <a name="see-also"></a>Se också
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Skydda virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
