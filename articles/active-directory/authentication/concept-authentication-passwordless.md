---
title: Azure Active Directory lösenordslös inloggning (förhandsversion)
description: Lär dig mer om alternativ för lösenordslös inloggning till Azure Active Directory med FIDO2-säkerhetsnycklar eller Microsoft Authenticator-appen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ba38a5d7e8e8a89ba122efb1734c1f13a94d48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332188"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Alternativ för lösenordslös autentisering för Azure Active Directory

MFA (Multifaktorautentisering) är ett bra sätt att skydda din organisation, men användarna blir ofta frustrerade över det extra säkerhetslagret ovanpå att behöva komma ihåg sina lösenord. Lösenordslös autentisering metoder är mer praktiskt eftersom lösenordet tas bort och ersättas med något du har, plus något du är eller något du vet.

|   | Något du har | Något du är eller vet |
| --- | --- | --- |
| Lösenordsfri | Windows 10-enhet, telefon eller säkerhetsnyckel | Biometriska eller PIN-kod |

Varje organisation har olika behov när det gäller autentisering. Microsoft erbjuder följande tre lösenordslösa autentiseringsalternativ som integreras med Azure Active Directory (Azure AD):

- Windows Hello för företag
- Microsoft Authenticator-appen
- FIDO2-säkerhetsnycklar

