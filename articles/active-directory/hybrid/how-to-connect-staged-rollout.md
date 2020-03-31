---
title: 'Azure AD Connect: Molnautentisering via stegvis distribution | Microsoft-dokument'
description: I den här artikeln beskrivs hur du migrerar från federerad autentisering till molnautentisering med hjälp av en stegvis distribution.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915232"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Migrera till molnautentisering med hjälp av stegvis distribution (förhandsversion)

Genom att använda en stegvis distributionsmetod kan du migrera från federerad autentisering till molnautentisering. I den här artikeln beskrivs hur du gör bytet. Innan du påbörjar den stegvisa distributionen bör du dock överväga konsekvenserna om ett eller flera av följande villkor är uppfyllda:
    
-  Du använder för närvarande en lokal multifaktorautentiseringsserver. 
-  Du använder smartkort för autentisering. 
-  Din nuvarande server erbjuder vissa federationsfunktioner.

Innan du provar den här funktionen föreslår vi att du läser igenom vår guide om hur du väljer rätt autentiseringsmetod. Mer information finns i tabellen "Jämföra metoder" i [Välj rätt autentiseringsmetod för din Azure Active Directory hybrididentitetslösning](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

En översikt över funktionen finns i den här "Azure Active Directory: What is staged-distributionen?" Video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Krav

-   Du har en Azure Active Directory -klient (Azure AD) med federerade domäner.

-   Du har bestämt dig för att flytta till något av två alternativ:
    - **Alternativ Ett** - *lösenord hash-synkronisering (synkronisering)* + sömlös enkel inloggning *(SSO)*
    - **Alternativ B** - *genomströmningsautentisering* + *sömlös SSO*
    
    Även om *sömlös SSO* är valfritt rekommenderar vi att du gör det möjligt att uppnå en tyst inloggningsupplevelse för användare som kör domänanslutna datorer inifrån ett företagsnätverk.

-   Du har konfigurerat alla lämpliga principer för klient-varumärke och villkorlig åtkomst som du behöver för användare som migreras till molnautentisering.

-   Om du planerar att använda Azure Multi-Factor Authentication rekommenderar vi att du använder [konvergerad registrering för självbetjäningslösenordsåterställning (SSPR) och Multifaktorautentisering](../authentication/concept-registration-mfa-sspr-combined.md) för att låta användarna registrera sina autentiseringsmetoder en gång.

-   Om du vill använda den stegvisa distributionsfunktionen måste du vara global administratör för din klientorganisation.

-   Om du vill aktivera *sömlös SSO* i en viss Active Directory-skog måste du vara domänadministratör.

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds för stegvis distribution. Funktionen fungerar bara för:

- Användare som etableras till Azure AD med hjälp av Azure AD Connect. Det gäller inte molnanvändare.

- Användaråtkomsttrafik i webbläsare och moderna autentiseringsklienter. *modern authentication* Program eller molntjänster som använder äldre autentisering återgår till federerade autentiseringsflöden. Ett exempel kan vara Exchange online med modern autentisering inaktiverad, eller Outlook 2010, som inte stöder modern autentisering.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds inte för stegvis distribution:

- Vissa program skickar frågeparametern "domain_hint" till Azure AD under autentiseringen. Dessa flöden fortsätter och användare som är aktiverade för stegvis distribution fortsätter att använda federation för autentisering.

<!-- -->

- Administratörer kan distribuera molnautentisering med hjälp av säkerhetsgrupper. Om du vill undvika synkroniseringsfördröjning när du använder lokala Active Directory-säkerhetsgrupper rekommenderar vi att du använder molnsäkerhetsgrupper. Följande villkor gäller:

    - Du kan använda högst 10 grupper per funktion. Det vill säga, du kan använda 10 grupper vardera för *lösenord hash sync,* *pass-through autentisering*och *sömlös SSO*.
    - Kapslade grupper *stöds inte*. Det här omfånget gäller även för offentlig förhandsversion.
    - Dynamiska grupper *stöds inte* för stegvis distribution.
    - Kontaktobjekt i gruppen blockerar gruppen från att läggas till.

- Du måste fortfarande göra den slutliga cutover från federerade till molnautentisering med hjälp av Azure AD Connect eller PowerShell. Stegvis distribution växlar inte domäner från federerade till hanterade.

- När du först lägger till en säkerhetsgrupp för stegvis distribution är du begränsad till 200 användare för att undvika en ux-timeout. När du har lagt till gruppen kan du lägga till fler användare direkt i den efter behov.

## <a name="get-started-with-staged-rollout"></a>Komma igång med stegvis distribution

Om du vill testa inloggningen för synkronisering av *lösenord hash-synkronisering* med hjälp av stegvis distribution följer du instruktionerna för förarbete i nästa avsnitt.

Information om vilka PowerShell-cmdlets som ska användas finns i [förhandsversionen av Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Förarbete för synkronisering av lösenord hash

1. Aktivera  *synkronisering av lösenordsh hash*från sidan [Valfria funktioner](how-to-connect-install-custom.md#optional-features) i Azure AD Connect. 

   ![Skärmbild av sidan "Valfria funktioner" i Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Kontrollera att en fullständig *synkroniseringscykel för lösenord hash* har körts så att alla användares lösenordsh hashar har synkroniserats med Azure AD. Om du vill kontrollera status för synkronisering av *lösenord hash*kan du använda PowerShell-diagnostiken i [Felsöka lösenordshã¤nda med Azure AD Connect-synkronisering](tshoot-connect-password-hash-synchronization.md).

   ![Skärmbild av felsökningsloggen för AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Om du vill testa *genomströmningsautentiseringsinaktivering* med hjälp av stegvis distribution aktiverar du den genom att följa förarbetsinstruktionerna i nästa avsnitt.

## <a name="pre-work-for-pass-through-authentication"></a>Förarbete för direktautentisering

1. Identifiera en server som kör Windows Server 2012 R2 eller senare där du vill att *direktautentiseringsagenten* ska köras. 

   *Välj inte* Azure AD Connect-servern.Kontrollera att servern är domänansluten, kan autentisera valda användare med Active Directory och kan kommunicera med Azure AD på utgående portar och webbadresser. Mer information finns i avsnittet "Steg 1: Kontrollera förutsättningarna" i [Snabbstart: Azure AD sömlös enkel inloggning](how-to-connect-sso-quick-start.md).

1. [Hämta Azure AD Connect-autentiseringsagenten](https://aka.ms/getauthagent)och installera den på servern. 

1. Om du vill aktivera [hög tillgänglighet](how-to-connect-sso-quick-start.md)installerar du ytterligare autentiseringsagenter på andra servrar.

1. Kontrollera att du har konfigurerat dina [inställningar för Smart Lockout](../authentication/howto-password-smart-lockout.md) på rätt sätt. Detta säkerställer att användarnas lokala Active Directory-konton inte blir utelåst av felaktiga aktörer.

Vi rekommenderar att du aktiverar *sömlös SSO* oavsett inloggningsmetod *(lösenordshim sync* eller *direktautentisering)* som du väljer för stegvis distribution. Om du vill aktivera *sömlös SSO*följer du instruktionerna för förarbeten i nästa avsnitt.

## <a name="pre-work-for-seamless-sso"></a>Förarbete för sömlös SSO

Aktivera *sömlös SSO* i Active Directory-skogarna med powershell. Om du har mer än en Active Directory-skog aktiverar du den för varje skog individuellt.  *Sömlös SSO* utlöses endast för användare som har valts för stegvis distribution. Det påverkar inte din befintliga federationskonfiguration.

Aktivera *sömlös SSO* genom att göra följande:

1. Logga in på Azure AD Connect Server.

2. Gå till mappen *\\%programfiler% Microsoft Azure Active Directory Connect.* 

3. Importera den *sömlösa SSO* PowerShell-modulen genom att köra följande kommando: 

   `Import-Module .\AzureADSSO.psd1`

4. Kör PowerShell som administratör. Ring `New-AzureADSSOAuthenticationContext`i PowerShell. Det här kommandot öppnar en ruta där du kan ange klientens globala administratörsautentiseringsuppgifter.

5. Ring. `Get-AzureADSSOStatus | ConvertFrom-Json` Det här kommandot visar en lista över Active Directory-skogar (se listan "Domäner" som den här funktionen har aktiverats på. Som standard är den inställd på false på klientnivå.

   ![Exempel på Windows PowerShell-utdata](./media/how-to-connect-staged-rollout/sr3.png)

6. Ring. `$creds = Get-Credential` Ange domänadministratörsuppgifterna för den avsedda Active Directory-skogen vid prompten.

7. Ring. `Enable-AzureADSSOForest -OnPremCredentials $creds` Det här kommandot skapar AZUREADSSOACC-datorkontot från den lokala domänkontrollanten för Active Directory-skogen som krävs för *sömlös SSO*.

8. *Sömlös SSO* kräver webbadresser för att vara i intranätzonen. Information om hur du distribuerar dessa url:er med hjälp av gruppprinciper finns i [Snabbstart: Azure AD sömlös enkel inloggning](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. För en fullständig genomgång kan du också ladda ner våra [distributionsplaner](https://aka.ms/SeamlessSSODPDownload) för *sömlös SSO*.

## <a name="enable-staged-rollout"></a>Aktivera stegvis utrullning

Om du vill distribuera en viss funktion *(direktautentisering,* *lösenordshã¤nsch-synkronisering*eller *sömlös SSO*) till en utvald uppsättning användare i en grupp följer du instruktionerna i nästa avsnitt.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Aktivera en stegvis distribution av en viss funktion på din klientorganisation

Du kan distribuera något av följande alternativ:

- **Alternativ Ett** - *lösenord hash synkronisera* + *sömlös SSO*
- **Alternativ B** - *genomströmningsautentisering* + *sömlös SSO*
- **Det går inte att** - *överföra lösenordshÃringskopa* + *genomsÃjdningsãriga SSO* + *seamless SSO*

Gör följande:

1. Logga in på [Azure AD-portalen](https://aka.ms/stagedrolloutux)om du vill komma åt förhandsversionen av användarupplevelsen .

2. Välj länken **Aktivera stegvis distribution för hanterad användarloggning (förhandsgranska).**

   Om du till exempel vill aktivera *alternativ A*drar du kontrollerna **Lösenord hash-synkronisering** och **sömlös enkel inloggning** till **På**, som visas i följande bilder.

   ![Sidan Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Sidan Aktivera stegvisa distributionsfunktioner (förhandsgranskning)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Lägg till grupperna i funktionen för att aktivera *direktautentisering* och *sömlös SSO*. För att undvika en ux-time-out, se till att säkerhetsgrupperna inte innehåller mer än 200 medlemmar från början.

   ![Sidan "Hantera grupper för synkronisering av lösenord hash (förhandsgranskning)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Medlemmarna i en grupp aktiveras automatiskt för stegvis distribution. Kapslade och dynamiska grupper stöds inte för stegvis distribution.

## <a name="auditing"></a>Granskning

Vi har aktiverat granskningshändelser för de olika åtgärder vi utför för stegvis distribution:

- Granskningshändelse när du aktiverar en stegvis distribution för *synkronisering av lösenord hash,* *direktautentisering*eller *sömlös SSO*.

  >[!NOTE]
  >En granskningshändelse loggas när *sömlös SSO* aktiveras med hjälp av stegvis distribution.

  ![Fönstret "Skapa distributionsprincip för funktionen" - fliken Aktivitet](./media/how-to-connect-staged-rollout/sr7.png)

  ![Fönstret "Skapa distributionsprincip för funktionen" - Fliken Ändrade egenskaper](./media/how-to-connect-staged-rollout/sr8.png)

- Granskningshändelse när en grupp läggs till i *synkronisering av lösenord hash,* *direktautentisering*eller *sömlös SSO*.

  >[!NOTE]
  >En granskningshändelse loggas när en grupp läggs till *i lösenordshã¤nsch-synkronisering* för stegvis distribution.

  ![Fönstret "Lägg till en grupp i funktionsutrullning" - fliken Aktivitet](./media/how-to-connect-staged-rollout/sr9.png)

  ![Fönstret "Lägg till en grupp i funktionsutrullning" - fliken Ändrade egenskaper](./media/how-to-connect-staged-rollout/sr10.png)

- Granskningshändelse när en användare som har lagts till i gruppen är aktiverad för stegvis distribution.

  ![Fönstret "Lägg till användare i funktionsutrullning" - fliken Aktivitet](media/how-to-connect-staged-rollout/sr11.png)

  ![Fönstret "Lägg till användare i funktionsutrullning" - Fliken Mål](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validering

Så här testar du inloggningen med *synkronisering av lösenordshÃ¶r eller* *direktautentisering* (inloggning med användarnamn och lösenord) gör du följande:

1. På extranätet går du till [sidan Appar](https://myapps.microsoft.com) i en privat webbläsarsession och anger sedan UPN (UserPrincipalName) för användarkontot som har valts för stegvis distribution.

   Användare som har blivit inriktade på stegvis distribution omdirigeras inte till din federerade inloggningssida. I stället uppmanas de att logga in på inloggningssidan för Azure AD-klienten.

1. Kontrollera att inloggningen har visas i [aktivitetsrapporten för Azure AD-inloggning](../reports-monitoring/concept-sign-ins.md) genom att filtrera med UserPrincipalName.

Så här testar du inloggning med *sömlös SSO:*

1. På intranätet går du till [sidan Appar](https://myapps.microsoft.com) i en privat webbläsarsession och anger sedan UPN (UserPrincipalName) för användarkontot som har valts för stegvis distribution.

   Användare som har blivit föremål för iscensatt utrullning av *sömlös SSO* presenteras med en "Försöker logga in dig ..." meddelandet innan de är tyst inloggade.

1. Kontrollera att inloggningen har visas i [aktivitetsrapporten för Azure AD-inloggning](../reports-monitoring/concept-sign-ins.md) genom att filtrera med UserPrincipalName.

   Om du vill spåra användarinloggningar som fortfarande förekommer i AD FS (Active Directory Federation Services) för valda stegvisa distributionsanvändare följer du instruktionerna vid [AD FS-felsökning: Händelser och loggning](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Kontrollera leverantörsdokumentation om hur du kontrollerar detta på tredjepartsfederationsleverantörer.

## <a name="remove-a-user-from-staged-rollout"></a>Ta bort en användare från stegvis distribution

Om du tar bort en användare från gruppen inaktiveras den stegvisa distributionen för den användaren. Om du vill inaktivera den stegvisa distributionsfunktionen drar du tillbaka kontrollen till **Av**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Kan jag använda den här funktionen i produktionen?**

S: Ja, du kan använda den här funktionen i din produktionsklient, men vi rekommenderar att du först provar den i testklienten.

**F: Kan den här funktionen användas för att upprätthålla en permanent "samexistens", där vissa användare använder federerad autentisering och andra använder molnautentisering?**

S: Nej, den här funktionen är utformad för att migrera från federerad till molnautentisering i etapper och sedan så småningom att gå över till molnautentisering. Vi rekommenderar inte att du använder ett permanent blandat tillstånd, eftersom den här metoden kan leda till oväntade autentiseringsflöden.

**F: Kan jag använda PowerShell för att utföra stegvis distribution?**

S: Ja. Mer information om hur du använder PowerShell för att utföra stegvis distribution finns i [Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Nästa steg
- [Förhandsversion av Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
