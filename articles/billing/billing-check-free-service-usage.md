---
title: Övervaka och spåra användningen av Azure kostnadsfria tjänster | Microsoft Docs
description: Lär dig att kontrollera användningen av kostnadsfria tjänster. Använd Azure portal och användning csv.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 4e940a12cd57ef136cfd9ead298f9afcd2d6ad1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617831"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrollera användningen av kostnadsfria tjänster ingår kostnadsfritt Azure-konto 

Du debiteras inte för tjänster som ingår utan kostnad med kostnadsfria Azure-konto, såvida du överskrider begränsningarna i dessa tjänster. För att fortsätta vara med gränserna, kan du antingen använda Azure portal eller i din användningsfil att övervaka och spåra användningen av kostnadsfria tjänster. 

## <a name="check-usage-on-the-azure-portal"></a>Kontrollera syntax på Azure portal

1.  Logga in på [Azure Portal](https://portal.azure.com).

2.  Vänstra området, Välj **alla tjänster**.

3.  Välj **Prenumerationer**.

4.  Välj den prenumeration som du skapade när du registrerade dig kostnadsfritt konto.

    ![Skärmbild som visar alla prenumerationer](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Översiktsavsnittet visar viktig information om din prenumeration, till exempel prenumerations-ID, Erbjud typ och namn på prenumeration. Du kan också hitta information om när din kredit för kostnadsfritt konto skulle upphöra att gälla.

    ![Skärmbild som visar viktig information för prenumeration](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Rulla ned till hitta information om dina aktuella och prognostiserade kostnader. Kostnaden omfattar användning av tjänster som inte ingår i ditt kostnadsfria konto och användning som överskrider gränserna för kostnadsfria tjänster. 

    ![Skärmbild som visar kostnadsinformation för prenumeration](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Den sista delen av översiktsavsnittet har en tabell på användningen av kostnadsfria tjänster. 

    ![Skärmbild som visar användningen av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabellen innehåller följande kolumner:

* **Mätarnamn:** Anger måttenhet för den mätning som används. Läs om tjänsten för mätningsmappning i [förstå kostnadsfria tjänsten för mätningsmappning](billing-understand-free-service-meter-mapping.md).
* **Användningsgränsen:** Aktuell månad användning och gränsen för mätaren. Du kan också använda informationen i statusfältet.
* **Status:** Status för användning av. Baserat på ditt användningsmönster, kan du ha en av dessa lagar.
  * **Inte är i användning:** Du inte har använt mätaren eller användningen för mätaren inte har nått faktureringssystemet.
  * **Överskrids den \<datum >:** Du har överskridit gränsen för mätaren för \<datum >.
  * **Kommer sannolikt inte överstiga:** Är du sannolikt inte kommer att överskrida gränsen för mätaren.
  * **Överstigs den \<datum >:** Du kommer förmodligen att överskrider gränsen för mätaren för \<datum >.

## <a name="check-usage-through-the-usage-file"></a>Kontrollera syntax igenom användningsfilen

Din användningsfil ger detaljerad information om din Azure-prenumeration. Du kan hämta din per månad och dag användningsfil från Azure Kontocenter. Läs hur du hämtar användningsfilen och förstå den åtkomst som krävs i [hämta faktura och användning](billing-download-azure-invoice-daily-usage-date.md). Läs om kolumner i användningsfilen i [Förstå villkoren på din användning](billing-understand-your-usage.md).

Användningsfilen har användningsinformation för både kostnadsfria och kostnadsbelagda tjänster. Kostnadsfri tjänst taxor skulle ha **kostnadsfri** tillägg i slutet av mätningsnamn. Du hittar kostnadsfria taxor, öppna filen i excel och filter i **underkategori för kolumnen** för celler som har texten **– kostnadsfritt** (Använd Textfilter &rarr; filtret innehåller) &nbsp;

![Skärmbild som visar användningen av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).