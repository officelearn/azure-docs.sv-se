---
title: 'Självstudier: Konfigurera Salesforce Sandbox för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 71a7c06309f52fc2f89852a3426cbb4d69248c5d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Salesforce Sandbox för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Salesforce Sandbox och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Salesforce Sandbox.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En giltig klient för Salesforce Sandbox för arbete eller Salesforce Sandbox för utbildning. Du kan använda ett kostnadsfritt utvärderingskonto för antingen service.
*   Ett användarkonto i Salesforce Sandbox Team administratörsbehörigheter.

## <a name="assigning-users-to-salesforce-sandbox"></a>Tilldela användare till Salesforce Sandbox

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till ditt Salesforce-Sandbox-app. När du har gjort detta beslut du kan tilldela dessa användare i appen Salesforce Sandbox genom att följa instruktionerna i [tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Viktiga tips för att tilldela användare till Salesforce Sandbox

* Vi rekommenderar att en enda Azure AD-användare har tilldelats till Salesforce Sandbox testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare Salesforce Sandbox, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

> [!NOTE]
> Den här appen importerar anpassade roller från Salesforce Sandbox som en del av etableringsprocessen som kunden kanske du väljer när du tilldelar användare.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Salesforce Sandbox användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelad användare konton i Salesforce Sandbox baserat på tilldelning av användare och grupper i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserade enkel inloggning för Salesforce begränsat läge, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering för Active Directory-användarkonton till Salesforce Sandbox.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat Salesforce Sandbox för enkel inloggning söka efter din instans av Salesforce Sandbox med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Salesforce Sandbox** i programgalleriet. Välj Salesforce Sandbox i sökresultatet och lägga till den i listan med program.

3. Välj din instans av Salesforce begränsat, och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![etablering](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** och ange följande inställningar:
   
    a. I den **Admin Username** textruta typen en Salesforce Sandbox kontonamn som har den **systemadministratören** profil i Salesforce.com som tilldelats.
   
    b. I den **adminlösenord** textruta skriver du lösenordet för det här kontot.

6. Om du vill hämta dina Salesforce Sandbox säkerhetstoken, öppnar du en ny flik och logga i samma Salesforce Sandbox-administratörskonto. Klicka på ditt namn i det övre högra hörnet på sidan och klicka sedan på **inställningar**.

     ![Aktivera automatisk användaretablering](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "aktivera automatisk användaretablering")

7. I det vänstra navigeringsfönstret klickar du på **mina personuppgifter** Expandera avsnittet relaterade och klicka sedan på **Återställ mina säkerhetstoken**.
  
    ![Aktivera automatisk användaretablering](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "aktivera automatisk användaretablering")

8. På den **återställa säkerhetstoken** klickar du på den **återställa säkerhetstoken** knappen.

    ![Aktivera automatisk användaretablering](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "aktivera automatisk användaretablering")

9. Kontrollera den inkorg som är associerade med den här administratörskonto. Leta efter ett e-postmeddelande från Salesforce-Sandbox.com som innehåller ny säkerhetstoken.

10. Kopiera token, gå till Azure AD-fönstret och klistrar in det i den **hemlighet Token** fältet.

11. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till ditt Salesforce-Sandbox-app.

12. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan.

13. Klicka på **spara.**  
    
14.  Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare Salesforce Sandbox.**

15. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till Salesforce Sandbox. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Salesforce Sandbox för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

16. Om du vill aktivera Azure AD etableras för Salesforce begränsat läge, ändra den **Status för etablering** till **på** i avsnittet Inställningar

17. Klicka på **spara.**

Startar den första synkroniseringen av användare och/eller grupper som tilldelas till Salesforce Sandbox i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av tjänsten etablering på Salesforce Sandbox app.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
