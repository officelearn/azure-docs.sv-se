---
title: "Fel uppsättning användare tillhandahålls till ett program för Azure AD-galleriet | Microsoft Docs"
description: "Lär dig att ta reda på varför en annan uppsättning användare tillhandahålls till ett program än de som du förväntade dig"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9e268aad5d3a7dcde9102216e9b3e8abfcca2cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Fel uppsättning användare tillhandahålls till ett program för Azure AD-galleriet

Vilka användare som har etablerats i appen är främst styrs av vilka användare och grupper har **tilldelade** till programmet.

Använda resurserna nedan för att lära dig att kontrollera vilka användare och grupper har tilldelats ett program i Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som en administratör

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användare** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll att tilldela användare som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda användarna.

Om etablering har konfigurerats och körs redan för en app kan nya användare ska etableras till ett program i cirka 10 minuter. Kontrollera den **granskningsloggar** mer information.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Följ stegen nedan om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständig gruppnamn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klickar du på kryssrutan bredvid gruppen profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en grupp**, typ i en annan **fullständig gruppnamn** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll som ska tilldelas de grupper som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda grupperna.

Om etablering har konfigurerats och körs redan för en app kan nya användare som ingår i gruppen ska etableras till ett program i cirka 10 minuter. Kontrollera den **granskningsloggar** mer information.

>[!IMPORTANT]
>Etablering av gruppnamn och gruppinformation förutom medlemmar, om stöd för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för gruppobjekt visas i den **etablering** fliken. 
>
>

Om etablering grupper är aktiverad bör du granska attributmappning för att säkerställa ett lämpligt fält som används för ”matchande ID”. Detta kan vara namn eller e-post alias, som gruppen och dess medlemmar inte etableras om matchning av egenskap är tomt eller inte fyllts i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md)
