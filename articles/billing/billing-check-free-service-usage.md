---
title: Övervaka och spåra användning av kostnadsfria Azure-tjänster
description: Lär dig hur du kontrollerar användningen av kostnadsfria tjänster i Azure-portalen.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c402327c0f419578fdfa7e4f95fd30e47098c918
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224082"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrollera användningen av kostnadsfria tjänster som ingår i ditt kostnadsfria Azure-konto

Du debiteras inte för tjänster som ingår utan kostnad i ditt kostnadsfria Azure-konto, såvida du inte överskrider tjänsternas användningsgränser. För att hålla dig inom gränserna kan du använda Azure-portalen till att spåra användningen av kostnadsfria tjänster.

## <a name="check-usage-in-the-azure-portal"></a>Kontrollera användning i Azure-portalen

1.  Logga in på [Azure-portalen](https://portal.azure.com).

2.  Sök efter **Prenumerationer**.

    ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/billing-check-usage-of-free-services/billing-search-subscriptions.png)

3.  Välj den prenumeration som skapades när du registrerade det kostnadsfria Azure-kontot.

4.  Rulla nedåt så att du ser tabellen med användning av kostnadsfria tjänster.

    ![Skärmbild som visar användning av kostnadsfria tjänster](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabellen innehåller följande kolumner:

* **Mätare:** Anger måttenheten för den tjänsten som förbrukas.
* **Användning/gräns:** Aktuell månads användning och gräns för mätaren.
* **Status:** Användningsstatus för tjänsten. Baserat på din användning kan du ha en av följande statusar:
  * **Används inte:** Du har inte använt mätaren, eller så har användningen för mätaren inte nått faktureringssystemet.
  * **Överskriden den \<Datum>:** Du har överskridit gränsen för mätaren på \<Datum>.
  * **Överskrider sannolikt inte:** Du överskrider sannolikt inte mätarens gräns.
  * **Överskrids den \<Datum>:** Du överskrider sannolikt mätarens gräns den \<Datum>.

> [!IMPORTANT]
>
> Kostnadsfria tjänster är bara tillgängliga för prenumerationen som skapades när du registrerade det kostnadsfria Azure-kontot. Om du inte ser tabellen med kostnadsfria tjänster i prenumerationsöversikten är de inte tillgängliga för prenumerationen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Uppgradera ditt kostnadsfria Azure-konto](billing-upgrade-azure-subscription.md)