![Autentisering: Säkerhet kontra bekvämlighet](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello för företag

Windows Hello för företag är perfekt för informationsarbetare som har en egen utsedd Windows-dator. Den biometriska och PIN-koden är direkt knuten till användarens dator, vilket förhindrar åtkomst från någon annan än ägaren. Med integrering av infrastruktur för offentliga nycklar (PKI) och inbyggt stöd för enkel inloggning (SSO) är Windows Hello för företag en praktisk metod för sömlös åtkomst till företagsresurser lokalt och i molnet.

![Exempel på en användares inloggning med Windows Hello för företag](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Följande steg visar hur inloggningsprocessen fungerar med Azure Active Directory.

![Diagram som beskriver de steg som ingår för användarloggning med Windows Hello för företag](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. En användare loggar in på Windows med biometrisk eller PIN-gest. Gesten låser upp den privata nyckeln för Windows Hello för företag och skickas till säkerhetssupportleverantören för molnautentisering, kallad *Cloud AP-providern*.
1. Cloud AP-providern begär en nonce från Azure AD.
1. Azure AD returnerar en nonce som är giltig i 5 minuter.
1. Cloud AP-providern signerar nonce med användarens privata nyckel och returnerar den signerade nonce till Azure AD.
1. Azure AD validerar den signerade nonce med hjälp av användarens säkert registrerade offentliga nyckel mot nonce signaturen. När du har validerat signaturen validerar Azure AD sedan den returnerade signerade nonce. När nonce valideras skapar Azure AD en primär uppdateringstoken (PRT) med sessionsnyckel som är krypterad till enhetens transportnyckel och returnerar den till Cloud AP-providern.
1. Cloud AP-providern tar emot den krypterade PRT med sessionsnyckeln. Med hjälp av enhetens privata transportnyckel dekrypterar Cloud AP-providern sessionsnyckeln och skyddar sessionsnyckeln med hjälp av enhetens TPM (Trusted Platform Module).
1. Cloud AP-providern returnerar ett lyckat autentiseringssvar till Windows. Användaren kan sedan komma åt Windows samt moln- och lokala program utan att behöva autentisera igen (SSO).

[Planeringsguiden](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) för Windows Hello för företag kan användas för att hjälpa dig att fatta beslut om vilken typ av Windows Hello för företag-distribution och de alternativ du behöver överväga.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Tillåt att den anställdes telefon blir en lösenordslös autentiseringsmetod. Du kanske redan använder Microsoft Authenticator-appen som ett bekvämt alternativ för multifaktorautentisering utöver ett lösenord. Du kan också använda Authenticator-appen som ett lösenordslöst alternativ.

![Logga in på Microsoft Edge med Microsoft Authenticator-appen](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator-appen förvandlar alla iOS- eller Android-telefoner till en stark, lösenordslös autentiseringsuppgifter. Användare kan logga in på valfri plattform eller webbläsare genom att få ett meddelande till sin telefon, matcha ett nummer som visas på skärmen till den på sin telefon, och sedan använda deras biometriska (touch eller ansikte) eller PIN-kod för att bekräfta. Mer information om hur du använder och installera Microsoft Authenticator-appen finns i [Hämta och installera Microsoft Authenticator-appen.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)

Lösenordslös autentisering med Authenticator-appen följer samma grundläggande mönster som Windows Hello for Business. Det är lite mer komplicerat eftersom användaren måste identifieras så att Azure AD kan hitta Den Microsoft Authenticator App-version som används:

![Diagram som beskriver de steg som ingår för användarloggning med Microsoft Authenticator-appen](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Användaren anger sitt användarnamn.
1. Azure AD upptäcker att användaren har en stark autentiseringsuppgifter och startar flödet Stark autentiseringsuppgifter.
1. Ett meddelande skickas till appen via Apple Push Notification Service (APNS) på iOS-enheter eller via Firebase Cloud Messaging (FCM) på Android-enheter.
1. Användaren får push-meddelandet och öppnar appen.
1. Appen anropar Azure AD och får en utmaning och nonce.
1. Användaren slutför utmaningen genom att ange sin biometriska eller PIN-kod för att låsa upp privat nyckel.
1. Nonce är signerad med den privata nyckeln och skickas tillbaka till Azure AD.
1. Azure AD utför validering av offentliga/privata nyckel och returnerar en token.

## <a name="fido2-security-keys"></a>FIDO2-säkerhetsnycklar

FIDO2 säkerhetsnycklar är en unphishable standard-baserad lösenordslös autentiseringsmetod som kan komma i någon formfaktor. Fast Identity Online (FIDO) är en öppen standard för lösenordslös autentisering. FIDO tillåter användare och organisationer att utnyttja standarden för att logga in på sina resurser utan ett användarnamn eller lösenord med hjälp av en extern säkerhetsnyckel eller en plattformsnyckel inbyggd i en enhet.

För offentlig förhandsversion kan anställda använda säkerhetsnycklar för att logga in på sina Azure AD- eller hybrid-Azure AD-anslutna Windows 10-enheter och få enkel inloggning på sina moln- och lokala resurser. Användare kan också logga in på webbläsare som stöds. FIDO2 säkerhetsnycklar är ett bra alternativ för företag som är mycket säkerhetskänsliga eller har scenarier eller anställda som inte vill eller kan använda sin telefon som en andra faktor.

![Logga in på Microsoft Edge med en säkerhetsnyckel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Följande process används när en användare loggar in med en FIDO2-säkerhetsnyckel:

![Diagram som beskriver de steg som ingår för användarloggning med en FIDO2-säkerhetsnyckel](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Användaren ansluter FIDO2-säkerhetsnyckeln till sin dator.
2. Säkerhetsnyckeln FIDO2 identifieras.
3. Windows skickar en autentiseringsbegäran.
4. Azure AD skickar tillbaka en nonce.
5. Användaren slutför sin gest för att låsa upp den privata nyckeln som lagras i FIDO2-säkerhetsnyckelns säkra enklav.
6. FIDO2-säkerhetsnyckeln signerar nonce med den privata nyckeln.
7. Den primära begäran om uppdateringstoken (PRT) med signerad nonce skickas till Azure AD.
8. Azure AD verifierar den signerade nonce med fido2 offentlig nyckel.
9. Azure AD returnerar PRT för att aktivera åtkomst till lokala resurser.

Även om det finns många nycklar som är FIDO2-certifierade av FIDO Alliance, kräver Microsoft vissa valfria tillägg av FIDO2 Client-to-Authenticator Protocol (CTAP) specifikation som ska genomföras av leverantören för att säkerställa maximal säkerhet och det bästa Erfarenhet.

En säkerhetsnyckel **MÅSTE** implementera följande funktioner och tillägg från FIDO2 CTAP-protokollet för att vara Microsoft-kompatibla:

| # | Funktion / Tillägg förtroende | Varför krävs den här funktionen eller tillägget? |
| --- | --- | --- |
| 1 | Resident nyckel | Med den här funktionen kan säkerhetsnyckeln vara bärbar, där autentiseringsuppgifterna lagras på säkerhetsnyckeln. |
| 2 | Klient stift | Med den här funktionen kan du skydda dina autentiseringsuppgifter med en andra faktor och gäller för säkerhetsnycklar som inte har något användargränssnitt. |
| 3 | hmac-hemlighet | Det här tillägget säkerställer att du kan logga in på enheten när den är offline eller i flygplansläge. |
| 4 | Flera konton per RP | Den här funktionen säkerställer att du kan använda samma säkerhetsnyckel för flera tjänster som Microsoft-konto och Azure Active Directory. |

Följande leverantörer erbjuder FIDO2-säkerhetsnycklar med olika formfaktorer som är kända för att vara kompatibla med den lösenordslösa upplevelsen. Vi uppmuntrar dig att utvärdera säkerhetsegenskaperna för dessa nycklar genom att kontakta leverantören samt FIDO Alliance.

| Leverantör | Kontakt |
| --- | --- |
| Yubico (på) | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| Hid | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM (på andra sätt) | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend (AuthenTrend) | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales-gruppen) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Om du köper och planerar att använda NFC-baserade säkerhetsnycklar behöver du en NFC-läsare som stöds för säkerhetsnyckeln. NFC-läsaren är inte ett Azure-krav eller en begränsning. Kontrollera med leverantören om din NFC-baserade säkerhetsnyckel för en lista över NFC-läsare som stöds.

Om du är leverantör och vill hämta enheten i den [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)här listan över enheter som stöds kontaktar du .

## <a name="what-scenarios-work-with-the-preview"></a>Vilka scenarier fungerar med förhandsgranskningen?

- Administratörer kan aktivera lösenordslösa autentiseringsmetoder för sin klientorganisation
- Administratörer kan rikta in sig på alla användare eller välja användare/grupper i sin klientorganisation för varje metod
- Slutanvändare kan registrera och hantera dessa lösenordslösa autentiseringsmetoder i sin kontoportal
- Slutanvändare kan logga in med dessa lösenordslösa autentiseringsmetoder
   - Microsoft Authenticator App: Fungerar i scenarier där Azure AD-autentisering används, även i alla webbläsare, under installationen av Windows 10 Out Of Box (OOBE) och med integrerade mobilappar i alla operativsystem.
   - Säkerhetsnycklar: Arbeta på låsskärmen för Windows 10 och webben i webbläsare som stöds som Microsoft Edge (både äldre och nya Edge).

## <a name="choose-a-passwordless-method"></a>Välj en lösenordslös metod

Valet mellan dessa tre lösenordslösa alternativ beror på företagets säkerhet, plattform och appkrav.

Här är några faktorer som du bör tänka på när du väljer Microsofts lösenordslösa teknik:

||**Windows Hello för företag**|**Lösenordslös inloggning med Microsoft Authenticator-appen**|**FIDO2-säkerhetsnycklar**|
|:-|:-|:-|:-|
|**Förutsättning för**| Windows 10, version 1809 eller senare<br>Azure Active Directory| Microsoft Authenticator-appen<br>Telefon (iOS- och Android-enheter som kör Android 6.0 eller senare.)|Windows 10, version 1809 eller senare<br>Azure Active Directory|
|**Läge**|Plattform|Programvara|Maskinvara|
|**System och enheter**|DATOR med en inbyggd Trusted Platform Module (TPM)<br>PIN-kod och erkännande av biometri |PIN-kod och biometriigenkänning på telefon|FIDO2-säkerhetsenheter som är Microsoft-kompatibla|
|**Användarupplevelse**|Logga in med en PIN-kod eller biometrisk igenkänning (ansikts-, iris- eller fingeravtryck) med Windows-enheter.<br>Windows Hello-autentisering är knuten till enheten. Användaren behöver både enheten och en inloggningskomponent, till exempel en PIN-kod eller biometrisk faktor för att komma åt företagets resurser.|Logga in med en mobiltelefon med fingeravtrycksskanning, ansikts- eller irisigenkänning eller PIN-kod.<br>Användare loggar in på jobbet eller personliga konto från sin dator eller mobiltelefon.|Logga in med FIDO2-säkerhetsenhet (biometri, PIN och NFC)<br>Användaren kan komma åt enheten baserat på organisationskontroller och autentisera baserat på PIN-kod, biometri med hjälp av enheter som USB-säkerhetsnycklar och NFC-aktiverade smartkort, nycklar eller bärbara.|
|**Aktiverade scenarier**| Lösenordslös upplevelse med Windows-enhet.<br>Gäller för dedikerad arbetsdator med möjlighet till enkel inloggning på enhet och program.|Lösenordslös lösning var som helst med hjälp av mobiltelefon.<br>Gäller för åtkomst till arbets- eller personliga program på webben från vilken enhet som helst.|Lösenordslös upplevelse för arbetare som använder biometri, PIN-kod och NFC.<br>Gäller för delade datorer och där en mobiltelefon inte är ett hållbart alternativ (t.ex. för helpdeskpersonal, offentlig kiosk eller sjukhusteam)|

Använd följande tabell för att välja vilken metod som ska stödja dina krav och användare.

|Persona|Scenario|Miljö|Lösenordslös teknik|
|:-|:-|:-|:-|
|**Administratör**|Säker åtkomst till en enhet för hanteringsuppgifter|Tilldelad Windows 10-enhet|Säkerhetsnyckeln Windows Hello för företag och/eller FIDO2|
|**Administratör**|Hanteringsuppgifter på enheter som inte är Windows-enheter| Mobil enhet eller enhet som inte är windows|Lösenordslös inloggning med Microsoft Authenticator-appen|
|**Informationsarbetare**|Produktivitetsarbete|Tilldelad Windows 10-enhet|Säkerhetsnyckeln Windows Hello för företag och/eller FIDO2|
|**Informationsarbetare**|Produktivitetsarbete| Mobil enhet eller enhet som inte är windows|Lösenordslös inloggning med Microsoft Authenticator-appen|
|**Frontlinjen arbetstagare**|Kiosker i en fabrik, anläggning, detaljhandel eller datainmatning|Delade Windows 10-enheter|FIDO2 Säkerhetsnycklar|

## <a name="next-steps"></a>Nästa steg

[Aktivera lösenordslösa alternativ för FIDO2-säkerhet nyckel i din organisation](howto-authentication-passwordless-security-key.md)

[Aktivera telefonbaserade lösenordslösa alternativ i organisationen](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externa länkar

[FIDO-alliansen](https://fidoalliance.org/)

[FIDO2 CTAP-specifikation](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
