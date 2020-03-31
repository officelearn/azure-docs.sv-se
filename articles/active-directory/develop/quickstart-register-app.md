---
title: 'Snabbstart: Registrera appar med Microsofts identitetsplattform | Azure'
description: I den här snabbstarten får du lära dig hur du lägger till och registrerar ett program med Microsofts identitetsplattform.
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
ms.openlocfilehash: 1625b48d86eebaf5d8fcd4c100d89b83716ba459
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408376"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snabbstart: Registrera ett program med Microsofts identitetsplattform

I den här snabbstarten registrerar du ett program med hjälp av **appregistreringsupplevelsen** i Azure-portalen. 

Din app är integrerad med Microsofts identitetsplattform genom att registrera den med en Azure Active Directory-klientorganisation. Företagsutvecklare och SaaS-leverantörer (Software-as-a-Service) kan utveckla kommersiella molntjänster eller affärsprogram som kan integreras med Microsofts identitetsplattform. Integrering ger säker inloggning och auktorisering för sådana tjänster.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En [Azure AD-tentant](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrera ett nytt program med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet. Ställ in portalsessionen till den Azure AD-klient som du vill ha.
1. Sök efter och välj **Azure Active Directory**. Under **Hantera**väljer du **Appregistreringar**.
1. Välj **Ny registrering**.
1. I **Registrera ett program**anger du ett meningsfullt programnamn som ska visas för användarna.
1. Ange vem som kan använda programmet enligt följande:

    | Kontotyper som stöds | Beskrivning |
    |-------------------------|-------------|
    | **Endast konton i den här organisationskatalogen** | Välj det här alternativet om du skapar en verksamhetsspecifik app. Det här alternativet är inte tillgängligt om du inte registrerar programmet i en katalog.<br><br>Det här alternativet mappar endast till Azure AD för en enskild klientorganisation.<br><br>Det här alternativet är standard om du inte registrerar appen utanför en katalog. I fall då appen registreras utanför en katalog är standardalternativet Azure AD för flera klientorganisationer och personliga Microsoft-konton. |
    | **Konton i valfri organisationskatalog** | Välj det här alternativet om du vill rikta dig till mot alla företags- och utbildningskunder.<br><br>Det här alternativet mappar endast till Azure AD för flera klientorganisationer.<br><br>Om du registrerade appen som Azure AD endast en enda klient kan du uppdatera den till Azure AD-flera innehavare och tillbaka till en enda klient via **autentiseringssidan.** |
    | **Konton i en valfri organisationskatalog och personliga Microsoft-konton** | Välj det här alternativet om målgruppen är bredast möjliga uppsättning av kunder.<br><br>Det här alternativet mappar till Azure AD för flera klientorganisationer och personliga Microsoft-konton.<br><br>Om du har registrerat appen som Azure AD-konton med flera innehavare och personliga Microsoft-konton kan du inte ändra den här inställningen i användargränssnittet. Du måste i stället använda redigeringsprogrammet för applikationsmanifest för att ändra de kontotyper som stöds. |

1. Under **Omdirigera URI (valfritt)** väljer du den typ av app du skapar: **Webb** eller Offentlig **klient (mobil & skrivbord).** Ange sedan url:en för omdirigering eller svar för ditt program.

    * För webbappar anger du grundläggande URL för appen. Till exempel kan `https://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
    * För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel `myapp://auth`.

    Exempel för webbprogram eller inbyggda program finns i snabbstarterna i [Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop).

1. När det är klart väljer du **Registrera**.

    ![Visar skärmen för att registrera ett nytt program i Azure-portalen](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD tilldelar ett unikt program, eller klient, ID till din app. Portalen öppnar programmets **översiktssida.** Om du vill lägga till funktioner i ditt program kan du välja andra konfigurationsalternativ, inklusive branding, certifikat och hemligheter, API-behörigheter med mera.

![Exempel på en nyligen registrerad appöversiktssida](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill komma åt webb-API:er finns i [Snabbstart: Konfigurera ett klientprogram för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md)

* Mer information om behörigheterna finns [i Behörigheter och medgivande i slutpunkten för Microsoft-identitetsplattformen](v2-permissions-and-consent.md).

* Om du vill visa webb-API:er finns i [Snabbstart: Konfigurera ett program för att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md).

* Om du vill hantera konton som stöds finns [snabbstart: Ändra konton som stöds av ett program](quickstart-modify-supported-accounts.md).

* Om du vill skapa en app och lägga till funktioner läser du snabbstarterna i [Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop).

* Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

* Mer information om de riktlinjer för varumärkesprofilering som du bör använda när du utvecklar appar finns i [Riktlinjer för varumärkesutsättning för program](howto-add-branding-in-azure-ad-apps.md).
