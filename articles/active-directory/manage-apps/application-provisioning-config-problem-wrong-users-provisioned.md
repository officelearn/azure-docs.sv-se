---
title: Fel uppsättning användare etableras till en Azure AD-galleriprogram | Microsoft Docs
description: Lär dig ta reda på varför en annan uppsättning användare etableras till ett program än de du förväntade dig
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: df32ed7a6d3bc0c9e349f576f5f48832bf9906dd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041931"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Fel uppsättning användare etableras till en Azure AD-galleriprogram

Vilka användare som tilldelas appen är främst drivs av vilka användare och grupper har **tilldelade** till programmet.

Använd följande resurser för att lära dig att kontrollera vilka användare och grupper har tilldelats till ett program i Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som en administratör

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Öppna den **Lägg till tilldelning** fönstret klickar du på den **Lägg till** knappen ovanpå den **användare och grupper** lista.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

Om etablering har konfigurerats och körs redan för en app kan nya användare etableras till ett program i cirka 10 minuter. Kontrollera den **granskningsloggar** mer information.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Öppna den **Lägg till tilldelning** fönstret klickar du på den **Lägg till** knappen ovanpå den **användare och grupper** lista.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständiga namn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid gruppen profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till mer än en grupp**, typ i en annan **fullständiga namn** till den **Sök efter namn eller e-postadress** sökrutan och Klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

Om etablering har konfigurerats och körs redan för en app kan nya användare som ingår i gruppen ska etableras till ett program i cirka 10 minuter. Kontrollera den **granskningsloggar** mer information.

>[!IMPORTANT]
>Etablering av namn- och gruppinformation, förutom medlemmarna, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för grupp-objekt som visas i den **etablering** fliken. 
>
>

Om etablering grupper är aktiverad, bör du granska attributmappningar för att säkerställa att ett lämpligt fält som används för ”matchande ID”. Den här matchande ID kan vara Visa namn eller e-postalias. Gruppen och dess medlemmar tillhandahålls inte om egenskapen matchande är tom eller inte fylls i automatiskt för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
