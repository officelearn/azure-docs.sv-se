---
title: Fel uppsättning användare tillhandahålls till en Azure AD-Galleri-app
description: Lär dig hur du tar reda på varför en annan uppsättning användare är etablerade i ett program än de du förväntar dig
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 5a109f1a06e7ee8aff3e455c009217ff670d2781
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782219"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Fel uppsättning användare tillhandahålls till ett Azure AD Gallery-program

Vilka användare som etablerats till appen styrs främst av vilka användare och grupper som har **tilldelats** till programmet.

Använd följande resurser för att lära dig att kontrol lera vilka användare och grupper som har tilldelats till ett program i Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som administratör

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill tilldela en användare till från listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Öppna fönstret **Lägg till tilldelning** genom att klicka på knappen **Lägg till** överst i listan **användare och grupper** .

9. Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga namnet** eller **e-postadressen** för den användare som du är intresse rad av att tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **användaren** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid användarens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är färdig med att välja användare klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda användarna.

Om etableringen har kon figurer ATS och redan körs för en app, ska nya användare vara etablerade i ett program på cirka 10 minuter. Mer information finns i **gransknings loggarna** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill tilldela en användare till från listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Öppna fönstret **Lägg till tilldelning** genom att klicka på knappen **Lägg till** överst i listan **användare och grupper** .

9. Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga grupp namnet** för den grupp som du vill tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **gruppen** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid gruppens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en grupp**skriver du in ett annat **fullständigt grupp namn** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till den här gruppen i den **markerade** listan.

13. När du är färdig med att välja grupper klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som du vill tilldela till de grupper som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda grupperna.

Om etableringen har kon figurer ATS och redan körs för en app, ska nya användare som ingår i gruppen vara etablerade i ett program på cirka 10 minuter. Mer information finns i **gransknings loggarna** .

>[!IMPORTANT]
>Etablering av grupp namn och grupp information, förutom medlemmarna, om de stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappningen** för grupp objekt som visas på fliken **etablering** . 
>
>

Om etablerings grupper har Aktiver ATS, se till att granska mappningar för attribut för att säkerställa att ett lämpligt fält används för "matchande ID". Detta matchnings-ID kan vara visnings namnet eller e-postaliaset. Gruppen och dess medlemmar är inte etablerade om matchande egenskap är tom eller inte har fyllts i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
