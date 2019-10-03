---
title: Övervaka och spåra användning av kostnadsfria Azure-tjänster
description: Lär dig hur du kontrollerar användning av kostnadsfria tjänster i Azure-portalen och CSV-filen för användning.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ae8b26f0032c6659da18b822f4f8568995eff82b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709771"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Kontrollera användningen av kostnadsfria tjänster som ingår i ditt kostnadsfria Azure-konto

Du debiteras inte för tjänster som ingår utan kostnad i ett kostnadsfritt Azure-konto, såvida du inte överskrider tjänsternas gränser. För att hålla dig inom gränserna kan du använda Azure-portalen eller användningsfilen till att övervaka och spåra användningen av kostnadsfria tjänster.

## <a name="check-usage-in-the-azure-portal"></a>Kontrollera användning i Azure-portalen

1.  Logga in på [Azure-portalen](https://portal.azure.com).

2.  I det vänstra navigeringsfältet väljer du **Alla tjänster**.

3.  Välj **Prenumerationer**.

4.  Välj den prenumeration som du skapade när du registrerade dig för det kostnadsfria kontot.

    ![Skärmbild som visar alla prenumerationer](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  I översiktsavsnittet visas viktig information om din prenumeration. Det gäller till exempel prenumerations-ID, erbjudandetyp och prenumerationsnamn. Där finns även information om när krediten för ditt kostnadsfria konto upphör.

    ![Skärmbild som visar viktig information om prenumerationen](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Rulla nedåt för att hitta information om din aktuella och prognostiserade kostnad. Kostnaden omfattar tjänstanvändning som inte ingår i ditt kostnadsfria konto samt användning som överskrider gränserna för kostnadsfria tjänster.

    ![Skärmbild som visar kostnadsinformation för prenumerationen](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Den sista delen av översiktsavsnittet innehåller en tabell med användning av kostnadsfria tjänster.

    ![Skärmbild som visar användning av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabellen innehåller följande kolumner:

* **Mätarnamn:** Anger måttenheten för den mätare som förbrukas. Information om tjänst-till-mätar-mappning finns i [Förstå kostnadsfri tjänst-till-mätar-mappning](billing-understand-free-service-meter-mapping.md).
* **Användning/gräns:** Aktuell månads användning och gräns för mätaren. Den här informationen finns även i statusfältet.
* **Status:** Användningsstatus för mätaren. Utifrån ditt användningsmönster kan du ha en av följande statusar:
  * **Används inte:** Du har inte använt mätaren, eller så har användningen för mätaren inte nått faktureringssystemet.
  * **Överskriden den \<Datum>:** Du har överskridit gränsen för mätaren på \<Datum>.
  * **Överskrider sannolikt inte:** Du överskrider sannolikt inte mätarens gräns.
  * **Överskrids den \<Datum>:** Du överskrider sannolikt mätarens gräns den \<Datum>.

## <a name="check-usage-with-the-usage-file"></a>Kontrollera användning med användningsfilen

Användningsfilen innehåller detaljerad information om din Azure-prenumeration. Du kan ladda ned din månatliga och dagliga användningsfil från Azure-kontocenter. Information om hur du laddar ned användningsfilen och förstår den åtkomst som krävs finns i [Hämta faktura och användning](billing-download-azure-invoice-daily-usage-date.md). Om du vill lära dig mer om kolumner i användningsfilen kan du läsa [Förstå termer för din användning](billing-understand-your-usage.md).

Användningsfilen innehåller användningsinformation för både kostnadsfria och betalda tjänster. För mätare för kostnadsfria tjänster läggs **Kostnadsfri** till i slutet av mätarnamnet. Om du vill hitta kostnadsfria mätare öppnar du filen i Excel och filtrerar på **kolumnen Mätarkategori** för celler där det står **- Kostnadsfri** (använd textfilter &rarr; Innehåller-filter).


![Skärmbild som visar användning av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Uppgradera din prenumeration](billing-upgrade-azure-subscription.md)
