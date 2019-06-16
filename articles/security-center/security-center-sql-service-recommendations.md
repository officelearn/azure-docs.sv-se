---
title: Skydda Azure SQL-tjänst och data i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänst och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: monhaber
ms.openlocfilehash: bbba5f380fddb4fdec43a7414e59778135c4e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428307"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Skydda Azure SQL-tjänst och data i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs.  Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM), nätverk, SQL och data och program.


### <a name="monitor-data-security"></a>Övervaka datasäkerhet

När du klickar på **Datasäkerhet** i avsnittet **Skydd** öppnas **Dataresurser** med rekommendationer för SQL och lagring. Här finns även [rekommendationer](security-center-sql-service-recommendations.md) gällande databasens allmänna hälsoläge. Mer information om lagringskryptering finns i [Aktivera kryptering för Azure-lagringskontot i Azure Security Center](security-center-enable-encryption-for-storage-account.md).

![Dataresurser](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Under **SQL-rekommendationer**, kan du klicka på valfri rekommendation och få mer information om ytterligare åtgärder för att lösa ett problem. Följande exempel visar den expanderade rekommendationen **Databasgranskning och hotidentifiering på SQL-databaser**.

![Information om en SQL-rekommendation](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Under **Aktivera granskning på SQL-databaser** visas följande information:

* en lista med SQL-databaser
* vilken server de ligger på
* information om huruvida inställningen har ärvts från servern eller om den är unik för databasen
* aktuell status
* problemets allvarlighetsgrad

När du klickar på databasen för att utföra rekommendationen öppnas **Granskning och hotidentifiering** som på följande skärmbild.

![Granskning och hotidentifiering](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Du aktiverar granskning genom att välja **PÅ** under alternativet **Granskning**.

## <a name="data-and-storage-recommendations"></a>Data- och lagringsrekommendationer

|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Lagringskonto|20|Säker överföring till storage-konton måste vara aktiverat|Säker överföring är ett alternativ som tvingar ditt storage-konto för att acceptera begäranden endast från säkra anslutningar (HTTPS). Använda HTTPS säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Redis|20|Endast säkra anslutningar till Redis-cachen ska aktiveras|Aktivera endast anslutningar via SSL till Azure Cache för Redis. Användning av säkra anslutningar säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|SQL|15|Transparent datakryptering på SQL-databaser ska aktiveras|Aktivera transparent datakryptering att skydda data i vila och uppfyller efterlevnadskrav.|
|SQL|15|SQL server-granskning ska aktiveras|Aktivera granskning för Azure SQL-servrar. (Endast azure SQL-tjänsten. Omfattar inte SQL som körs på dina virtuella datorer.)|
|Data lake analytics|5|Diagnostikloggar i Data Lake Analytics ska aktiveras|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Data lake store|5|Diagnostikloggar i Azure Data Lake Store ska aktiveras|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|SQL|30|Säkerhetsproblem på SQL-databaser bör åtgärdas|Sårbarhetsbedömning för SQL söker igenom din databas för säkerhetsrisker och visar alla avvikelser från regelverk som felkonfigurationer, onödigt generösa behörigheter och oskyddade känsliga data. Åtgärda sårbarheter upptäcktes kan förbättra din säkerhet datasekretesstandarder för databasen.|
|SQL|20|Etablera en Azure AD-administratör för SQLServer|Etablera en Azure AD-administratör för SQL-servern om du vill aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.|
|Lagringskonto|15|Åtkomst till lagringskonton med brandvägg och konfigurationer av virtuella ska vara begränsad|Granska obegränsad nätverksåtkomst i brandväggsinställningarna för storage-konto. Konfigurera i stället Nätverksregler så att endast program från tillåtna nätverk har åtkomst till lagringskontot. För att tillåta anslutningar från specifika Internet eller lokala klienter, kan du bevilja åtkomst till trafik från specifika Azure-nätverk eller till offentliga Internet-IP-adressintervall.|
|Lagringskonto|1|Storage-konton som ska migreras till nya Azure Resource Manager-resurser|Använda den nya Azure Resource Manager v2 för storage-konton för att tillhandahålla förbättringar av säkerhet som: starkare åtkomstkontroll (RBAC), bättre granskning, Resource Manager-baserade distributionen och styrning åtkomst till hanterade identiteter, åtkomst till nyckelvalvet för hemligheter, och Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering.|

## <a name="see-also"></a>Se också
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Skydda virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
