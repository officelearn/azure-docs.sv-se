---
title: "Självstudier: Konfigurera GoToMeeting för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 82c7e8ff7d8a6692005bdc7a40d5e12be09bca5d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Självstudier: Konfigurera GoToMeeting för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i GoToMeeting och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till GoToMeeting.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   Aktivera prenumerationen en GoToMeeting enkel inloggning.
*   Ett användarkonto i GoToMeeting Team administratörsbehörigheter.

## <a name="assigning-users-to-gotomeeting"></a>Tilldela användare till GoToMeeting

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till appen GoToMeeting. När bestämt, kan du tilldela dessa användare i appen GoToMeeting genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Viktiga tips för att tilldela användare till GoToMeeting

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats GoToMeeting för att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare GoToMeeting, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatiserad etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Gotomeetings användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i GoToMeeting baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för GoToMeeting, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering:

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat GoToMeeting för enkel inloggning, söka efter din instans av GoToMeeting med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **GoToMeeting** i programgalleriet. Välj GoToMeeting i sökresultatet och lägga till den i listan med program.

3. Välj din instans av GoToMeeting och sedan den **etablering** fliken.

4. Ange den **etablering** läge för att **automatisk**. 

    ![etablering](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. Under avsnittet autentiseringsuppgifter som administratör utför du följande steg:
   
    a. I den **GoToMeeting administratörsanvändarnamnet** textruta anger användarnamn för en administratör.

    b. I den **GoToMeeting adminlösenord** textruta administratörslösenord.

6. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till din GoToMeeting app. Om anslutningen misslyckas, kontrollera kontots GoToMeeting har administratörsbehörigheter för teamet och försök den **”administratörsautentiseringsuppgifter”** steg igen.

7. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan.

8. Klicka på **spara.**

9. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare GoToMeeting.**

10. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till GoToMeeting. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i GoToMeeting för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD-tjänsten för GoToMeeting-etablering, ändra den **Status för etablering** till **på** i avsnittet Inställningar

12. Klicka på **spara.**

Startar den första synkroniseringen av användare och/eller grupper som tilldelas till GoToMeeting i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen GoToMeeting.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


