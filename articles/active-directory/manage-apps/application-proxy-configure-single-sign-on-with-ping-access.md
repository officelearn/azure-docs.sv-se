---
title: Rubrikbaserad autentisering med PingAccess för Azure AD Application Proxy | Microsoft Docs
description: Publicera program med PingAccess och App Proxy som stöd för rubrikbaserad autentisering.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365f017fe7d71500c17d0a9ccd9c5a0a26a78b75
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989500"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Rubrikbaserad autentisering för enkel inloggning med Application Proxy och PingAccess

Azure Active Directory (Azure AD) Application Proxy samarbetar med PingAccess så att din Azure AD-kunder kan komma åt flera av dina program. PingAccess expanderar den [befintliga Application Proxy-erbjudanden](application-proxy.md) med enkel inloggning till program som använder rubriker för autentisering.

## <a name="whats-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan du ge användare åtkomst och enkel inloggning (SSO) för program som använder rubriker för autentisering. Application Proxy behandlar programmen som någon annan, med Azure AD för att autentisera åtkomst och sedan skicka trafik via kopplingstjänsten. PingAccess är placerad framför program och omvandlar den åtkomst-token från Azure AD till en rubrik. Sedan tar emot autentiseringen i det format som den kan läsa.

Användarna ser inte något annorlunda när de loggar in att använda företagets program. De kan fortsätta att arbeta från var som helst på valfri enhet. Programproxyanslutningar dirigera fjärrtrafik till alla appar utan hänsyn till deras autentiseringstyp, så att de fortfarande ska fördela belastningen automatiskt.

## <a name="how-do-i-get-access"></a>Hur gör jag för att få åtkomst?

Eftersom det här scenariot kommer från ett samarbete mellan Azure Active Directory och PingAccess, behöver du licenser för båda tjänsterna. Azure Active Directory Premium-prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du vill publicera fler än 20 rubrikbaserad program kan köpa du ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicera dina program i Azure

Den här artikeln är att publicera ett program med det här scenariot för första gången. Förutom med information om de Publicerande stegen, lär den du dig komma igång med både Application Proxy och PingAccess. Om du redan har konfigurerat båda tjänsterna, men du vill uppdatera dig om publicering stegen, gå vidare till den [Lägg till ditt program till Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy) avsnittet.

> [!NOTE]
> Eftersom det här scenariot är ett partnerskap mellan Azure AD och PingAccess, några av instruktionerna finns på webbplatsen Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installera en Application Proxy connector

Om du har aktiverat Application Proxy aktiverad och redan har installerat en connector kan du hoppa över det här avsnittet och gå till [Lägg till ditt program till Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy).

