---
title: "Självstudier: Konfigurera arbetsplats av Facebook för användaretablering | Microsoft Docs"
description: "Lär dig hur du automatiskt etablera och avetablera användarkonton från Azure AD till arbetsplatsen med Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Självstudier: Konfigurera arbetsplats av Facebook för användaretablering

De här självstudierna visar steg som krävs för att automatiskt etablera och avinstallation etablera användarkonton från Azure Active Directory (AD Azure) till arbetsplatsen med Facebook.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med arbetsplats av Facebook, behöver du följande:

- En Azure AD-prenumeration
- En arbetsplats av Facebook enkel inloggning (SSO) aktiverat prenumeration

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du få en [utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Tilldela användare till arbetsplatsen av Facebook

Azure AD använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, bestämma vilka användare och grupper i Azure AD representerar de användare som behöver åtkomst till din arbetsplats av Facebook-app. Du kan sedan tilldela dessa användare till din arbetsplats av Facebook-app genom att följa instruktionerna i [tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Testa allokering konfigurationen genom att tilldela ett enda Azure AD-användare till arbetsplatsen med Facebook. Tilldela ytterligare användare och grupper senare.
>*   Du måste välja en giltig användarroll när du tilldelar en användare till arbetsplatsen med Facebook. Rollen som standard åtkomst fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till det användarkonto som etablerar API för arbetsplats med Facebook. Du också lära dig hur du konfigurerar tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i arbetsplats med Facebook. Detta baseras på användare och grupptilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserade SSO för arbetsplats av Facebook, av följa instruktionerna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurera användarkonto till arbetsplatsen av Facebook-etablering i Azure AD

Azure AD stöder möjligheten att synkronisera automatiskt kontoinformation för tilldelade användare till arbetsplatsen med Facebook. Den automatiska synkroniseringen kan arbetsplatsen av Facebook och hämta data som behövs för att auktorisera användare för åtkomst, innan de försöker logga in för första gången. Den också etablerar Frigör användare från arbetsplats av Facebook när åtkomst har återkallats i Azure AD.

1. I den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory** > **Företagsappar** > **alla program**.

2. Om du redan har konfigurerat arbetsplats av Facebook för enkel inloggning kan du söka efter din arbetsplats av Facebook-instans med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **arbetsplats av Facebook** i programgalleriet. Välj **arbetsplats av Facebook** i sökresultatet och lägga till den i listan över program.

3. Välj din instans av arbetsplatsen av Facebook och välj sedan den **etablering** fliken.

4. Ange **Etableringsläge** till **automatisk**. 

    ![Skärmbild av arbetsplatsen av Facebook alternativ för etablering](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** ange den **hemlighet Token** och **klient URL** för din arbetsplats av Facebook-administratör.

6. Välj i Azure-portalen **Testanslutningen** så Azure AD kan ansluta till din arbetsplats med Facebook-app. Om anslutningen misslyckas, kan du kontrollera att din arbetsplats av Facebook-konto har teamet administratörsbehörigheter.

7. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan.

8. Välj **Spara**.

9. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare till arbetsplatsen av Facebook**.

10. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till arbetsplatsen med Facebook. De attribut som valts som **matchande** egenskaper används för att matcha användarkonton i arbetsplats av Facebook för uppdateringsåtgärder. För att genomföra ändringarna, Välj **spara**.

11. Aktivera Azure AD etableras för arbetsplats av Facebook, i den **inställningar** ändrar den **Status för etablering** till **på**.

12. Välj **Spara**.

Mer information om hur du konfigurerar automatisk etablering finns [Facebook-dokumentationen](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Du kan nu skapa ett testkonto. Vänta i upp till 20 minuter att verifiera att kontot har synkroniserats till arbetsplatsen med Facebook.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företagsappar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-facebook-at-work-tutorial.md)

