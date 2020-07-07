---
title: 'Snabb start: lägga till ett program till din Azure Active Directory-klient (Azure AD)'
description: I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044c55e64c6532bf08d31b2c052e4ee9f5069ff1
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956108"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: lägga till ett program till din Azure Active Directory-klient (Azure AD)

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program. Många av de program som organisationen använder finns förmodligen redan i galleriet. Den här snabb starten använder Azure Portal för att lägga till ett galleri program i Azure AD-klienten. Den här snabb starten fokuserar på att lägga till en app som redan ingår i galleriet. Appar som inte ingår i galleriet kan också integreras med Azure AD, men det ingår inte i den här snabb starten. 

När ett program har lagts till Azure AD-klienten kan du:

- Konfigurera egenskaper för appen.
- Hantera användar åtkomst till appen med en princip för villkorlig åtkomst.
- Konfigurera enkel inloggning så att användarna kan logga in i appen med sina autentiseringsuppgifter för Azure AD.

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till ett program i Azure AD-klienten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- (Valfritt: slut för ande av [Visa dina appar](view-applications-portal.md)).

>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Lägg till en app till din Azure AD-klient

Så här lägger du till ett galleriprogram i din Azure AD-klientorganisation:

1. Välj **Azure Active Directory**på den vänstra navigerings panelen i [Azure Portal](https://portal.azure.com).
2. I fönstret **Azure Active Directory** väljer du **företags program**. Fönstret **alla program** öppnas och visar ett slumpmässigt exempel på programmen i din Azure AD-klient.
3. I fönstret **företags program** väljer du **nytt program**. 
    ![Välj nytt program för att lägga till en Galleri-app till din klient organisation](media/add-application-portal/new-application.png)
4. Växla till den nya Galleri förhands granskningen: i banderollen högst upp på **sidan Lägg till ett program**väljer du den länk som visas **Klicka här för att prova det nya och förbättrade app-galleriet**.
5. Fönstret **Bläddra i Azure AD-galleriet (förhands granskning)** öppnas och visar paneler för moln plattformar, lokala program och aktuella program. Program som listas i avsnittet **aktuella program** innehåller ikoner som visar om de stöder federerad enkel inloggning (SSO) och etablering.
    ![Sök efter en app efter namn eller kategori](media/add-application-portal/browse-gallery.png)
6. Du kan bläddra i galleriet för det program som du vill lägga till eller söka efter programmet genom att ange dess namn i sökrutan. Välj sedan programmet från resultaten. I formuläret kan du redigera namnet på programmet så att det matchar organisationens behov. I det här exemplet har vi ändrat namnet till **GitHub-test**.
    ![Visar hur du lägger till ett program från galleriet](media/add-application-portal/create-application.png)
7. Välj **Skapa**. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

Du har lagt till ett program. Nästa snabb start visar hur du ändrar logo typen och redigerar andra egenskaper för programmet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en app](add-application-portal-configure.md)
- [Konfigurera enkel inloggning](add-application-portal-setup-sso.md)
- [Ta bort en app](delete-application-portal.md)