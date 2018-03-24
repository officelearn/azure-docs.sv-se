---
title: 'Azure Active Directory B2C: Stöd för | Microsoft Docs'
description: Så här supportförfrågningar för Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 2cb881e50b9a08c99b59796a23107bfafb847b75
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Filen supportärenden
Du kan supportförfrågningar för Azure Active Directory (AD Azure) B2C på Azure-portalen med följande steg:

1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Växla från din B2C-klient till en annan klient som har en Azure-prenumeration som är kopplade till den. Denna är vanligtvis dina anställda eller standard-klient skapas automatiskt när du registrerade dig för en Azure-prenumeration. Läs mer i [hur en Azure-prenumeration är relaterad till Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
   
    ![Support - växel klienter](./media/active-directory-b2c-support/support-switch-dir.png)
3. Efter att ha växlat klienter klickar du på **hjälp + support**.
   
    ![Support - hjälp + Support](./media/active-directory-b2c-support/support-support.png)
4. Klicka på **ny supportbegäran**.
   
    ![Support - ny](./media/active-directory-b2c-support/support-new.png)
5. I den **grunderna** blad, Använd följande information och klicka på **nästa**.
   
   * **Utfärda typ** är **tekniska**.
   * Välj lämplig **prenumeration**.
   * **Tjänsten** är **Active Directory**.
   * Välj lämplig **supportavtal**. Om du inte har någon kan du registrera dig för en [här](https://azure.microsoft.com/en-us/support/plans/).
     
     ![Support - grunderna](./media/active-directory-b2c-support/support-basics.png)
6. I den **Problem** blad, Använd följande information och klicka på **nästa**.
   
   * Välj lämplig **allvarlighetsgrad** nivå.
   * **Problemtyp** är **B2C**.
   * Välj lämplig **kategori**.
   * Beskrivning av problemet i de **information** fältet. Innehåller information såsom B2C innehavarens namn, beskrivning av problemet, felmeddelanden, Korrelations-ID: N (om tillgängligt) och så vidare.
   * I den **tidsram** anger datum och tid (inklusive tidszon) när problemet uppstod.
   * Under **filuppladdning**, ladda upp alla skärmdumpar och filer som du tror bidrar till att lösa problemet.
     
     ![Support - Problem](./media/active-directory-b2c-support/support-problem.png)
7. I den **kontaktinformation** bladet Lägg till din kontaktinformation. Klicka på **Skapa**.
   
    ![Support - kontakt](./media/active-directory-b2c-support/support-contact.png)
8. När du skickar din supportbegäran om kan du övervaka det genom att klicka på **hjälp + support** på startsidan, och sedan **hantera supportärenden**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Kända problem: Arkivera en supportbegäran i kontexten för en B2C-klient
Om du har missat steg 2 ovan och försök att skapa en supportbegäran i samband med din B2C-klient, visas följande fel.

> [!IMPORTANT]
> Försök inte att registrera dig för en ny Azure-prenumeration i din B2C-klient.  
> 
> 

![Support - ingen prenumeration](./media/active-directory-b2c-support/support-no-sub.png)

