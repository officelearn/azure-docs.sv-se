---
title: Stöd för Azure Active Directory B2C | Microsoft-dokument
description: Filsupportbegäranden för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5195241003b1ce4ea505002e2cc3c10410e6cde1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183728"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Begäranden om filsupport
Du kan filsupportbegäranden för Azure Active Directory B2C (Azure AD B2C) på Azure-portalen med hjälp av följande steg:

1. Växla från din B2C-klient till en annan klientorganisation som har en Azure-prenumeration kopplad till sig. Vanligtvis är den senare din medarbetare eller standardklienten som skapades åt dig när du registrerade dig för en Azure-prenumeration. Mer information finns i [hur en Azure-prenumeration är relaterad till Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Azure-portal med klientval markerat](./media/support-options/support-switch-dir.png)

1. När du har bytt klienter klickar du på **Stöd + stöd**.

    ![Hjälp + supportpanel markerad i Azure-portalen](./media/support-options/support-support.png)

1. Klicka på **Ny supportbegäran**.

    ![Ny panel för supportbegäran markerad i Azure-portalen](./media/support-options/support-new.png)

1. Använd dessa uppgifter i bladet **Grunderna** och klicka på **Nästa**.

    * **Problemtyp** är **teknisk**.
    * Välj lämplig **prenumeration**.
    * **Tjänsten** är **Active Directory**.
    * Välj lämplig **supportplan**. Om du inte har en, kan du registrera dig för en [här](https://azure.microsoft.com/support/plans/).

     ![Sidan Grunderna med knappen Nästa markerad i Azure-portalen](./media/support-options/support-basics.png)

1. Använd dessa uppgifter i **bladet Problem** och klicka på **Nästa**.

    * Välj lämplig **allvarlighetsgrad.**
    * **Problemtypen** är **B2C**.
    * Välj lämplig **kategori**.
    * Beskriv problemet i fältet **Detaljer.** Ange information som B2C-klientnamnet, beskrivning av problemet, felmeddelanden, korrelations-ID (om tillgängligt) och så vidare.
    * Ange datum och tid (inklusive tidszon) i fältet **Tidsram.**
    * Under **Filuppladdning**laddar du upp alla skärmbilder och filer som du tror skulle hjälpa till att lösa problemet.

     ![Problemsida med knappen Nästa markerad i Azure-portalen](./media/support-options/support-problem.png)

1. Lägg till dina kontaktuppgifter i bladet **Med kontaktinformation.** Klicka på **Skapa**.

    ![Kontaktinformationssida med knappen Skapa markerad i portalen](./media/support-options/support-contact.png)

1. När du har skickat din supportbegäran kan du övervaka den genom att klicka på **Hjälp + support** på Startboard och sedan hantera **supportbegäranden**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Känt problem: Lämna in en supportbegäran i samband med en B2C-klient

Om du missade steg 2 som beskrivs ovan och försöker skapa en supportbegäran i samband med din B2C-klient, visas följande fel.

> [!IMPORTANT]
> Försök inte registrera dig för en ny Azure-prenumeration i din B2C-klientorganisation.

![Du har inget prenumerationsfel som visas i Azure-portalen](./media/support-options/support-no-sub.png)
