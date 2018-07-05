---
title: Stöd för Azure Active Directory B2C | Microsoft Docs
description: Så här supportförfrågningar för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4ceef533145bea75247eb26998b998552293ef63
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450351"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Filen supportärenden
Du kan supportförfrågningar för Azure Active Directory (Azure AD) B2C på Azure-portalen med följande steg:

1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Växla från din B2C-klient till en annan klient som har en Azure-prenumeration som är associerade med den. Dessa är vanligtvis dina anställda eller standard-klient som skapas åt dig när du registrerade dig för en Azure-prenumeration. Mer information finns i [hur en Azure-prenumeration är kopplad till Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
   
    ![Support – växel klienter](./media/active-directory-b2c-support/support-switch-dir.png)
3. När du har växlat klienter, klickar du på **hjälp + support**.
   
    ![Support – hjälp + Support](./media/active-directory-b2c-support/support-support.png)
4. Klicka på **ny supportbegäran**.
   
    ![Support – ny](./media/active-directory-b2c-support/support-new.png)
5. I den **grunderna** bladet, Använd följande information och klicka på **nästa**.
   
   * **Typ av problem** är **teknisk**.
   * Välj lämplig **prenumeration**.
   * **Tjänsten** är **Active Directory**.
   * Välj lämplig **supportavtal**. Om du inte har något du kan registrera dig för en [här](https://azure.microsoft.com/support/plans/).
     
     ![Support - grunderna](./media/active-directory-b2c-support/support-basics.png)
6. I den **Problem** bladet, Använd följande information och klicka på **nästa**.
   
   * Välj lämplig **allvarlighetsgrad** nivå.
   * **Problemtyp** är **B2C**.
   * Välj lämplig **kategori**.
   * Beskrivning av problemet i den **information** fält. Innehåller information som klientnamn B2C, beskrivning av problem, felmeddelanden, Korrelations-ID: N (om tillgängligt) och så vidare.
   * I den **tidsram** fältet, ange datum och tid (inklusive tidszon) när problemet uppstod.
   * Under **filuppladdning**, ladda upp alla skärmbilder och filer som du tror skulle hjälpa för att lösa problemet.
     
     ![Support - Problem](./media/active-directory-b2c-support/support-problem.png)
7. I den **kontaktinformation** bladet Lägg till din kontaktinformation. Klicka på **Skapa**.
   
    ![Support – kontakta](./media/active-directory-b2c-support/support-contact.png)
8. När du har skickat din supportbegäran kan du övervaka den genom att klicka på **hjälp + support** på startsidan och sedan **hantera supportärenden**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Kända problem: Arkivera en supportbegäran i kontexten för en B2C-klient
Om du missade steg 2 ovan och försöker skapa en supportbegäran i samband med din B2C-klient, visas följande fel.

> [!IMPORTANT]
> Försök inte att registrera dig för en ny Azure-prenumeration i din B2C-klient.  
> 
> 

![Support – ingen prenumeration](./media/active-directory-b2c-support/support-no-sub.png)

