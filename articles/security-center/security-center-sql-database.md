---
title: Azure Security Center-och Azure SQL Database-tjänsten | Microsoft Docs
description: Den här artikeln visar hur Security Center kan hjälpa dig att skydda dina databaser i Azure SQL Database.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704619"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center-och Azure SQL Database-tjänsten
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) hjälper dig att förebygga, identifiera och reagera på hot. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Den här artikeln visar hur Security Center kan hjälpa dig att skydda dina databaser i Azure SQL Database.

## <a name="why-use-security-center"></a>Varför ska man använda Security Center?
Security Center hjälper dig att skydda data i SQL Database genom att ge insyn i säkerheten för alla dina servrar och databaser. Med Security Center kan du:

* Definiera principer för SQL Database kryptering och granskning.
* Övervaka säkerheten för SQL Database resurser i alla dina prenumerationer.
* Identifiera och åtgärda säkerhets problem snabbt.
* Integrera aviseringar från [Azure SQL Database hot identifiering](../sql-database/sql-database-threat-detection.md).

Förutom att hjälpa till att skydda dina SQL Database-resurser, kan Security Center också övervaka och hantera virtuella Azure-datorer, Cloud Services, App Services, virtuella nätverk och mycket annat. Läs mer om Security Center [här](security-center-intro.md).

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Den kostnads fria nivån av Security Center har Aktiver ATS med din prenumeration. Mer information om Security Center kostnads fria och standard nivåer finns i [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

Security Center stöder rollbaserad åtkomst. Mer information om rollbaserad åtkomst kontroll (RBAC) i Azure finns [Azure Active Directory rollbaserad Access Control](../role-based-access-control/role-assignments-portal.md). Security Center vanliga frågor och svar innehåller information om [hur behörigheter hanteras i Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Öppna Security Center
Security Center öppnas från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Logga in på portalen](https://portal.azure.com/) och välj **alternativet Security Center**.

![Security Center alternativ][1]

Bladet **Security Center** öppnas.
![Security Center bladet][2]

## <a name="set-security-policy"></a>Ange säkerhetsprincip
En säkerhets princip definierar en uppsättning kontroller som rekommenderas för resurser i den angivna prenumerationen eller resurs gruppen. I Security Center definierar du principer för dina prenumerationer eller resurs grupper enligt företagets säkerhets behov och typ av program eller känslighet för data i varje prenumeration.

Du kan ange en princip för att Visa rekommendationer för SQL-granskning och SQL transparent data kryptering (TDE).

* När du aktiverar **SQL-granskning och hot identifiering**rekommenderar Security Center att granskning av åtkomst till Azure-databasen aktive ras för efterlevnad, Avancerad identifiering och undersöknings ändamål.
* När du aktiverar **transparent data kryptering i SQL**rekommenderar Security Center att kryptering i vila ska aktive ras för Azure SQL Database, tillhör ande säkerhets kopior och transaktionsloggfiler.

Om du vill ange en säkerhets princip väljer du panelen **princip** på bladet Security Center. På bladet **säkerhets princip** väljer du den prenumeration som du vill aktivera säkerhets principen för. Välj **skydds princip** och aktivera de säkerhets rekommendationer som du vill använda för den här prenumerationen.
![Säkerhetsprincip][3]

Läs mer i [Ange säkerhets principer](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Hantera säkerhets rekommendation
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

När du har angett en säkerhets princip analyserar Security Center säkerhets status för dina resurser för att identifiera potentiella sårbarheter. Rekommendationerna visas i tabell format där varje rad representerar en viss rekommendation. Använd följande tabell som referens för att hjälpa dig att förstå de rekommendationer som är tillgängliga för Azure SQL Database och vilka rekommendationer som gäller om du använder den. Genom att välja en rekommendation går du till en artikel som förklarar hur du implementerar rekommendationen i Security Center.

| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera granskning och hot identifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hot identifiering för SQL Database-servrar. (Endast SQL Database-tjänsten. Inkluderar inte Microsoft SQL Server som körs på dina virtuella datorer.) |
| [Aktivera granskning och hot identifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hot identifiering för SQL Database databaser. (Endast SQL Database-tjänsten. Inkluderar inte Microsoft SQL Server som körs på dina virtuella datorer.) |
| [Aktivera transparent datakryptering](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser. (Endast SQL Database tjänst.) |

Om du vill se rekommendationer för dina Azure-resurser väljer du panelen **rekommendationer** på bladet Security Center. På bladet **rekommendationer** väljer du en rekommendation för att se information. I det här exemplet ska vi välja **aktivera granskning & hot identifiering på SQL-servrar**.

![Rekommendationer][4]

Som visas nedan visar Security Center SQL-servrarna där granskning och hot identifiering inte är aktiverat. När du har aktiverat granskning kan du konfigurera inställningar för hot identifiering och e-postinställningar för att få säkerhets aviseringar. Hot identifiering varnar dig när den identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen. Aviseringarna visas på instrument panelen för Security Center.
![Granskning och hotidentifiering][5]

Följ stegen i [SQL Database hot identifiering i Azure Portal](../sql-database/sql-database-threat-detection-portal.md) för att aktivera och konfigurera hot identifiering och för att konfigurera listan över e-postmeddelanden som får säkerhets aviseringar vid identifiering av avvikande aktiviteter.

Mer information om rekommendationer finns i [hantera säkerhets rekommendationer](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Övervaka säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](tutorial-security-policy.md) för resurser i en prenumeration analyseras resursernas säkerhet för upptäckt av eventuella säkerhetsrisker.  Du kan visa säkerhets tillståndet för dina resurser i panelen **resurs säkerhets hälsa** . När du klickar på **data** i rutan **resurs säkerhets hälsa** öppnas bladet **data resurser** med SQL-rekommendationer för problem som granskning och transparent data kryptering inte aktive ras. Här finns även rekommendationer gällande databasens allmänna hälsoläge.
![Resurs säkerhets hälsa][6]

Mer information finns i [övervakning av säkerhets hälsa](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Hantera och besvara säkerhetsvarningar
Security Center samlar automatiskt in, analyserar och integrerar loggdata från [Azure SQL hot identifiering](../sql-database/sql-database-threat-detection.md), samt andra Azure-resurser, för att identifiera verkliga hot och minska antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

Om du vill visa aviseringar väljer du panelen **säkerhets aviseringar** på bladet Security Center. På bladet **säkerhets aviseringar** väljer du en avisering för att lära dig mer om de händelser som utlöste aviseringen och vad, om det finns några, steg som du måste vidta för att åtgärda ett angrepp. I det här exemplet ska vi välja **potentiell SQL-inmatning**.
![Säkerhetsaviseringar][7]

Som det visas nedan ger Security Center ytterligare information som ger insikt om vad som utlöste aviseringen, mål resursen, vid tillämplig Källans IP-adress och rekommendationer om hur du kan åtgärda det.
![Potentiell SQL-inmatning][8]

Mer information finns i [Hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Nästa steg
* [Security Center vanliga frågor och svar](security-center-faq.md) – hitta vanliga frågor om att använda tjänsten.
* [Security Center planering och drifts guide](security-center-planning-and-operations-guide.md) – Följ en uppsättning steg och aktiviteter för att optimera din användning av Security Center baserat på organisationens säkerhets krav och moln hanterings modell.
* [Security Center data säkerhet](security-center-data-security.md) – lär dig hur Security Center samlar in och bearbetar data om dina Azure-resurser, inklusive konfigurations information, metadata, händelse loggar, krasch dum par filer med mera.
* [Hantera säkerhets incidenter](security-center-incident.md) – lär dig hur du använder säkerhets aviserings funktionen i Security Center för att hjälpa dig att hantera säkerhets incidenter.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
