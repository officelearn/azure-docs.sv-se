---
title: 'Snabb start: registrera appar med Microsoft Identity Platform | Azure'
description: I den här snabb starten får du lära dig hur du lägger till och registrerar ett program med Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115364"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snabb start: registrera ett program med Microsoft Identity Platform

I den här snabb starten registrerar du ett program med hjälp av **Appregistreringars** upplevelsen i Azure Portal. 

Din app är integrerad med Microsoft Identity Platform genom att registrera den med en Azure Active Directory klient. Enterprise-utvecklare och SaaS-leverantörer (Software-as-a-Service) kan utveckla kommersiella moln tjänster eller branschspecifika program som kan integreras med Microsoft Identity Platform. Integrering ger säker inloggning och auktorisering för sådana tjänster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En [Azure AD-klient](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrera ett nytt program med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet. Ange din portal-session till den Azure AD-klient som du vill använda.
1. Sök efter och välj **Azure Active Directory**. Välj **Appregistreringar** under **Hantera**.
1. Välj **ny registrering**.
1. I **Registrera ett program**anger du ett meningsfullt program namn som ska visas för användarna.
1. Ange vem som kan använda programmet enligt följande:

    | Kontotyper som stöds | Beskrivning |
    |-------------------------|-------------|
    | **Endast konton i den här organisationskatalogen** | Välj det här alternativet om du skapar en verksamhetsspecifik app. Det här alternativet är inte tillgängligt om du inte registrerar programmet i en katalog.<br><br>Det här alternativet mappar endast till Azure AD för en enskild klientorganisation.<br><br>Det här alternativet är standard om du inte registrerar appen utanför en katalog. I fall då appen registreras utanför en katalog är standardalternativet Azure AD för flera klientorganisationer och personliga Microsoft-konton. |
    | **Konton i valfri organisationskatalog** | Välj det här alternativet om du vill rikta dig till mot alla företags- och utbildningskunder.<br><br>Det här alternativet mappar endast till Azure AD för flera klientorganisationer.<br><br>Om du har registrerat appen som endast Azure AD-klient kan du uppdatera den så att den blir Azure AD multi-Tenant och tillbaka till en enda klient via sidan **autentisering** . |
    | **Konton i en valfri organisationskatalog och personliga Microsoft-konton** | Välj det här alternativet om målgruppen är bredast möjliga uppsättning av kunder.<br><br>Det här alternativet mappar till Azure AD för flera klientorganisationer och personliga Microsoft-konton.<br><br>Om du har registrerat appen som Azure AD-konto för flera klienter och personliga Microsoft-konton kan du inte ändra den här inställningen i användar gränssnittet. Du måste i stället använda redigeringsprogrammet för applikationsmanifest för att ändra de kontotyper som stöds. |

1. Under **omdirigerings-URI (valfritt)** väljer du den typ av app som du skapar: **webb** eller **offentlig klient (mobil & Desktop)**. Ange omdirigerings-URI eller svars-URL för programmet.

    * För webbappar anger du grundläggande URL för appen. Till exempel kan `https://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
    * För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel `myapp://auth`.

    Exempel för webb program och interna program finns i snabb starter i [Microsoft Identity Platform](./index.yml).

1. När det är klart väljer du **Registrera**.

    ![Visar skärmen för att registrera ett nytt program i Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD tilldelar ett unikt program, eller ett klient-ID till din app. Portalen öppnar programmets **översikts** sida. Om du vill lägga till funktioner i programmet kan du välja andra konfigurations alternativ, till exempel anpassning, certifikat och hemligheter, API-behörigheter med mera.

![Exempel på en nyligen registrerad app-översikt](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Nästa steg

* Åtkomst till webb-API: er finns i [snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md)

* Läs mer om behörigheterna [i behörigheter och medgivande i slut punkten för Microsoft Identity Platform](v2-permissions-and-consent.md).

* Information om webb-API: er finns i [snabb start: Konfigurera ett program för att exponera webb-API: er](quickstart-configure-app-expose-web-apis.md).

* Information om hur du hanterar konton som stöds finns i [snabb start: ändra de konton som stöds av ett program](quickstart-modify-supported-accounts.md).

* Information om hur du skapar en app och lägger till funktioner finns i snabb starter i [Microsoft Identity Platform](./index.yml).

* Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

* Mer information om anpassnings rikt linjerna som du bör använda när du utvecklar appar finns i [rikt linjer för anpassning av program](howto-add-branding-in-azure-ad-apps.md).