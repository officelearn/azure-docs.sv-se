---
title: "Övervaka och spåra användning av gratistjänster - Azure | Microsoft Docs"
description: "Lär dig att kontrollera användningen av gratistjänster. Använda Azure portal och användning csv."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 27ff6c92904a0b32cd4a37c8b1930adc121a7231
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrollera användningen av ledigt tjänster som ingår i din kostnadsfritt Azure-konto 

Du debiteras inte för tjänster som ingår kostnadsfritt i kostnadsfritt Azure-konto, såvida inte du överskrider gränserna för dessa tjänster. Om du vill vara med gränserna som använda du antingen Azure-portalen eller användning av filen att övervaka och spåra användningen av gratistjänster. 

## <a name="check-usage-on-the-azure-portal"></a>Kontrollera användning på Azure portal

1.  Logga in på [Azure-portalen]( http://portal.azure.com).

2.  Längst ned i navigeringen till vänster, Välj **fler tjänster**.

3.  Välj **prenumerationer**.

4.  Välj den prenumeration som du skapade när du registrerade dig kostnadsfritt konto.

    ![Skärmbild som visar alla prenumerationer](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Översiktsavsnittet visar grundläggande information om din prenumeration, till exempel prenumerations-ID, erbjuder typ och namn. Du kan också hitta information om när kreditkortet kostnadsfritt konto skulle upphöra att gälla.

    ![Skärmbild som visar viktig information för prenumeration](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Rulla hitta information om dina aktuella och förväntade kostnader. Kostnaden innefattar användning av tjänster som inte ingår i din kostnadsfritt konto- och användningsdata som överskrider gränserna för gratistjänster. 

    ![Skärmbild som visar prenumeration kostnadsinformation](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Den sista delen av översiktsavsnittet har en tabell på användning av gratistjänster. 

    ![Skärmbild som visar användningen av gratistjänster](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabellen innehåller följande kolumner:

* **Namn på mätaren:** identifierar måttenheten för mätaren används. Läs om tjänsten för mätaren mappning i [förstå kostnadsfri tjänst för mätaren mappning](billing-understand-free-service-meter-mapping.md). 
* **Användningsnivån:** aktuella månaden användnings- och gränsen för mätaren. Du kan också använda informationen i statusfältet.
* **Status:** användning status för mätaren. Baserat på ditt användningsmönster, kan du ha något av dessa lagar.
  * **Inte i användning:** du inte har använt mätaren eller användning för mätaren inte har nått faktureringssystemet.
  * **Överskridits för \<datum >:** du har överskridit gränsen för mätaren för \<datum >.
  * **Osannolikt att överskridas:** du troligen inte överskrider gränsen för mätaren.
  * **Överskrider på \<datum >:** troligen kommer att överskrida gränsen för mätaren på \<datum >.


## <a name="check-usage-through-the-usage-file"></a>Kontrollera användning i filen för användning

Användning-filen innehåller detaljerad information för din Azure-prenumeration. Du kan hämta din månatliga och daglig användning filen från Azure Kontocenter. Information om hur du hämtar filen användningen och förstå åtkomsten som krävs finns [hämta faktura och användning](billing-download-azure-invoice-daily-usage-date.md). Läs om kolumner i filen användning i [Förstå villkoren på din användning](billing-understand-your-usage.md). 

Användning-filen innehåller användningsinformation för både gratis och betald tjänster. Kostnadsfria tjänsten mätare skulle ha **lediga** läggas till i slutet av namnet på mätaren. Att hitta ledigt mätare, öppna filen i excel och filter den **mätaren kategori kolumnen** för celler som innehåller text **- ledigt** (Textfilter &rarr; filtret innehåller)&nbsp;

![Skärmbild som visar användningen av gratistjänster](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
