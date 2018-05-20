---
title: 'Självstudier: Konfigurera Concur för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c439e6ca784bf031cd4102a6cb15b5e210130810
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Concur för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Concur och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Concur.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   Aktivera prenumerationen en Concur enkel inloggning.
*   Ett användarkonto i Concur Team administratörsbehörigheter.

## <a name="assigning-users-to-concur"></a>Tilldela användare till Concur

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till appen Concur. När bestämt, kan du tilldela dessa användare i appen Concur genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Viktiga tips för att tilldela användare till Concur

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Concur att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Concur, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Concurs användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Concur baserat på tilldelning av användare och grupper i Azure AD.

> [!Tip] 
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Concur, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera användaretablering för kontot:

Syftet med det här avsnittet är att beskriva hur du aktiverar etablering av Active Directory-användarkonton till Concur.

Om du vill aktivera appar i utgifter tjänsten, det måste vara rätt inställning och användning av en Web Service Admin-profil. Lägg inte till den WS administratörsroll till din befintliga administratörsprofil som du använder för T & E administrativa funktioner.

Verklig konsulter eller klient-administratören måste skapa en egen Web tjänstadministratör profil och administratören klienten måste använda den här profilen för Web Services Administrator funktioner (till exempel aktiverar appar). Dessa profiler måste hållas separat från klienten administratörens dagliga T & E admin-profil (T & E admin profilen inte ska ha rollen WSAdmin).

När du skapar en profil som ska användas för att aktivera appen, ange klienten administratörens namn i profilen Användarfält. Den här tilldelas ägarskap till profilen. När du har skapat en eller flera profiler klienten måste logga in med den här profilen på den ”*aktivera*” knappen för en Partner-App i menyn webbtjänster.

Av följande skäl bör den här åtgärden inte utföras med den profil som de använder för normal T & E-administration.

* Klienten måste vara det som klickar på ”*Ja*” i dialog-fönstret som visas när en app är aktiverad. Detta klickar du på om klienten är villigt för programmets Partner att komma åt sina data så att du eller partnern inte kan klicka på knappen Ja.

* Om en administratör som har aktiverat en app lämnar med T & E administratör profil företaget (vilket ger den profil som inaktiverat), appar aktiverad med profil inte fungerar förrän appen aktiveras med en annan active WS Admin-profil. Det är därför du ska skapa distinkta WS-Admin-profiler.

* Om en administratör lämnar företaget, kan namnet som är kopplat till WS-Admin-profil ändras till ersättning-administratören om du vill utan att påverka aktiverade appen eftersom den här profilen inte behöver inaktiverat.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Concur** klient.

2. Från den **Administration** väljer du **Web Services**.
   
    ![Concur klient](./media/active-directory-saas-concur-provisioning-tutorial/IC721729.png "Concur klient")

3. På vänster sida från den **Web Services** väljer **aktivera Partner program**.
   
    ![Aktivera Partner program](./media/active-directory-saas-concur-provisioning-tutorial/ic721730.png "gör att Partner-program")

4. Från den **aktivera programmet** väljer **Azure Active Directory**, och klicka sedan på **aktivera**.
   
    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klicka på **Ja** att stänga den **bekräfta åtgärden** dialogrutan.
   
    ![Bekräfta åtgärden](./media/active-directory-saas-concur-provisioning-tutorial/ic721732.png "bekräfta åtgärden")

6. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

7. Om du redan har konfigurerat Concur för enkel inloggning, söka efter din instans av Concur med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Concur** i programgalleriet. Välj Concur i sökresultatet och lägga till den i listan med program.

8. Välj din instans av Concur och sedan den **etablering** fliken.

9. Ange den **Etableringsläge** till **automatisk**. 
 
    ![etablering](./media/active-directory-saas-concur-provisioning-tutorial/provisioning.png)

10. Under den **administratörsautentiseringsuppgifter** ange den **användarnamn** och **lösenord** av administratören Concur.

11. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till din Concur app. Om anslutningen misslyckas, kontrollera kontots Concur har teamet administratörsbehörigheter.

12. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan.

13. Klicka på **spara.**

14. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare Concur.**

15. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till Concur. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Concur för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

16. Om du vill aktivera Azure AD-tjänsten för Concur-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

17. Klicka på **spara.**

Du kan nu skapa ett testkonto. Vänta i upp till 20 minuter för att verifiera att kontot har synkroniserats till Concur.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](active-directory-saas-concur-tutorial.md)

