---
title: Konfigurera lokal villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: En stegvis vägledning för att aktivera villkorlig åtkomst till lokala program med hjälp av Active Directory Federation Services (AD FS) i Windows Server 2012 R2.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 38d024de0fd2490d33f7c06498d3ff8d0d06e503
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056112"
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory-enhetsregistrering
När du behöver användare till arbetsplatsen på sina personliga enheter till Azure Active Directory (Azure AD) enhetsregistreringstjänsten kan sina enheter markeras som känner till din organisation. Följande är en stegvis guide för att aktivera villkorlig åtkomst till lokala program med hjälp av Active Directory Federation Services (AD FS) i Windows Server 2012 R2.

> [!NOTE]
> En Office 365-licens eller Azure AD Premium-licens krävs när du använder enheter som är registrerade i Azure Active Directory enheten villkorliga åtkomstprinciper för registrering av tjänsten. Dessa inkluderar principer som tillämpas av AD FS i lokala resurser.
> 
> Mer information om scenarier för villkorlig åtkomst för lokala resurser finns i [ansluta till arbetsplatsen från valfri enhet för SSO och sömlös second-factor-autentisering mellan företagets program](https://technet.microsoft.com/library/dn280945.aspx).

Dessa funktioner är tillgängliga för kunder som köper en Azure Active Directory Premium-licens.

## <a name="supported-devices"></a>Enheter som stöds
* Windows 7-domänanslutna enheter
* Windows 8.1 personliga och -domänanslutna enheter
* iOS 6 eller senare för Safari-webbläsaren
* Android 4.0 eller senare, Samsung GS3 eller senare telefoner, Samsung Galaxy anmärkning 2 eller senare surfplattor

## <a name="scenario-prerequisites"></a>Krav för scenario
* Prenumeration på Office 365 eller Azure Active Directory Premium
* En Azure Active Directory-klientorganisation
* Windows Server Active Directory (Windows Server 2008 eller senare)
* Uppdaterade schemat i Windows Server 2012 R2
* Licens för Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, konfigurerats för enkel inloggning till Azure AD
* Windows Server 2012 R2 Web Application Proxy 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(ladda ned Azure AD Connect)](http://www.microsoft.com/download/details.aspx?id=47594)
* Verifierad domän

## <a name="known-issues-in-this-release"></a>Kända problem i den här versionen
* Principer för enhetsbaserad villkorlig åtkomst kräver tillbakaskrivning av enhet-objekt till Active Directory från Azure Active Directory. Det kan ta upp till tre timmar innan enhetsobjekt som ska skrivas tillbaka till Active Directory.
* iOS 7-enheter uppmana alltid användaren att välja ett certifikat under autentisering av klientcertifikat.
* Vissa versioner av iOS 8 innan iOS 8.3 inte fungerar.

## <a name="scenario-assumptions"></a>Scenariot antaganden
Det här scenariot förutsätter att du har en hybridmiljö som består av en Azure AD-klient och en lokal Active Directory. Dessa klienter bör vara ansluten med Azure AD Connect, med en verifierad domän och med AD FS för enkel inloggning. Använd följande checklista för att konfigurera din miljö enligt kraven.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Checklista: Krav för scenariot för villkorlig åtkomst
Anslut Azure AD-klienten med din lokala Active Directory-instans.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurera enhetsregistreringstjänsten för Azure Active Directory
Använd den här guiden för att distribuera och konfigurera registreringstjänsten för Azure Active Directory-enheter för din organisation.

Den här guiden förutsätter att du har konfigurerat Windows Server Active Directory och prenumererar på Microsoft Azure Active Directory. Se de krav som beskrivs tidigare.

Om du vill distribuera Azure Active Directory device registration service med Azure Active Directory-klienten utför du uppgifterna i följande checklista i ordning. När en referenslänk tar dig till ett konceptuellt avsnitt kan du återgå till den här checklistan när allt är klart, så att du kan fortsätta med de återstående aktiviteterna. Vissa kan till exempel ett scenario verifiering steg som kan hjälpa dig att bekräfta om steget har slutförts.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Del 1: Aktivera Azure Active Directory-enhetsregistrering
Följ stegen i checklistan om du vill aktivera och konfigurera Azure Active Directory device registration service.

| Aktivitet | Referens | 
| --- | --- |
| Aktivera enhetsregistrering i Azure Active Directory-klienten att tillåta enheter att ansluta till arbetsplatsen. Som standard aktiveras inte Azure Multi-Factor Authentication för tjänsten. Vi rekommenderar dock att du använder Multifaktorautentisering när du registrerar en enhet. Se till att AD FS är konfigurerad för en Multi-Factor Authentication-provider innan du aktiverar Multifaktorautentisering i registreringstjänsten för Active Directory. |[Aktivera Azure Active Directory-enhetsregistrering](active-directory-device-registration-get-started.md)| 
|Enheter identifiera din Azure Active Directory-enhetsregistreringstjänsten genom att söka efter välkända DNS-poster. Konfigurera företagets DNS så att enheter kan identifiera din enhetsregistreringstjänsten för Azure Active Directory. |[Konfigurera identifiering av enhetsregistrering i Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Del 2: Distribuera och konfigurera Windows Server 2012 R2 Active Directory Federation Services och ställer in en federationsrelation med Azure AD

| Aktivitet | Referens |
| --- | --- |
| Distribuera Active Directory Domain Services med schematillägg för Windows Server 2012 R2. Du behöver inte uppgradera någon av dina domänkontrollanter till Windows Server 2012 R2. Schemauppgradering är det enda kravet. |[Uppgradera ditt Active Directory Domain Services-schema](#upgrade-your-active-directory-domain-services-schema) |
| Enheter identifiera din Azure Active Directory-enhetsregistreringstjänsten genom att söka efter välkända DNS-poster. Konfigurera företagets DNS så att enheter kan identifiera din enhetsregistreringstjänsten för Azure Active Directory. |[Förbereda dina enheter för Active Directory-stöd](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Del 3: Aktivera tillbakaskrivning av enheter i Azure AD
| Aktivitet | Referens |
| --- | --- |
| Slutför del två av ”aktivera tillbakaskrivning av enheter i Azure AD Connect”. När du har det kan gå tillbaka till den här guiden. |[Aktivera tillbakaskrivning av enheter i Azure AD Connect](./connect/active-directory-aadconnect-feature-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Valfritt] Del 4: Aktivera Multi-Factor Authentication
Vi rekommenderar starkt att du konfigurerar ett av flera alternativ för Multifaktorautentisering. Om du vill kräva Multifaktorautentisering finns i [Välj Multi-Factor Authentication-säkerhetslösning för dig](authentication/concept-mfa-whichversion.md). Den innehåller en beskrivning av varje lösning och länkar om du vill konfigurera lösning för jobbschemaläggning.

## <a name="part-5-verification"></a>Del 5: verifiering
Distributionen är nu klar och du kan prova några scenarier. Använd följande länkar för att experimentera med tjänsten och bekanta dig med dess funktioner.

| Aktivitet | Referens |
| --- | --- |
| Ansluta till vissa enheter till arbetsplatsen med hjälp av enhetsregistreringstjänsten för Azure Active Directory. Du kan ansluta till iOS-, Windows- och Android-enheter. |[Ansluta enheter till arbetsplatsen med hjälp av enhetsregistreringstjänsten för Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Visa och aktivera eller inaktivera registrerade enheter med hjälp av administratörsportalen. I den här uppgiften ska visa du några registrerade enheter med hjälp av administratörsportalen. |[Azure Active Directory device registration tjänstöversikt](active-directory-device-registration-get-started.md) |
| Kontrollera att enhetsobjekt skrivs tillbaka från Azure Active Directory till Windows Server Active Directory. |[Verifiera registrerade enheter skrivs tillbaka till Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Nu när användare kan registrera sina enheter, kan du skapa program åtkomstprinciper i AD FS som endast tillåta registrerade enheter. I det här steget skapar du en regel för åtkomst av programmet och ett anpassat meddelande för nekad åtkomst. |[Skapa en programåtkomstprincip och ett anpassat meddelande för nekad åtkomst](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrera Azure Active Directory med en lokal Active Directory

**Se:**

- [Integrerar dina lokala kataloger med Azure Active Directory](./connect/active-directory-aadconnect.md) – om du vill granska konceptuell information.

- [Anpassad installation av Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) – anvisningar för installation.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Uppgradera ditt Active Directory Domain Services-schema
> [!NOTE]
> När du har uppgraderat din Active Directory-schemat kan inte processen ångras. Vi rekommenderar att du först utför uppgraderingen i en testmiljö.
> 

1. Logga in på domänkontrollanten med ett konto som har både Företagsadministratörer och schema-administratörsbehörighet.
2. Kopiera den **[media] \support\adprep** katalog och underkataloger till en av dina Active Directory-domänkontrollanter (där **[media]** är sökvägen till Windows Server 2012 R2-installationsmediet).
4. Från en kommandotolk går du till den **adprep** katalogen och kör **adprep.exe/forestprep**. Följ instruktionerna på skärmen för att slutföra uppgraderingen schemat.

## <a name="prepare-your-active-directory-to-support-devices"></a>Förbereda din Active Directory att stödja enheter
> [!NOTE]
> Det här är en engångsåtgärd som du måste köra för att förbereda Active Directory-skog för att stödja enheter. För att slutföra den här proceduren måste du vara inloggad med administratörsbehörighet för företag och Active Directory-skogen måste ha Windows Server 2012 R2-schemat.
> 


### <a name="prepare-your-active-directory-forest"></a>Förbered Active Directory-skog
1. Öppna ett Windows PowerShell-kommandofönster på din federationsserver och skriv sedan **initiera ADDeviceRegistration**. 
2. När du tillfrågas om **ServiceAccountName**, anger du namnet på kontot du valde som tjänstkonto för AD FS. Om det är ett gMSA-konto, ange kontot i den **domain\accountname$** format. För ett domänkonto, använder du formatet **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Aktivera enhetsautentisering i AD FS
1. Öppna hanteringskonsolen för AD FS på din federationsserver och gå till **AD FS** > **autentiseringsprinciper**.
2. På den **åtgärder** väljer **redigera globala primär autentisering**.
3. Kontrollera **aktivera enhetsautentisering**, och välj sedan **OK**.
4. Om du regelbundet som standard AD FS tar du bort oanvända enheter från Active Directory. Inaktivera den här uppgiften när du använder Azure Active Directory device registration-tjänsten så att enheter kan hanteras i Azure.

### <a name="disable-unused-device-cleanup"></a>Inaktivera oanvända rensning
Öppna ett Windows PowerShell-kommandofönster på din federationsserver och skriv sedan **Set-AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Förbereda Azure AD Connect för tillbakaskrivning av enhet
Slutför del 1: förbereda Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Anslut enheter till din arbetsplats med hjälp av enhetsregistreringstjänsten för Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Ansluta till en iOS-enhet med hjälp av Azure Active Directory-enhetsregistrering
Azure Active Directory-enhetsregistrering använder luftburna registreringsprocessen för iOS-enheter. Den här processen börjar när användaren ansluter till URL: en profil för registrering med Safari. URL-format är följande:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

I det här fallet `yourdomainname` är det domännamn som du har konfigurerat med Azure Active Directory. Till exempel om ditt domännamn är contoso.com, är URL: en följande:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Det finns många olika sätt att förmedla denna URL till dina användare. Till exempel publicerar en metod som vi rekommenderar att du den här URL: en i ett anpassat program nekad meddelande i AD FS. Den här informationen ingår i nästa avsnitt [skapa en programåtkomstprincip och ett anpassat meddelande för nekad åtkomst](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Anslut en Windows 8.1-enhet med hjälp av Azure Active Directory-enhetsregistrering
1. På din Windows 8.1-enhet, väljer **datorinställningar** > **nätverk** > **arbetsplats**.
2. Ange ditt användarnamn i UPN-format. till exempel **dan@contoso.com**.
3. Välj **ansluta**.
4. När du uppmanas logga in med dina autentiseringsuppgifter. Enheten är nu ansluten.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Anslut en Windows 7-enhet med hjälp av Azure Active Directory-enhetsregistrering
Om du vill registrera Windows 7-domänanslutna enheter, måste du distribuera den [programpaket för enhetsregistrering](https://www.microsoft.com/download/details.aspx?id=53554).

Anvisningar om hur du använder paketet finns i [Windows Installer-paket för Windows 10-datorer](devices/hybrid-azuread-join-control.md#control-windows-down-level-devices).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Kontrollera att registrerade enheter skrivs tillbaka till Active Directory
Du kan visa och verifiera att din enhetsobjekt har skrivits tillbaka till din Active Directory med hjälp av LDP.exe eller ADSI-redigering. Båda är tillgängliga med verktyg för Active Directory-administratör.

Som standard är enhetsobjekt som skrivs tillbaka från Azure Active Directory placerade i samma domän som AD FS-gruppen.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Skapa en programåtkomstprincip och ett anpassat meddelande för nekad åtkomst
Studera följande scenario: du skapar ett program förlitande Partsförtroendet i AD FS och konfigurerar en regel för auktorisering av utfärdande som tillåter endast registrerade enheter. Nu tillåts endast enheter som är registrerade åtkomst till programmet. 

Om du vill göra det enklare för användarna att få åtkomst till programmet, kan du konfigurera ett anpassat nekad meddelande med instruktioner för hur du ansluter till sin enhet. Användarna har nu ett sömlöst sätt att registrera sina enheter så att de kan komma åt ett program.

Följande steg visar hur du implementerar det här scenariot.

> [!NOTE]
> Det här avsnittet förutsätter att du redan har konfigurerat ett förtroende för förlitande part för programmet i AD FS.
> 

1. Öppna verktyget AD FS-MMC och välj sedan **AD FS** > **förtroenden** > **förlitande Partsförtroenden**.
2. Leta upp det program som den här nya åtkomstregel som tillämpas. Högerklicka på programmet och välj sedan **redigera Anspråksregler**.
3. Välj den **regler för Utfärdandeauktorisering** fliken och välj sedan **Lägg till regel**.
4. Från den **anspråksregel** mall listrutan, väljer **bevilja eller neka användare baserat på ett inkommande anspråk**. Välj sedan **Nästa**.
5. I den **Regelnamn för anspråk** skriver **tillåta åtkomst från registrerade enheter**.
6. Från den **inkommande Anspråkstyp** listrutan, väljer **är registrerad användare**.
7. I den **inkommande anspråksvärde** skriver **SANT**.
8. Välj den **Tillåt åtkomst för användare med detta inkommande anspråk** alternativknappen.
9. Välj **Slutför**, och välj sedan **tillämpa**.
10. Ta bort alla regler som är mer Tillåtande än regeln som du skapade. Till exempel ta bort Standardregeln **Tillåt åtkomst till alla användare**.

Programmet har nu konfigurerats för att tillåta åtkomst endast när användaren kommer från en enhet som de har registrerats och ansluten till arbetsplatsen. För mer avancerade åtkomstprinciper i [hantera risker med ytterligare Multifaktorautentisering för känsliga program](https://technet.microsoft.com/library/dn280949.aspx).

Därefter konfigurerar du ett anpassat felmeddelande för ditt program. Felmeddelandet kan användarna om att de måste ansluta enheten till arbetsplatsen innan de kan komma åt programmet. Du kan skapa ett anpassat program åtkomst nekad-meddelande med hjälp av anpassade HTML och PowerShell.

Öppna ett PowerShell-kommandofönster på din federationsserver och skriv sedan följande kommando. Ersätt delar av kommandot med objekt som är specifika för ditt system:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Innan du kan komma åt det här programmet måste du registrera din enhet.

**Om du använder en iOS-enhet, väljer du den här länken för att ansluta din enhet**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Anslut den här iOS-enheten till arbetsplatsen.

Om du använder en Windows 8.1-enhet kan du ansluta din enhet genom att välja **datorinställningar**> **nätverk** > **arbetsplats**.

I föregående kommandon **förlitande part förtroende namnet** är namnet på ditt programs förlitande Partsförtroendet i AD FS.
Och **Dindomän.com** är det domännamn som du har konfigurerat med Azure Active Directory (till exempel contoso.com).
Se till att ta bort eventuella radbrytningar (i förekommande fall) från HTML-innehåll som du skickar till den **Set-AdfsRelyingPartyWebContent** cmdlet.

Nu när användare kommer åt programmet från en enhet som inte är registrerad med Azure Active Directory device registration service, visas ett fel.

