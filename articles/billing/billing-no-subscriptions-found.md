---
title: Inga prenumerationer påträffade fel när du försöker logga in på Azure-portalen och Azure kontocenter | Microsoft Docs
description: Innehåller lösningen på problemet som inga prenumerationer hittades fel uppstår när loggar in på Azure-portalen eller Azure kontocenter.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 475a4ad72a1c2fc2ebf99387e193713797cc2586
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Inga prenumerationer hittades fel i Azure-portalen eller Azure-kontocenter

Du kan få ett meddelande om ”inga prenumerationer hittades” när du försöker logga in på den [Azure-portalen](https://portal.azure.com/) eller [Azure Kontocenter](https://account.windowsazure.com/Subscriptions). Den här artikeln innehåller en lösning på problemet.

## <a name="symptom"></a>Symtom

När du försöker logga in på den [Azure-portalen](https://portal.azure.com/) eller [Azure kontocenter](https://account.windowsazure.com/Subscriptions), visas följande felmeddelande: ”inga prenumerationer hittades”.

## <a name="cause"></a>Orsak

Det här problemet uppstår om du har valt i fel katalog eller om ditt konto inte har tillräcklig behörighet. 

## <a name="solution"></a>Lösning

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenario 1: Meddelande tas emot i den [Azure-portalen](https://portal.azure.com)

Åtgärda det här problemet:

* Kontrollera att rätt Azure-katalogen är markerad genom att klicka på ditt konto längst upp till höger.

  ![Välj en katalog längst upp i Azure-portalen](./media/billing-no-subscriptions-found/directory-switch.png)
* Om rätt Azure-katalogen har valts men du fortfarande får felmeddelandet [ditt konto som ägare](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenario 2: Felmeddelande tas emot i den [Azure Account Center](https://account.windowsazure.com/Subscriptions)

Kontrollera om det konto som du använde kontoadministratör. Följ dessa steg för att verifiera som kontoadministratör är:

1. Logga in på den [prenumerationer visa i Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och se **inställningar**.
1. Välj **egenskaper**. Kontoadministratören för prenumerationen visas i den **kontoadministratören** rutan.  

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) få snabbt lösa problemet. 
