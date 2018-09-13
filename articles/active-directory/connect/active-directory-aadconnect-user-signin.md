---
title: 'Azure AD Connect: Användarinloggning | Microsoft Docs'
description: Azure AD Connect användarinloggning för anpassade inställningar.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1bf20ebec5792fc01c62966a0454c37c3c950182
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35964009"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect-inloggning användaralternativ
Azure Active Directory (Azure AD) Connect kan användarna att logga in på både i molnet och lokala resurser med hjälp av samma lösenord. Den här artikeln beskriver viktiga begrepp för varje identitet modell för att välja den identitet som du vill använda för att logga in till Azure AD.

Om du redan är bekant med Azure AD identitetsmodellen och vill ha mer information om en viss metod, se länken:

* [Synkronisering av lösenordshash](#password-hash-synchronization) med [sömlös enkel inloggning (SSO)](active-directory-aadconnect-sso.md)
* [Direktautentisering](active-directory-aadconnect-pass-through-authentication.md) med [sömlös enkel inloggning (SSO)](active-directory-aadconnect-sso.md)
* [Federerad enkel inloggning (med Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federation med PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Det är viktigt att komma ihåg att genom att konfigurera federation för Azure AD kan du upprätta förtroende mellan Azure AD-klienten och din federerade domäner. Med det här förtroendet federerad domän har användare åtkomst till Azure AD-molnresurser i klienten.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Välja användare logga in metod för din organisation
Det första beslutet att implementera Azure AD Connect är att välja vilken autentiseringsmetod som dina användare ska använda för att logga in. Det är viktigt att se till att välja rätt metod som uppfyller organisationens krav på säkerhet och avancerade. Autentisering är viktigt, eftersom det kommer att validera användaridentiteter att få åtkomst till appar och data i molnet. Om du vill välja rätt autentiseringsmetod som du behöver tänka på tid, befintlig infrastruktur, komplexiteten och kostnaden för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan ändras över tid.

Azure AD stöder följande autentiseringsmetoder: 

* **Molnbaserad autentisering** – när du väljer den här autentiseringsmetoden Azure AD hanterar autentiseringsprocessen för användarens inloggning. Med autentisering i molnet kan du välja mellan två alternativ: 
   * **Synkronisering av lösenordshash (PHS)** -Lösenordshashsynkronisering kan du använda samma användarnamn och lösenord som de använder lokalt utan att behöva distribuera ytterligare infrastruktur förutom Azure AD Connect. 
   * **Direktautentisering (PTA)** – det här alternativet liknar lösenordets hash-synkronisering, men ger en enkel lösenordsverifieringen som använder en lokal programvaruagenter för organisationer med starka policyer för säkerhet och efterlevnad.
* **Federerad autentisering** – när du väljer den här autentiseringsmetoden Azure AD kommer lämnar in en separat betrodda autentiseringssystem autentiseringsprocessen som AD FS eller en tredje parts federation-system för att validera att användaren är logga in. 

För de flesta organisationer som vill aktivera användare logga in på Office 365, SaaS-program och andra Azure AD-baserade resurser, rekommenderar vi alternativet standard lösenord hash-synkronisering.
 
Detaljerad information om hur du väljer en autentiseringsmetod finns i [välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning](../../security/azure-ad-choose-authn.md)

### <a name="password-hash-synchronization"></a>Synkronisering av lösenordshash
Med synkronisering av lösenordshash synkroniseras hash-värden för användarlösenord från lokala Active Directory till Azure AD. När lösenord har ändrats eller återställa den lokala, nya hashvärden för lösenord synkroniseras till Azure AD direkt så att användarna kan alltid använda samma lösenord för molnresurser och lokala resurser. Lösenorden aldrig skickas till Azure AD eller lagras i Azure AD i klartext. Du kan använda synkronisering av lösenordshash tillsammans med tillbakaskrivning av lösenord för att aktivera Självbetjäning för återställning av lösenord i Azure AD.

Dessutom kan du aktivera [sömlös SSO](active-directory-aadconnect-sso.md) för användare på domänanslutna datorer som är ansluten till företagets nätverk. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att hjälpa dem på ett säkert sätt komma åt resurser i molnet.

![Synkronisering av lösenordshash](./media/active-directory-aadconnect-user-signin/passwordhash.png)

Mer information finns i den [lösenordshashsynkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md) artikeln.

### <a name="pass-through-authentication"></a>Direktautentisering
Med direktautentisering, har användarens lösenord verifierats mot den lokala Active Directory-domänkontrollant. Lösenordet behöver inte finnas i Azure AD i någon form. Det möjliggör lokala principer, till exempel begränsningar för inloggning timme, som ska utvärderas under autentiseringen till cloud services.

Direktautentisering använder en enkel agent på en Windows Server 2012 R2 domänansluten dator i den lokala miljön. Den här agenten lyssnar efter förfrågningar för verifiering av lösenord. Det kräver inte några ingående portar är öppna till Internet.

Du kan dessutom också aktivera enkel inloggning för användare på domänanslutna datorer som är ansluten till företagets nätverk. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att hjälpa dem på ett säkert sätt komma åt resurser i molnet.
![Direktautentisering](./media/active-directory-aadconnect-user-signin/pta.png)

Mer information finns i:
- [Direktautentisering](active-directory-aadconnect-pass-through-authentication.md)
- [Enkel inloggning](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federation med en ny eller befintlig servergrupp med AD FS i Windows Server 2012 R2
Med federerad inloggning, kan användarna logga in på Azure AD-baserade tjänster med sina lokala lösenord. Även om de är i företagets nätverk, behöver inte de även ange sina lösenord. Du kan distribuera en ny eller befintlig servergrupp med AD FS i Windows Server 2012 R2 med hjälp av alternativet federation med AD FS. Om du vill ange en befintlig servergrupp konfigurerar Azure AD Connect förtroendet mellan din grupp och Azure AD så att användarna kan logga in.

<center>![Federation med AD FS i Windows Server 2012 R2](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Distribuera federation med AD FS i Windows Server 2012 R2

Om du distribuerar en ny servergrupp, måste du:

* En Windows Server 2012 R2-server för federationsservern.
* En Windows Server 2012 R2-server för Web Application Proxy.
* En PFX-fil med ett SSL-certifikat för din avsedda federationstjänstens namn. Till exempel: fs.contoso.com.

Om du distribuerar en ny servergrupp eller använder en befintlig servergrupp, måste du:

* Lokal administratörsbehörighet på dina federationsservrar.
* Behörighet som lokal administratör på arbetsgruppsservrar (inte anslutna till en domän) som du tänker distribuera rolltjänsten Web Application Proxy på.
* Den dator som du kör guiden på för att kunna ansluta till andra datorer som du vill installera AD FS eller Web Application Proxy på med hjälp av Windows Remote Management.

Mer information finns i [Konfigurera enkel inloggning med AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federation med PingFederate
Med federerad inloggning, kan användarna logga in på Azure AD-baserade tjänster med sina lokala lösenord. Även om de är i företagets nätverk, behöver inte de även ange sina lösenord.

Mer information om hur du konfigurerar PingFederate för användning med Azure Active Directory finns i [PingFederate-integrering med Azure Active Directory och Office 365](https://www.pingidentity.com/AzureADConnect)

Information om hur du konfigurerar Azure AD Connect med PingFederate finns i [anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Logga in med en tidigare version av AD FS eller en lösning från tredje part
Om du redan har konfigurerat molninloggning med hjälp av en tidigare version av AD FS (till exempel AD FS 2.0) eller en tredje parts federationsleverantör kan du hoppa över Användarkonfiguration inloggning via Azure AD Connect. Detta aktiverar du får den senaste synkroniseringen och andra funktioner i Azure AD Connect när du fortfarande använder din befintliga lösning för att logga in.

Mer information finns i den [kompatibilitetslistan för Azure AD från tredje part federation](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Logga in användaren och användarens huvudnamn
### <a name="understanding-user-principal-name"></a>Förstå användarens huvudnamn
I Active Directory är suffixet standard användarens huvudnamn (UPN) DNS-namnet på den domän där användarkontot har skapats. I de flesta fall är detta det domännamn som är registrerad som företagsdomänen på Internet. Du kan dock lägga till flera UPN-suffix genom att använda Active Directory-domäner och förtroenden.

UPN för användaren har formatet username@domain. För en Active Directory-domän med namnet ”contoso.com” kan till exempel en användare med namnet John ha UPN-namnet ”john@contoso.com”. UPN för användaren baserat på RFC 822. Även om delar samma format UPN och e-post, värdet för UPN för en användare kanske eller kanske inte är samma som e-postadress för användaren.

### <a name="user-principal-name-in-azure-ad"></a>Användarens huvudnamn i Azure AD
Azure AD Connect-guiden använder attributet userPrincipalName eller kan du ange attributet (i en anpassad installation) för att användas från en lokal plats som användarens huvudnamn i Azure AD. Detta är det värde som används för att logga in till Azure AD. Om värdet för attributet userPrincipalName inte motsvarar en verifierad domän i Azure AD, Azure AD ersätter den med en standard. onmicrosoft.com-värde.

Alla kataloger i Azure Active Directory levereras med en inbyggd domännamn, med formatet contoso.onmicrosoft.com, som gör att du komma igång med Azure eller andra Microsoft-tjänster. Du kan förbättra och förenkla inloggningen går genom att använda anpassade domäner. Information om anpassade domännamn i Azure AD och hur du verifierar en domän finns i [lägga till ett anpassat domännamn i Azure Active Directory](../fundamentals/add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Inloggningskonfiguration för Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD-inloggningen konfiguration med Azure AD Connect
Azure AD-inloggningen beror på om Azure AD kan matcha suffixet för användarens huvudnamn för en användare som synkroniseras till en av de anpassa domäner som har verifierats i Azure AD-katalog. Azure AD Connect tillhandahåller hjälp när du konfigurerar Azure AD-inloggningen inställningar, så att logga in användarupplevelsen i molnet som liknar den lokala miljön.

Azure AD Connect listas de UPN-suffix som har definierats för domänerna och paras ihop dem med en anpassad domän i Azure AD. Sedan hjälper det dig med lämplig åtgärd som ska vidtas.
Azure AD-inloggningssida Listar de UPN-suffix som har definierats för den lokala Active Directory och visar statusen motsvarande mot varje suffix. Statusvärden kan vara något av följande:

| Status | Beskrivning | Åtgärd krävs |
|:--- |:--- |:--- |
| Verifierad |Azure AD Connect att hitta matchande verifierade domän i Azure AD. Alla användare för den här domänen kan logga in med sina lokala autentiseringsuppgifter. |Ingen åtgärd krävs. |
| Verifierades inte |Azure AD Connect finns en motsvarande anpassade domän i Azure AD, men det är inte verifierad. UPN-suffixet användare av den här domänen kommer att ändras till standardvärdet. onmicrosoft.com suffix efter synkroniseringen om domänen inte verifieras. | [Verifiera den anpassade domänen i Azure AD.](../fundamentals/add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Inte har lagts till |Azure AD Connect kunde inte hitta en anpassad domän som motsvarade UPN-suffixet. UPN-suffixet användare av den här domänen kommer att ändras till standardvärdet. onmicrosoft.com-suffix om domänen inte har lagts till och verifieras i Azure. | [Lägg till och verifiera en anpassad domän som motsvarar UPN-suffixet.](../fundamentals/add-custom-domain.md) |

Azure AD-inloggningssida Listar de UPN-suffix som har definierats för den lokala Active Directory och motsvarande anpassade domän i Azure AD med aktuell status för verifiering. I en anpassad installation kan du nu välja attributet för user principal name på den **Azure AD-inloggningen** sidan.

![Azure AD-inloggningssida](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Du kan klicka på Uppdatera för att hämta den senaste statusen för anpassade domäner igen från Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Att välja attribut för användarens huvudnamn i Azure AD
Attributet userPrincipalName är det attributet som användare använder när de loggar in till Azure AD och Office 365. Du bör kontrollera domäner (även kallat UPN-suffix) som används i Azure AD innan användarna synkroniseras.

Vi rekommenderar starkt att du behåller standardattributet userPrincipalName. Om det här attributet är nonroutable och går inte att verifiera, är det möjligt att välja ett annat attribut (e-post, till exempel) som attributet som innehåller inloggnings-ID. Detta kallas den alternativa-ID. Attributvärdet Alternativt ID måste följa standarden RFC 822. Du kan använda ett alternativt ID med både enkel inloggning med lösenord och federation enkel inloggning som lösning för inloggning.

> [!NOTE]
> Använda ett alternativt ID är inte kompatibel med alla Office 365-arbetsbelastningar. Mer information finns i [konfigurera alternativa inloggnings-ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Annan anpassad domän tillstånd och deras effekt på den Azure-inloggningen
Det är mycket viktigt att förstå förhållandet mellan de anpassade domänen tillstånd i Azure AD-katalogen och de UPN-suffix som är definierade på plats. Vi ska gå igenom de olika möjliga Azure inloggning upplevelserna när du konfigurerar synkronisering med hjälp av Azure AD Connect.

I följande information, antar vi att vi är bekymrad över UPN-suffixet contoso.com, som används i en lokal katalog som en del av UPN – till exempel user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express-inställningar/synkronisering av lösenordshash
| Status | Användarens inloggning i Azure-upplevelse |
|:---:|:--- |
| Inte har lagts till |I det här fallet har ingen anpassad domän för contoso.com lagts till i Azure AD-katalog. Användare som har UPN lokalt med suffixet @contoso.com kommer inte att kunna använda sina lokala UPN för att logga in på Azure. De måste i stället använda en ny UPN som har angetts för dem av Azure AD genom att lägga till suffix för standard Azure AD-katalog. Exempel: Om du synkroniserar användare till Azure AD directory azurecontoso.onmicrosoft.com sedan lokala användare user@contoso.com får ett UPN för user@azurecontoso.onmicrosoft.com. |
| Verifierades inte |I det här fallet har vi en anpassad domän contoso.com som läggs till i Azure AD-katalog. Men har den ännu inte har verifierats. Om du fortsätter med att synkronisera användare utan att verifiera domänen, och sedan användarna kommer att tilldelas ett nytt UPN av Azure AD, precis som i ”inte lägga till”-scenariot. |
| Verifierad |I det här fallet har vi en anpassad domän contoso.com som redan har lagts till och verifierats i Azure AD för UPN-suffixet. Användare kommer att kunna använda sina lokala användarens huvudnamn, till exempel user@contoso.com, för att logga in på Azure när de är synkroniserad med Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS-federation
Du kan inte skapa en federation med standard. onmicrosoft.com-domän i Azure AD eller en overifierade anpassad domän i Azure AD. När du kör Azure AD Connect-guiden om du väljer en overifierade domän för att skapa en federation med, sedan visas Azure AD Connect de nödvändiga posterna skapas där din DNS-Server är värd för domänen. Mer information finns i [verifiera Azure AD-domänen som valts för federation](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Om du har valt alternativet för användare **Federation med AD FS**, du måste ha en anpassad domän till fortsätta att skapa en federation med Azure AD. Det innebär att vi ska ha en anpassad domän contoso.com som lagts till i Azure AD-katalog för vår diskussion.

| Status | Påverkan på användaren Azure-inloggningen |
|:---:|:--- |
| Inte har lagts till |I det här fallet kunde inte Azure AD Connect hitta en motsvarande anpassade domän för UPN-suffixet contoso.com i Azure AD-katalog. Du måste lägga till en anpassad domän contoso.com om du behöver användare att logga in med hjälp av AD FS med sina lokala UPN (t.ex. user@contoso.com). |
| Verifierades inte |I det här fallet får Azure AD Connect du lämplig information om hur du kan verifiera din domän i ett senare skede. |
| Verifierad |I det här fallet går du vidare med konfiguration utan några ytterligare åtgärder. |

## <a name="changing-the-user-sign-in-method"></a>Ändrar inloggningsmetod för användare
Du kan ändra metoden för användaren från federation, synkronisering av lösenordshash och direktautentisering med hjälp av de uppgifter som är tillgängliga i Azure AD Connect efter den inledande konfigurationen av Azure AD Connect med guiden. Kör Azure AD Connect-guiden igen och du ser en lista med aktiviteter som du kan utföra. Välj **ändra användarinloggning** i listan över uppgifter.

![Ändra användarinloggning](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

På nästa sida uppmanas du att ange autentiseringsuppgifter för Azure AD.

![Anslut till Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

På den **användarinloggning** väljer du den önskade användare logga in.

![Anslut till Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Om du bara gör en tillfällig växel till synkronisering av lösenordshash, välj sedan den **konvertera inte användarkonton** markerar du kryssrutan. Markera inte alternativet konverterar varje användare till federerad och det kan ta flera timmar.
>
>

## <a name="next-steps"></a>Nästa steg
- Läs mer om [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
- Läs mer om [designbegrepp för Azure AD Connect](active-directory-aadconnect-design-concepts.md).
