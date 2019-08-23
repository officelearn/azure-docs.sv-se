---
title: Skydda Azure-data och lagrings tjänster i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänsten och hålla dig informerad om säkerhets principer.
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
ms.author: v-mohabe
ms.openlocfilehash: 0704db7444756fb44c5587ae8e8e743f671c9dcc
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907950"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Skydda Azure-data och lagrings tjänster i Azure Security Center
Det här avsnittet visar hur du visar och implementerar säkerhets rekommendationer för data-och lagrings resurser. Azure Security Center hittade rekommendationerna när du analyserar säkerhets status för dina Azure-resurser.

## <a name="view-your-data-security-information"></a>Visa data säkerhets information

1. I avsnittet **resurs säkerhets hygien** klickar du på **data-och lagrings resurser**.

   ![Data & lagrings resurser](./media/security-center-monitoring/click-data.png)

    Sidan **data säkerhet** öppnas med rekommendationer för data resurser.

     ![Dataresurser](./media/security-center-monitoring/sql-overview.png)

Från den här sidan kan du:

* Klicka på fliken **Översikt** visar en lista över alla rekommendationer för data resurser som ska åtgärdas. 
* Klicka på varje flik och Visa rekommendationerna efter resurs typ.

    > [!NOTE]
    > Mer information om lagrings kryptering finns i [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Åtgärda en rekommendation på en data resurs

1. Klicka på en resurs från någon av resurs flikarna. Informations sidan öppnas och visar de rekommendationer som ska åtgärdas.

    ![Resursinformation](./media/security-center-monitoring/sql-recommendations.png)

2. Klicka på en rekommendation. Sidan rekommendation öppnas och visar åtgärds **stegen** för att implementera rekommendationen.

   ![Reparationssteg](./media/security-center-monitoring/remediate1.png)

3. Klicka på **vidta åtgärd**. Sidan resurs inställningar visas.

    ![Aktivera rekommendation](./media/security-center-monitoring/remediate2.png)

4. Följ **stegen för reparation** och klicka på **Spara**.

## <a name="data-and-storage-recommendations"></a>Rekommendationer för data och lagring

|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Lagringskonto|20|Säker överföring till lagrings konton ska vara aktiverat|Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). HTTPS säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning.|
|Redis|20|Endast säkra anslutningar till din Redis Cache ska vara aktiverade|Aktivera endast anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning.|
|SQL|15|transparent datakryptering på SQL-databaser ska aktive ras|Aktivera transparent data kryptering för att skydda data i vila och uppfylla kraven på efterlevnad.|
|SQL|15|SQL Server Auditing måste vara aktiverat|Aktivera granskning för Azure SQL-servrar. (Endast Azure SQL-tjänsten. Inkluderar inte SQL som körs på dina virtuella datorer.)|
|Data Lake Analytics|5|Diagnostikloggar i Data Lake Analytics ska vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|Data Lake Store|5|Diagnostikloggar i Azure Data Lake Store ska vara aktive rad|Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. |
|SQL|30|Säkerhets risker i SQL-databaser bör åtgärdas|SQL sårbarhet Assessment söker igenom din databas efter säkerhets risker och exponerar eventuella avvikelser från bästa praxis, till exempel felkonfigurationer, överdriven behörighet och oskyddade känsliga data. Att lösa de problem som upptäcks kan avsevärt förbättra din databas säkerhets datasekretesstandarder.|
|SQL|20|Etablera en Azure AD-administratör för SQL Server|Etablera en Azure AD-administratör för SQL-servern för att aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster.|
|Lagringskonto|15|Åtkomst till lagrings konton med brand väggar och virtuella nätverkskonfigurationer bör begränsas|Granska obegränsad nätverks åtkomst i brand Väggs inställningarna för ditt lagrings konto. Konfigurera i stället nätverks regler så att endast program från tillåtna nätverk kan komma åt lagrings kontot. Om du vill tillåta anslutningar från vissa Internet-eller lokala klienter kan du bevilja åtkomst till trafik från vissa virtuella Azure-nätverk eller offentliga IP-adressintervall för Internet.|
|Lagringskonto|1|Lagrings konton ska migreras till nya Azure Resource Manager resurser|Använd nya Azure Resource Manager v2 för dina lagrings konton för att tillhandahålla säkerhets förbättringar som: starkare åtkomst kontroll (RBAC), bättre granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter och Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering.|

## <a name="see-also"></a>Se också
Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande avsnitt:

* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)

Mer information om Security Center finns i följande avsnitt:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
