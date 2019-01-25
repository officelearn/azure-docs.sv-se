---
title: Inga prenumerationer hittades fel - portalen för inloggning i Azure | Microsoft Docs
description: Ger lösningen för ett problem där inga prenumerationer hittades fel uppstår när loggar in på Azure portal eller Azure kontocenter.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: c6a2f14900d3a0d6f9e16e9b0c6d0bdfff97d6b5
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903844"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Inga prenumerationer hittades logga fel för Azure-portalen eller Azure-kontocenter

Du kan få ett meddelande om ”inga prenumerationer hittades” när du försöker logga in på den [Azure-portalen](https://portal.azure.com/) eller [Azure Kontocenter](https://account.windowsazure.com/Subscriptions). Den här artikeln innehåller en lösning på problemet.

## <a name="symptom"></a>Symtom

När du försöker logga in på den [Azure-portalen](https://portal.azure.com/) eller [Azure kontocenter](https://account.windowsazure.com/Subscriptions), du får följande felmeddelande visas: ”Inga prenumerationer hittades”.

## <a name="cause"></a>Orsak

Det här problemet uppstår om du valde i fel katalog, eller om ditt konto inte har tillräcklig behörighet. 

## <a name="solution"></a>Lösning

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenario 1: Felmeddelande tas emot i den [Azure-portalen](https://portal.azure.com)

Att åtgärda problemet:

* Kontrollera att rätt Azure-katalogen har valts genom att klicka på ditt konto längst upp till höger.

  ![Välj katalogen längst upp höger på Azure portal](./media/billing-no-subscriptions-found/directory-switch.png)
* Om rätt Azure-katalogen har valts men du fortfarande får felmeddelandet [tilldela rollen ägare till ditt konto](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenario 2: Felmeddelande tas emot i den [Azures Kontocenter](https://account.windowsazure.com/Subscriptions)

Kontrollera om det konto som du använde kontoadministratör. Följ dessa steg för att kontrollera vem som är kontoadministratör:

1. Logga in på den [prenumerationer visa i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och tittar sedan under **inställningar**.
1. Välj **egenskaper**. Kontoadministratör för prenumerationen visas i den **kontoadministratören** box.  

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
