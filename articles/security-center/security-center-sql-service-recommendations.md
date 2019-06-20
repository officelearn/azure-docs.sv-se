---
title: Skydda Azure-data- och tjänster i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänst och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275296"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Skydda Azure-data- och tjänster i Azure Security Center
Det här avsnittet visar hur du visar och implementera säkerhetsrekommendationer för data- och lagringsresurser. Azure Security Center att hitta följande rekommendationer när du analyserar säkerhetstillståndet hos dina Azure-resurser.

## <a name="view-your-data-security-information"></a>Visa din säkerhetsinformation för data

1. I den **Resource security hygien** klickar du på **Data- och lagringsresurser**.

   ![Data- och lagringsresurser](./media/security-center-monitoring/click-data.png)

    Den **datasäkerhet** öppnas med rekommendationer för dataresurser.

     ![Dataresurser](./media/security-center-monitoring/sql-overview.png)

Från den här sidan kan du:

* Klicka på den **översikt** fliken listar alla data resurser rekommendationerna för att åtgärdas. 
* Klicka på varje flik och visa rekommendationer efter resurstyp.

    > [!NOTE]
    > Mer information om lagringskryptering finns i [Aktivera kryptering för Azure-lagringskontot i Azure Security Center](security-center-enable-encryption-for-storage-account.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Åtgärda en rekommendation på en Dataresurs

1. Från någon av flikarna resurs, klickar du på en resurs. Informationssidan öppnas lista över rekommendationerna för att åtgärdas.

    ![Resursinformation](./media/security-center-monitoring/sql-recommendations.png)

2. Klicka på en rekommendation. Rekommendationssidan öppnas och visar den **åtgärdssteg** du implementerar rekommendationen.

   ![Åtgärdssteg](./media/security-center-monitoring/remediate1.png)

3. Klicka på **vidta åtgärder**. Inställningssidan resurs visas.

    ![Aktivera rekommendationen](./media/security-center-monitoring/remediate2.png)

4. Följ den **reparationssteg** och klicka på **spara**.

## <a name="data-and-storage-recommendations"></a>Data- och lagringsrekommendationer

|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Lagringskonto|20|Säker överföring till storage-konton måste vara aktiverat|Säker överföring är ett alternativ som tvingar ditt storage-konto för att acceptera begäranden endast från säkra anslutningar (HTTPS). HTTPS säkerställer autentisering mellan servern och tjänsten och skyddar data under överföring från network layer attacker, till exempel man-in-the-middle, avlyssning och sessionskapning.|
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
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper, finns i följande avsnitt:

* [Skydda virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)

Om du vill veta mer om Security Center finns i följande avsnitt:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
