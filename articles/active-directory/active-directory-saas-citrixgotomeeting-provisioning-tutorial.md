---
title: "Självstudier: Azure Active Directory-integrering med Citrix GoToMeeting | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 1ddfcd991431a11e5c3e306bd5905003d094ac18
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-citrix-gotomeeting-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Citrix GoToMeeting för automatisk Användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Citrix GoToMeeting och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Citrix GoToMeeting.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En Citrix GoToMeeting enkel inloggning för aktiverade prenumerationen.
*   Ett användarkonto i Citrix GoToMeeting Team administratörsbehörigheter.

## <a name="assigning-users-to-citrix-gotomeeting"></a>Tilldela användare till Citrix GoToMeeting

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till appen Citrix GoToMeeting. När bestämt, kan du tilldela dessa användare i appen Citrix GoToMeeting genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-citrix-gotomeeting"></a>Viktiga tips för att tilldela användare till Citrix GoToMeeting

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats till Citrix GoToMeeting testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Citrix GoToMeeting, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatiserad etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Citrix GoToMeeting användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelad användare konton i Citrix GoToMeeting baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Citrix GoToMeeting, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering:

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat Citrix GoToMeeting för enkel inloggning, söka efter din instans av Citrix GoToMeeting med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Citrix GoToMeeting** i programgalleriet. Välj Citrix GoToMeeting i sökresultatet och lägga till den i listan med program.

3. Välj din instans av Citrix GoToMeeting och sedan den **etablering** fliken.

4. Ange den **etablering** läge för att **automatisk**. 

    ![Etablering](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. Under avsnittet autentiseringsuppgifter som administratör utför du följande steg:
   
    a. I den **Citrix GoToMeeting administratörsanvändarnamnet** textruta anger användarnamn för en administratör.

    b. I den **Citrix GoToMeeting adminlösenord** textruta administratörslösenord.

6. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till Citrix GoToMeeting appen. Om anslutningen misslyckas, kontrollera kontots Citrix GoToMeeting har administratörsbehörigheter för teamet och försök den **”administratörsautentiseringsuppgifter”** steg igen.

7. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan.

8. Klicka på **spara.**

9. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare till Citrix GoToMeeting.**

10. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till Citrix GoToMeeting. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Citrix GoToMeeting för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD-tjänsten för Citrix GoToMeeting-etablering, ändra den **Status för etablering** till **på** i avsnittet Inställningar

12. Klicka på **spara.**

Startar den första synkroniseringen av användare och/eller grupper som tilldelas till Citrix GoToMeeting i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen Citrix GoToMeeting.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-citrix-gotomeeting-tutorial.md)


