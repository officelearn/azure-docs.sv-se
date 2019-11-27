---
title: Migrera principer för villkorlig åtkomst – Azure Active Directory
description: Den här artikeln visar hur du migrerar en klassisk princip som kräver Multi-Factor Authentication i Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca00ae62ba114aecef48117fd8a54b7f2e962dfd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380314"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrera en klassisk princip som kräver Multi-Factor Authentication i Azure Portal

Den här självstudien visar hur du migrerar en klassisk princip som kräver **Multi-Factor Authentication** för en molnbaserad app. Även om det inte är ett krav rekommenderar vi att du läser [migrera klassiska principer i Azure Portal](policy-migration.md) innan du börjar migrera dina klassiska principer.

## <a name="overview"></a>Översikt

Scenariot i den här artikeln visar hur du migrerar en klassisk princip som kräver **Multi-Factor Authentication** för en molnbaserad app.

![Azure Active Directory](./media/policy-migration/33.png)

Migreringsprocessen består av följande steg:

1. [Öppna den klassiska principen](#open-a-classic-policy) för att hämta konfigurations inställningarna.
1. Skapa en ny Azure AD-princip för villkorlig åtkomst för att ersätta den klassiska principen. 
1. Inaktivera den klassiska principen.

## <a name="open-a-classic-policy"></a>Öppna en klassisk princip

1. Klicka på **Azure Active Directory**i [Azure Portal](https://portal.azure.com)i det vänstra navigerings fältet.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Klicka på **villkorlig åtkomst**i avsnittet **hantera** på sidan **Azure Active Directory** .

   ![Villkorlig åtkomst](./media/policy-migration-mfa/02.png)

1. I avsnittet **Hantera** klickar du på **klassiska principer (för hands version)** .

   ![Klassiska principer](./media/policy-migration-mfa/12.png)

1. Klicka på principen som kräver **Multi-Factor Authentication** för en molnbaserad app i listan med klassiska principer.

   ![Klassiska principer](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Skapa en ny princip för villkorlig åtkomst

1. Klicka på **Azure Active Directory**i [Azure Portal](https://portal.azure.com)i det vänstra navigerings fältet.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Klicka på **villkorlig åtkomst**i avsnittet **hantera** på sidan **Azure Active Directory** .

   ![Villkorlig åtkomst](./media/policy-migration/02.png)

1. Öppna den **nya** sidan genom att klicka på **Lägg till**i verktygsfältet högst upp på sidan för **villkorlig åtkomst** .

   ![Villkorlig åtkomst](./media/policy-migration/03.png)

1. På sidan **nytt** i text rutan **namn** anger du ett namn för principen.

   ![Villkorlig åtkomst](./media/policy-migration/29.png)

1. I avsnittet **tilldelningar** klickar du på **användare och grupper**.

   ![Villkorlig åtkomst](./media/policy-migration/05.png)

   1. Om du har valt alla användare i den klassiska principen klickar du på **alla användare**. 

      ![Villkorlig åtkomst](./media/policy-migration/35.png)

   1. Om du har valt grupper i den klassiska principen klickar du på **Välj användare och grupper**och väljer sedan de användare och grupper som krävs.

      ![Villkorlig åtkomst](./media/policy-migration/36.png)

   1. Om du har exkluderade grupper klickar du på fliken **exkludera** och väljer sedan de användare och grupper som krävs. 

      ![Villkorlig åtkomst](./media/policy-migration/37.png)

1. Klicka på **molnappar**i avsnittet **tilldelning** på sidan **ny** för att öppna sidan **Cloud Apps** .
1. Utför följande steg på sidan **Cloud Apps** :
   1. Klicka på **Välj appar**.
   1. Klicka på **Välj**.
   1. På sidan **Välj** väljer du din Cloud App och klickar sedan på **Välj**.
   1. På sidan **molnappar** klickar du på **Slutför**.
1. Om du **behöver Multi-Factor Authentication** markerat:

   ![Villkorlig åtkomst](./media/policy-migration/26.png)

   1. I avsnittet **åtkomst kontroller** klickar du på **bevilja**.

      ![Villkorlig åtkomst](./media/policy-migration/27.png)

   1. På sidan **bevilja** klickar du på **bevilja åtkomst**och klickar sedan på **Kräv Multi-Factor Authentication**.
   1. Klicka på **Välj**.
1. Aktivera principen genom att klicka **på på** .

   ![Villkorlig åtkomst](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Inaktivera den klassiska principen

Om du vill inaktivera den klassiska principen klickar du på **inaktivera** i vyn **information** .

![Klassiska principer](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om den klassiska princip migreringen finns [i Migrera klassiska principer i Azure Portal](policy-migration.md).
- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md).
