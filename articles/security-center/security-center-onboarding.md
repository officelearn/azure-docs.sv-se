---
title: Standard nivå för förbättrad säkerhet – Azure Security Center
description: " Lär dig hur du kan publicera till Azure Security Center standard för förbättrad säkerhet. "
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912060"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Onboarding to Azure Security Center standard för förbättrad säkerhet
Uppgradera till Security Center standard för att dra nytta av förbättrad säkerhets hantering och skydd mot hot för dina hybrid moln arbets belastningar. Du kan prova standard kostnads fritt. Mer information finns på sidan med Security Center [prissättning](https://azure.microsoft.com/pricing/details/security-center/) .

Security Center standard nivån innehåller:

- **Hybrid säkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbets belastningar. Tillämpa säkerhets principer och utvärdera kontinuerligt säkerheten för dina hybrid moln arbets belastningar för att säkerställa efterlevnaden av säkerhets standarder. Samla in, Sök efter och analysera säkerhets data från en mängd olika källor, inklusive brand väggar och andra partner lösningar.
- **Säkerhets aviseringar** – Använd avancerad analys och Microsoft Intelligent Security Graph för att få en gräns för de växande cyberhot-angrepp. Använd inbyggd beteende analys och maskin inlärning för att identifiera attacker och den långsiktiga ingreppen. Övervaka nätverk, datorer och moln tjänster för inkommande attacker och aktiviteter efter intrång. Effektivisera undersökningen med interaktiva verktyg och sammanhangsbaserad Hot information.
- **Åtkomst-och program kontroller** – blockera skadlig kod och andra oönskade program genom att använda vit listning rekommendationer som är anpassade till dina specifika arbets belastningar och drivs av maskin inlärning. Minska nätverks Attacks ytan med just-in-Time-kontrollerad åtkomst till hanterings portar på virtuella Azure-datorer, vilket drastiskt minskar exponeringen för bruten kraft och andra nätverks attacker.

## <a name="detecting-unprotected-resources"></a>Identifiera oskyddade resurser
Security Center identifierar automatiskt eventuella Azure-prenumerationer eller arbetsytor som inte är aktiverade för Security Center Standard. Det inkluderar Azure-prenumerationer med Security Center – Kostnadsfri och arbetsytor som inte har någon aktiverad säkerhetslösning.

Du kan uppgradera en hel Azure-prenumeration till standard nivån, som ärvs av alla resurser som stöds i prenumerationen. Att tillämpa standard nivån på en arbets yta gäller för alla resurser som rapporterar till arbets ytan.

> [!NOTE]
> Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. Med [lösnings mål](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du tillämpa en omfattning för lösningen och rikta in dig på en delmängd av datorerna i arbets ytan. Om du använder lösnings mål, Security Center visar arbets ytan som en lösning.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Uppgradera en Azure-prenumeration eller arbets yta
Så här uppgraderar du en prenumeration eller arbets yta till standard:
1. På huvudmenyn i Security Center väljer du **Komma igång**.
  ![Komma igång](./media/security-center-onboarding/get-started.png)
2. Under **Uppgradera** listar Security Center prenumerationer och arbetsytor som är behöriga för registrering. 
   - Du kan klicka på den expanderbara texten **Använd din utvärderingsversion** för att se en lista över alla prenumerationer och arbetsytor med deras berättigandestatus för utvärderingsversion.
   -    Du kan uppgradera prenumerationer och arbetsytor som inte är berättigade till utvärderingsversionen.
   -    Du kan välja berättigade arbetsytor och prenumerationer för att påbörja din utvärderingsperiod.
3.  Klicka på **Starta utvärdering** för att påbörja din utvärderingsperiod för de valda prenumerationerna.
  ![Välj prenumerations](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Security Centerens kostnads fria funktioner tillämpas bara på dina virtuella Azure-datorer och VMSS. De kostnads fria funktionerna gäller inte för datorer som inte är Azure-datorer. Om du väljer standard tillämpas standard funktionerna på alla virtuella Azure-datorer, VM Scale set och icke-Azure-datorer som rapporterar till arbets ytan. Vi rekommenderar att du använder standard för att tillhandahålla avancerad säkerhet för dina Azure-och icke-Azure-resurser.
   >

## <a name="onboard-non-azure-computers"></a>Publicera icke-Azure-datorer
Security Center kan övervaka säkerhetsstatusen för icke-Azure-datorer men du måste först publicera dessa resurser. Du kan lägga till datorer som inte är Azure-datorer från bladet **komma igång** eller från **Compute** -bladet. Vi går igenom båda metoderna.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Lägg till nya icke-Azure-datorer från **komma igång**

1. Återgå till att **komma igång**.
2. Välj fliken **Kom igång**.

   ![Icke-Azure](./media/security-center-onboarding/non-azure.png)

3. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer**. En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill använda.

   ![Lägga till en dator som inte är en Azure-dator][7]

Om du har befintliga arbets ytor visas de under **Lägg till nya icke-Azure-datorer**. Du kan lägga till datorer till en befintlig arbets yta eller skapa en ny arbets yta. Om du vill skapa en ny arbets yta väljer du länken **Lägg till en ny arbets yta**.

### <a name="add-new-non-azure-computers-from-compute"></a>Lägg till nya icke-Azure-datorer från **Compute**

**Skapa en ny arbets yta och Lägg till dator**

1. Under **Lägg till nya icke-Azure-datorer**väljer du **Lägg till en ny arbets yta**.

   ![Lägg till en ny arbets yta][4]

2. Under **säkerhet och granskning**väljer du **OMS-arbetsyta** för att skapa en ny arbets yta.
   > [!NOTE]
   > OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.
3. Under **OMS-arbetsyta**anger du informationen för din arbets yta.
4. Under **OMS-arbetsyta**väljer du **OK**. När du har valt OK får du en länk för att ladda ned en Windows-eller Linux-Agent och-nycklar för ditt arbetsyte-ID som du kan använda för att konfigurera agenten.
5. Under **säkerhet och granskning**väljer du **OK**.

**Välj en befintlig arbets yta och Lägg till dator**

Du kan lägga till en dator genom att följa arbets flödet från **onboarding**, som du ser ovan. Du kan också lägga till en dator genom att följa arbets flödet från **Compute**. I det här exemplet använder vi **Compute**.

1. Gå tillbaka till Security Centerens huvud meny och **översikts** instrument panel.

   ![Översikt][5]

2. Välj **compute &-appar**.
3. Under **compute & appar**väljer du **Lägg till datorer**.

   ![Compute-bladet][6]

4. Under **Lägg till nya icke-Azure-datorer**väljer du en arbets yta för att ansluta datorn till och klickar på **Lägg till datorer**.

   ![Lägga till datorer][7]

   Bladet **Direct agent** innehåller en länk för att ladda ned en Windows-eller Linux-Agent samt det arbetsyte-ID och nycklar som ska användas för att konfigurera agenten.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att publicera Azure-och icke-Azure-resurser för att kunna dra nytta av Security Centers avancerade säkerhet. Information om hur du gör mer med dina inbyggda resurser finns i

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
