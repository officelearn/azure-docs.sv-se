---
title: 'Azure AD Connect: Cloud Authentication-stegvis distribution | Microsoft Docs'
description: Förklarar hur du migrerar från federerad autentisering till molnbaserad autentisering med en stegvis distribution.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847236"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Molnbaserad autentisering: mellanlagrad distribution (offentlig för hands version)

Med den här funktionen kan du migrera från federerad autentisering till molnbaserad autentisering med hjälp av en stegvis metod.

Att flytta bort från federerad autentisering har konsekvenser. Om du till exempel har något av följande:
    
-  en lokal MFA-Server 
-  använder smartkort för autentisering 
-  andra Federations funktioner

Dessa funktioner bör beaktas innan du växlar till molnbaserad autentisering.  Vi rekommenderar att du läser vår guide om hur du väljer rätt autentiseringsmetod innan du provar den här funktionen. Mer information finns i [den här tabellen](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) .

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Krav

-   Du har en Azure AD-klient med federerade domäner.

-   Du har valt att flytta till antingen Password hash Sync + sömlös SSO **(alternativ A)** eller genom strömning + sömlös SSO **(alternativ B).** Även om sömlös enkel inloggning är valfritt, rekommenderar vi att du aktiverar sömlös SSO för att få en tyst inloggnings upplevelse för användare som använder domänanslutna datorer inifrån företags nätverket.

-   Du har konfigurerat alla lämpliga klient anpassnings-och villkorliga åtkomst principer som du behöver för användare som migreras till molnbaserad autentisering.

-   Om du planerar att använda Azure Multi-Factor Authentication rekommenderar vi att du använder [konvergerad registrering för självbetjäning för återställning av lösen ord (SSPR) och Azure MFA](../authentication/concept-registration-mfa-sspr-combined.md) för att få användarna att registrera sina autentiseringsmetoder en gång.

-   Om du vill använda den här funktionen måste du vara global administratör för din klient.

-   Om du vill aktivera sömlös SSO på en särskild AD-skog måste du vara domän administratör.

## <a name="supported-scenarios"></a>Scenarier som stöds

De här scenarierna stöds för stegvis distribution:

- Den här funktionen fungerar bara för användare som har etablerad till Azure AD med hjälp av Azure AD Connect och är inte tillämpligt för enbart moln användare.

- Den här funktionen fungerar bara för användarens inloggnings trafik på webbläsare och moderna autentiserings klienter. Program eller moln tjänster som använder äldre autentisering kommer tillbaka till federerade autentiserings flöden. (Exempel: Exchange Online med modern autentisering inaktive rad eller Outlook 2010, som inte stöder modern autentisering.)

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

De här scenarierna stöds inte för stegvis distribution:

- Vissa program skickar frågeparametern "domän\_tips" till Azure AD under autentisering. Dessa flöden fortsätter och användare som är aktiverade för stegvis distribution fortsätter att använda Federation för autentisering.

<!-- -->

- Admin kan distribuera molnbaserad autentisering med hjälp av säkerhets grupper. (Moln säkerhets grupper rekommenderas för att undvika synkronisering av svars tider när du använder lokala AD-säkerhetsgrupper.)

    - Du kan använda **max 10 grupper per funktion**. för var och en av hash-synkroniseringen för lösen ord/direktautentisering/sömlös SSO.

    - Kapslade grupper **stöds inte**. Detta är även omfattningen för offentlig för hands version.

    - Dynamiska grupper **stöds inte** för mellanlagrad distribution.

    - Kontakt objekt inuti gruppen kommer att blockera det grupp formulär som läggs till.

- Den slutliga start punkt från federerade till molnbaserad autentisering måste fortfarande ske med hjälp av Azure AD Connect eller PowerShell. Den här funktionen växlar inte domäner från Federer till hanterad.

- När du först lägger till en säkerhets grupp för stegvis distribution är den begränsad till 200 användare för att undvika att UXen går ut. När gruppen har lagts till i UX-UX kan du lägga till fler användare direkt i gruppen efter behov.

## <a name="get-started-with-staged-rollout"></a>Kom igång med stegvis distribution

Om du vill testa PHS-inloggningen (Password hash Sync) med hjälp av mellanlagrad distribution slutför du nedanstående för arbete för att aktivera distribution av hash-synkronisering med lösen ord.

