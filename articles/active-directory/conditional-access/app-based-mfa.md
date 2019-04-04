---
title: Snabbstart – kräva multifaktorautentisering (MFA) för specifika appar med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan koppla din autentiseringskrav för typ av används molnapp med villkorlig åtkomst i Azure Active Directory (AD Azure).
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5ab513034d6e2946dcb31f3a31dbf86f14873e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895993"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Snabbstart: Kräva MFA för specifika appar med villkorlig åtkomst i Azure Active Directory

För att förenkla inloggning för dina användare kan vilja du att de kan logga in på dina appar i molnet med ett användarnamn och ett lösenord. Men har många miljöer minst ett fåtal program som det är lämpligt att kräva en starkare form av verifiering för kontot, till exempel multifaktorautentisering (MFA). Det kan vara, för exempel Sant för åtkomst till organisationens e-postsystem eller HR-appar. I Azure Active Directory (AD Azure), kan du göra det här målet med en princip för villkorlig åtkomst.

Den här snabbstarten visar hur du konfigurerar en [princip för villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md) som kräver multifaktorautentisering för ett valt moln-app i din miljö.

![Exempelprincip för villkorlig åtkomst i Azure portal](./media/app-based-mfa/32.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra scenariot i den här snabbstarten behöver du:

- **Åtkomst till en Azure AD Premium-versionen** -villkorlig åtkomst i Azure AD är en Azure AD Premium-funktion.

- **Ett testkonto kallas Isabella Simonsen** – om du inte vet hur du skapar ett testkonto finns [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Scenariot i den här snabbstarten kräver att per användare MFA inte har aktiverats för ditt konto för testning. Mer information finns i [kräva tvåstegsverifiering för en användare](../authentication/howto-mfa-userstates.md).

## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att få en bild av inloggning utan en princip för villkorlig åtkomst.

**Initiera miljön:**

1. Logga in på Azure portal som Isabella Simonsen.
1. Logga ut.

## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Scenariot i den här snabbstarten använder:

- Azure-portalen som platshållare för en molnapp som kräver MFA. 
- Exempelanvändaren att testa principen för villkorlig åtkomst.  

Ange i din princip:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Isabella Simonsen |
| Molnappar | Microsoft Azure Management |
| Bevilja åtkomst | Kräv multifaktorautentisering |

![Expanderad princip för villkorlig åtkomst](./media/app-based-mfa/31.png)

**Konfigurera principer för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

1. I Azure portal, på det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. På den **Azure Active Directory** sidan den **Security** klickar du på **villkorlig åtkomst**.

   ![Villkorlig åtkomst](./media/app-based-mfa/03.png)

1. På den **villkorlig åtkomst** , i verktygsfältet högst upp, klickar du på **ny princip**.

   ![Lägg till](./media/app-based-mfa/04.png)

1. På den **New** sidan den **namn** textrutan typ **kräver MFA för Azure portalåtkomst**.

   ![Namn](./media/app-based-mfa/05.png)

1. I den **tilldelning** klickar du på **användare och grupper**.

   ![Användare och grupper](./media/app-based-mfa/06.png)

1. På den **användare och grupper** utför följande steg:

   ![Användare och grupper](./media/app-based-mfa/24.png)

   1. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

   1. På den **användare och grupper** klickar du på **klar**.

1. Klicka på **Molnappar**.

   ![Molnappar](./media/app-based-mfa/08.png)

1. På den **Molnappar** utför följande steg:

   ![Välj molnappar](./media/app-based-mfa/26.png)

   1. Klicka på **Välj appar**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

   1. På den **Molnappar** klickar du på **klar**.

1. I den **åtkomstkontroller** klickar du på **bevilja**.

   ![Åtkomstkontroller](./media/app-based-mfa/10.png)

1. På den **bevilja** utför följande steg:

   ![Bevilja](./media/app-based-mfa/11.png)

   1. Välj **bevilja åtkomst**.

   1. Välj **kräva multifaktorautentisering**.

   1. Klicka på **Välj**.

1. I den **aktiverar principen** klickar du på **på**.

   ![Aktivera princip](./media/app-based-mfa/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorsstyrd åtkomst vill du förmodligen veta om den fungerar som förväntat. Använd villkorlig åtkomst som ett första steg, vad händer om principen för att simulera en inloggning av din testanvändare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

Initiera vad om utvärderingsverktyg för principen, Ställ in:

- **Isabella Simonsen** som användare
- **Microsoft Azure Management** som appar i molnet

Klicka på **vad händer om** skapar en simuleringsrapport som visar:

- **Kräva MFA för Azure portalåtkomst** under **principer som gäller**
- **Kräv multifaktorautentisering** som **bevilja kontroller**.

![Vad händer om principen verktyget](./media/app-based-mfa/23.png)

**Utvärdera din princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn längst upp på sidan klickar du på **vad händer om**.  

   ![What If](./media/app-based-mfa/14.png)

1. Klicka på **användare**väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

   ![Användare](./media/app-based-mfa/15.png)

1. Utför följande steg för att välja en molnapp:

   ![Molnappar](./media/app-based-mfa/16.png)

   1. Klicka på **Molnappar**.

   1. På den **molnet appsida**, klickar du på **Välj appar**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

   1. På sidan moln appar **klar**.

1. Klicka på **vad händer om**.

## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorsstyrd åtkomst

I det föregående avsnittet har du lärt dig hur att utvärdera en simulerad inloggning. Förutom en simulering, bör du också testa principer för villkorlig åtkomst för att säkerställa att den fungerar som förväntat.

Om du vill testa din princip, försöker logga in på din [Azure-portalen](https://portal.azure.com) med hjälp av din **Isabella Simonsen** testa konto. Du bör se en dialogruta som du måste ange ditt konto för ytterligare säkerhetsverifiering.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändaren och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Välj din princip för att ta bort principen, och klicka sedan på **ta bort** i verktygsfältet för snabb åtkomst.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv användningsvillkor godkännas](require-tou.md)
> [blockera åtkomst när en risk för session har identifierats](app-sign-in-risk.md)
