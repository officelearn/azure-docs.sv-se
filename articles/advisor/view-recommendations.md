---
title: Visa Azure Advisor-rekommendationer som är viktigast för dig
description: Visa och filtrera Azure Advisor-rekommendationer för att minska bruset.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052844"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Visa Azure Advisor-rekommendationer som är viktigast för dig

Azure Advisor tillhandahåller rekommendationer för att hjälpa dig att optimera dina Azure-distributioner. I Advisor har du åtkomst till några funktioner som hjälper dig att begränsa dina rekommendationer till dem som är viktiga för dig.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurera prenumerationer och resursgrupper

Advisor ger dig möjlighet att välja prenumerationer och resursgrupper som är viktigast för dig och din organisation. Du kan bara se rekommendationer för prenumerationer och resursgrupper som du väljer. Som standard markeras alla. Konfigurationsinställningar gäller i prenumeration eller resursgrupp gruppen, så samma inställningar som gäller för alla som har åtkomst till den prenumeration eller resursgrupp gruppen. Inställningarna kan ändras i Azure portal eller programmässigt.

Göra ändringar i Azure portal:

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.

1. Välj **Configuration** på menyn.

   ![Menyn för Advisor-konfiguration](./media/view-recommendations/configuration.png)

1. Markera kryssrutan den **inkludera** kolumn för alla prenumerationer och resursgrupper för att ta emot Advisor-rekommendationer. Om rutan är tillgänglig kan du inte har behörighet att göra en konfigurationsändring på gruppen prenumeration eller resursgrupp. Läs mer om [behörigheter i Azure Advisor](permissions.md).

1. Klicka på **tillämpa** längst ned på sidan när du har gjort en ändring.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrera vyn i Azure portal

Konfigurationsinställningar förblir aktiva tills ändrats. Om du vill begränsa vy över rekommendationer för en enda visning, kan du använda listrutor tillhandahålls överst i panelen Advisor. Du kan välja de prenumerationer, resurstyper och rekommendation status som du vill se från panelerna översikt, hög tillgänglighet, säkerhet, prestanda, kostnad och alla rekommendationer.

   ![Filtrera Advisor-menyn](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Ignorera och skjuter upp rekommendationer

Azure Advisor kan du stänga eller skjuta upp rekommendationer för en enskild resurs. Om du stänger en rekommendation visas inte det igen om du aktiverar den manuellt. Dock kan skjuter upp en rekommendation du ange en tid efter vilken rekommendationen aktiveras automatiskt igen. Skjuter upp kan göras i Azure portal eller programmässigt.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Skjuta upp en enda rekommendationen i Azure portal 

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.
1. Välj en kategori för rekommendationen att visa rekommendationer
1. Välj en rekommendation från listan över rekommendationer
1. Välj Skjut upp eller ignorera för den rekommendationen som du vill skjuta upp eller avvisa

     ![Filtrera Advisor-menyn](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Skjut upp och avvisa en flera rekommendationer i Azure portal

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.
1. Välj en rekommendation kategori för att visa rekommendationer.
1. Välj en rekommendation från listan över rekommendationer.
1. Markera kryssrutan längst till vänster på raden för alla resurser som du vill skjuta upp eller ignorera rekommendationen.
1. Välj **Skjut upp** eller **Stäng** på upp till vänster i tabellen.

     ![Filtrera Advisor-menyn](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Du behöver deltagare eller ägare behörighet att Stäng eller skjuta upp en rekommendation. Läs mer om behörigheter i Azure Advisor.

> [!NOTE]
> Om markeringsrutor är inaktiverade kan laddas fortfarande rekommendationer. Vänta tills alla rekommendationer att läsa in innan du försöker att skjuta upp och avvisa.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Återaktivera en uppskjutna eller avvisade rekommendation

Du kan aktivera en rekommendation som har skjutits upp eller Avvisad. Den här åtgärden kan utföras i Azure portal eller programmässigt. På Azure Portal:

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure-portalen.

1. Ändra filtret på översiktspanelen till **uppskjuten**. Advisor visar uppskjutna eller avvisade rekommendationer.

    ![Filtrera Advisor-menyn](./media/view-recommendations/activate-postponed.png)

1. Välj en kategori för att se **uppskjuten** och **avvisat** rekommendationer.

1. Välj en rekommendation från listan över rekommendationer. Då öppnas rekommendationer med de **uppskjuten & avvisade** fliken som redan har valt att visa de resurser som den här rekommendationen har skjutits upp eller Avvisad.

1. Klicka på **aktivera** i slutet av raden. När du klickar på är rekommendationen aktiva för den här resursen och så tas bort från den här tabellen. Rekommendationen visas nu i den **Active** fliken.
 
     ![Filtrera Advisor-menyn](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklarar hur du kan visa rekommendationer som är viktigast för dig i Azure Advisor. Mer information om Advisor finns: 

- [Vad är Azure Advisor?](advisor-overview.md)
- [Komma igång med Advisor](advisor-get-started.md)
- [Behörigheter i Azure Advisor](permissions.md)



