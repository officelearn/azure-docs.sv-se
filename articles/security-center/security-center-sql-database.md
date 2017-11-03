---
title: "Tjänsten Azure Security Center och Azure SQL Database | Microsoft Docs"
description: "Den här artikeln visar hur Security Center kan hjälpa dig att skydda dina databaser i Azure SQL Database."
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 46dd298a5664d914e55d45c5b7599d5983287476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center och Azure SQL Database-tjänsten
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Den här artikeln visar hur Security Center kan hjälpa dig att skydda dina databaser i Azure SQL Database.

## <a name="why-use-security-center"></a>Varför ska man använda Security Center?
Security Center hjälper dig att skydda data i SQL-databas genom att tillhandahålla insyn i säkerheten för servrar och databaser. Med Security Center kan du:

* Definiera principer för kryptering av SQL-databas och granskning.
* Övervaka säkerheten i SQL-databas resurser över alla prenumerationer.
* Snabbt identifiera och åtgärda säkerhetsproblem.
* Integrera aviseringar från [Azure SQL Database hotidentifiering](../sql-database/sql-database-threat-detection.md).

Förutom att bidra till att skydda din SQL Database-resurser, ger också Security Center säkerhetsövervakning och hantering av virtuella datorer i Azure Cloud Services, Apptjänster, virtuella nätverk och mer. Lär dig mer om Security Center [här](security-center-intro.md).

