---
title: Visa Azure Advisor-rekommendationer som är viktiga för dig
description: Visa och filtrera Azure Advisor-rekommendationer för att minska bruset.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422363"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Visa Azure Advisor-rekommendationer som är viktiga för dig

Azure Advisor innehåller rekommendationer som hjälper dig att optimera dina Azure-distributioner. Inom Advisor har du tillgång till några funktioner som hjälper dig att begränsa dina rekommendationer till endast de som är viktiga för dig.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurera prenumerationer och resursgrupper

Advisor ger dig möjlighet att välja Prenumerationer och resursgrupper som är viktiga för dig och din organisation. Du ser bara rekommendationer för de prenumerationer och resursgrupper som du väljer. Som standard är alla markerade. Konfigurationsinställningarna gäller för prenumerationen eller resursgruppen, så samma inställningar gäller för alla som har åtkomst till den prenumerationen eller resursgruppen. Konfigurationsinställningar kan ändras i Azure-portalen eller programmässigt.

Så här gör du ändringar i Azure-portalen:

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.

1. Välj **Konfiguration** på menyn.

   ![Konfigurationsmenyn för rådgivare](./media/view-recommendations/configuration.png)

1. Markera kryssrutan i kolumnen **Inkludera** för prenumerationer eller resursgrupper som ska få Advisor-rekommendationer. Om rutan är inaktiverad kanske du inte har behörighet att göra en konfigurationsändring för den prenumerationen eller resursgruppen. Läs mer om [behörigheter i Azure Advisor](permissions.md).

1. Klicka på **Använd** längst ned när du har gjort en ändring.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrera vyn i Azure-portalen

Konfigurationsinställningarna förblir aktiva tills de ändras. Om du vill begränsa vyn med rekommendationer för en enskild visning kan du använda listrutan högst upp på panelen Advisor. I panelerna Översikt, Hög tillgänglighet, Säkerhet, Prestanda, Kostnad och Alla Rekommendationer kan du välja den prenumeration, resurstyper och rekommendationsstatus som du vill visa.

   ![Menyn Klassificeringsfiltrering](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Avvisa och skjuta upp rekommendationer

Med Azure Advisor kan du avvisa eller skjuta upp rekommendationer för en enda resurs. Om du avvisar en rekommendation visas den inte igen om du inte aktiverar den manuellt. Om du skjuter upp en rekommendation kan du dock ange en varaktighet efter vilken rekommendationen aktiveras automatiskt igen. Uppskjutande kan göras i Azure-portalen eller programmässigt.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Skjuta upp en rekommendation i Azure-portalen 

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.
1. Välj en rekommendationskategori för att visa dina rekommendationer
1. Välj en rekommendation i listan med rekommendationer
1. Välj Skjuta upp eller avvisa för den rekommendation du vill skjuta upp eller avvisa

     ![Menyn Klassificeringsfiltrering](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Skjuta upp eller avvisa flera rekommendationer i Azure-portalen

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.
1. Välj en rekommendationskategori om du vill visa dina rekommendationer.
1. Välj en rekommendation i listan med rekommendationer.
1. Markera kryssrutan till vänster på raden för alla resurser som du vill skjuta upp eller avvisa rekommendationen.
1. Välj **Skjut upp** eller **Stäng** längst upp till vänster i tabellen.

     ![Menyn Klassificeringsfiltrering](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Du behöver behörighet till deltagare eller ägare för att avvisa eller skjuta upp en rekommendation. Läs mer om behörigheter i Azure Advisor.

> [!NOTE]
> Om markeringsrutorna är inaktiverade kan rekommendationer fortfarande läsas in. Vänta tills alla rekommendationer läses in innan du försöker skjuta upp eller avvisa.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Återaktivera en uppskjuten eller avvisad rekommendation

Du kan aktivera en rekommendation som har skjutits upp eller avvisats. Den här åtgärden kan göras i Azure-portalen eller programmässigt. På Azure Portal:

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.

1. Ändra filtret på översiktspanelen till **Uppskjuten**. Rådgivaren visar sedan uppskjutna eller avvisade rekommendationer.

    ![Menyn Klassificeringsfiltrering](./media/view-recommendations/activate-postponed.png)

1. Välj en kategori om du vill se **Uppskjutna** och **avvisade** rekommendationer.

1. Välj en rekommendation i listan med rekommendationer. Detta öppnar rekommendationer med fliken **Uppskjuten & Redan markerad** för att visa de resurser som rekommendationen har skjutits upp eller avvisats för.

1. Klicka på **Aktivera** i slutet av raden. När du har klickat på den är rekommendationen aktiv för den resursen och tas därför bort från den här tabellen. Rekommendationen visas nu på fliken **Aktiv.**
 
     ![Menyn Klassificeringsfiltrering](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du kan visa rekommendationer som är viktiga för dig i Azure Advisor. Mer information om Advisor finns i: 

- [Vad är Azure Advisor?](advisor-overview.md)
- [Komma igång med Advisor](advisor-get-started.md)
- [Behörigheter i Azure Advisor](permissions.md)



