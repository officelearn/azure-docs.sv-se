---
title: Övervaka och spåra användningen av kostnadsfria Azure-tjänst
description: Lär dig mer om att kontrollera gratistjänst syntax i Azure portal och användning CSV-filen.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491430"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Kontrollera kostnadsfria tjänstanvändning ingår kostnadsfritt Azure-konto

Du debiteras inte för tjänster som ingår utan kostnad med ett kostnadsfritt Azure-konto, såvida du överskrider begränsningarna i tjänsterna. För att fortsätta vara i gränserna, kan du använda Azure portal eller i din användningsfil att övervaka och spåra användningen av kostnadsfria tjänsten.

## <a name="check-usage-in-the-azure-portal"></a>Kontrollera syntax i Azure portal

1.  Logga in på [Azure Portal](https://portal.azure.com).

2.  I det vänstra navigeringsfönstret i området väljer **alla tjänster**.

3.  Välj **Prenumerationer**.

4.  Välj den prenumeration som du skapade när du registrerade dig kostnadsfritt konto.

    ![Skärmbild som visar alla prenumerationer](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Översiktsavsnittet visar viktig information om din prenumeration. Prenumerations-ID, typ och namn på prenumeration. Du kan också hitta information när din kredit för kostnadsfria konto upphör att gälla.

    ![Skärmbild som visar viktig information för prenumeration](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Rulla ned till hitta information om dina aktuella och prognostiserade kostnader. Kostnaden omfattar tjänstanvändning som inte ingår i ditt kostnadsfria konto och din användning som överskrider gränserna för kostnadsfria tjänster.

    ![Skärmbild som visar kostnadsinformation för prenumeration](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Den sista delen av översiktsavsnittet har en tabell som visar kostnadsfri användning.

    ![Skärmbild som visar användningen av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabellen innehåller följande kolumner:

* **Mätarnamn:** Anger måttenhet för den mätning som används. Läs om tjänsten för mätningsmappning i [förstå kostnadsfria tjänsten för mätningsmappning](billing-understand-free-service-meter-mapping.md).
* **Användningsgränsen:** Aktuell månad användning och gränsen för mätaren. Du kan också använda informationen i statusfältet.
* **Status:** Status för användning av. Baserat på ditt användningsmönster, kan du ha något av följande lagar:
  * **Inte är i användning:** Du inte har använt mätaren eller användningen för mätaren inte har nått faktureringssystemet.
  * **Överskrids den \<datum >:** Du har överskridit gränsen för mätaren för \<datum >.
  * **Kommer sannolikt inte överstiga:** Är du sannolikt inte kommer att överskrida gränsen för mätaren.
  * **Överstigs den \<datum >:** Du kommer förmodligen att överskrider gränsen för mätaren för \<datum >.

## <a name="check-usage-with-the-usage-file"></a>Kontrollera syntax med användningsfilen

Din användningsfil visar detaljerad information om din Azure-prenumeration. Du kan ladda ner användningsdatafilen månads- och daglig från Azure Kontocenter. Läs hur du hämtar användningsfilen och förstå den åtkomst som krävs i [hämta faktura och användning](billing-download-azure-invoice-daily-usage-date.md). Läs om kolumner i användningsfilen i [Förstå villkoren på din användning](billing-understand-your-usage.md).

Användningsfilen har användningsinformation för både kostnadsfria och kostnadsbelagda tjänster. Kostnadsfri tjänst taxor skulle ha **kostnadsfri** tillägg i slutet av mätningsnamn. Du hittar kostnadsfria taxor, öppna filen i excel och filter i **underkategori för kolumnen** för celler som har texten **– kostnadsfritt** (Använd Textfilter &rarr; filtret innehåller).


![Skärmbild som visar användningen av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Uppgradera din prenumeration](billing-upgrade-azure-subscription.md)
