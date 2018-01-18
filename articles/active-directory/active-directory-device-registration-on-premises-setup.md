---
title: "Konfigurera lokal villkorlig åtkomst i Azure Active Directory | Microsoft Docs"
description: "Stegvisa instruktioner för att aktivera villkorlig åtkomst till lokala program med hjälp av Active Directory Federation Services (AD FS) i Windows Server 2012 R2."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 466cd564f08b07d443db7cb1de59c5778682ed73
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory-enhetsregistrering
När du behöver användare till arbetsplatsen på sina personliga enheter till Azure Active Directory (AD Azure) enhetsregistreringstjänsten kan sina enheter markeras som kända för din organisation. Följande är en stegvis guide för att aktivera villkorlig åtkomst till lokala program med hjälp av Active Directory Federation Services (AD FS) i Windows Server 2012 R2.

> [!NOTE]
> En licens för Office 365 eller Azure AD Premium-licens krävs när du använder enheter som är registrerade i Azure Active Directory device registrering service policyer. Dessa inkluderar principer som tillämpas av AD FS i lokala resurser.
> 
> Mer information om scenarier för villkorlig åtkomst för lokala resurser, se [ansluta till arbetsytan från valfri enhet för SSO och sömlös andra faktor-autentisering över företagsprogram](https://technet.microsoft.com/library/dn280945.aspx).

Dessa funktioner är tillgängliga för kunder som köper en Azure Active Directory Premium-licens.

## <a name="supported-devices"></a>Enheter som stöds
* Windows 7-domänanslutna enheter
* Windows 8.1 personliga och domänanslutna enheter
* iOS 6 och senare för webbläsaren Safari
* Android 4.0 eller senare, Samsung GS3 eller senare telefoner, Samsung Galaxy anmärkning 2 eller senare surfplattor

## <a name="scenario-prerequisites"></a>Krav för scenario
* Prenumeration på Office 365 eller Azure Active Directory Premium
* En Azure Active Directory-klient
* Windows Server Active Directory (Windows Server 2008 eller senare)
* Uppdaterade schemat i Windows Server 2012 R2
* Licens för Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, konfigurerad för enkel inloggning till Azure AD
* Windows Server 2012 R2 Web Application Proxy 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(hämta Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Verifierad domän

## <a name="known-issues-in-this-release"></a>Kända problem i den här versionen
* Enhetsbaserad villkorliga åtkomstprinciper kräver objektet tillbakaskrivning av enheter till Active Directory från Azure Active Directory. Det kan ta upp till tre timmar för enhetsobjekt som ska skrivas tillbaka till Active Directory.
* iOS 7-enheter uppmanas användaren att välja ett certifikat under autentisering av klientcertifikat alltid.
* Vissa versioner av iOS 8 innan iOS 8.3 inte fungerar.

## <a name="scenario-assumptions"></a>Scenariot antaganden
Det här scenariot förutsätter att du har en hybridmiljö som består av en Azure AD-klient och en lokal Active Directory. Dessa klienter ska anslutas med Azure AD Connect, med en verifierad domän och med AD FS för enkel inloggning. Använd följande checklista för att konfigurera din miljö enligt kraven.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Checklista: Krav för villkorlig åtkomst scenario
Anslut din Azure AD-klient med din lokala Active Directory-instans.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurera enhetsregistreringstjänsten för Azure Active Directory
Använd den här guiden för att distribuera och konfigurera enhetsregistreringstjänsten för Azure Active Directory för din organisation.

Den här guiden förutsätter att du har konfigurerat Windows Server Active Directory och prenumererar på Microsoft Azure Active Directory. Se förutsättningarna som beskrivs ovan.

Utför uppgifterna i den här checklistan i ordning för att distribuera enhetsregistreringstjänsten för Azure Active Directory med Azure Active Directory-klient. Om en referenslänk leder till ett konceptuellt avsnitt, gå tillbaka till den här checklistan efteråt, så att du kan fortsätta med de återstående uppgifterna. Vissa uppgifter inkluderar ett scenario validering steg som hjälper dig att bekräfta om steget har slutförts.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Del 1: Aktivera Azure Active Directory-enhetsregistrering
Följ stegen i checklistan om du vill aktivera och konfigurera enhetsregistreringstjänsten för Azure Active Directory.

| Aktivitet | Referens | 
| --- | --- |
| Aktivera registrering av enheten i Azure Active Directory-klienten till att enheter ska kunna ansluta till arbetsplatsen. Som standard aktiveras inte Azure Multi-Factor Authentication för tjänsten. Vi rekommenderar dock att du använder multi-Factor Authentication när du registrerar en enhet. Se till att AD FS är konfigurerat för en flerfunktionsautentiseringsleverantör innan du aktiverar Multifaktorautentisering i registreringstjänsten för Active Directory. |[Aktivera enhetsregistrering för Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Enheter som identifierar din enhetsregistreringstjänsten för Azure Active Directory genom att söka efter välkända DNS-poster. Konfigurera företagets DNS så att enheter kan identifiera din enhetsregistreringstjänsten för Azure Active Directory. |[Konfigurera identifiering av Azure Active Directory device registration](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Del 2: Distribuera och konfigurera Windows Server 2012 R2 Active Directory Federation Services och ställer in en federationsrelation med Azure AD

| Aktivitet | Referens |
| --- | --- |
| Distribuera Active Directory Domain Services med schematillägg för Windows Server 2012 R2. Du behöver inte uppgradera någon av dina domänkontrollanter till Windows Server 2012 R2. Schemauppgradering är det enda kravet. |[Uppgradera schemat Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| Enheter som identifierar din enhetsregistreringstjänsten för Azure Active Directory genom att söka efter välkända DNS-poster. Konfigurera företagets DNS så att enheter kan identifiera din enhetsregistreringstjänsten för Azure Active Directory. |[Förbereda dina Active Directory-stöd för enheter](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Del 3: Aktivera tillbakaskrivning av enhet i Azure AD
| Aktivitet | Referens |
| --- | --- |
| Slutföra två tillhör ”aktivera tillbakaskrivning av enheter i Azure AD Connect”. Återgå till den här guiden när du avslutar den. |[Aktivera tillbakaskrivning av enheter i Azure AD Connect](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Valfritt] Del 4: Aktivera Multi-Factor Authentication
Vi rekommenderar starkt att du konfigurerar ett av flera alternativ för Multifaktorautentisering. Om du vill kräva Multifaktorautentisering [väljer säkerhetslösning Multi-Factor Authentication för du](../multi-factor-authentication/multi-factor-authentication-get-started.md). Den innehåller en beskrivning av varje lösning och länkar som hjälper dig att konfigurera lösningen för ditt val.

## <a name="part-5-verification"></a>Del 5: verifiering
Distributionen är slutförd och du kan testa vissa scenarier. Använd följande länkar om du vill experimentera med tjänsten och bekanta dig med dess funktioner.

| Aktivitet | Referens |
| --- | --- |
| Anslut vissa enheter till din arbetsplats med hjälp av enhetsregistreringstjänsten för Azure Active Directory. Du kan ansluta till iOS-, Windows- och Android-enheter. |[Ansluta enheter till arbetsplatsen med enhetsregistreringstjänsten för Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Visa och aktivera eller inaktivera registrerade enheter med hjälp av administratörsportalen. I den här uppgiften visa vissa registrerade enheter med hjälp av administratörsportalen. |[Azure Active Directory device översikt över enhetsregistreringstjänst](active-directory-device-registration-get-started.md) |
| Kontrollera att enhetsobjekt skrivs tillbaka från Azure Active Directory till Windows Server Active Directory. |[Verifiera registrerade enheter skrivs tillbaka till Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Nu när användarna kan registrera sina enheter, kan du skapa program åtkomstprinciper i AD FS som endast tillåta registrerade enheter. I det här steget skapar du en åtkomstregel för programmet och ett anpassat meddelande om nekad åtkomst. |[Skapa en programåtkomstprincip och ett anpassat meddelande om nekad åtkomst](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrera Azure Active Directory med lokala Active Directory

**Se:**

- [Integrera dina lokala kataloger med Azure Active Directory](./connect/active-directory-aadconnect.md) - om du vill granska konceptuell information.

- [Anpassad installation av Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) - instruktioner för installationen.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Uppgradera schemat Active Directory Domain Services
> [!NOTE]
> När du har uppgraderat din Active Directory-schemat kan processen inte ångras. Vi rekommenderar att du först uppgraderar i en testmiljö.
> 

1. Logga in på domänkontrollanten med ett konto som har både Företagsadministratörer och schema-administratörsbehörighet.
2. Kopiera den **[media] \support\adprep** katalog och underkataloger till en Active Directory-domänkontrollanter (där **[media]** är sökvägen till Windows Server 2012 R2-installationsmediet).
4. Gå till från en kommandotolk i **adprep** katalog och kör **adprep.exe/forestprep**. Följ instruktionerna på skärmen för att slutföra uppgraderingen schemat.

## <a name="prepare-your-active-directory-to-support-devices"></a>Förbered din Active Directory att stödja enheter
> [!NOTE]
> Det här är en engångsåtgärd som ska köras för att förbereda Active Directory-skogen för att stödja enheter. Om du vill slutföra den här proceduren måste du vara inloggad med administratörsbehörighet för företag och Active Directory-skogen måste ha Windows Server 2012 R2-schemat.
> 


### <a name="prepare-your-active-directory-forest"></a>Förbered Active Directory-skog
1. På din federationsserver, öppna ett Windows PowerShell-kommandofönster och skriv **initiera ADDeviceRegistration**. 
2. När du tillfrågas om **ServiceAccountName**, ange namnet på kontot du valde som tjänstkontot för AD FS. Om det är ett konto för gMSA kan du ange kontot i den **domain\accountname$** format. För ett domänkonto, använder du formatet **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Aktivera enhetsautentisering i AD FS
1. Öppna hanteringskonsolen för AD FS på din federationsserver och gå till **AD FS** > **autentiseringsprinciper**.
2. På den **åtgärder** väljer **redigera Global primär autentisering**.
3. Kontrollera **aktivera enhetsautentisering**, och välj sedan **OK**.
4. Om du regelbundet som standard AD FS tar du bort oanvända enheter från Active Directory. Inaktivera den här uppgiften när du använder enhetsregistreringstjänsten för Azure Active Directory så att enheter kan hanteras i Azure.

### <a name="disable-unused-device-cleanup"></a>Inaktivera oanvända enheten rensning
På din federationsserver, öppna ett Windows PowerShell-kommandofönster och skriv **Set-AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Förbereda Azure AD Connect för tillbakaskrivning av enhet.
Slutföra del 1: förbereda Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Ansluta enheter till din arbetsplats med hjälp av enhetsregistreringstjänsten för Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Ansluta en iOS-enhet med hjälp av Azure Active Directory-enhetsregistrering
Azure Active Directory-enhetsregistrering använder luftburna registreringen för iOS-enheter. Den här processen startar när användaren ansluter till profilen registrerings-URL med Safari. URL-formatet är:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

I det här fallet `yourdomainname` det domännamn som du har konfigurerat med Azure Active Directory. Till exempel om ditt domännamn är contoso.com, är URL: en följande:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Det finns många olika sätt att förmedla denna URL till dina användare. En metod som rekommenderas publicera URL: en i ett anpassat program meddelande om nekad åtkomst i AD FS. Den här informationen ingår i nästa avsnitt [skapa en programåtkomstprincip och ett anpassat meddelande om nekad åtkomst](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Ansluta till en Windows 8.1-enhet med hjälp av Azure Active Directory-enhetsregistrering
1. På din Windows 8.1-enhet väljer **datorinställningar** > **nätverk** > **arbetsplats**.
2. Ange ditt användarnamn i UPN-format. till exempel  **dan@contoso.com** .
3. Välj **ansluta**.
4. När du uppmanas logga in med dina autentiseringsuppgifter. Enheten är nu ansluten.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Ansluta till en Windows 7-enhet med hjälp av Azure Active Directory-enhetsregistrering
För att registrera Windows 7-domänanslutna enheter måste du distribuera den [programpaket för enhetsregistrering](https://www.microsoft.com/download/details.aspx?id=53554).

Anvisningar om hur du använder paketet finns [Windows Installer-paket för Windows 10-datorer](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Kontrollera att registrerade enheter skrivs tillbaka till Active Directory
Du kan visa och verifiera att din enhetsobjekt har skrivits tillbaka till din Active Directory med hjälp av LDP.exe eller ADSI-redigering. Båda är tillgängliga med verktyg för Active Directory-administratör.

Enhetsobjekt som skrivs från Azure Active Directory placeras i samma domän som AD FS-gruppen.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Skapa en programåtkomstprincip och ett anpassat meddelande om nekad åtkomst
Studera följande scenario: du skapar ett program förlitande part i AD FS och konfigurera en regel för auktorisering av utfärdande som tillåter endast registrerade enheter. Nu tillåts endast enheter som registreras åtkomst till programmet. 

Om du vill göra det enklare för användarna att få åtkomst till programmet, kan du konfigurera ett anpassat meddelande om nekad åtkomst som innehåller instruktioner för hur du ansluter till sina enheter. Användarna har nu en sömlös sättet att registrera sina enheter så att de kan komma åt ett program.

Följande steg visar hur du implementerar det här scenariot.

> [!NOTE]
> Det här avsnittet förutsätter att du redan har konfigurerat ett förtroende för förlitande part för programmet i AD FS.
> 

1. Öppna verktyget AD FS-MMC och välj sedan **AD FS** > **förtroenden** > **förtroende för förlitande part**.
2. Leta reda på programmet som den här nya regeln gäller. Högerklicka på programmet och välj sedan **redigera Anspråksregler**.
3. Välj den **auktoriseringsregler** och välj sedan **Lägg till regel**.
4. Från den **anspråksregel** mall listrutan, Välj **bevilja eller neka användare baserat på ett inkommande anspråk**. Välj sedan **nästa**.
5. I den **Regelnamn för anspråk** anger **tillåta åtkomst från registrerade enheter**.
6. Från den **inkommande Anspråkstyp** listrutan, Välj **är registrerad användare**.
7. I den **inkommande anspråksvärde** anger **SANT**.
8. Välj den **Tillåt åtkomst för användare med detta inkommande anspråk** knappen.
9. Välj **Slutför**, och välj sedan **tillämpa**.
10. Ta bort alla regler som är mer Tillåtande än den regel som du skapade. Till exempel ta bort Standardregeln **Tillåt åtkomst till alla användare**.

Tillämpningsprogrammet är nu konfigurerad för att ge åtkomst endast när användaren kommer från en enhet som de har registrerats och ansluten till arbetsplatsen. För mer avancerade åtkomstprinciper finns [hantera risker med ytterligare Multifaktorautentisering för känsliga program](https://technet.microsoft.com/library/dn280949.aspx).

Därefter konfigurerar du ett anpassat felmeddelande för ditt program. Felmeddelandet kan användarna vet att de måste ansluta enheten till arbetsplatsen innan de kan komma åt programmet. Du kan skapa meddelande om nekad åtkomst ett anpassat program med hjälp av anpassade HTML- och PowerShell.

Öppna ett PowerShell-kommandofönster och Skriv följande kommando på din federationsserver. Ersätt delar av kommandot med objekt som är specifika för ditt system:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Du måste registrera enheten innan du kan komma åt det här programmet.

**Om du använder en iOS-enhet väljer du den här länken ansluta enheten till**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Anslut iOS-enheten till arbetsplatsen.

Om du använder en Windows 8.1-enhet kan du ansluta enheten genom att välja **datorinställningar**> **nätverk** > **arbetsplats**.

I föregående kommandona **förlitande part förtroende namnet** är namnet på ditt program för förlitande part objekt i AD FS.
Och **yourdomain.com** det domännamn som du har konfigurerat med Azure Active Directory (t.ex, contoso.com).
Se till att ta bort några radbrytningar (eventuella) från HTML-innehåll som överförs till den **Set AdfsRelyingPartyWebContent** cmdlet.

När användare har åtkomst till programmet från en enhet som inte är registrerad med Azure Active Directory device registration service, visas nu ett fel.

