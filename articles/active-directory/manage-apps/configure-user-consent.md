---
title: Konfigurera användarens medgivande till ett program – Azure Active Directory | Microsoft Docs
description: Lär dig mer om att hantera hur användare godkänna att behörigheter för programmet. Du kan förenkla användarupplevelsen genom att tilldela administratörens godkännande. Dessa metoder gäller alla användare i din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 25f5d79b900cf15a9bdcb8f037fa95494c63b7e1
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088861"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Konfigurera hur slutanvändare samtycka till ett program i Azure Active Directory
Lär dig mer om att konfigurera hur användare godkänna att behörigheter för programmet. Du kan förenkla användarupplevelsen genom att tilldela administratörens godkännande. Den här artikeln innehåller olika sätt du kan konfigurera användarens medgivande. Metoderna som gäller för alla användare i din Azure Active Directory (Azure AD)-klient. 

Mer information om principer för program finns i [Azure Active Directory-ramverket för medgivande](../develop/consent-framework.md).

## <a name="prerequisites"></a>Förutsättningar

Bevilja administratörens godkännande måste du logga in som global administratör, programadministratör eller en molnprogramadministratör.

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Bevilja administratörens godkännande till enterprise-appar i Azure portal

Så här ger administratörens godkännande till en företagsapp:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör, programadministratör eller en molnprogramadministratör.
2. Klicka på **alla tjänster** överst i den vänstra navigeringsmenyn. Den **Azure Active Directory-tillägget** öppnas.
3. Skriv i sökrutan filtrera **”Azure Active Directory”** och välj den **Azure Active Directory** objekt.
4. Navigeringsmenyn klickar du på **företagsprogram**.
5. Klicka på **bevilja administratörens godkännande**. Du uppmanas att logga in på Administrera programmet.
6. Logga in med ett konto som har behörighet att bevilja administratörens godkännande för programmet. 
7. Accepterar du behörigheter för programmet.

Det här alternativet fungerar bara om programmet är: 

- Registrerade i klientorganisationen, eller
- Registrerade i en annan Azure AD-klient och samtyckt av minst en slutanvändare. När en användare har samtyckt till ett program, Azure AD innehåller programmet under **företagsappar** i Azure-portalen.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Bevilja administratörens godkännande när du registrerar en app i Azure portal

Så här ger administratörens godkännande när du registrerar en app: 

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Navigera till den **Appregistreringar** bladet.
3. Välj ett program för medgivande.
4. Välj **behörigheter som krävs för**.
5. Klicka på **bevilja behörigheter** överst på bladet.


## <a name="grant-admin-consent-through-a-url-request"></a>Bevilja administratörens godkännande via en URL-begäran

Så här ger administratörens godkännande via en URL-begäran:

1. Skapa en begäran att *login.microsoftonline.com* med dina konfigurationer och lägger till på `&prompt=admin_consent`. 
2. Appen har beviljats medgivande för alla användare för när du har loggat in med autentiseringsuppgifter som administratör.


## <a name="force-user-consent-through-a-url-request"></a>Tvinga användargodkännande via en URL-begäran

Om du vill kräva av slutanvändarna samtycker till att ett program varje gång de autentiserar, lägger du till `&prompt=consent` med autentiseringen URL för begäran.

## <a name="next-steps"></a>Nästa steg

[Medgivande och integrera appar till AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Medgivande och ge behörighet till för AzureAD v2.0 konvergerat appar](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
