---
title: "Självstudier: Konfigurera Pingboard för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Pingboard."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 36fc33813595183856713f01b5a94f84e1f3db4e
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Pingboard för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du måste utföra om du vill aktivera automatisk etablering och avetablering av användarkonton från Azure Active Directory (AD Azure) till Pingboard.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure AD-klient
*   En klient Pingboard [Pro-konto](https://pingboard.com/pricing) 
*   Ett användarkonto i Pingboard med administratörsbehörigheter 

> [!NOTE] 
> Azure AD-etablering integration förlitar sig på den [Pingboard API](`https://your_domain.pingboard.com/scim/v2`), som är tillgänglig för ditt konto.

## <a name="assign-users-to-pingboard"></a>Tilldela användare till Pingboard

Azure AD använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda program. I samband med automatisk konto användaretablering, synkroniseras de användare som tilldelats ett program i Azure AD. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare i Azure AD behöver åtkomst till appen Pingboard. Du kan sedan tilldela dessa användare i appen Pingboard genom att följa anvisningarna här:

[Tilldela en användare till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Viktiga tips för att tilldela användare till Pingboard

Vi rekommenderar att du ger en enda Azure AD-användare till Pingboard att testa allokering konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurera användaretablering till Pingboard 

Det här avsnittet hjälper dig att ansluta din Azure AD Pingboard användarkontot etablering API. Du kan också konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Pingboard baserat på användare i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserade enkel inloggning för Pingboard, följ instruktionerna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Så här konfigurerar du automatiska konto användaretablering till Pingboard i Azure AD

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnitt.

2. Om du redan har konfigurerat Pingboard för enkel inloggning kan du söka efter din instans av Pingboard med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Pingboard** i programgalleriet. Välj **Pingboard** i sökresultatet och lägga till den i listan över program.

3. Välj din instans av Pingboard och välj sedan den **etablering** fliken.

4. Ange **Etableringsläge** till **automatisk**.

    ![Pingboard etablering](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5. Under den **administratörsautentiseringsuppgifter** avsnittet, utför följande steg:

    a. I **klient URL**, ange `https://your_domain.pingboard.com/scim/v2`, och Ersätt ”domän” med den verkliga domänen.

    b. Logga in på [Pingboard](https://pingboard.com/) med ditt administratörskonto.

    c. Välj **tillägg** > **integreringar** > **Azure Active Directory**.

    d. Gå till den **konfigurera** fliken och markera **aktivera användarförsörjning från Azure**.

    e. Kopiera token i **OAuth ägar-Token**, och ange den i **hemlighet Token**.

6. I Azure portal, väljer **Testanslutningen** så Azure AD kan ansluta till din Pingboard app. Om anslutningen misslyckas, kontrollera att ditt konto för Pingboard har administratörsbehörigheter och försök den **Testanslutningen** steg igen.

7. Ange e-postadressen för en person eller grupp som du vill ta emot meddelanden om etablering fel i **e-postmeddelande**. Markera kryssrutan under.

8. Välj **Spara**. 

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Pingboard**.

10. I den **attributmappning** avsnittet kan du granska användarattribut ska synkroniseras från Azure AD med Pingboard. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Pingboard för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna. Mer information finns i [anpassa användaretablering attributmappning](active-directory-saas-customizing-attribute-mappings.md).

11. Aktivera Azure AD etableras för Pingboard, i den **inställningar** ändrar **Status för etablering** till **på**.

12. Välj **spara** att starta den första synkroniseringen av användare som tilldelats Pingboard.

Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Använd den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter. Rapporterna beskrivs alla åtgärder som utförs av tjänsten etablering Pingboard appen.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapport om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företagsappar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-pingboard-tutorial.md)
