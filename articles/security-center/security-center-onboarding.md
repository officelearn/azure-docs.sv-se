---
title: "Onboarding till Azure Security Center Standard för ökad säkerhet | Microsoft Docs"
description: " Lär dig hur att publicera till Azure Security Center Standard för förbättrad säkerhet. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Onboarding till Azure Security Center Standard för ökad säkerhet
Uppgradera till Security Center Standard dra nytta av förbättrad säkerhet, hantering och skydd för dina hybrid cloud arbetsbelastningar.  Du kan försöka Standard gratis under 60 dagar. Se Security Center [sida med priser](https://azure.microsoft.com/pricing/details/security-center/) för mer information.

Security Center Standard omfattar:

- **Hybrid säkerhet** – få en samlad bild av säkerhet för alla dina lokala och molnet arbetsbelastningar. Tillämpa säkerhetsprinciper och kontinuerligt utvärdera säkerheten för dina hybrid cloud arbetsbelastningar för efterlevnad säkerhetskrav. Samla in, sök efter och analysera säkerhetsdata från flera olika källor, till exempel brandväggar och andra partnerlösningar.
- **Avancerade hotidentifiering** -använda avancerade analyser och Microsoft Intelligent säkerhet Graph för att hämta en kant över utvecklas cyber-attacker.  Utnyttja inbyggda beteendeanalyser och maskinlärning för att identifiera attacker och nolldagarshot. Övervaka nätverk, datorer och molntjänster för att se inkommande attacker och aktiviteter efter intrång. Effektiviseras undersökningen med interaktiva verktyg och kontextbaserad hotinformation.
- **Åtkomst-och** -Block skadlig kod och andra oönskade program genom att använda vitlistning rekommendationer anpassade till din specifika arbetsbelastningar och drivs av machine learning. Minska nätverket risken för angrepp med just-in-time, kontrollerad åtkomst till hanteringsportar på Azure Virtual Machines, vilket drastiskt minskar risken för brute force och andra nätverksattacker.

## <a name="detecting-unprotected-resources"></a>Identifiera oskyddade resurser     
Security Center identifierar automatiskt alla Azure-prenumerationer eller arbetsytor som inte har aktiverats för Security Center Standard. Detta inkluderar Azure-prenumerationer med hjälp av Security Center ledigt och arbetsytor som inte har säkerhetslösning aktiverad.

Du kan uppgradera en hela Azure-prenumeration till standardnivån som ärvs av alla resurser i prenumerationen, eller så kan du definiera en unik princip för att uppgradera en specifik resursgrupp. Om resursen grupprincipinställningar är unika, åsidosätter inte Security Center prisnivå principerna när du uppgraderar prenumerationen till standardnivån. Tillämpning av standarden nivå till en prenumeration gäller bara för de virtuella datorerna i prenumerationen som rapporterar till arbetsytor som skapats av Security Center. Tillämpning av standarden nivån till arbetsytan gäller för alla resurser som rapporterar till arbetsytan.

> [!NOTE]
> Du kanske vill hantera kostnaderna och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. [Mål för lösning](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du använda ett omfång till lösningen och rikta en delmängd av datorerna i arbetsytan.  Om du använder lösningen som mål, visar Security Center arbetsytan som inte har en lösning.
>
>

## <a name="upgrade-an-azure-subscription"></a>Uppgradera en Azure-prenumeration
Uppgradera alla prenumerationer till Standard:
1. Välj under huvudmenyn Security Center **Onboarding**.
2. Under **Onboarding till avancerad säkerhet**, Security Center visar en lista över prenumerationer som är berättigad till onboarding. Du kan uppgradera alla listade prenumerationer genom att välja **gäller standardplan**.

  ![Uppgradera alla prenumerationer][1]

Du uppgraderar ett enskilt abonnemang till Standard: du kan uppgradera en prenumeration från **Onboarding** genom att välja **gäller standardnivån**. Välj prenumerationen om du vill uppgradera en resursgrupp för den här prenumerationen till Standard:
1. Välj en prenumeration.  **Säkerhetsprincip** innehåller information om den resursgrupp som ingår i prenumerationen.
2. Välj prenumerationen eller resursgrupp.

  ![Uppgradera alla prenumerationer][2]

3. Välj **Standard** att uppgradera från lediga till Standard.
4. Välj **Spara**.

> [!NOTE]
> Uppgradera en prenumeration till Standard att aktivera [Automatisk etablering](security-center-enable-data-collection.md) om det tidigare har inaktiverats. Vi rekommenderar att Automatisk etablering av övervakning av agenter.
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
   > Det finns ett scenario där du inte kan lediga eller Standard tillämpas på arbetsytan. Om du väljer lediga tillämpas ledigt funktionerna i Security Center på ditt Azure virtuella datorer bara. Ledigt funktionerna tillämpas inte på Azure-datorer. Om du väljer Standard tillämpas funktionerna som Standard för alla virtuella datorer i Azure och Azure-datorer som rapporterar till arbetsytan. Vi rekommenderar att du använder Standard för att ge avancerad säkerhet för dina Azure och Azure-resurser.
   >
   >

## <a name="onboard-non-azure-computers"></a>Publicera Azure-datorer
Security Center kan övervaka säkerhetsstatusen för icke-Azure-datorer men du måste först publicera dessa resurser. Du kan lägga till Azure-datorer från den **Onboarding** bladet eller från den **Compute** bladet. Vi går igenom båda metoderna.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Lägga till nya Azure-datorer från Onboarding

1. Gå tillbaka till **Onboarding**.   
2. Välj **du vill lägga till nya Azure-datorer**.

  ![Lägg till Azure-dator][3]

Om du har befintliga arbetsytor de finns under **lägga till nya datorer i icke-Azure**. Du kan lägga till datorer i en befintlig arbetsyta eller skapa en ny arbetsyta. Klicka på länken om du vill skapa en ny arbetsyta **lägga till en ny arbetsyta**.

Vi går igenom båda metoderna:

- Skapa en ny arbetsyta och lägger till datorn
- Välj en befintlig arbetsyta och Lägg till dator

**Skapa en ny arbetsyta och lägger till datorn**

1. Under **lägga till nya Azure-datorer**väljer **lägga till en ny arbetsyta**.

   ![Lägg till en ny arbetsyta][4]

2. Under **säkerhet och granska**väljer **OMS-arbetsytan** att skapa en ny arbetsyta.
3. Under **OMS-arbetsytan**, ange information för din arbetsyta.
4. Under **OMS-arbetsytan**väljer **OK**.  När du har valt OK får du en länk för att hämta en Windows- eller Linux-agent och nycklar för ditt arbetsyte-ID som ska användas i Konfigurera agenten.
5. Under **säkerhet och granska**väljer **OK**.

**Välj en befintlig arbetsyta och Lägg till dator**

Du kan lägga till en dator genom att följa arbetsflödet från **Onboarding**, enligt ovan. Du kan också lägga till en dator genom att följa arbetsflödet från **Compute**. I det här exemplet använder vi **Compute**.

1. Gå tillbaka till huvudmenyn i Security Center och **översikt** instrumentpanelen.

   ![Översikt][5]

2. Välj den **Compute** panelen.
3. Under **Compute**väljer **lägga till datorer**.

   ![Compute-bladet][6]

4. Under **lägga till nya Azure-datorer**, Välj en arbetsyta för att ansluta datorn till och klickar på **Lägg till datorer**.

   ![Lägga till datorer][7]

 Den **direkt Agent** bladet innehåller en länk för att ladda ned en Windows- eller Linux-agent och nycklar för ditt arbetsyte-ID som ska användas i Konfigurera agenten.   

## <a name="next-steps"></a>Nästa steg
I den här artikeln du lärt dig hur du publicera Azure och Azure-resurser för att kunna dra nytta av Security Center avancerad säkerhet.  För att göra mer med se dina publicerats så resurser,

- [Aktivera datainsamling](security-center-enable-data-collection.md)
- [Hotinformationsrapport](security-center-threat-report.md)
- [Just-in-time-åtkomst för VM](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
