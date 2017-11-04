---
title: "Snabbstartsguide för Azure Security Center | Microsoft Docs"
description: "Den här artikeln hjälper dig att komma igång snabbt med Azure Security Center och guidar dig genom komponenterna för säkerhetsövervakning och principhantering och länkar till nästa steg."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Snabbstartsguide för Azure Security Center
Den här artikeln hjälper dig att komma igång med Azure Security Center och guidar dig genom komponenterna för säkerhetsövervakning och principhantering i Security Center.

## <a name="prerequisites"></a>Krav
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Den kostnadsfria nivån av Security Center aktiveras automatiskt på alla Azure-prenumerationer och ger säkerhetsprincip och kontinuerlig security assessment tillämplig säkerhetsrekommendationer som hjälper dig att skydda dina Azure-resurser.

Security Center öppnas från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Om du vill ta reda på mer om Azure Portal går du till [portaldokumentationen](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Behörigheter
I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs tillhör. Se [behörigheter i Azure Security Center](security-center-permissions.md) för mer information om roller och tillåtna åtgärder i Security Center.

## <a name="data-collection"></a>Datainsamling
Security Center samlar in data från dina virtuella Azure-datorer (VM) och Azure-datorer att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent som läser olika säkerhetsrelaterade konfigurationer och händelseloggar på datorn och kopierar data till din arbetsyta för analys. Security Center tillhandahåller Microsoft Monitoring Agent på alla befintliga stöds virtuella Azure-datorer och nya filer som skapas. Se [Aktivera datainsamling](security-center-enable-data-collection.md) vill veta mer om hur datainsamling fungerar.

Automatisk etablering rekommenderas och krävs för prenumerationer på standardnivån av Security Center. Inaktivera automatisk etablering gränser säkerhetsövervakning för dina resurser.

Se [Security Center priser](security-center-pricing.md) lära dig mer om den kostnadsfria och Standard prisnivåer.

I följande steg beskrivs hur du får åtkomst till och använder komponenterna i Security Center.

> [!NOTE]
> I den här artikeln beskrivs tjänsten genom en exempeldistribution. Artikeln är alltså inte en steg-för-steg-guide.
>
>

## <a name="access-security-center"></a>Öppna Security Center
Följ anvisningarna för att öppna Security Center i portalen:

1. På menyn **Microsoft Azure** väljer du **Security Center**.

   ![Azure-menyn][1]
2. Om du öppnar Security Center för första gången visas **välkomstbladet**. Välj **starta Security Center** att öppna **Security Center**.
   ![Välkomstskärmen][10]
3. När du startar Security Center från bladet Välkommen eller välj Security Center från Microsoft Azure-menyn **Security Center** öppnas. Om du enkelt vill komma åt bladet **Security Center** framöver markerar du alternativet **Fäst bladet på instrumentpanelen** (längst upp till höger).
   ![Alternativet Fäst bladet på instrumentpanelen][2]

## <a name="use-security-center"></a>Använda Security Center
Du kan konfigurera säkerhetsprinciper för dina Azure-prenumerationer och -resursgrupper. Vi ska konfigurera en säkerhetsprincip för din prenumeration:

1. Välj på huvudmenyn Security Center **säkerhetsprincip**.
2. Under **Security Center - säkerhetsprincip**, välja en prenumeration.
3. Under **säkerhetsprincip - datainsamling**, **Automatisk etablering** är aktiverat. Security Center tillhandahåller Microsoft Monitoring Agent på alla befintliga stöds virtuella Azure-datorer och nya filer som skapas.

    ![Säkerhetsprincip][12]

4. På den **säkerhetsprincip** bladet Välj komponenten princip **säkerhetsprincip**.

     ![Säkerhetsprincip][11]

5. Under **visa rekommendationer för**, aktivera rekommendationer som du vill ska visas som en del av din säkerhetsprincip. Exempel:

   * Ange **systemuppdateringar** till **på** genomsökningar alla stöd för virtuella datorer för saknade OS-uppdateringar.
   * Ange **OS säkerhetsrisker** till **på** genomsökningar alla stöd för virtuella datorer för att identifiera OS-konfigurationer som kan göra den virtuella datorn mer sårbara för angrepp.

6. Välj **Spara**.

### <a name="view-recommendations"></a>Visa rekommendationer
1. Gå tillbaka till bladet **Security Center** och välj panelen **Rekommendationer**. Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem visas rekommendationer på bladet **Rekommendationer**.
   ![Rekommendationer i Azure Security Center][5]
2. Markera en rekommendation på bladet **Rekommendationer** om du vill se mer information och/eller vidta åtgärder för att lösa problemet.

### <a name="view-the-security-state-of-your-resources"></a>Se säkerhetsstatus för dina resurser
1. Gå tillbaka till bladet **Security Center**. Den **förebyggande** på instrumentpanelen innehåller indikatorer gällande säkerhetsstatus för virtuella datorer, nätverk, data och program.
2. Välj **Compute** vill visa mer information. Den **Compute** blad öppnas som visar tre flikar:

  - **Översikt över** -innehåller övervakning och rekommendationer för Virtuella datorer.
  - **Virtuella datorer och datorer** -visar det aktuella tillståndet för säkerhet för alla virtuella datorer och datorer.
  - **Molntjänster** -listar webb- och arbetsroller roller som övervakas via Security Center.

    ![Beräkna säkerhetshälsa][6]

3. På den **översikt** väljer du en rekommendation under för att visa mer information och/eller vidta åtgärder för att konfigurera nödvändiga kontroller.
4. På den **virtuella datorer och datorer** väljer du en resurs för att visa ytterligare information.

### <a name="view-security-alerts"></a>Visa säkerhetsaviseringar
1. Gå tillbaka till bladet **Security Center** och välj panelen **Säkerhetsaviseringar**. Bladet **Säkerhetsaviseringar** öppnas och visar en lista över aviseringar. Aviseringarna genereras av Security Centers analys av dina säkerhetsloggar och din nätverksaktivitet. Aviseringar från integrerade partnerlösningar ingår.
   ![Säkerhetsaviseringar i Azure Security Center][7]

2. Välj en avisering om du vill visa ytterligare information. I det här exemplet ska vi väljer **ändras för systemet dump filter har identifierats**. Blad som kan ger mer information om aviseringen öppnas.
   ![Information om säkerhetsaviseringar i Azure Security Center][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Visa hälsotillstånd för dina partnerlösningar
1. Gå tillbaka till bladet **Security Center**. Den **säkerhetslösningar** kan du snabbt kan övervaka hälsostatusen på de partnerlösningar som är integrerade i azureprenumerationen.
2. Välj den **säkerhetslösningar** panelen. Då öppnas ett blad som visar lista över alla partnerlösningar som är anslutna till Security Center.
   ![Partnerlösningar][9]
3. Markera en partnerlösning. Då öppnas ett blad som visar status för partnerlösningen och resurserna som är kopplade till lösningen. Om du klickar på **Solution console (Lösningskonsol)** öppnas partnerhanteringsfunktionen för lösningen i fråga.

   ![Partnerlösningar][13]

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du introducerats för komponenterna säkerhetsövervakning och principhantering i Security Center. Nu när du är bekant med Security Center kan du prova följande steg:

* Konfigurera en säkerhetsprincip för din Azure-prenumeration, se [ställa in säkerhetsprinciper i Azure Security Center](security-center-policies.md).
* Använder rekommendationerna i Security Center hjälper dig skydda dina Azure-resurser, se [hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).
* Granska och hantera din aktuella säkerhetsaviseringar, se [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).
- Lär dig mer om hur du integrerar med partners för att förbättra den övergripande säkerheten finns [Partner och lösningar Integration](security-center-partner-integration.md).
- Lär dig hur data hanteras och skyddade i Security Center, se [datasäkerhet i Azure Security Center](security-center-data-security.md).
* Lär dig mer om [funktionerna för avancerad hotidentifiering](security-center-detection-capabilities.md) som medföljde [standardnivån](security-center-pricing.md) i Security Center. Standardnivån erbjuds kostnadsfritt de första 60 dagarna.
* Om du har frågor om hur du använder Security Center går du till [Vanliga frågor och svar om Azure Security Center](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
