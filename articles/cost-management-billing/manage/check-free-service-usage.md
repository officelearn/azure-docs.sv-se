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
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992834"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrollera användningen av kostnadsfria tjänster som ingår i ditt kostnadsfria Azure-konto

Du debiteras inte för tjänster som ingår utan kostnad i ditt kostnadsfria Azure-konto, såvida du inte överskrider tjänsternas användningsgränser. För att hålla dig inom gränserna kan du använda Azure-portalen till att spåra användningen av kostnadsfria tjänster.

## <a name="check-usage-in-the-azure-portal"></a>Kontrollera användning i Azure-portalen

1.  Logga in på [Azure-portalen](https://portal.azure.com).

2.  Sök efter **Prenumerationer**.

    ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Välj den prenumeration som skapades när du registrerade det kostnadsfria Azure-kontot.

4.  Rulla nedåt så att du ser tabellen med användning av kostnadsfria tjänster.

    ![Skärmbild som visar användning av kostnadsfria tjänster](./media/check-free-service-usage/subscription-usage-free-services.png)

    Tabellen innehåller följande kolumner:

* **Mätare:** Identifierar mått enheten för den tjänst som förbrukas.
* **Användning/gräns:** Nuvarande månads användning och gräns för mätaren.
* **Status:** Tjänstens användnings status. Baserat på din användning kan du ha en av följande statusar:
  * **Används inte:** Du har inte använt mätaren eller användningen för mätaren har inte nått fakturerings systemet.
  * **Överskrids \<>:** Du har överskridit gränsen för mätning på \<s datum >.
  * **Troligen inte överstiga:** Du är troligen inte större än begränsningen för mätaren.
  * **Överskrider \<s datum >:** Du kan förmodligen överskrida gränsen för mätningen på \<datum >.

> [!IMPORTANT]
>
> Kostnadsfria tjänster är bara tillgängliga för prenumerationen som skapades när du registrerade det kostnadsfria Azure-kontot. Om du inte ser tabellen med kostnadsfria tjänster i prenumerationsöversikten är de inte tillgängliga för prenumerationen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Uppgradera ditt kostnadsfria Azure-konto](upgrade-azure-subscription.md)