Programproxy-kopplingen är en Windows Server-tjänst som dirigerar trafiken från fjärranslutna anställda till dina publicerade program. Mer detaljerade instruktioner för installation finns i [självstudien: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör. Den **Azure Active Directory Administrationscenter** visas.
2. Välj **Azure Active Directory** > **programproxy** > **hämta anslutningsapptjänsten**. Den **hämta för Application Proxy Connector** visas.

   ![Application proxy connector download](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Följ installationsanvisningarna.

Hämta anslutningsappen bör automatiskt att aktivera Application Proxy för din katalog, men om inte, kan du välja **aktivera Application Proxy**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Lägg till ditt program till Azure AD med Application Proxy

Det finns två åtgärder som du behöver göra i Azure-portalen. Först måste du publicera ditt program med programproxy. Måste du samla in viss information om det program som du kan använda under PingAccess-steg.

#### <a name="publish-your-application"></a>Publicera programmet

Du måste först publicera programmet. Den här åtgärden omfattar:

- Lägga till dina lokala program till Azure AD
- Tilldela en användare för att testa programmet och välja huvud-baserad enkel inloggning
- Konfigurera programmets omdirigerings-URL
- Bevilja behörigheter för användare och andra program för att använda ditt lokala program

Om du vill publicera din egen lokala program:

1. Om du inte gjort i det sista avsnittet loggar du in den [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör.
2. Välj **företagsprogram** > **nytt program** > **lokala program**. Den **lägga till dina egna lokala program** visas.

   ![Lägg till ditt egen lokala program](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Fyll i de obligatoriska fälten med information om det nya programmet. Använda riktlinjerna nedan för inställningar.

   > [!NOTE]
   > En mer detaljerad genomgång av det här steget finns i [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Intern URL**: Normalt kan du ange den URL som tar dig till inloggningssidan för appens när du är i företagsnätverket. Det här scenariot måste kopplingen behandla PingAccess-proxy som förstasida av programmet. Använd det här formatet: `https://<host name of your PingAccess server>:<port>`. Porten är 3000 som standard, men du kan konfigurera det i PingAccess.

      > [!WARNING]
      > För den här typen av enkel inloggning för den interna URL: en måste använda `https` och kan inte använda `http`.

   2. **Förautentiseringsmetod**: Välj **Azure Active Directory**.
   3. **Översätt URL i rubriker**: Välj **nr**.

   > [!NOTE]
   > Om det är ditt första program kan använda port 3000 att starta och gå tillbaka till att uppdatera den här inställningen om du ändrar din PingAccess-konfiguration. För efterföljande program måste porten matcha den lyssnare som du har konfigurerat i PingAccess. Läs mer om [lyssnare inom PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Välj **Lägg till**. Översiktssidan för det nya programmet visas.

Nu tilldela en användare för att testa program och välj rubrikbaserad enkel inloggning:

1. Program-sidopanelen väljer **användare och grupper** > **Lägg till användare** > **användare och grupper (\<Number > valda)** . En lista över användare och grupper visas där du kan välja från.

   ![Användare och grupper](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Välj en användare för att testa program och välj **Välj**. Kontrollera att det här testkontot har åtkomst till dina lokala program.
3. Välj **Tilldela**.
4. Program-sidopanelen väljer **enkel inloggning** > **rubrikbaserad**.

   > [!TIP]
   > Om det här är första gången du använder rubrikbaserad enkel inloggning måste du installera PingAccess. Kontrollera din Azure-prenumeration associeras automatiskt med PingAccess-installationen genom att använda länken på den här sidan för enkel inloggning för att hämta PingAccess. Du kan öppna hämtningsplatsen nu eller kommer till den här sidan tillbaka senare.

   ![Rubrikbaserad inloggning](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Välj **Spara**.

Kontrollera din omdirigerings-URL: en har angetts till din externa URL: en:

1. Från den **Azure Active Directory Administrationscenter** sidofält väljer **Azure Active Directory** > **appregistreringar**. En lista över program visas.

   ![Appregistreringar](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Välj ditt program.
3. Klicka på länken bredvid **omdirigerings-URI: er**, som visar antalet omdirigerings-URI: er som har ställts in för webb- och offentliga klienter. Den  **\<programnamn >-autentisering** visas.
4. Kontrollera om det är en extern URL som du har tilldelat till ditt program tidigare i den **omdirigerings-URI: er** lista. Om det inte finns lägger du till den externa Webbadressen nu, med hjälp av en omdirigerings-URI-typ av **Web**, och välj **spara**.

Slutligen, Ställ in ditt lokala program så att användarna har läsbehörighet och andra program har läs-/ skrivåtkomst:

1. Från den **appregistreringar** sidopanelen för ditt program, Välj **API-behörigheter** > **lägga till en behörighet**  >   **Microsoft API: er** > **Microsoft Graph**. Den **begär API-behörigheter** för **Microsoft Graph** visas, som innehåller API: er för Windows Azure Active Directory.

   ![Begär API-behörigheter](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Välj **delegerade behörigheter** > **användaren** > **User.Read**.
3. Välj **programbehörigheter** > **programmet** > **Application.ReadWrite.All**.
4. Välj **Lägg till behörigheter**.
5. I den **API-behörigheter** väljer **bevilja administratörens godkännande för \<directory-namn >**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för PingAccess-steg

Du behöver samla in dessa tre typer av information (alla GUID) för att konfigurera ditt program med PingAccess:

| Namnet på Azure AD-fält | Namnet på PingAccess-fält | Dataformat |
| --- | --- | --- |
| **ID för programmet (klient)** | **Klient-ID** | GUID |
| **Katalog (klient)-ID** | **Utfärdare** | GUID |
| `PingAccess key` | **Klienthemlighet** | Slumpmässig sträng |

Att samla in den här informationen:

1. Från den **Azure Active Directory Administrationscenter** sidofält väljer **Azure Active Directory** > **appregistreringar**. En lista över program visas.
2. Välj ditt program. Den **appregistreringar** sidan för ditt program visas.

   ![Översikt över registreringen för ett program](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. Bredvid den **(klient)-ID: T** värde, väljer den **kopiera till Urklipp** ikon, och sedan kopiera och spara den. Du anger det här värdet senare som Pingaccesss klient-ID.
4. Nästa den **katalog (klient)-ID** värde, även välja **kopiera till Urklipp**och kopiera och spara den. Du kan ange det här värdet senare som Pingaccesss utfärdare.
5. På sidopanelen av den **appregistreringar** för ditt program, Välj **certifikat och hemligheter** > **nya klienthemligheten**. Den **lägga till en klienthemlighet** visas.

   ![Lägg till en klienthemlighet](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. I **beskrivning**, typen `PingAccess key`.
7. Under **förfaller**, Välj hur du ställer in PingAccess-nyckel: **1 år**, **i 2 år**, eller **aldrig**.
8. Välj **Lägg till**. PingAccess nyckel visas i tabellen på klienten hemligheter, med en slumpmässig sträng som autofills i den **värdet** fält.
9. Bredvid nyckeln PingAccess **värdet** fältet, Välj den **kopiera till Urklipp** ikon, och sedan kopiera och spara den. Du kan ange det här värdet senare som Pingaccesss klienthemlighet.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Uppdatera GraphAPI för att skicka anpassade fält (valfritt)

En lista över säkerhetstoken som Azure AD skickar för autentisering, se [ID-token för Microsoft identity-plattformen](../develop/id-tokens.md). Om du behöver ett anpassat anspråk som skickar andra token, ange den `acceptMappedClaims` fält för programmet att `True`. Du kan använda Graph-testaren eller programmanifestet för Azure AD-portalen för att göra ändringen.

Det här exemplet använder Graph-testaren:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

Det här exemplet används den [Azure Active Directory-portalen](https://aad.portal.azure.com/) att uppdatera den `acceptMappedClaims` fält:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör.
2. Välj **Azure Active Directory** > **appregistreringar**. En lista över program visas.
3. Välj ditt program.
4. På sidopanelen av den **appregistreringar** sidan för ditt program, Välj **Manifest**. Manifest JSON-koden för registrering av ditt program visas.
5. Sök efter den `acceptMappedClaims` fältet och ändra värdet till `True`.
6. Välj **Spara**.

### <a name="use-a-custom-claim-optional"></a>Använd ett anpassat anspråk (valfritt)

För att göra ditt program använder ett anpassat anspråk och ta med ytterligare fält, vara säker på att du har också [skapas en anpassad princip för Anspråksmappning och den tilldelas programmet](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Om du vill använda ett anpassat anspråk måste du också ha en anpassad princip definieras och tilldelas programmet. Den här principen ska inkludera alla nödvändiga anpassade attribut.
>
> Du kan göra principdefinitionen och tilldelas med PowerShell, Azure AD Graph Explorer eller Microsoft Graph. Om du gör dem i PowerShell, kan du behöva använda först `New-AzureADPolicy` och tilldela den till programmet med `Add-AzureADServicePrincipalPolicy`. Mer information finns i [anspråk mappning principtilldelning](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-application"></a>Hämta PingAccess och konfigurera ditt program

Nu när du har slutfört alla steg för Azure Active Directory-installationen, kan du gå vidare till att konfigurera PingAccess.

Detaljerade anvisningar för PingAccess-delen av det här scenariot fortsätter i Ping Identity-dokumentationen. Följ instruktionerna i [konfigurera PingAccess för Azure AD för att skydda program som publicerats med hjälp av Microsoft Azure AD-programproxyn](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) på webbplatsen Ping Identity.

De här stegen för att installera PingAccess och ställa in ett PingAccess-konto (om du inte redan har en). För att upprätta en anslutning med Azure AD OpenID Connect (OIDC) måste du skapa sedan en leverantör av säkerhetstoken med den **katalog (klient)-ID** värde som du kopierade från Azure AD-portalen. Sedan för att skapa en webbsession på PingAccess, använder du den **(klient)-ID: T** och `PingAccess key` värden. Efter det kan du konfigurera identitetsmappning och skapa en virtuell värd, plats och program.

### <a name="test-your-application"></a>Testa ditt program

När du har slutfört de här stegen, bör ditt program vara igång. Testa den genom att öppna en webbläsare och navigera till den externa URL: en som du skapade när du har publicerat programmet i Azure. Logga in med kontot test som du tilldelat till programmet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD för att skydda program som publicerats med Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka problem med Application Proxy och felmeddelanden](application-proxy-troubleshoot.md)
