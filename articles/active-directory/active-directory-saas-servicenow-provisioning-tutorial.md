---
title: "Självstudier: Konfigurera ServiceNow för automatisk Användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig etablera och avetablera användarkonton från Azure AD till ServiceNow automatiskt."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 06772c9e21936d224f721413ccdc1c859b07b940
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Självstudier: Konfigurera ServiceNow för automatisk Användaretablering med Azure Active Directory

Syftet med den här kursen är att visa de steg som du behöver göra i ServiceNow och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till ServiceNow.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   Du måste ha en giltig klient för ServiceNow för arbets- eller ServiceNow för utbildning. Du kan använda ett kostnadsfritt utvärderingskonto för antingen service.
*   Ett användarkonto i ServiceNow-teamet administratörsbehörigheter.

## <a name="assigning-users-to-servicenow"></a>Tilldela användare till ServiceNow

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din ServiceNow-app. När du valt, du kan tilldela dessa användare ServiceNow-app genom att följa anvisningarna här: [tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Vi rekommenderar att en enda Azure AD-användare har tilldelats ServiceNow för att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.
>*   När du tilldelar en användare ServiceNow, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Servicenows användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i ServiceNow baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
>Du kan också välja att aktivera SAML-baserade enkel inloggning för ServiceNow, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat ServiceNow för enkel inloggning, söka efter din instans av ServiceNow med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **ServiceNow** i programgalleriet. Välj ServiceNow i sökresultatet och lägga till den i listan med program.

3. Välj din instans av ServiceNow och sedan den **etablering** fliken.

4. Ange den **etablering** läge för att **automatisk**. 

    ![Etablering](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. Under avsnittet autentiseringsuppgifter som administratör utför du följande steg:
   
    a. I den **ServiceNow instansnamn** textruta skriver du namnet på ServiceNow-instansen.

    b. I den **ServiceNow-administratörsanvändarnamnet** textruta anger användarnamn för en administratör.

    c. I den **ServiceNow adminlösenord** textruta administratörslösenord.

6. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till din ServiceNow-app. Om anslutningen misslyckas, kontrollera ditt ServiceNow-konto har administratörsbehörigheter för teamet och försök den **”administratörsautentiseringsuppgifter”** steg igen.

7. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan.

8. Klicka på **spara.**

9. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare ServiceNow.**

10. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till ServiceNow. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i ServiceNow för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD-tjänsten för ServiceNow-etablering, ändra den **Status för etablering** till **på** i avsnittet Inställningar

12. Klicka på **spara.**

Startar den första synkroniseringen av användare och/eller grupper som tilldelas till ServiceNow i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering på ServiceNow-app.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-servicenow-tutorial.md)