## <a name="prerequisites"></a>Krav
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Den kostnadsfria nivån av Security Center aktiveras med din prenumeration. Läs mer om Security Center lediga och Standard nivåer [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

Security Center stöder rollbaserad åtkomst. Mer information om rollbaserad åtkomstkontroll (RBAC) i Azure finns [Azure Active Directory-rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md). Vanliga frågor om med Security Center innehåller information om [hur behörigheter ska hanteras i Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Öppna Security Center
Security Center öppnas från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Logga in på portalen](https://portal.azure.com/) och välj den **Security Center alternativet**.

![Security Center-alternativet][1]

Den **Security Center** blad öppnas.
![Security Center-bladet][2]

## <a name="set-security-policy"></a>Ange säkerhetsprincip
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Security Center kan du definiera principer för dina prenumerationer eller resursgrupper enligt ditt företags säkerhetskrav och typ av program eller datakänslighetsnivå i varje prenumeration.

Du kan ange en princip för att visa rekommendationer för SQL-granskning och SQL transparent datakryptering (TDE).

* När du aktiverar **SQL-granskning och Hotidentifiering**, Security Center rekommenderar att granskning av åtkomst till Azure-databas är aktiverat för regelefterlevnad, avancerad identifiering och undersökningsskäl.
* När du aktiverar **SQL transparent datakryptering**, Security Center rekommenderar att kryptering i vila aktiveras för din Azure SQL-databas, tillhörande säkerhetskopior och transaktionsloggfiler.

Ställ in en säkerhetsprincip för att välja den **princip** panelen på bladet Security Center. På den **säkerhetsprincip** bladet Välj den prenumeration som du vill aktivera säkerhetsprincipen. Välj **skyddsprincip** och aktivera **på** säkerhetsrekommendationer som du vill använda för den här prenumerationen.
![Säkerhetsprincip][3]

Läs mer i [ställa in säkerhetsprinciper](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Hantera säkerhetsrekommendation
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

När du ställer in en säkerhetsprincip för analyserar Security Center säkerhetstillståndet hos dina resurser för att identifiera potentiella säkerhetsrisker. Rekommendationerna som visas i tabellformat där varje rad motsvarar en viss rekommendation. Använd tabellen nedan som referens som hjälper dig att förstå tillgängliga rekommendationer för Azure SQL Database och vad varje rekommendation gör om du använder den. Markera en rekommendation går du till en artikel som förklarar hur du implementerar rekommendationen i Security Center.

| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera granskning och Hotidentifiering identifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hotidentifiering identifiering för SQL Database-servrar. (Endast SQL Database-tjänsten. Inkluderar inte Microsoft SQL Server körs på virtuella datorer.) |
| [Aktivera granskning och Hotidentifiering identifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hotidentifiering identifiering för databaser i SQL-databas. (Endast SQL Database-tjänsten. Inkluderar inte Microsoft SQL Server körs på virtuella datorer.) |
| [Aktivera transparent datakryptering](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser. (Endast SQL Database-tjänsten.) |

Om du vill visa rekommendationer för Azure-resurser, Välj den **rekommendationer** panelen på bladet Security Center. På den **rekommendationer** bladet Välj en rekommendation att se information. I det här exemplet ska vi väljer **aktivera Auditing & Threat detection på SQL-servrar**.

![Rekommendationer][4]

Enligt nedan visar Security Center SQL-servrar där granskning och hotidentifiering inte har aktiverats. När du aktiverar granskning kan konfigurera du inställningar för Hotidentifiering och e-post för att ta emot säkerhetsmeddelanden. Hotidentifiering varnar dig när den identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. Aviseringar visas i instrumentpanelen i Security Center.
![Granskning och hotidentifiering][5]

Följ stegen i [hotidentifiering för SQL-databas på Azure-portalen](../sql-database/sql-database-threat-detection-portal.md) att aktivera och konfigurera hotidentifiering och för att konfigurera listan över e-postmeddelanden som ska ta emot säkerhetsaviseringar vid identifiering av avvikande aktiviteter.

Läs mer om rekommendationer i [hantera säkerhetsrekommendationer](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Övervaka säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](security-center-policies.md) för resurser i en prenumeration analyseras resursernas säkerhet för upptäckt av eventuella säkerhetsrisker.  Du kan se säkerhetsstatus för dina resurser i den **resurssäkerhetshälsa** panelen. När du klickar på **Data** i den **resurssäkerhetshälsa** panelen, den **dataresurser** blad öppnas med SQL rekommendationer för problem som till exempel granskning och transparent data kryptering inte har aktiverats. Här finns även rekommendationer gällande databasens allmänna hälsoläge.
![Resurssäkerhetshälsa][6]

Läs mer i [övervakning av säkerhetshälsa](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Hantera och åtgärda säkerhetsaviseringar
Security Center automatiskt samlar in, analyseras och integreras loggdata från [Azure SQL-Hotidentifiering](../sql-database/sql-database-threat-detection.md)och andra Azure-resurser, till upptäckt av verkliga hot och falsklarm. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

Om du vill se aviseringar, väljer den **säkerhetsaviseringar** panelen på bladet Security Center. På den **säkerhetsaviseringar** bladet, Välj en avisering om du vill veta mer om de händelser som utlöste aviseringen och vad, om sådant finns, hur du behöver göra för att åtgärda angreppet. I det här exemplet ska vi väljer **potentiella SQL injection**.
![Säkerhetsaviseringar][7]

Enligt nedan Security Center innehåller ytterligare information som ger inblick i vad som utlöste aviseringen målresursen när så är tillämpligt källans IP-adress och rekommendationer om hur du åtgärdar.
![Potentiella SQL injection][8]

Läs mer i [hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor om Security Center](security-center-faq.md) – finns vanliga frågor om hur du använder tjänsten.
* [Security Center planerings- och operations guide](security-center-planning-and-operations-guide.md) – följer en uppsättning steg och aktiviteter för att optimera din användning av Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell.
* [Security Center datasäkerhet](security-center-data-security.md) – Lär dig hur Security Center samlar in och bearbetar data om din Azure-resurser, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och mycket mer.
* [Hantering av säkerhetsincidenter](security-center-incident.md) – Lär dig hur du använder den avisering kapaciteten för säkerhet i Security Center som hjälper dig att hantera säkerhetsincidenter.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
