---
title: "Självstudier: Konfigurera LucidChart för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till LucidChart."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 7245ee95e53217872f0b01cf8951695c993a929c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="tutorial-configuring-lucidchart-for-automatic-user-provisioning"></a>Självstudier: Konfigurera LucidChart för automatisk Användaretablering


Syftet med den här kursen är att visa de steg som du behöver göra i LucidChart och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till LucidChart. 

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En LucidChart klient med den [företagsplan](https://www.lucidchart.com/user/117598685#/subscriptionLevel) eller bättre aktiverat 
*   Ett användarkonto i LucidChart med administratörsbehörigheter 

## <a name="assigning-users-to-lucidchart"></a>Tilldela användare till LucidChart

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till appen LucidChart. När bestämt, kan du tilldela dessa användare i appen LucidChart genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Viktiga tips för att tilldela användare till LucidChart

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats LucidChart för att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare LucidChart, måste du välja någon av **användaren** roll eller en annan giltig programspecifika roll (om tillgängliga) i dialogrutan tilldelning. Den **standard åtkomst** roll fungerar inte för etablering och dessa användare hoppas över.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurering av användarförsörjning till LucidChart 

Det här avsnittet hjälper dig att ansluta din Azure AD till Lucidcharts användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i LucidChart baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för LucidChart, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurera automatisk konto användaretablering till LucidChart i Azure AD


1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat LucidChart för enkel inloggning, söka efter din instans av LucidChart med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **LucidChart** i programgalleriet. Välj LucidChart i sökresultatet och lägga till den i listan med program.

3. Välj din instans av LucidChart och sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![LucidChart etablering](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **hemlighet Token** genereras av din LucidChart konto (du kan söka efter token under kontot: **Team**  >  **Appintegrering** > **SCIM**). 

    ![LucidChart etablering](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart2.png)

6. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till din LucidChart app. Om anslutningen misslyckas, kontrollera kontots LucidChart har administratörsbehörigheter och försök steg 5 igen.

7. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan ”Skicka ett e-postmeddelande när ett fel uppstår”.

8. Klicka på **Spara**. 

9. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare LucidChart**.

10. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till LucidChart. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i LucidChart för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD-tjänsten för LucidChart-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

12. Klicka på **Spara**. 

Den här åtgärden startar den första synkroniseringen av användare och/eller grupper som tilldelas till LucidChart i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](active-directory-saas-provisioning-reporting.md)
