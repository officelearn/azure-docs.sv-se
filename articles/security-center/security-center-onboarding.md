---
title: Standardnivåns förbättrade säkerhet – Azure Security Center
description: " Lär dig hur du går ombord på Azure Security Center Standard för förbättrad säkerhet. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: be26a9d4c66412518079de303ac0764d979c3e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912060"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Registrering till Azure Security Center Standard för ökad säkerhet
Uppgradera till Security Center Standard för att dra nytta av förbättrad säkerhetshantering och hotskydd för dina hybridmolnarbetsbelastningar. Du kan prova Standard gratis. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/) för Security Center.

Standardnivån i Security Center omfattar:

- **Hybridsäkerhet** – Få en enhetlig säkerhetsvy över alla dina lokala och molnarbetsbelastningar. Tillämpa säkerhetsprinciper och utvärdera kontinuerligt säkerheten för dina hybridmolnarbetsbelastningar för att säkerställa efterlevnad av säkerhetsstandarder. Samla in, söka efter och analysera säkerhetsdata från en mängd olika källor, inklusive brandväggar och andra partnerlösningar.
- **Säkerhetsvarningar** – Använd avancerad analys och Microsoft Intelligent Security Graph för att få ett försprång jämfört med nya cyberattacker. Utnyttja inbyggda beteendeanalyser och maskininlärning för att identifiera attacker och zero-day-kryphål. Övervaka nätverk, datorer och molntjänster för inkommande attacker och aktivitet efter överträdelsen. Effektivisera undersökningen med interaktiva verktyg och kontextuell hotinformation.
- **Åtkomst- och programkontroller** – Blockera skadlig kod och andra oönskade program genom att tillämpa vitlistningsrekommendationer som är anpassade till dina specifika arbetsbelastningar och som drivs av maskininlärning. Minska nätverksattackytan med just-in-time, kontrollerad åtkomst till hanteringsportar på virtuella Azure-datorer, vilket drastiskt minskar exponeringen för brute force och andra nätverksattacker.

## <a name="detecting-unprotected-resources"></a>Identifiera oskyddade resurser
Security Center identifierar automatiskt eventuella Azure-prenumerationer eller arbetsytor som inte är aktiverade för Security Center Standard. Det inkluderar Azure-prenumerationer med Security Center – Kostnadsfri och arbetsytor som inte har någon aktiverad säkerhetslösning.

Du kan uppgradera en hel Azure-prenumeration till standardnivån, som ärvs av alla resurser som stöds inom prenumerationen. Att tillämpa standardnivån på en arbetsyta gäller för alla resurser som rapporterar till arbetsytan.

> [!NOTE]
> Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. [Med lösningsinriktning](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du använda ett scope på lösningen och rikta in dig på en delmängd datorer på arbetsytan. Om du använder lösningsinriktning visar Security Center att arbetsytan inte har någon lösning.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Uppgradera en Azure-prenumeration eller arbetsyta
Så här uppgraderar du en prenumeration eller arbetsyta till standard:
1. På huvudmenyn i Security Center väljer du **Komma igång**.
  ![Komma igång](./media/security-center-onboarding/get-started.png)
2. Under **Uppgradera** listar Security Center prenumerationer och arbetsytor som är behöriga för registrering. 
   - Du kan klicka på den expanderbara texten **Använd din utvärderingsversion** för att se en lista över alla prenumerationer och arbetsytor med deras berättigandestatus för utvärderingsversion.
   -    Du kan uppgradera prenumerationer och arbetsytor som inte är berättigade till utvärderingsversionen.
   -    Du kan välja berättigade arbetsytor och prenumerationer för att påbörja din utvärderingsperiod.
3.  Klicka på **Starta utvärdering** för att påbörja din utvärderingsperiod för de valda prenumerationerna.
  ![Välj prenumeration](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Security Centers kostnadsfria funktioner tillämpas endast på dina virtuella Azure-datorer och VMSS. De kostnadsfria funktionerna tillämpas inte på dina datorer som inte är Azure. Om du väljer Standard tillämpas standardfunktionerna på alla virtuella Azure-datorer, vm-skalningsuppsättningar och icke-Azure-datorer som rapporterar till arbetsytan. Vi rekommenderar att du tillämpar Standard för att tillhandahålla avancerad säkerhet för dina Azure- och icke-Azure-resurser.
   >

## <a name="onboard-non-azure-computers"></a>Inbyggda datorer som inte är Azure-datorer
Security Center kan övervaka säkerhetsstatusen för icke-Azure-datorer men du måste först publicera dessa resurser. Du kan lägga till datorer som inte är Azure från bladet **Komma igång** eller från **beräkningsbladet.** Vi går igenom båda metoderna.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Lägga till nya datorer som inte kommer från **Azure**

1. Återgå till **Komma igång**.
2. Välj fliken **Kom igång**.

   ![Icke-Azure](./media/security-center-onboarding/non-azure.png)

3. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer**. En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill använda.

   ![Lägga till en dator som inte är en Azure-dator][7]

Om du har befintliga arbetsytor visas de under **Lägg till nya icke-Azure-datorer**. Du kan lägga till datorer på en befintlig arbetsyta eller skapa en ny arbetsyta. Om du vill skapa en ny arbetsyta markerar du länken **lägga till en ny arbetsyta**.

### <a name="add-new-non-azure-computers-from-compute"></a>Lägga till nya datorer som inte är Azure från **Beräkning**

**Skapa en ny arbetsyta och lägga till dator**

1. Under **Lägg till nya datorer som inte är Azure**väljer du Lägg till en ny **arbetsyta**.

   ![Lägga till en ny arbetsyta][4]

2. Under **Säkerhet och granskning**väljer du **OMS Workspace** för att skapa en ny arbetsyta.
   > [!NOTE]
   > OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.
3. Ange informationen för arbetsytan under **OMS Workspace.**
4. Under **OMS Workspace**väljer du **OK**. När du har valt OK får du en länk för att hämta en Windows- eller Linux-agent och nycklar som ditt arbetsyte-ID kan använda för att konfigurera agenten.
5. Under **Säkerhet och granskning**väljer du **OK**.

**Markera en befintlig arbetsyta och lägga till dator**

Du kan lägga till en dator genom att följa arbetsflödet från **Onboarding**, som visas ovan. Du kan också lägga till en dator genom att följa arbetsflödet från **Compute**. I det här exemplet använder vi **Compute**.

1. Gå tillbaka till Säkerhetscenters huvudmeny och **översiktsinstrumentpanel.**

   ![Översikt][5]

2. Välj **Beräkna & appar**.
3. Under **& appar**väljer du Lägg till **datorer**.

   ![Compute-bladet][6]

4. Under **Lägg till nya datorer som inte är Azure**väljer du en arbetsyta som du vill ansluta datorn till och klickar på Lägg till **datorer**.

   ![Lägga till datorer][7]

   **Direct** Agent-bladet innehåller en länk för att hämta en Windows- eller Linux-agent samt arbetsyte-ID och nycklar som ska användas för att konfigurera agenten.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du ombord på Azure och icke-Azure-resurser för att dra nytta av Security Centers avancerade säkerhet. Om du vill göra mer med dina inbyggda resurser läser du

- [Aktivera datainsamling](security-center-enable-data-collection.md)
- [Hotinformationsrapport](security-center-threat-report.md)
- [Just-in-time-åtkomst till virtuella datorer](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
