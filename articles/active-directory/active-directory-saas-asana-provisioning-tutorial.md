---
title: "Självstudier: Konfigurera Asana för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Asana."
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
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Asana för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Asana och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Asana.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active Active directory-klient
*   En Asana klient med [Enterprise](https://www.asana.com/pricing) plan eller bättre aktiverad 
*   Ett användarkonto i Asana med administratörsbehörigheter 

> [!NOTE] 
> Azure AD-etablering integration förlitar sig på den [Asana API](https://app.asana.com/api/1.0/scim/Users) som är tillgänglig för Asana.

## <a name="assigning-users-to-asana"></a>Tilldela användare till Asana

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. Endast de användare som har ”tilldelats” till ett program i Azure AD i samband med automatisk konto användaretablering, kommer att synkroniseras. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare i Azure AD representerar de användare som behöver åtkomst till appen Asana. När bestämt, kan du tilldela dessa användare i appen Asana genom att följa anvisningarna här:

[Tilldela en användare till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Viktiga tips för att tilldela användare till Asana

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Asana att testa allokering konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configuring-user-provisioning-to-asana"></a>Konfigurering av användarförsörjning till Asana 

Det här avsnittet hjälper dig att ansluta din Azure AD till Asana användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Asana baserat på Användartilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Asana, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurera automatisk konto användaretablering till Asana i Azure AD:

1)  I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2) Om du redan har konfigurerat Asana för enkel inloggning, söka efter din instans av Asana med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Asana** i programgalleriet. Välj **Asana** i sökresultatet och lägga till den i listan över program.

3)  Välj din instans av Asana och sedan den **etablering** fliken.

4)  Ange den **Etableringsläge** till **automatisk**.

![Asana etablering](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) Under avsnittet administratörsautentiseringsuppgifter följer nedan instruktioner för att skapa token och ange den i den **hemlighet Token** textruta.

* Logga in på [Asana](https://app.asana.com) med administratörskonto
* Klicka på profilfoto från det översta fältet och välj den aktuella organisationsnamn inställningar
* Gå till fliken tjänstkonton
* Klicka på Lägg till tjänstkontot
* Uppdatera namn, om och profilen foto efter behov, kopiera **Token** och klicka på Spara ändringar

6) I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till din Asana app. Om anslutningen misslyckas, kan du kontrollera att ditt Asana-konto som har administratörsbehörigheter och försök den **”Testa anslutning”** steg igen.

7) Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan nedan.

8) Klicka på **Spara**. 

9) Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare Asana**.

10) I den **attributmappning** avsnittet kan du granska användarattribut som kommer att synkroniseras från Azure AD Asana. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkonton i Asana för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna. Se [anpassa användaren etablering attributmappning](active-directory-saas-customizing-attribute-mappings.md) mer information

11) Om du vill aktivera Azure AD-tjänsten för Asana-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

12) Klicka på **Spara**. 

Detta startar den första synkroniseringen av alla användare som tilldelats Asana i avsnittet användare. Första tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen Asana.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-asana-tutorial.md)
