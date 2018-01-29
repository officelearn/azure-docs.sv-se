---
title: "Självstudier: Konfigurera Netsuite för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netsuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3de048a88b2a14936f7a49afefc8c96e56909c37
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Netsuite för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Netsuite och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Netsuite.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En Netsuite enkel inloggning för aktiverade prenumerationen.
*   Ett användarkonto i Netsuite Team administratörsbehörigheter.

## <a name="assigning-users-to-netsuite"></a>Tilldela användare till Netsuite

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till appen Netsuite. När bestämt, kan du tilldela dessa användare i appen Netsuite genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Viktiga tips för att tilldela användare till Netsuite

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Netsuite för att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Netsuite, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Netsuites användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Netsuite baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP] 
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Netsuite, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera användaretablering för kontot:

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering för Active Directory-användarkonton till Netsuite.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat Netsuite för enkel inloggning, söka efter din instans av Netsuite med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Netsuite** i programgalleriet. Välj Netsuite i sökresultatet och lägga till den i listan med program.

3. Välj din instans av Netsuite och sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/active-directory-saas-netsuite-provisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** och ange följande inställningar:
   
    a. I den **administratörsanvändarnamnet** textruta typen en Netsuite kontonamn som har den **systemadministratören** profil i Netsuite.com som tilldelats.
   
    b. I den **adminlösenord** textruta skriver du lösenordet för det här kontot.
      
6. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till din Netsuite app.

7. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan.

8. Klicka på **spara.**

9. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare Netsuite.**

10. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till Netsuite. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkonton i Netsuite för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD-tjänsten för Netsuite-etablering, ändra den **Status för etablering** till **på** i avsnittet Inställningar

12. Klicka på **spara.**

Startar den första synkroniseringen av användare och/eller grupper som tilldelas till Netsuite i avsnittet användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen Netsuite.

Du kan nu skapa ett testkonto. Vänta i upp till 20 minuter för att verifiera att kontot har synkroniserats till Netsuite.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-netsuite-tutorial.md)