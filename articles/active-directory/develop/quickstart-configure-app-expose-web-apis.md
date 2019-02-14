---
title: Konfigurera en app att exponera webb-API:er (förhandsversion) | Azure
description: Lär dig hur du konfigurerar en app att exponera en ny behörighet/ett nytt omfång och roller för att göra appen tillgänglig för klientprogram.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940ca126eb18b81fd31f1ee2876948563e9d97af
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188384"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis-preview"></a>Snabbstart: Konfigurera en app att exponera webb-API:er (förhandsversion)

Du kan utveckla ett webb-API och göra det tillgängligt för klientprogram genom att exponera [behörigheter/omfång](developer-glossary.md#scopes) och [roller](developer-glossary.md#roles). Ett korrekt konfigurerat webb-API tillhandahålls precis som de andra webb-API:erna från Microsoft, inklusive Graph API och Office 365-API:erna.

I den här snabbstarten lär du dig hur du konfigurerar en app att exponera ett nytt omfång för att göra den tillgänglig för klientprogram.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar kontrollerar du att följande krav är uppfyllda:

* Lär dig mer om [behörigheter och medgivande](v2-permissions-and-consent.md) som stöds, vilket är viktigt att förstå när du skapar appar som måste användas av andra användare eller appar.
* Ha en klientorganisation som har appar som är registrerade till den.
  * Om du inte har några registrerade appar kan du [få information om hur du registrerar appar på Microsoft Identity Platform](quickstart-register-app.md).
* Registrera dig för förhandsversionsfunktionerna appregistreringar i Azure-portalen. Stegen i den här snabbstarten motsvarar det nya användargränssnittet och fungerar bara om du använder förhandsversionsfunktionerna.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

Innan du kan konfigurera appen gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller en personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. Välj vilken metod du vill använda, användargränssnitt eller applikationsmanifest, för att exponera ett nytt omfång:
    * [Exponera ett nytt omfång via användargränssnittet](#expose-a-new-scope-through-the-ui)
    * [Exponera ett nytt omfång eller en ny roll via applikationsmanifestet](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Exponera ett nytt omfång via användargränssnittet

[![Exponera ett API med hjälp av användargränssnittet](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Så här exponerar du ett nytt omfång via användargränssnittet:

1. På appens **översiktssida** väljer du avsnittet **Exponera ett API**.

1. Välj **Lägg till omfång**.

1. Om du inte har angett **Program-ID-URI** visas en uppmaning att ange en. Ange din program-ID-URI eller en använd den som ges och välj sedan **Spara och fortsätt**.

1. När sidan **Lägg till omfång** visas anger du omfångets information:

    | Fält | Beskrivning |
    |-------|-------------|
    | **Omfångsnamn** | Ange ett beskrivande namn på omfånget.<br><br>Till exempel `Employees.Read.All`. |
    | **Vem kan ge medgivande** | Välj om användare kan ge sitt medgivande för det här omfånget eller om administratörsmedgivande krävs. Välj **Endast administratörer** för behörigheter på högre nivå. |
    | **Visningsnamn för administratörsmedgivande** | Ge en beskrivning av omfånget, som administratörer kan se.<br><br>Till exempel, `Read-only access to Employee records` |
    | **Beskrivning av administratörsmedgivande** | Ge en beskrivning av omfånget, som administratörer kan se.<br><br>Till exempel, `Allow the application to have read-only access to all Employee data.` |

    Om användare kan ge sitt medgivande för omfånget lägger du även till värden för följande fält:

    | Fält | Beskrivning |
    |-------|-------------|
    | **Visningsnamn för användarmedgivande** | Ange ett beskrivande namn på omfånget, som användarna kan se.<br><br>Till exempel, `Read-only access to your Employee records` |
    | **Beskrivning av användarmedgivande** | Ge en beskrivning av omfånget, som användarna kan se.<br><br>Till exempel, `Allow the application to have read-only access to your Employee data.` |

1. Ange **Tillstånd** och välj **Lägg till omfång** när du är klar.

1. Följ stegen för att [kontrollera att webb-API:et exponeras för andra appar](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Exponera ett nytt omfång eller en ny roll via applikationsmanifestet

[![Exponera ett nytt omfång med hjälp av oauth2Permissions-samlingen i manifestet](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Så här exponerar du ett nytt omfång eller en ny roll via applikationsmanifestet:

1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad manifestredigerare öppnas så att du kan **Redigera** manifestet i portalen. Du kan också välja **Ladda ned** och redigera manifestet lokalt, och sedan använda **Ladda upp** för att tillämpa det på appen igen.
    
    I följande exempel visas hur du exponerar ett nytt omfång med namnet `Employees.Read.All` i resursen/API:et genom att lägga till följande JSON-element i `oauth2Permissions`-samlingen.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

  > [!NOTE]
  > Värdet `id` måste genereras programmatiskt eller med hjälp av ett GUID-genereringsverktyg såsom [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). `id` representerar en unik identifierare för omfånget såsom det görs tillgängligt av webb-API:et. När en klient är korrekt konfigurerad med behörigheter att komma åt ditt webb-API utfärdas en OAuth 2.0-åtkomsttoken till klienten av Azure AD. När klienten anropar webb-API:et presenterar den den åtkomsttoken som har omfångsanspråket (scp) till de behörigheter som begärs i dess programregistrering.
  >
  > Du kan göra ytterligare omfång tillgängliga senare, vid behov. Tänk på att ditt webb-API kanske gör flera omfång tillgängliga som är associerade med en mängd olika funktioner. Din resurs kan styra åtkomsten till webb-API:et vid körning genom att utvärdera omfångsanspråken (`scp`) i den mottagna OAuth 2.0-åtkomsttoken.

1. När det är klart klickar du på **Spara**. Nu är ditt webb-API konfigurerat för användning av andra program i din katalog.
1. Följ stegen för att [kontrollera att webb-API:et exponeras för andra appar](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verifiera att webb-API:et görs tillgängligt för andra appar

1. Gå tillbaka till din Azure AD-klientorganisation, välj **Appregistreringar**, leta upp och välj det klientprogram som du vill konfigurera.
1. Upprepa stegen som beskrivs i Konfigurera ett klientprogram för åtkomst till webb-API:er.
1. När du kommer till steget **Välja ett API** väljer du din resurs. Du bör se det nya omfånget, tillgängligt för begäranden om klientbehörighet.

## <a name="more-on-the-application-manifest"></a>Mer information om programmanifestet

Applikationsmanifestet fungerar som en mekanism för att uppdatera programentiteten, som definierar alla attribut för ett Azure AD-programs identitetskonfiguration. Mer information om programentiteten och dess schema finns i [dokumentationen om Graph API-programentiteten](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Artikeln innehåller utförlig referensinformation om de programentitetsmedlemmar som används för att ange behörigheter för ditt API, inklusive:  

* Medlemmen appRoles, som är en samling [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type)-entiteter som används för att definiera [programbehörigheter](developer-glossary.md#permissions) för ett webb-API.
* Medlemmen oauth2Permissions, som är en samling [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type)-entiteter som används för att definiera [delegerade behörigheter](developer-glossary.md#permissions) för ett webb-API.

Mer information om applikationsmanifestbegrepp i allmänhet finns i [Förstå Azure Active Directory-applikationsmanifestet](reference-app-manifest.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering:

* [Registrera en app på Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurera ett klientprogram för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md)
* [Ändra konton som stöds av en app](quickstart-modify-supported-accounts.md)
* [Ta bort en app registrerad på Microsoft Identity Platform](quickstart-remove-app.md)

Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar program med Azure Active Directory finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).
