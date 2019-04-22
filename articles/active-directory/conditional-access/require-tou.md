---
title: Snabbstart – kräver användningsvillkor godkännas före åtkomst till molnappar som skyddas av Azure Active Directory villkorlig åtkomst | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan kräva att dina användningsvillkor accepteras innan beviljas åtkomst till valda molnappar genom villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c37a58cb9f9d1082d02854f43e511e5431d90c13
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894259"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Snabbstart: Kräv användningsvillkor godkännas före åtkomst till molnappar

Innan du använder vissa molnappar i din miljö, kanske du vill få medgivande från användare i form av accepterar dina användningsvillkor (ToU). Villkorlig åtkomst i Azure Active Directory (Azure AD) ger dig:

- En enkel metod för att konfigurera villkor för användning
- Alternativet för att kräva accepterar dina användningsvillkor via principer för villkorlig åtkomst  

Den här snabbstarten visar hur du konfigurerar en [princip för villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md) som kräver en ToU ska godkännas för ett valt moln-app i din miljö.

![Skapa princip](./media/require-tou/5555.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra scenariot i den här snabbstarten behöver du:

- **Åtkomst till en Azure AD Premium-versionen** -villkorlig åtkomst i Azure AD är en Azure AD Premium-funktion.
- **Ett testkonto kallas Isabella Simonsen** – om du inte vet hur du skapar ett testkonto finns [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att få en bild av inloggning utan en princip för villkorlig åtkomst.

**Så här testar din inloggning:**

1. Logga in på din [Azure-portalen](https://portal.azure.com/) som Isabella Simonsen.
1. Logga ut.

## <a name="create-your-terms-of-use"></a>Skapa dina användningsvillkor

Det här avsnittet ger dig stegen för att skapa ett exempel på villkor för användning. När du skapar en ToU kan du välja ett värde för **tvinga med villkorlig åtkomst principmallar**. Att välja **kundpolicy** öppnas dialogrutan för att skapa en ny princip för villkorlig åtkomst så snart din ToU har skapats.

**Skapa dina användningsvillkor:**

1. Skapa ett nytt dokument i Microsoft Word.

1. Typ **min användningsvillkor**, och spara dokumentet på datorn som **mytou.pdf**.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

1. I Azure portal, på det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. På den **Azure Active Directory** sidan den **Security** klickar du på **villkorlig åtkomst**.

   ![Villkorad åtkomst](./media/require-tou/03.png)

1. I den **hantera** klickar du på **användningsvillkoren**.

   ![Användningsvillkor](./media/require-tou/04.png)

1. Klicka på menyn längst upp **nya allmänna**.

   ![Användningsvillkor](./media/require-tou/05.png)

1. På den **nya användningsvillkor** sidan:

   ![Användningsvillkor](./media/require-tou/112.png)

   1. I den **namn** textrutan typ **Mina TOU**.

   1. I den **visningsnamn** textrutan typ **Mina TOU**.

   1. Ladda upp dina villkor för användning PDF-fil.

   1. Som **språk**väljer **engelska**.

   1. Som **kräva att användarna expanderar användningsvillkoren**väljer **på**.

   1. Som **tvinga med villkorlig åtkomst principmallar**väljer **kundpolicy**.

   1. Klicka på **Skapa**.

## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Scenariot i den här snabbstarten använder:

- Azure-portalen som platshållare för en molnapp som kräver att dina användningsvillkor godkännas. 
- Exempelanvändaren att testa principen för villkorlig åtkomst.  

Ange i din princip:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Isabella Simonsen |
| Molnappar | Microsoft Azure Management |
| Bevilja åtkomst | Min TOU |

![Skapa princip](./media/require-tou/1234.png)

**Konfigurera principer för villkorlig åtkomst:**

1. På den **New** sidan den **namn** textrutan typ **kräver TOU för Isabella**.

   ![Namn](./media/require-tou/71.png)

1. I den **tilldelning** klickar du på **användare och grupper**.

   ![Användare och grupper](./media/require-tou/06.png)

1. På den **användare och grupper** sidan:

   ![Användare och grupper](./media/require-tou/24.png)

   1. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

   1. På den **användare och grupper** klickar du på **klar**.

1. Klicka på **Molnappar**.

   ![Molnappar](./media/require-tou/08.png)

1. På den **Molnappar** sidan:

   ![Välj molnappar](./media/require-tou/26.png)

   1. Klicka på **Välj appar**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

   1. På den **Molnappar** klickar du på **klar**.

1. I den **åtkomstkontroller** klickar du på **bevilja**.

   ![Åtkomstkontroller](./media/require-tou/10.png)

1. På den **bevilja** sidan:

   ![Bevilja](./media/require-tou/111.png)

   1. Välj **bevilja åtkomst**.

   1. Välj **min TOU**.

   1. Klicka på **Välj**.

1. I den **aktiverar principen** klickar du på **på**.

   ![Aktivera princip](./media/require-tou/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorsstyrd åtkomst vill du förmodligen veta om den fungerar som förväntat. Använd villkorlig åtkomst som ett första steg, vad händer om principen för att simulera en inloggning av din testanvändare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

Initiera vad om utvärderingsverktyg för principen, Ställ in:

- **Isabella Simonsen** som användare
- **Microsoft Azure Management** som appar i molnet

Klicka på **vad händer om** skapar en simuleringsrapport som visar:

- **Kräv TOU för Isabella** under **principer som gäller**
- **Min TOU** som **bevilja kontroller**.

![Vad händer om principen verktyget](./media/require-tou/79.png)

**Utvärdera din princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn längst upp på sidan klickar du på **vad händer om**.  

   ![What If](./media/require-tou/14.png)

1. Klicka på **användare**väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

   ![Användare](./media/require-tou/15.png)

1. Att välja en molnapp:

   ![Molnappar](./media/require-tou/16.png)

   1. Klicka på **Molnappar**.

   1. På den **molnet appsida**, klickar du på **Välj appar**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

   1. På sidan moln appar **klar**.

1. Klicka på **vad händer om**.

## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorsstyrd åtkomst

I det föregående avsnittet har du lärt dig hur att utvärdera en simulerad inloggning. Förutom en simulering, bör du också testa principer för villkorlig åtkomst för att säkerställa att den fungerar som förväntat.

Om du vill testa din princip, försöker logga in på din [Azure-portalen](https://portal.azure.com) med hjälp av din **Isabella Simonsen** testa konto. Du bör se en dialogruta som kräver att du accepterar dina användningsvillkor.

![Användningsvillkor](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändaren och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Välj din princip för att ta bort principen, och klicka sedan på **ta bort** i verktygsfältet för snabb åtkomst.

    ![Multifaktorautentisering](./media/require-tou/33.png)

- Om du vill ta bort dina användningsvillkor, markera den och klicka sedan på **ta bort termer** i verktygsfältet högst upp.

    ![Multifaktorautentisering](./media/require-tou/29.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräva MFA för specifika appar](app-based-mfa.md)
> [blockera åtkomst när en risk för session har identifierats](app-sign-in-risk.md)
