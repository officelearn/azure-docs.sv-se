---
title: Kom igång med Azure Security Center Standard för förbättrad säkerhet | Microsoft Docs
description: " Lär dig hur du Kom igång med Azure Security Center Standard för förbättrad säkerhet. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: a68c0ecc15f81efe6045f4c298b48f9809916297
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38479469"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Kom igång med Azure Security Center Standard för ökad säkerhet
Uppgradera till Security Center Standard att dra nytta av förbättrad säkerhetshantering och skydd mot hot i dina hybridmolnarbetsbelastningar.  Du kan prova Standard kostnadsfritt under 60 dagar. Se de Säkerhetscenter [prissättningssidan](https://azure.microsoft.com/pricing/details/security-center/) för mer information.

Security Center Standard omfattar:

- **Hybridsäkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbetsbelastningar. Tillämpa säkerhetsprinciper och utvärdera säkerheten för dina hybridmolnarbetsbelastningar för att säkerställa att säkerhetsstandarder kontinuerligt. Samla in, sök efter och analysera säkerhetsdata från flera olika källor, till exempel brandväggar och andra partnerlösningar.
- **Avancerad hotidentifiering** -Använd avancerade analyser och Microsoft Intelligent Security Graph för att få en kant med utvecklas cyberattacker.  Utnyttja inbyggda beteendeanalyser och maskinlärning för att identifiera attacker och nolldagarshot. Övervaka nätverk, datorer och molntjänster för att se inkommande attacker och aktiviteter efter intrång. Effektiviseras undersökningen med interaktiva verktyg och kontextbaserad hotinformation.
- **Åtkomst- och programkontroll** – blockera skadliga och andra oönskade program genom att tillämpa rekommendationer anpassas efter dina specifika arbetsbelastningar och drivs av maskininlärning. Minska nätverkskontakter med just-in-time och kontrollerad åtkomst till hanteringsportar på virtuella Azure-datorer drastiskt minska exponeringen för råstyrkeattacker och andra nätverksattacker.

## <a name="detecting-unprotected-resources"></a>Identifiera oskyddade resurser     
Security Center identifierar automatiskt eventuella Azure-prenumerationer eller arbetsytor som inte är aktiverade för Security Center Standard. Det inkluderar Azure-prenumerationer med Security Center – Kostnadsfri och arbetsytor som inte har någon aktiverad säkerhetslösning.

Du kan uppgradera en hel Azure-prenumeration till standardnivån som ärvs av alla resurser i prenumerationen, eller du kan definiera en unik princip för att uppgradera en specifik resursgrupp endast. Om resursen grupprincipinställningar är unika, åsidosätter inte Security Center prissättning principerna när du uppgraderar prenumerationen till standardnivån. Tillämpning av standarden nivå till en prenumeration gäller endast för de virtuella datorer i prenumerationen som rapporterar till arbetsytor som skapats av Security Center. Tillämpning av standarden nivån till arbetsytan som gäller för alla resurser som rapporterar till arbetsytan.

> [!NOTE]
> Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning med agenter. [Lösningsmål](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du använda ett omfång till lösningen och rikta en delmängd av datorerna i arbetsytan.  Om du använder lösningsmål visas arbetsytan som inte har en lösning i Security Center.
>
>

## <a name="upgrade-an-azure-subscription"></a>Uppgradera en Azure-prenumeration
Uppgradera alla prenumerationer till Standard:
1. På huvudmenyn i Security Center väljer du **Kom igång med avancerad säkerhet**.
2. Under **registrering för avancerad säkerhet**, Security Center visar en lista över prenumerationer som är behöriga för registrering. Du kan uppgradera alla prenumerationerna som visas genom att välja **gäller standardprenumerationen**.

  ![Uppgradera alla prenumerationer][1]

Så här uppgraderar du en enskild prenumeration till Standard: du kan uppgradera en prenumeration från **Onboarding** genom att välja **tillämpa standardnivån**. Välj prenumerationen för att uppgradera en resursgrupp i prenumeration till Standard:
1. Välj en prenumeration.  **Säkerhetsprincip** innehåller information om resursgrupp i prenumerationen.
2. Välj prenumerationen eller resursgrupp.

  ![Uppgradera alla prenumerationer][2]

3. Välj **Standard** uppgraderingen från kostnadsfri till Standard.
4. Välj **Spara**.

> [!NOTE]
> Uppgradera en prenumeration till Standard så aktiveras [Automatisk etablering](security-center-enable-data-collection.md) om det tidigare har inaktiverats. Vi rekommenderar att du automatisk försörjning av övervakningsagenter.
>
>

## <a name="upgrade-a-workspace"></a>Uppgradera en arbetsyta
Tillämpa Standard till arbetsytan gäller för alla resurser som rapporterar till arbetsytan.

1. Gå tillbaka till den **Onboarding** bladet.
2. Välj en arbetsyta.

  ![Uppgradera en arbetsyta][8]

3. Välj **Standard** att uppgradera.  
4. Välj **Spara**.

   > [!NOTE]
   > Det är ett scenario där du inte kanske har kostnadsfri eller Standard tillämpas på din arbetsyta. Om du väljer kostnadsfri tillämpas kostnadsfria funktioner i Security Center på virtuella datorer i Azure endast. De kostnadsfria funktionerna tillämpas inte på icke-Azure-datorer. Om du väljer Standard tillämpas Standard-funktionerna på alla virtuella Azure-datorer och icke-Azure-datorer som rapporterar till arbetsytan. Vi rekommenderar att du tillämpar Standard för att tillhandahålla avancerad säkerhet för Azure- och icke-Azure-resurser.
   >
   >

## <a name="onboard-non-azure-computers"></a>Publicera icke-Azure-datorer
Security Center kan övervaka säkerhetsstatusen för icke-Azure-datorer men du måste först publicera dessa resurser. Du kan lägga till icke-Azure-datorer från den **Onboarding** bladet eller från den **Compute** bladet. Vi gå igenom båda metoderna.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Lägga till nya Azure-datorer från Onboarding

1. Gå tillbaka till **Onboarding**.   
2. Välj **vill du lägga till nya Azure-datorer**.

  ![Lägga till en dator som inte är en Azure-dator][3]

Om du har befintliga arbetsytor kan de visas under **lägga till nya icke-Azure-datorer**. Du kan lägga till datorer till en befintlig arbetsyta eller skapa en ny arbetsyta. Klicka på länken om du vill skapa en ny arbetsyta **lägga till en ny arbetsyta**.

Vi gå igenom båda metoderna:

- Skapa en ny arbetsyta och lägger till datorn
- Välj en befintlig arbetsyta och Lägg till dator

**Skapa en ny arbetsyta och lägger till datorn**

1. Under **lägga till nya Azure-datorer**väljer **lägga till en ny arbetsyta**.

   ![Lägg till en ny arbetsyta][4]

2. Under **säkerhet och granskning**väljer **OMS-arbetsyta** att skapa en ny arbetsyta.
3. Under **OMS-arbetsyta**, anger du informationen för din arbetsyta.
4. Under **OMS-arbetsyta**väljer **OK**.  När du väljer OK, du får en länk för att hämta en Windows- eller Linux-agent och nycklar för arbetsyte-ID för användning vid konfigureringen av agenten.
5. Under **säkerhet och granskning**väljer **OK**.

**Välj en befintlig arbetsyta och Lägg till dator**

Du kan lägga till en dator genom att följa arbetsflödet från **Onboarding**, enligt ovan. Du kan också lägga till en dator genom att följa arbetsflödet från **Compute**. I det här exemplet använder vi **Compute**.

1. Återgå till huvudmenyn i Security Center och **översikt** instrumentpanelen.

   ![Översikt][5]

2. Välj **Compute och appar**.
3. Under **Compute och appar**väljer **lägga till datorer**.

   ![Compute-bladet][6]

4. Under **lägga till nya Azure-datorer**, Välj en arbetsyta för att ansluta datorn till och klicka på **Lägg till datorer**.

   ![Lägga till datorer][7]

 Den **Direktagent** bladet innehåller en länk för att ladda ned en Windows- eller Linux-agent och nycklar för arbetsyte-ID för användning vid konfigureringen av agenten.   

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du integrera Azure och icke-Azure-resurser för att kunna dra nytta av avancerad säkerhet i Security Center.  Om du vill göra mer med din integrerade resurser finns i

- [Aktivera datainsamling](security-center-enable-data-collection.md)
- [Hotinformationsrapport](security-center-threat-report.md)
- [Just-in-time-åtkomst till virtuell dator](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
