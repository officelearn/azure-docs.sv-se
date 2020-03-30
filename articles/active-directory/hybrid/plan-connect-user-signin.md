---
title: 'Azure AD Connect: Användarloggning | Microsoft-dokument'
description: Azure AD Connect-användarloggning för anpassade inställningar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a08120b98c7a08bca50453df59df313b1645c5c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331271"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect user sign-in options (Alternativ för användarinloggning i Azure AD Connect)
Azure Active Directory (Azure AD) Connect gör det möjligt för användarna att logga in på både molnresurser och lokala resurser med samma lösenord. I den här artikeln beskrivs nyckelbegrepp för varje identitetsmodell som hjälper dig att välja den identitet som du vill använda för att logga in på Azure AD.

Om du redan är bekant med Azure AD-identitetsmodellen och vill veta mer om en viss metod läser du lämplig länk:

* [Synkronisering av lösenordshösch](#password-hash-synchronization) med [sömlös enkel inloggning (SSO)](how-to-connect-sso.md)
* [Direktautentisering](how-to-connect-pta.md) med [sömlös enkel inloggning (SSO)](how-to-connect-sso.md)
* [Federerad SSO (med Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federation med PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Det är viktigt att komma ihåg att genom att konfigurera federation för Azure AD skapar du förtroende mellan din Azure AD-klientorganisation och dina federerade domäner. Med detta förtroende federerade domän användare kommer att ha tillgång till Azure AD molnresurser inom klienten.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Välja användarinloggningsmetod för din organisation
Det första beslutet att implementera Azure AD Connect är att välja vilken autentiseringsmetod användarna ska använda för att logga in. Det är viktigt att du väljer rätt metod som uppfyller organisationens säkerhet och avancerade krav. Autentisering är viktigt eftersom det validerar användarens identiteter för att komma åt appar och data i molnet. Om du vill välja rätt autentiseringsmetod måste du tänka på tid, befintlig infrastruktur, komplexitet och kostnad för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan förändras med tiden.

Azure AD stöder följande autentiseringsmetoder: 

* **Molnautentisering** - När du väljer den här autentiseringsmetoden hanterar Azure AD autentiseringsprocessen för användarens inloggning. Med molnautentisering kan du välja mellan två alternativ: 
   * **PHS (Password hash synchronization)** - Password Hash Sync gör det möjligt för användare att använda samma användarnamn och lösenord som de använder lokalt utan att behöva distribuera någon ytterligare infrastruktur förutom Azure AD Connect. 
   * **Direktautentisering (PTA)** – Det här alternativet liknar synkronisering av lösenord hash- meddelanden, men ger en enkel lösenordsvalidering med lokala programvaruagenter för organisationer med starka säkerhets- och efterlevnadsprinciper.
* **Federerad autentisering** – När du väljer den här autentiseringsmetoden kommer Azure AD att lämna över autentiseringsprocessen till ett separat tillförlitligt autentiseringssystem, till exempel AD FS eller ett federationssystem från tredje part, för att validera användarens inloggning. 

För de flesta organisationer som bara vill aktivera användarloggning till Office 365, SaaS-program och andra Azure AD-baserade resurser rekommenderar vi standardalternativet för synkronisering av lösenord hash-synkronisering.
 
Detaljerad information om hur du väljer en autentiseringsmetod finns i [Välja rätt autentiseringsmetod för azure Active Directory-hybrididentitetslösningen](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Synkronisering av lösenordshash
Med synkronisering av lösenord hash synkroniseras hashar av användarlösenord från lokala Active Directory till Azure AD. När lösenord ändras eller återställs lokalt synkroniseras de nya lösenordshasharen omedelbart till Azure AD så att användarna alltid kan använda samma lösenord för molnresurser och lokala resurser. Lösenorden skickas aldrig till Azure AD eller lagras i Azure AD i klartext. Du kan använda synkronisering av lösenordsh hash tillsammans med tillbakaskrivning av lösenord för att aktivera återställning av lösenord för självbetjäning i Azure AD.

Dessutom kan du aktivera [Sömlös SSO](how-to-connect-sso.md) för användare på domänanslutna datorer som finns i företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att hjälpa dem att komma åt molnresurser på ett säkert sätt.

![Synkronisering av lösenordshash](./media/plan-connect-user-signin/passwordhash.png)

Mer information finns i artikeln för synkronisering av [lösenordshÃ¤nning.](how-to-connect-password-hash-synchronization.md)

### <a name="pass-through-authentication"></a>Direktautentisering
Med direktautentisering valideras användarens lösenord mot den lokala Active Directory-styrenheten. Lösenordet behöver inte finnas i Azure AD i någon form. Detta gör det möjligt att utvärdera lokala principer, till exempel begränsningar för inloggningstimmen, under autentisering till molntjänster.

Genomströmningsautentisering används en enkel agent på en Windows Server 2012 R2-domän ansluten dator i den lokala miljön. Den här agenten lyssnar efter begäranden om lösenordsverifiering. Det kräver inte att inkommande portar ska vara öppna för Internet.

Dessutom kan du också aktivera enkel inloggning för användare på domänanslutna datorer som finns i företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att hjälpa dem att komma åt molnresurser på ett säkert sätt.
![Direktautentisering](./media/plan-connect-user-signin/pta.png)

Mer information finns i:
- [Direktautentisering](how-to-connect-pta.md)
- [Enkel inloggning](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federation som använder en ny eller befintlig servergrupp med AD FS i Windows Server 2012 R2
Med federerade inloggningar kan användarna logga in på Azure AD-baserade tjänster med sina lokala lösenord. När de är i företagets nätverk behöver de inte ens ange sina lösenord. Genom att använda federationsalternativet med AD FS kan du distribuera en ny eller befintlig servergrupp med AD FS i Windows Server 2012 R2. Om du väljer att ange en befintlig servergrupp konfigurerar Azure AD Connect förtroendet mellan din servergrupp och Azure AD så att användarna kan logga in.

<center>

![Federation med AD FS i Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Distribuera federation med AD FS i Windows Server 2012 R2

Om du distribuerar en ny servergrupp behöver du:

* En Windows Server 2012 R2-server för federationsservern.
* En Windows Server 2012 R2-server för webbprogramproxyn.
* En PFX-fil med ett TLS/SSL-certifikat för ditt avsedda federationstjänstnamn. Till exempel: fs.contoso.com.

Om du distribuerar en ny servergrupp eller använder en befintlig servergrupp behöver du:

* Lokala administratörsuppgifter på federationsservrarna.
* Lokala administratörsautentiseringsuppgifter på alla arbetsgruppsservrar (inte domänanslutna) som du tänker distribuera proxyrollen webbprogram på.
* Datorn som du kör guiden på för att kunna ansluta till andra datorer som du vill installera AD FS eller Web Application Proxy på med hjälp av Windows Remote Management.

Mer information finns i [Konfigurera SSO med AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federation med PingFederate
Med federerade inloggningar kan användarna logga in på Azure AD-baserade tjänster med sina lokala lösenord. När de är i företagets nätverk behöver de inte ens ange sina lösenord.

Mer information om hur du konfigurerar PingFederate för användning med Azure Active Directory finns i [PingFederate Integration med Azure Active Directory och Office 365](https://www.pingidentity.com/AzureADConnect)

Information om hur du konfigurerar Azure AD Connect med PingFederate finns i [azure AD Connect anpassad installation](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Logga in med hjälp av en tidigare version av AD FS eller en tredjepartslösning
Om du redan har konfigurerat molnloggning med hjälp av en tidigare version av AD FS (till exempel AD FS 2.0) eller en tredjepartsfederationsleverantör kan du välja att hoppa över användarloggningskonfiguration via Azure AD Connect. På så sätt kan du få den senaste synkroniseringen och andra funktioner i Azure AD Connect samtidigt som du använder din befintliga lösning för inloggning.

Mer information finns i [kompatibilitetslistan för Azure AD-federationen](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Användarens inloggning och användarens huvudnamn
### <a name="understanding-user-principal-name"></a>Förstå användarens huvudnamn
I Active Directory är UPN-suffixet (Default Principal Principal Name) DNS-namnet på den domän där användarkontot skapades. I de flesta fall är detta domännamnet som är registrerat som företagsdomän på Internet. Du kan dock lägga till fler UPN-suffix med hjälp av Active Directory-domäner och förtroenden.

Användarens UPN har formatet username@domain. För en Active Directory-domän med namnet "contoso.com" kan en användare medjohn@contoso.comnamnet John ha UPN " ". Användarens UPN baseras på RFC 822. Även om UPN och e-post delar samma format, kan värdet på UPN för en användare vara samma som användarens e-postadress.

### <a name="user-principal-name-in-azure-ad"></a>Användarens huvudnamn i Azure AD
Azure AD Connect-guiden använder attributet userPrincipalName eller låter dig ange attributet (i en anpassad installation) som ska användas lokalt som användarens huvudnamn i Azure AD. Det här är värdet som används för att logga in på Azure AD. Om värdet för attributet userPrincipalName inte motsvarar en verifierad domän i Azure AD ersätter Azure AD det med ett standardvärde för .onmicrosoft.com.

Varje katalog i Azure Active Directory levereras med ett inbyggt domännamn, med formatet contoso.onmicrosoft.com, som gör att du kan komma igång med Azure eller andra Microsoft-tjänster. Du kan förbättra och förenkla inloggningsupplevelsen med hjälp av anpassade domäner. Information om anpassade domännamn i Azure AD och hur du verifierar en domän finns i [Lägga till ditt anpassade domännamn i Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Inloggningskonfiguration för Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD-inloggningskonfiguration med Azure AD Connect
Azure AD-inloggningsupplevelsen beror på om Azure AD kan matcha användarens huvudnamnsuffix för en användare som synkroniseras med en av de anpassade domäner som verifieras i Azure AD-katalogen. Azure AD Connect ger hjälp medan du konfigurerar Azure AD-inloggningsinställningar, så att användarens inloggningsupplevelse i molnet liknar den lokala upplevelsen.

Azure AD Connect listar upn-suffix som har definierats för domänerna och försöker matcha dem med en anpassad domän i Azure AD. Då hjälper det dig med lämpliga åtgärder som måste vidtas.
På inloggningssidan för Azure AD visas de UPN-suffix som har definierats för lokal Active Directory och visar motsvarande status mot varje suffix. Statusvärdena kan vara något av följande:

| Status | Beskrivning | Åtgärder som behövs |
|:--- |:--- |:--- |
| Verifierat |Azure AD Connect hittade en matchande verifierad domän i Azure AD. Alla användare för den här domänen kan logga in med hjälp av sina lokala autentiseringsuppgifter. |Inga åtgärder behövs. |
| Inte verifierad |Azure AD Connect hittade en matchande anpassad domän i Azure AD, men den verifieras inte. UPN-suffixet för användarna av den här domänen ändras till standard -onmicrosoft.com suffixet efter synkroniseringen om domänen inte verifieras. | [Verifiera den anpassade domänen i Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Inte tillagd |Azure AD Connect hittade inte en anpassad domän som motsvarade UPN-suffixet. UPN-suffixet för användarna av den här domänen ändras till standard -onmicrosoft.com suffix om domänen inte läggs till och verifieras i Azure. | [Lägg till och verifiera en anpassad domän som motsvarar UPN-suffixet.](../fundamentals/add-custom-domain.md) |

På inloggningssidan för Azure AD visas de UPN-suffix som har definierats för lokal Active Directory och motsvarande anpassade domän i Azure AD med aktuell verifieringsstatus. I en anpassad installation kan du nu välja attributet för användarens huvudnamn på **inloggningssidan för Azure AD.**

![Inloggningssida för Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Du kan klicka på uppdateringsknappen för att hämta den senaste statusen för anpassade domäner från Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Välja attribut för användarens huvudnamn i Azure AD
Attributet userPrincipalName är det attribut som användare använder när de loggar in på Azure AD och Office 365. Du bör verifiera de domäner (kallas även UPN-suffix) som används i Azure AD innan användarna synkroniseras.

Vi rekommenderar starkt att du behåller standardattributet UserPrincipalName. Om det här attributet inte kan skickas och inte kan verifieras är det möjligt att välja ett annat attribut (till exempel e-post) som det attribut som innehåller inloggnings-ID:t. Detta kallas alternativt ID. Attributvärdet för alternativt ID måste följa RFC 822-standarden. Du kan använda ett alternativt ID med både lösenord SSO och federation SSO som inloggningslösning.

> [!NOTE]
> Att använda ett alternativt ID är inte kompatibelt med alla Office 365-arbetsbelastningar. Mer information finns i [Konfigurera alternativt inloggnings-ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Olika anpassade domäntillstånd och deras effekt på Azure-inloggningsupplevelsen
Det är mycket viktigt att förstå förhållandet mellan de anpassade domäntillstånden i din Azure AD-katalog och UPN-suffixen som definieras lokalt. Låt oss gå igenom de olika möjliga Azure-inloggningsupplevelserna när du konfigurerar synkronisering med hjälp av Azure AD Connect.

För följande information, låt oss anta att vi är berörda av UPN-suffixet contoso.com, som används i den lokala user@contoso.comkatalogen som en del av UPN - till exempel .

###### <a name="express-settingspassword-hash-synchronization"></a>Synkronisering av expressinställningar/lösenordshÃ¤n

| Status | Effekt på användarens Azure-inloggningsupplevelse |
|:---:|:--- |
| Inte tillagd |I det här fallet har ingen anpassad domän för contoso.com lagts till i Azure AD-katalogen. Användare som har UPN lokalt med @contoso.com suffixet kan inte använda sitt lokala UPN för att logga in på Azure. De måste i stället använda ett nytt UPN som tillhandahålls dem av Azure AD genom att lägga till suffixet för standardkatalogen för Azure AD. Om du till exempel synkroniserar användare med Azure AD-katalogen azurecontoso.onmicrosoft.com får user@contoso.com den lokala användaren user@azurecontoso.onmicrosoft.comett UPN med . |
| Inte verifierad |I det här fallet har vi en anpassad domän contoso.com som läggs till i Azure AD-katalogen. Det är dock ännu inte verifierat. Om du går vidare med synkronisering av användare utan att verifiera domänen, kommer användarna att tilldelas en ny UPN av Azure AD, precis som i scenariot "Inte tillagd". |
| Verifierat |I det här fallet har vi en anpassad domän contoso.com som redan har lagts till och verifierats i Azure AD för UPN-suffixet. Användare kan använda sitt lokala användarnamn för att user@contoso.comtill exempel logga in på Azure när de har synkroniserats med Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS-federation
Du kan inte skapa en federation med standarddomänen .onmicrosoft.com i Azure AD eller en overifierad anpassad domän i Azure AD. När du kör Azure AD Connect-guiden, om du väljer en overifierad domän att skapa en federation med, uppmanar Azure AD Connect dig med nödvändiga poster som ska skapas där din DNS finns för domänen. Mer information finns i [Verifiera den Azure AD-domän som valts för federation](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Om du har valt alternativet användares **inloggningstillffört med AD FS**måste du ha en anpassad domän för att kunna fortsätta skapa en federation i Azure AD. För vår diskussion innebär det att vi bör ha en anpassad domän contoso.com som läggs till i Azure AD-katalogen.

| Status | Effekt på användarens Azure-inloggningsupplevelse |
|:---:|:--- |
| Inte tillagd |I det här fallet hittade Azure AD Connect inte en matchande anpassad domän för UPN-suffixet contoso.com i Azure AD-katalogen. Du måste lägga till en anpassad domän contoso.com om du behöver användare att logga in med user@contoso.comhjälp av AD FS med sina lokala UPN (som). |
| Inte verifierad |I det här fallet frågar Azure AD Connect dig med lämplig information om hur du kan verifiera din domän i ett senare skede. |
| Verifierat |I det här fallet kan du gå vidare med konfigurationen utan ytterligare åtgärder. |

## <a name="changing-the-user-sign-in-method"></a>Ändra användarinloggningsmetod
Du kan ändra inloggningsmetoden för användare från federation, synkronisering av lösenord hash- eller direktautentisering med hjälp av de uppgifter som är tillgängliga i Azure AD Connect efter den första konfigurationen av Azure AD Connect med guiden. Kör Azure AD Connect-guiden igen så visas en lista över uppgifter som du kan utföra. Välj **Ändra användarloggning i** listan över uppgifter.

![Ändra inloggning för användare](./media/plan-connect-user-signin/changeusersignin.png)

På nästa sida uppmanas du att ange autentiseringsuppgifter för Azure AD.

![Anslut till Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

På sidan **Använda inloggning** väljer du önskad användarinloggning.

![Anslut till Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Om du bara gör en tillfällig övergång till synkronisering av lösenordshÃ¤nde markerar du kryssrutan **Konvertera inte användarkonton.** Om du inte markerar alternativet konverteras varje användare till federerade, och det kan ta flera timmar.
>
>

## <a name="next-steps"></a>Nästa steg
- Läs mer om [hur du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
- Läs mer om [azure AD Connect-designkoncept](plan-connect-design-concepts.md).
