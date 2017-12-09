---
title: "Azure AD Connect: Användaren logga in | Microsoft Docs"
description: "Azure AD Connect användaren logga in för anpassade inställningar."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 1d580ae43925bfb2cbe0fd9461cfb7e207fa56ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect användaren inloggningsalternativ
Azure Active Directory (AD Azure) Connect kan användarna att logga in på både till molnet och lokala resurser med hjälp av samma lösenord. Den här artikeln beskriver viktiga begrepp för varje identitetsmodellen som hjälper dig att välja den identitet som du vill använda för att logga in till Azure AD.

Om du redan är bekant med Azure AD identitetsmodellen och vill veta mer om en viss metod, finns i länken:

* [Synkronisering av lösenords-hash-](#password-synchronization) med [sömlös enkel inloggning (SSO)](active-directory-aadconnect-sso.md)
* [Direkt-autentisering](active-directory-aadconnect-pass-through-authentication.md) med [sömlös enkel inloggning (SSO)](active-directory-aadconnect-sso.md)
* [Federerad enkel inloggning (med Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Välja användare logga in metod för din organisation
För de flesta organisationer som vill aktivera användare logga in på Office 365, SaaS-program och andra Azure AD-baserade resurser, rekommenderar vi alternativet standard lösenord hash-synkronisering. Vissa organisationer har dock en viss orsak till att de inte kan använda det här alternativet. De kan välja antingen en federerad inloggningsalternativ, till exempel AD FS eller direktautentisering. Du kan använda följande tabell som hjälper dig att fatta rätt beslut.

Jag behöver | PHS med enkel inloggning| Tereftalsyra med enkel inloggning| AD FS |
 --- | --- | --- | --- |
Synkronisera automatiskt nya användarkonton, kontakta och gruppkonton i lokala Active Directory till molnet.|x|x|x|
Konfigurera min klient för hybridscenarion för Office 365.|x|x|x|
Aktivera Mina användare att logga in och komma åt molntjänster med hjälp av sina lokala lösenord.|x|x|x|
Implementera enkel inloggning med företagets autentiseringsuppgifter.|x|x|x|
Se till att inga lösenord lagras i molnet.||x *|x|
Aktivera lokal multifaktorautentisering lösningar.|||x|

* Via en enkel agent.

### <a name="password-hash-synchronization"></a>Synkronisering av lösenords-hash
Med synkronisering av lösenords-hash synkroniseras hash-värden för användarlösenord från lokala Active Directory till Azure AD. När lösenord har ändrats eller Återställ lokalt, nya lösenordshashvärden synkroniseras till Azure AD omedelbart så att användarna kan alltid använda samma lösenord för molnresurser och lokala resurser. Lösenorden aldrig skickas till Azure AD eller lagras i Azure AD i klartext. Du kan använda synkronisering av lösenords-hash tillsammans med tillbakaskrivning av lösenord för att aktivera Självbetjäning för återställning av lösenord i Azure AD.

Dessutom kan du aktivera [sömlös SSO](active-directory-aadconnect-sso.md) för användare på domänanslutna datorer som finns i företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att hjälpa dem att på ett säkert sätt komma åt resurser i molnet.

![Synkronisering av lösenords-hash](./media/active-directory-aadconnect-user-signin/passwordhash.png)

Mer information finns i [synkronisering av lösenords-hash](active-directory-aadconnectsync-implement-password-synchronization.md) artikel.

### <a name="pass-through-authentication"></a>Direkt-autentisering
Med direktautentisering verifieras användarens lösenord mot den lokala Active Directory-domänkontrollanten. Lösenordet behöver inte finnas i Azure AD i någon form. Detta ger lokala principer, till exempel inloggning timme begränsningar som ska utvärderas vid autentisering till molnet services.

Direkt-autentisering använder en enkel agent på en Windows Server 2012 R2-domänansluten dator i den lokala miljön. Den här agenten lyssnar efter begäranden för verifiering av lösenord. Det kräver inte någon ingående portar vara öppna till Internet.

Du kan dessutom också aktivera enkel inloggning för användare på domänanslutna datorer som finns i företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att hjälpa dem att på ett säkert sätt komma åt resurser i molnet.
![Direkt-autentisering](./media/active-directory-aadconnect-user-signin/pta.png)

Mer information finns i:
- [Direkt-autentisering](active-directory-aadconnect-pass-through-authentication.md)
- [Enkel inloggning](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federation som använder en ny eller befintlig servergrupp med AD FS i Windows Server 2012 R2
Med federerad inloggning, kan dina användare logga in på Azure AD-baserade tjänster med sina lokala lösenord. När de inte är i företagsnätverket, behöver de även ange sina lösenord. Du kan distribuera en ny eller befintlig servergrupp med AD FS i Windows Server 2012 R2 med hjälp av alternativet federation med AD FS. Om du vill ange en befintlig servergrupp konfigurerar Azure AD Connect förtroendet mellan din grupp och Azure AD så att användarna kan logga in.

<center>![Federation med AD FS i Windows Server 2012 R2](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Distribuera federation med AD FS i Windows Server 2012 R2

Om du distribuerar en ny servergrupp, behöver du:

* En Windows Server 2012 R2-server för federationsservern.
* En Windows Server 2012 R2-server för Webbprogramproxy.
* En PFX-fil med ett SSL-certifikat för din avsedda federationstjänstens namn. Till exempel: fs.contoso.com.

Om du distribuerar en ny servergrupp eller använder en befintlig servergrupp, behöver du:

* Lokal administratörsbehörighet på dina federationsservrar.
* Lokal administratörsbehörighet på alla arbetsgruppsservrar (inte ansluten till domänen) som som du tänker distribuera rollen Web Application Proxy på.
* Den dator som du kör guiden på för att kunna ansluta till andra datorer som du vill installera AD FS eller Web Application Proxy på med hjälp av Windows Remote Management.

Mer information finns i [Konfigurera enkel inloggning med AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Logga in med hjälp av en tidigare version av AD FS eller en lösning från tredje part
Om du redan har konfigurerat moln-inloggning med hjälp av en tidigare version av AD FS (till exempel AD FS 2.0) eller en tredje parts federationsleverantör kan du hoppa över Användarkonfiguration inloggning via Azure AD Connect. Detta gör att du får den senaste synkroniseringen och andra Azure AD Connect-funktioner när du fortfarande använder din befintliga lösning för inloggning.

Mer information finns i [kompatibilitetslista för Azure AD från tredje part federation](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Logga in användaren och användarens huvudnamn
### <a name="understanding-user-principal-name"></a>Förstå användarens huvudnamn
I Active Directory är suffixet standard användarens huvudnamn (UPN) DNS-namnet på den domän där användarkontot skapades. I de flesta fall är detta det domännamn som är registrerad som företagsdomänen på Internet. Du kan dock lägga till flera UPN-suffix genom att använda Active Directory-domäner och förtroenden.

UPN-namnet för användaren har formatet username@domain. För en Active Directory-domän med namnet ”contoso.com” kan till exempel en användare med namnet John ha UPN ”john@contoso.com”. UPN-namnet för användaren baserat på RFC 822. Även om delar samma format UPN och e-post, värdet för UPN-namnet för en användare kan eller kan inte vara samma som e-postadressen för användaren.

### <a name="user-principal-name-in-azure-ad"></a>Användarens huvudnamn i Azure AD
Azure AD Connect-guiden kan du ange attribut (i en anpassad installation) ska användas från lokala som användarens huvudnamn i Azure AD eller använder attributet userPrincipalName. Detta är det värde som används för att logga in till Azure AD. Om värdet för attributet userPrincipalName inte motsvarar en verifierad domän i Azure AD, Azure AD ersätter den med ett standardvärde. onmicrosoft.com värde.

Varje katalog i Azure Active Directory innehåller en inbyggd domännamnet med formatet contoso.onmicrosoft.com, som kan du komma igång med Azure eller andra Microsoft-tjänster. Du kan förbättra och förenkla inloggning med hjälp av anpassade domäner. Mer information om anpassade domännamn i Azure AD och hur man verifiera en domän finns [lägga till ett eget domännamn i Azure Active Directory](../add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Inloggningskonfiguration för Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD-inloggning konfiguration med Azure AD Connect
Azure AD-inloggningsupplevelsen beror på om Azure AD kan matcha användarens huvudnamnssuffix för en användare som är som synkroniseras till en av de anpassa domäner som har verifierats i Azure AD-katalog. Azure AD Connect innehåller hjälp när du konfigurerar Azure AD-inloggning inställningar så att inloggning användarupplevelsen i molnet som liknar den lokala miljön.

Azure AD Connect listas UPN-suffix som är definierade i domäner och paras ihop dem med en anpassad domän i Azure AD. Sedan hjälper den dig med lämplig åtgärd som ska vidtas.
Azure AD-inloggningssida visas UPN-suffix som är definierade för lokala Active Directory och visar motsvarande status mot varje suffix. Statusvärden kan vara något av följande:

| Status | Beskrivning | Åtgärd krävs |
|:--- |:--- |:--- |
| Verifiera |Azure AD Connect att hitta en matchande verifierade domän i Azure AD. Alla användare för den här domänen kan logga in med sina lokala autentiseringsuppgifter. |Ingen åtgärd krävs. |
| Inte verifieras |Azure AD Connect finns matchande anpassade domäner i Azure AD, men verifieras inte. UPN-suffixet användare av den här domänen kommer att ändras till standardvärdet. onmicrosoft.com suffix efter synkroniseringen om domänen inte verifieras. | [Kontrollera den anpassade domänen i Azure AD.](../add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Inte lägga till |Azure AD Connect hittade inte en anpassad domän som motsvarade UPN-suffixet. UPN-suffixet användare av den här domänen kommer att ändras till standardvärdet. onmicrosoft.com-suffix om domänen inte lagts till och verifieras i Azure. | [Lägg till och verifiera en anpassad domän som motsvarar UPN-suffixet.](../add-custom-domain.md) |

Azure AD-inloggningssida visas UPN-suffix som har definierats för lokala Active Directory och motsvarande anpassade domäner i Azure AD med aktuell status för verifiering. I en anpassad installation kan du nu välja attribut för användarens huvudnamn på den **Azure AD-inloggning** sidan.

![Azure AD-inloggningssida](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Du kan klicka på Uppdatera för att hämta den senaste statusen för anpassade domäner igen från Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Att välja attribut för användarens huvudnamn i Azure AD
Attributet userPrincipalName är attributet som användare använder när de loggar in på Azure AD och Office 365. Du bör kontrollera domäner (även kallat UPN-suffix) som används i Azure AD innan användarna synkroniseras.

Vi rekommenderar starkt att du behåller standard attributet userPrincipalName. Om det här attributet är nonroutable och går inte att verifiera, är det möjligt att välja ett annat attribut (e-post, till exempel) som attributet som innehåller inloggnings-ID. Detta kallas den alternativa-ID. Attributvärdet som alternativt ID måste följa standarden RFC 822. Du kan använda ett alternativt ID med både lösenord SSO och federation enkel inloggning som lösning för inloggning.

> [!NOTE]
> Använda ett alternativt ID är inte kompatibel med alla Office 365-arbetsbelastningar. Mer information finns i [konfigurera alternativa inloggnings-ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Anpassad domän för olika tillstånd och deras effekt på Azure inloggning
Det är viktigt att förstå sambandet mellan domänen tillstånd i Azure AD-katalogen och UPN-suffix som definierats lokalt. Vi gå igenom olika möjliga Azure inloggning upplevelser när du konfigurerar synkronisering med hjälp av Azure AD Connect.

I följande information, antar vi att vi kan berörda med UPN-suffixet contoso.com, som används i den lokala katalogen som en del av UPN – till exempel user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express-inställningar och lösenord hash-synkronisering
| Status | Effekt på Azure-inloggning användarupplevelse |
|:---:|:--- |
| Inte lägga till |I det här fallet har ingen anpassad domän för contoso.com lagts till i Azure AD-katalog. Användare som har UPN lokalt med suffixet @contoso.com kommer inte att kunna använda sina lokala UPN för att logga in på Azure. De måste i stället använda ett nytt UPN som har angetts för dem av Azure AD genom att lägga till suffix för standard-Azure AD-katalog. Om du synkroniserar användare till Azure AD directory azurecontoso.onmicrosoft.com sedan lokala användare exempelvis user@contoso.com kommer att få ett UPN för user@azurecontoso.onmicrosoft.com. |
| Inte verifieras |I detta fall har vi en domänen contoso.com som har lagts till i Azure AD-katalog. Men har ännu inte kontrolleras. Om du fortsätter med att synkronisera användare utan att verifiera domänen, och användarna kommer att tilldelas nya UPN av Azure AD, precis som i fallet ”inte lägga till”. |
| Verifiera |I detta fall har vi en domänen contoso.com som redan har lagts till och verifieras i Azure AD för UPN-suffixet. Användare kommer att kunna använda sina lokala användarens huvudnamn, till exempel user@contoso.com, för att logga in på Azure, när de har synkroniserats till Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS-federation
Du kan inte skapa en federation med standardinställningarna. onmicrosoft.com-domän i Azure AD eller en anpassad overifierade domän i Azure AD. När du använder Azure AD Connect-guiden om du väljer en overifierade domän för att skapa en federation med uppmanar Azure AD Connect dig med nödvändiga poster skapas där din DNS-Server är värd för domänen. Mer information finns i [verifiera Azure AD-domänen som valts för federation](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Om du har valt alternativet för användare **Federation med AD FS**, du måste ha en anpassad domän för att fortsätta skapa en federation i Azure AD. Vår beskrivning innebär det att vi ska ha en anpassad domän contoso.com som lagts till i Azure AD-katalog.

| Status | Påverkan på användaren Azure inloggning |
|:---:|:--- |
| Inte lägga till |I det här fallet hittade Azure AD Connect en matchande anpassad domän för UPN-suffixet contoso.com i Azure AD-katalog. Du måste lägga till en anpassad domän för contoso.com om du behöver användare att logga in med hjälp av AD FS med deras lokala UPN (t.ex. user@contoso.com). |
| Inte verifieras |I det här fallet får Azure AD Connect du lämplig information om hur du kan verifiera din domän i ett senare skede. |
| Verifiera |I det här fallet kan du gå vidare med konfiguration utan några ytterligare åtgärder. |

## <a name="changing-the-user-sign-in-method"></a>Ändra metoden för användaren
Du kan ändra metoden för användare från federationstjänsten, synkronisering av lösenords-hash eller direktautentisering med hjälp av de uppgifter som är tillgängliga i Azure AD Connect efter den inledande konfigurationen av Azure AD Connect med hjälp av guiden. Kör Azure AD Connect-guiden igen och visas en lista över aktiviteter som du kan utföra. Välj **ändra användarens inloggning** från listan över aktiviteter.

![Ändra användarens inloggning](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

På nästa sida uppmanas du att ange autentiseringsuppgifter för Azure AD.

![Anslut till Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

På den **användarinloggning** markerar du önskade användare logga in.

![Anslut till Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Om du bara gör en tillfällig växel i synkronisering av lösenords-hash, väljer du den **konverterar inte användarkonton** kryssrutan. Markera inte alternativet konverterar varje användare att federerad och det kan ta flera timmar.
>
>

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
- Lär dig mer om [designbegrepp för Azure AD Connect](active-directory-aadconnect-design-concepts.md).
