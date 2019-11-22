---
title: Fel uppsättning användare tillhandahålls till en Azure AD-Galleri-app
description: Lär dig hur du tar reda på varför en annan uppsättning användare är etablerade i ett program än de du förväntar dig
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77e8f31bd0adeabea79cd11553fd77a3e5ab6404
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275779"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Fel uppsättning användare tillhandahålls till ett Azure AD Gallery-program

Vilka användare som etablerats till appen styrs främst av vilka användare och grupper som har **tilldelats** till programmet.

Använd följande resurser för att lära dig att kontrol lera vilka användare och grupper som har tilldelats till ett program i Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som administratör

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Öppna fönstret **Lägg till tilldelning** genom att klicka på knappen **Lägg till** överst i listan **användare och grupper** .

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

Om etableringen har kon figurer ATS och redan körs för en app, ska nya användare vara etablerade i ett program på cirka 10 minuter. Mer information finns i **gransknings loggarna** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Öppna fönstret **Lägg till tilldelning** genom att klicka på knappen **Lägg till** överst i listan **användare och grupper** .

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständiga namn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid gruppen profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till mer än en grupp**, typ i en annan **fullständiga namn** till den **Sök efter namn eller e-postadress** sökrutan och Klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

Om etableringen har kon figurer ATS och redan körs för en app, ska nya användare som ingår i gruppen vara etablerade i ett program på cirka 10 minuter. Mer information finns i **gransknings loggarna** .

>[!IMPORTANT]
>Etablering av grupp namn och grupp information, förutom medlemmarna, om de stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappningen** för grupp objekt som visas på fliken **etablering** . 
>
>

Om etablerings grupper har Aktiver ATS, se till att granska mappningar för attribut för att säkerställa att ett lämpligt fält används för "matchande ID". Detta matchnings-ID kan vara visnings namnet eller e-postaliaset. Gruppen och dess medlemmar är inte etablerade om matchande egenskap är tom eller inte har fyllts i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
