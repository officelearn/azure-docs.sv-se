---
title: Skydda Azure SQL-tjänst och data i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänst och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 977c464e0c172a25d069fa7db55d8aefb78d89d9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339101"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Skydda Azure SQL-tjänst och data i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs.  Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM), nätverk, SQL och data och program.

Den här artikeln belyser rekommendationer som gäller för Azure SQL-tjänst och data. Rekommendationer center för att aktivera granskning för Azure SQL-servrar och databaser, att aktivera kryptering för SQL-databaser och aktivering av kryptering för Azure storage-kontot.  Använd tabellen nedan som referens för att förstå de tillgängliga rekommendationerna för SQL-tjänsten och data och vad var och en gör om du använder den.
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
|Lagringskonto|20|Kräv säker överföring till storage-konto|Säker överföring är ett alternativ som tvingar ditt storage-konto för att acceptera begäranden endast från säkra anslutningar (HTTPS). Använda HTTPS säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|Redis|20|Aktivera endast säkra anslutningar till din Azure Cache för Redis|Aktivera endast anslutningar via SSL till Azure Cache för Redis. Användning av säkra anslutningar säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning.|
|SQL|15|Aktivera Transparent datakryptering på SQL-databaser|Aktivera transparent datakryptering för att skydda vilande data och uppfylla efterlevnadskraven.|
|SQL|15|Aktivera granskning på SQL-servrar|Aktivera granskning för Azure SQL-servrar. (Endast azure SQL-tjänsten. Omfattar inte SQL som körs på dina virtuella datorer.)|
|SQL|15|Aktivera granskning på SQL-databaser|Aktivera granskning för Azure SQL-databaser. (Endast azure SQL-tjänsten. Omfattar inte SQL som körs på dina virtuella datorer.)|
|Data lake analytics|15|Aktivera kryptering i vila för Data Lake Analytics|Aktivera transparent datakryptering att skydda data i vila i Data Lake Analytics. Vilande kryptering är transparent, vilket innebär att krypteras Data Lake Analytics automatiskt data före beständig lagring, och dekrypterar data för hämtning. Det finns inga ändringar som krävs i i program och tjänster som interagerar med Data Lake Analytics på grund av krypteringen. Kryptering i vila minimerar risken för dataförlust från fysiska stöld och bidrar även till att uppfylla efterlevnadskrav.|
|Data lake store|15|Aktivera kryptering i vila för Data Lake Store|Aktivera transparent datakryptering att skydda data i vila i ditt Data Lake Store. Vilande kryptering är transparent, vilket innebär att data före beständig lagring, krypteras automatiskt Data Lake Store och dekrypterar data för hämtning. Du behöver göra några ändringar i program och tjänster som interagerar med Data Lake Store för kryptering. Kryptering i vila minimerar risken för dataförlust från fysiska stöld och bidrar även till att uppfylla efterlevnadskrav.|
|Lagringskonto|15|Aktivera kryptering för Azure Storage-kontot|Aktivera Azure Storage Service Encryption för vilande data. Storage Service Encryption (SSE) fungerar genom att kryptera data när den skrivs till Azure-lagring och dekrypterar före hämtning. SSE är för närvarande endast tillgänglig för Azure Blob service och kan användas för blockblobbar, sidblobbar och tilläggsblobbar.|
|Data lake analytics|5|Aktivera diagnostikloggar i Data Lake Analytics|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Data lake store|5|Aktivera diagnostikloggar i Azure Data Lake Store|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|SQL|30|Åtgärda sårbarheter på SQL-databaser|Sårbarhetsbedömning för SQL söker igenom din databas för säkerhetsrisker och visar alla avvikelser från regelverk som felkonfigurationer, onödigt generösa behörigheter och oskyddade känsliga data. Åtgärda sårbarheter upptäcktes kan förbättra din säkerhet datasekretesstandarder för databasen.|
|SQL|20|Etablera en Azure AD-administratör för SQLServer|Etablera en Azure AD-administratör för SQL-servern om du vill aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.|
|Lagringskonto|15|Inaktivera obegränsad nätverksåtkomst till storage-konto|Granska obegränsad nätverksåtkomst i brandväggsinställningarna för storage-konto. Konfigurera i stället Nätverksregler så att endast program från tillåtna nätverk har åtkomst till lagringskontot. Om du vill tillåta anslutningar från specifika Internet eller en lokal klienter beviljas åtkomst till trafik från specifika Azure-nätverk eller till offentliga Internet-IP-adressintervall.|
|Lagringskonto|1||Migrera storage-konton till nya AzureRM-resurser|Använd den nya Azure Resource Manager v2 för storage-konton att tillhandahålla förbättringar av säkerhet som: starkare åtkomstkontroll (RBAC), bättre granskning, Resource Manager-baserade distributionen och styrning åtkomst till hanterade identiteter, åtkomst till nyckelvalvet för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering.|



## <a name="see-also"></a>Se också
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Skydda virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
