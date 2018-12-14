---
title: Azure Security Center och Azure SQL Database-tjänsten | Microsoft Docs
description: Den här artikeln visar hur Security Center kan hjälpa dig att skydda dina databaser i Azure SQL Database.
services: sql-database
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 839c8bfe6748c1aeaf7c8804ef4388cf8a623bdb
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337843"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center och Azure SQL Database-tjänsten
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Den här artikeln visar hur Security Center kan hjälpa dig att skydda dina databaser i Azure SQL Database.

## <a name="why-use-security-center"></a>Varför ska man använda Security Center?
Security Center hjälper dig att skydda data i SQL-databas genom att tillhandahålla bättre inblick i säkerheten hos alla dina servrar och databaser. Med Security Center kan du:

* Definiera principer för SQL Database-kryptering och granskning.
* Övervaka säkerheten i SQL Database-resurser för alla dina prenumerationer.
* Snabbt identifiera och åtgärda säkerhetsproblem.
* Integrera varningar från [Azure SQL Database-hotidentifiering](../sql-database/sql-database-threat-detection.md).

Utöver att skydda din SQL Database-resurser, tillhandahåller även Security Center säkerhetsövervakning och hantering för Azure-datorer, molntjänster, App Services, virtuella nätverk med mera. Läs mer om Security Center [här](security-center-intro.md).

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Den kostnadsfria nivån av Security Center aktiveras med din prenumeration. Läs mer på Security Centers kostnadsfria nivån och standardnivån, [Security Center-prissättning](https://azure.microsoft.com/pricing/details/security-center/).

Security Center stöder rollbaserad åtkomst. Mer information om rollbaserad åtkomstkontroll (RBAC) i Azure finns [Azure Active Directory rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md). Security Center vanliga frågor och svar innehåller information om [hur behörigheter ska hanteras i Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Öppna Security Center
Security Center öppnas från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Logga in på portalen](https://portal.azure.com/) och välj den **Security Center alternativet**.

![Security Center-alternativet][1]

Den **Security Center** blad öppnas.
![Security Center-bladet][2]

## <a name="set-security-policy"></a>Ange säkerhetsprincip
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Security Center ställer in du principer för dina prenumerationer och resursgrupper enligt ditt företags behov och typ av program eller efter Känslighetsnivån på datauppgifterna i respektive prenumeration.

Du kan ange en princip för att visa rekommendationer för SQL-granskning och transparent datakryptering (TDE) för SQL.

* När du aktiverar **SQL-granskning och Hotidentifiering**, Security Center rekommenderar att granskning av åtkomst till Azure Database aktiveras för regelefterlevnad, avancerad identifiering och undersökning.
* När du aktiverar **SQL transparent datakryptering**, rekommenderar att kryptering i vila i Security Center aktiveras för Azure SQL Database, tillhörande säkerhetskopior och transaktionsloggfiler.

För att ställa in en säkerhetsprincip, Välj den **princip** panelen på bladet Security Center. På den **säkerhetsprincip** bladet väljer du den prenumeration som du vill aktivera en säkerhetsprincip. Välj **skyddsprincip** och aktivera **på** de säkerhetsrekommendationer du vill använda för den här prenumerationen.
![Säkerhetsprincip][3]

Mer information finns i [ställa in säkerhetsprinciper](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Hantera säkerhetsrekommendation
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

När du har angett en säkerhetsprincip för analyserar Security Center säkerhetstillståndet för dina resurser för att upptäcka potentiella säkerhetsrisker. Rekommendationerna visas i tabellformat där varje rad motsvarar en viss rekommendation. Använd tabellen nedan som referens för att förstå de tillgängliga rekommendationerna för Azure SQL Database och vad varje rekommendation gör om du använder den. Att välja en rekommendation kommer du till en artikel som förklarar hur du implementerar rekommendationen i Security Center.

| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera granskning och Hotidentifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hotidentifiering för SQL Database-servrar. (Endast SQL Database-tjänsten. Omfattar inte Microsoft SQL Server som körs på dina virtuella datorer.) |
| [Aktivera granskning och Hotidentifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hotidentifiering för SQL Database-databaser. (Endast SQL Database-tjänsten. Omfattar inte Microsoft SQL Server som körs på dina virtuella datorer.) |
| [Aktivera transparent datakryptering](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser. (Endast SQL Database-tjänsten.) |

Om du vill se rekommendationer för dina Azure-resurser, Välj den **rekommendationer** panelen på bladet Security Center. På den **rekommendationer** bladet och välja en rekommendation att se information. I det här exemplet väljer vi **aktivera granskning på SQL-servrar**.

![Rekommendationer][4]

Enligt nedan visar Security Center SQL-servrar där granskning och hotidentifiering inte har aktiverats. När du aktiverar granskning kan konfigurera du inställningarna för Hotidentifiering och e-postinställningar för att få säkerhetsaviseringar. Hotidentifiering varnar dig när den identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. Aviseringarna visas i instrumentpanelen i Security Center.
![Granskning och hotidentifiering][5]

Följ stegen i [SQL Database-hotidentifiering i Azure-portalen](../sql-database/sql-database-threat-detection-portal.md) att aktivera och konfigurera identifiering av hot och för att konfigurera en lista över e-postmeddelanden som ska få säkerhetsaviseringar när avvikande aktiviteter.

Läs mer om rekommendationer i [hantera säkerhetsrekommendationer](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Övervaka säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](tutorial-security-policy.md) för resurser i en prenumeration analyseras resursernas säkerhet för upptäckt av eventuella säkerhetsrisker.  Du kan se säkerhetsstatus för dina resurser i den **resurssäkerhetshälsa** panelen. När du klickar på **Data** i den **resurssäkerhetshälsa** öppnas den **dataresurser** blad öppnas med SQL-rekommendationer för problem, till exempel för granskning och transparent kryptering inte är aktiverade. Här finns även rekommendationer gällande databasens allmänna hälsoläge.
![Resurssäkerhetshälsa][6]

Mer information finns i [övervakning av säkerhetshälsa](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Hantera och åtgärda säkerhetsaviseringar
Security Center automatiskt samlar in, analyserar och integrerar loggdata från [Azure SQL-Hotidentifiering](../sql-database/sql-database-threat-detection.md), samt andra Azure-resurser, att identifiera verkliga hot och minska falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

Om du vill se aviseringar, Välj den **säkerhetsaviseringar** panelen på bladet Security Center. På den **säkerhetsaviseringar** bladet, väljer du en avisering om du vill veta mer om de händelser som utlöste aviseringen och vad, om sådant finns, steg du måste avlägga åtgärdar ett angrepp. I det här exemplet väljer vi **potentiell SQL-inmatning**.
![Säkerhetsaviseringar][7]

Enligt nedan Security Center tillhandahåller ytterligare information som ger inblick i vad som utlöste aviseringen, målresursen när så är tillämpligt källans IP-adress och rekommendationer om hur du åtgärdar.
![Potentiell SQL-inmatning][8]

Mer information finns i [hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Nästa steg
* [Security Center vanliga frågor och svar](security-center-faq.md) – hittar du vanliga frågor och svar om tjänsten.
* [Planerings- och bruksanvisning för Security Center](security-center-planning-and-operations-guide.md) – Följ en uppsättning steg och uppgifter för att optimera användningen av Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell.
* [Datasäkerhet i Security Center](security-center-data-security.md) – Lär dig hur Security Center samlar in och bearbeta data om dina Azure-resurser, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och mer.
* [Hantering av säkerhetsincidenter](security-center-incident.md) – Lär dig hur du använder funktionen för säkerhetsavisering i Security Center som hjälper dig att hantera säkerhetsincidenter.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