Mer information om de PowerShell-cmdletar som används finns i [AzureAD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>För arbete för synkronisering av lösen ords-hash

1. Aktivera hash-synkronisering av lösen ord från sidan [valfria funktioner](how-to-connect-install-custom.md#optional-features) i Azure AD Connect. 

   ![Skärm bild av sidan med valfria funktioner i Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Se till att en fullständig synkronisering av lösen ord för hash-synkronisering har körts igenom så att alla användares lösen ords-hashvärden har synkroniserats med Azure AD. Du kan använda PowerShell-diagnostik [här](tshoot-connect-password-hash-synchronization.md) för att kontrol lera statusen för hash-synkronisering av lösen ord.

   ![Skärm bild av fel söknings loggen AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Om du vill testa PTA-inloggningen (Passing through-Authentication) med hjälp av mellanlagrad distribution slutför du följande för att aktivera PTA för stegvis distribution.

## <a name="pre-work-for-pass-through-authentication"></a>För bearbetning i förväg för direktautentisering

1. Identifiera en server som kör Windows Server 2012 R2 eller senare där du vill att pass-The-Authentication agent ska köras (**Välj inte den Azure AD Connect servern**). Kontrol lera att servern är domänansluten, kan autentisera valda användare med Active Directory och kan kommunicera med Azure AD på utgående portar/URL: er (se detaljerade [krav](how-to-connect-sso-quick-start.md)).

1. [Hämta](https://aka.ms/getauthagent) & installera Microsoft Azure AD Connect Authentication agent på servern. 

1. Om du vill aktivera [hög tillgänglighet](how-to-connect-sso-quick-start.md)installerar du ytterligare autentiseringsmetoder på andra servrar.

1. Se till att du har konfigurerat [inställningarna för smart utelåsning](../authentication/howto-password-smart-lockout.md) på lämpligt sätt. Detta är att se till att användarnas lokala Active Directory-konton inte blir utelåsta av felaktiga aktörer.

Vi rekommenderar att du aktiverar sömlös SSO oberoende av inloggnings metoden (PHS eller PTA) som du väljer för stegvis distribution. Slutför det här arbetet för att aktivera sömlös enkel inloggning för mellanlagrad distribution.

## <a name="pre-work-for-seamless-sso"></a>För hands arbete för sömlös enkel inloggning

Aktivera sömlös SSO på AD-skogar med hjälp av PowerShell. Om du har mer än en AD-skog aktiverar du samma för varje skog individuellt. Sömlös SSO utlöses bara för användare som valts för mellanlagrad distribution och påverkar inte den befintliga Federations konfigurationen.

**Sammanfattning av stegen**

1. Logga in på Azure AD Connect Server först.

2. Gå till mappen% ProgramFiles%\\Microsoft Azure Active Directory Connect.

3. Importera den sömlös SSO PowerShell-modulen med hjälp av följande kommando: `Import-Module .\AzureADSSO.psd1`.

4. Kör PowerShell som administratör. Anropa `New-AzureADSSOAuthenticationContext`i PowerShell. Det här kommandot bör ge dig en dialog ruta där du kan ange din klients globala administratörs behörigheter.

5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (titta på \"domäner\" listan) där funktionen har Aktiver ATS. Som standard är den inställd på false på klient nivå.

   > **Exempel:** 
   > ![exempel på Windows PowerShell-utdata](./media/how-to-connect-staged-rollout/sr3.png)

6. Anropa `$creds = Get-Credential`. När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda AD-skogen.

7. Anropa `Enable-AzureADSSOForest -OnPremCredentials $creds`. Det här kommandot skapar AZUREADSSOACC-datornamnet från den lokala domänkontrollanten för den här speciella Active Directorys skogen som krävs för sömlös SSO.

8. Sömlös SSO kräver att URL: er är i zonen Intranät. Gå till snabb starten för [sömlös enkel inloggning](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) för att distribuera dessa URL: er med hjälp av grup principer.

9.  Du kan också hämta våra [distributions planer](https://aka.ms/SeamlessSSODPDownload) för sömlös SSO för en fullständig genom gång.

## <a name="enable-staged-rollout"></a>Aktivera mellanlagrad distribution

Använd följande steg för att distribuera en speciell funktion (genom strömnings-och lösen ords-hash Sync/sömlös SSO) till en SELECT-uppsättning användare i en grupp:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Aktivera den mellanlagrade distributionen av en speciell funktion på din klient organisation

Du kan distribuera ett av följande alternativ

-   Password hash Sync + sömlös SSO **(alternativ A)**

-   Direktautentisering + sömlös SSO **(alternativ B)**

-   Password hash Sync + direktautentisering och sömlös SSO- **-\>** ***stöds inte***

Utför följande steg:

1. Logga in på Azure AD-portalen med hjälp av nedanstående URL för att få åtkomst till för hands versions UX.

   > <https://aka.ms/stagedrolloutux>

2. Klicka på Aktivera mellanlagrad distribution för hanterad användar inloggning (förhands granskning)

   *Till exempel:* (**alternativ B**) om du vill aktivera hash-synkronisering av lösen ord och sömlös SSO, drar du en bild av hash-synkroniseringen för lösen ord och sömlösa funktioner för enkel inloggning till **"på"** som visas nedan.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Lägg till respektive grupper i funktionen för att aktivera direktautentisering och sömlös enkel inloggning. Kontrol lera att säkerhets grupperna inte har fler än 200 medlemmar inlednings vis för att undvika UX-timeout.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Medlemmarna i en grupp aktive ras automatiskt för mellanlagrad distribution. Kapslade och dynamiska grupper stöds inte för mellanlagrad distribution.

## <a name="auditing"></a>Granskning

Vi har aktiverat gransknings händelser för de olika åtgärder som vi utför för stegvis distribution.

- Gransknings händelse när du aktiverar mellanlagrad distribution för lösen ords-hash-synkronisering/direktautentisering/sömlös SSO.

  >[!NOTE]
  >Gransknings händelse som loggas när **SeamlessSSO aktive ras med** StagedRollout.

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Gransknings händelse när en grupp läggs till i Password hash Sync/direktautentisering/sömlös SSO.

  >[!NOTE]
  >Gransknings händelse som loggas när en grupp läggs till i lösen ords-hash-synkronisering för stegvis distribution

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Gransknings händelse när en användare som har lagts till i gruppen har Aktiver ATS för stegvis distribution

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validering

Så här testar du inloggning med lösen ordets hash-synkronisering eller direktautentisering (username/Password Sign-in):

1. Bläddra till <https://myapps.microsoft.com> i en privat webbläsarsession från extra nätet och ange UserPrincipalName (UPN) för det användar konto som har valts för mellanlagrad distribution.

1. Om användaren har varit mål för stegvis distribution omdirigeras inte användaren till din federerade inloggnings sida. i stället uppmanas du att logga in på inloggnings sidan för Azure AD-klient.

1. Kontrol lera att inloggningen visas i [rapporten inloggnings aktivitet i Azure AD](../reports-monitoring/concept-sign-ins.md) genom att filtrera med userPrincipalName..

Så här testar du inloggning med sömlös SSO:

1. Bläddra till <https://myapps.microsoft.com>/i en privat webbläsarsession från intranätet och ange UserPrincipalName (UPN) för det användar konto som valts för mellanlagrad distribution.

1. Om användaren har varit mål för stegvis distribution av sömlös SSO visas en "försök att logga in dig..." meddelande innan det loggas tyst.

1. Kontrol lera att inloggningen visas i [rapporten inloggnings aktivitet i Azure AD](../reports-monitoring/concept-sign-ins.md) genom att filtrera med userPrincipalName.

Så här kontrollerar du att användar inloggnings programmen fortfarande sker på Federations leverantörer:

Så här kan du spåra användar inloggningar som fortfarande händer på AD FS för utvalda, distribuerade distributions användare med hjälp av [dessa instruktioner](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Kontrol lera leverantörens dokumentation om hur du kan kontrol lera detta på leverantörer från tredje part.

## <a name="roll-back"></a>Återställ

### <a name="remove-a-user-from-staged-rollout"></a>Ta bort en användare från mellanlagrad distribution

1.  Om du tar bort användaren från gruppen inaktive ras den stegvisa distributionen för användaren.

2.  Om du vill inaktivera funktionen för mellanlagrad distribution drar du tillbaka till tillståndet **"off"** för att stänga av den mellanlagrade distributionen.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

-   **F: kan en kund använda den här funktionen i produktionen?**

-   A: Ja, den här funktionen kan användas i produktions klienten, men vi rekommenderar att du först provar den här funktionen i test klienten.

-   **F: kan den här funktionen användas för att underhålla en permanent "samtidig existens" där vissa användare använder federerad autentisering och annan molnbaserad autentisering?**

-   A: Nej, den här funktionen är utformad för att migrera från federerad till molnbaserad autentisering i steg och sedan till sist klippa över till molnbaserad autentisering. Vi rekommenderar inte ett permanent blandat tillstånd eftersom det kan leda till oväntade autentiserings flöden.

-   **F: kan vi använda PowerShell för att utföra stegvis distribution?**

-   A: Ja, se dokumentationen för att använda PowerShell för att utföra stegvis distribution [här](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Nästa steg
- [AzureAD 2,0-förhandsgranskning](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
