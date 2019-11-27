---
title: Kräv Azure MFA med villkorlig åtkomst – Azure Active Directory
description: I den här snabb starten får du lära dig hur du kan knyta dina autentiseringskrav till den typ av molnbaserad app som används med hjälp av Azure Active Directory (Azure AD) villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55c9188a1320b92aafa5fc67a253b42b6b107711
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381085"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Snabb start: Kräv MFA för vissa appar med Azure Active Directory villkorlig åtkomst

För att förenkla inloggnings upplevelsen av dina användare kanske du vill att de ska kunna logga in på dina molnappar med ett användar namn och ett lösen ord. Många miljöer har dock minst några få appar för vilka det är lämpligt att kräva en starkare form av konto verifiering, till exempel Multi-Factor Authentication (MFA). Den här principen kan vara sann för åtkomst till din organisations e-postsystem eller dina HR-appar. I Azure Active Directory (Azure AD) kan du göra det här målet med en princip för villkorlig åtkomst.

Den här snabb starten visar hur du konfigurerar en [princip för villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md) som kräver Multi-Factor Authentication för en vald molnbaserad app i din miljö.

![Exempel princip för villkorlig åtkomst i Azure Portal](./media/app-based-mfa/32.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra scenariot i den här snabb starten behöver du:

- **Åtkomst till en Azure AD Premium-utgåva** – villkorlig åtkomst i Azure AD är en Azure AD Premium funktion.
- **Ett test konto med namnet Isabella Simonsen** – om du inte vet hur du skapar ett test konto läser du [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Scenariot i den här snabb starten kräver att MFA per användare inte är aktiverat för ditt test konto. Mer information finns i [så här kräver du tvåstegsverifiering för en användare](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>Testa din upplevelse

Målet med det här steget är att få en överblick över upplevelsen utan en princip för villkorlig åtkomst.

**Så här initierar du din miljö:**

1. Logga in på Azure Portal som Isabella Simonsen.
1. Logga ut.

## <a name="create-your-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

I det här avsnittet visas hur du skapar den nödvändiga principen för villkorlig åtkomst. Scenariot i den här snabb starten använder:

- Azure Portal som plats hållare för en molnbaserad app som kräver MFA. 
- Exempel användaren för att testa principen för villkorlig åtkomst.  

Ange följande i principen:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Isabella Simonsen |
| Molnappar | Microsoft Azure Management |
| Bevilja åtkomst | Kräv Multi-Factor Authentication |

![Utökad princip för villkorlig åtkomst](./media/app-based-mfa/31.png)

**Så här konfigurerar du en princip för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhets administratör eller en administratör för villkorlig åtkomst.
1. I Azure Portal söker du efter och väljer **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Klicka på **villkorlig åtkomst**i avsnittet **säkerhet** på sidan **Azure Active Directory** .

   ![Villkorlig åtkomst](./media/app-based-mfa/03.png)

1. På sidan **villkorlig åtkomst** , i verktygsfältet högst upp, klickar du på **ny princip**.

   ![Lägg till](./media/app-based-mfa/04.png)

1. Skriv **KRÄV MFA för Azure Portal åtkomst**i text rutan **namn** på sidan **nytt** .

   ![Namn](./media/app-based-mfa/05.png)

1. I avsnittet **tilldelning** klickar du på **användare och grupper**.

   ![Användare och grupper](./media/app-based-mfa/06.png)

1. Utför följande steg på sidan **användare och grupper** :

   ![Användare och grupper](./media/app-based-mfa/24.png)

   1. Klicka på **Välj användare och grupper**och välj sedan **användare och grupper**.
   1. Klicka på **Välj**.
   1. På sidan **Välj** väljer du **Isabella Simonsen**och klickar sedan på **Välj**.
   1. På sidan **användare och grupper** klickar du på **Slutför**.

1. Klicka på **molnappar**.

   ![Molnappar](./media/app-based-mfa/08.png)

1. Utför följande steg på sidan **Cloud Apps** :

   ![Välj molnappar](./media/app-based-mfa/26.png)

   1. Klicka på **Välj appar**.
   1. Klicka på **Välj**.
   1. På sidan **Välj** väljer du **Microsoft Azure hantering**och klickar sedan på **Välj**.
   1. På sidan **molnappar** klickar du på **Slutför**.

1. I avsnittet **åtkomst kontroller** klickar du på **bevilja**.

   ![Åtkomst kontroller](./media/app-based-mfa/10.png)

1. Utför följande steg på sidan **bevilja** :

   ![Ömsesidigt](./media/app-based-mfa/11.png)

   1. Välj **bevilja åtkomst**.
   1. Välj **Kräv Multi-Factor Authentication**.
   1. Klicka på **Välj**.

1. I avsnittet **Aktivera princip** klickar du **på på**.

   ![Aktivera princip](./media/app-based-mfa/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera ett simulerat inloggnings tecken

Nu när du har konfigurerat din princip för villkorlig åtkomst vill du förmodligen veta om den fungerar som förväntat. Som ett första steg använder du den villkorliga åtkomsten vad gör om-princip verktyget för att simulera en inloggning av test användaren. Simuleringen uppskattar den effekt som denna inloggning har på dina principer och genererar en simulerings rapport.  

För att initiera utvärderings verktyget för **What If** policy, ange:

- **Isabella Simonsen** som användare
- **Microsoft Azure hantering** som molnbaserad app

Om du klickar på **What If** skapas en simulerings rapport som visar:

- **KRÄV MFA för Azure Portal åtkomst** under **principer som ska gälla**
- **Kräv Multi-Factor Authentication** som **beviljande kontroller**.

![Verktyget om princip](./media/app-based-mfa/23.png)

**Så här utvärderar du principen för villkorlig åtkomst:**

1. På sidan [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) går du till menyn högst upp och klickar på **What If**.  

   ![What If](./media/app-based-mfa/14.png)

1. Klicka på **användare**, Välj **Isabella Simonsen**och klicka sedan på **Välj**.

   ![Användare](./media/app-based-mfa/15.png)

1. Utför följande steg för att välja en molnbaserad app:

   ![Molnappar](./media/app-based-mfa/16.png)

   1. Klicka på **molnappar**.
   1. På **sidan molnappar**klickar du på **Välj appar**.
   1. Klicka på **Välj**.
   1. På sidan **Välj** väljer du **Microsoft Azure hantering**och klickar sedan på **Välj**.
   1. På sidan molnappar klickar du på **Slutför**.

1. Klicka på **What If**.

## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorlig åtkomst

I föregående avsnitt har du lärt dig hur du utvärderar en simulerad inloggning. Förutom en simulering bör du också testa principen för villkorlig åtkomst för att säkerställa att den fungerar som förväntat.

Testa principen genom att försöka logga in på din [Azure Portal](https://portal.azure.com) med ditt **Isabella Simonsen** test konto. Du bör se en dialog ruta som kräver att du konfigurerar ditt konto för ytterligare säkerhets verifiering.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort test användaren och principen för villkorlig åtkomst när de inte längre behövs:

- Om du inte vet hur du tar bort en Azure AD-användare kan du läsa [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Om du vill ta bort principen väljer du principen och klickar sedan på **ta bort** i verktygsfältet snabb åtkomst.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv användnings villkor för att godkännas](require-tou.md)
> [blockera åtkomst när en sessions risk identifieras](app-sign-in-risk.md)
