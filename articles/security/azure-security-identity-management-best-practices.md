---
title: Azure identitet och åtkomst säkerhetsmetoder | Microsoft Docs
description: Den här artikeln innehåller en uppsättning Metodtips för Identitetshantering och åtkomstkontroll med inbyggda funktioner i Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2a669f5b46db4d5de7d1d6863b94e6c117667aee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153246"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identitetshantering och åtkomstkontroll säkerhetsmetoder
I den här artikeln diskuterar vi en samling Azure identity management och säkerhetsmetoder för access control. Dessa metodtips härleds från vår erfarenhet av [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) och erfarenheter från kunder som dig själv.

För varje rekommenderar förklarar vi:

* Vad den bästa metoden är
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera den bästa metoden
* Möjliga alternativ till den bästa praxis
* Hur du kan lära dig att aktivera ett metodtips

Den här Azure identity management och säkerhet för åtkomstkontroll artikel om bästa praxis baseras på en konsensus åsikter och funktioner för Azure-plattformen och funktioner, som de finns när den här artikeln skrevs. Andras åsikter och tekniker som ändras med tiden och den här artikeln kommer att uppdateras regelbundet att återspegla dessa ändringar.

Azure identitet och kontroll säkerhetsmetoder beskrivs i den här artikeln är:

* Hantera identitet som primär säkerhetsperimeter
* Centralisera Identitetshantering
* Hantera anslutna klienter
* Aktivera enkel inloggning
* Aktivera villkorlig åtkomst
* Aktivera lösenordshantering
* Framtvinga multifaktorverifiering för användare
* Använd rollbaserad åtkomstkontroll
* Lägre exponering av Privilegierade konton
* Kontrollera platser där resurser finns
* Använd Azure AD för autentisering för lagring

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Hantera identitet som primär säkerhetsperimeter

Många Överväg identitet för att vara primär perimeternätverket för säkerhet. Det här är en förändring jämfört med traditionella fokus på nätverkssäkerhet. Nätverket perimetrar fortsätta att få mer porös och den perimeterskydd får inte vara så effektiv innan alltfler [BYOD](https://aka.ms/byodcg) enheter och molnprogram.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) är Azure-lösning för identitets- och åtkomsthantering. Azure AD är en multitenant, molnbaserade företagskatalogen och identitetshanteringen management-tjänst från Microsoft. Den kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd i en enda lösning.

I följande avsnitt listas Metodtips för identitets- och säkerhet med hjälp av Azure AD.

## <a name="centralize-identity-management"></a>Centralisera Identitetshantering

I en [hybrididentitet](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) scenario rekommenderar vi att du integrera dina lokala och molnbaserade kataloger. Integrering gör det möjligt för IT-teamet och hantera konton från en plats, oavsett var ett konto har skapats. Integrering hjälper även användarna att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst till både i molnet och lokala resurser.

**Bästa praxis**: Upprätta en enda Azure AD-instans. Konsekvens och en enda auktoritativa källor ökar tydlighet och minska säkerhetsrisker från mänskliga misstag och konfiguration av komplexitet.
**Information om**: Ange en enda Azure AD directory som auktoritativ källa för företagets och organisatoriska konton.

**Bästa praxis**: Integrera dina lokala kataloger med Azure AD.  
**Information om**: Använd [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) att synkronisera din lokala katalog med din molnkatalog.

> [!Note]
> Det finns [faktorer som påverkar prestanda för Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Kontrollera Azure AD Connect har tillräckligt med kapacitet för att hålla presterar som förväntat system från hindra säkerhet och produktivitet. Stora och komplexa organisationer (organisationer etablering mer än 100 000 objekt) bör följa den [rekommendationer](../active-directory/hybrid/whatis-hybrid-identity.md) att optimera sina Azure AD Connect-implementering.

**Bästa praxis**: Synkronisera inte konton till Azure AD som har höga privilegier i din befintliga Active Directory-instans.
**Information om**: Ändra inte standardvärdet [Azure AD Connect-konfigurationen](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) som filtrerar ut dessa konton. Den här konfigurationen minskar risken med att angripare pivotering från molnet till lokala resurser (vilket kan skapa en större incident).

**Bästa praxis**: Aktivera synkronisering av lösenordshash.  
**Information om**: Synkronisering av lösenordshash är en funktion som används för att synkronisera användare lösenords-hash från en lokal Active Directory-instans till en molnbaserad Azure AD-instans. Den här synkroniseringen hjälper dig för att skydda mot läckta autentiseringsuppgifter som spelas från föregående attacker.

Även om du vill använda federation med Active Directory Federation Services (AD FS) eller andra identitetsleverantörer, kan du också ställa in synkronisering av lösenordshash som en säkerhetskopia om dina lokala servrar misslyckas eller bli tillfälligt otillgängliga. Den här synkroniseringen gör det möjligt för användare att logga in på tjänsten med hjälp av samma lösenord som de använder för att logga in på sina lokala Active Directory-instans. Det gör också Identity Protection att identifiera avslöjade autentiseringsuppgifter genom att jämföra synkroniserade lösenords-hash med kända äventyras, om en användare har använt samma e-postadress och lösenord för andra tjänster som inte är anslutna till Azure AD-lösenord.

Mer information finns i [implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Bästa praxis**: För programutveckling, använder du Azure AD för autentisering.
**Information om**: Använd rätt funktioner för att stödja autentisering:

  - Azure AD för anställda
  - [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) för gästanvändare och externa partners
  - [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) för att styra hur kunderna registrerar jag mig, logga in och hanterar sina profiler när de använder dina program

Organisationer som inte integrerar sin lokala identitet med sin molnidentitet kan ha fler tillägg i hanteringen av konton. Det här arbetet ökar sannolikheten för fel och säkerhetsintrång.

> [!Note]
> Du måste välja vilka kritiska konton kommer att finnas i och om administratörsarbetsstation som används är hanteras av nya molntjänster eller befintliga bearbetar kataloger. Med hjälp av befintliga hanterings- och identitet etablering processer kan minska risker, men kan även skapa risken för en angripare att kompromissa med ett lokalt konto och pivotering till molnet. Du kanske vill använda en egen strategi för olika roller (till exempel IT-administratörer och business unit-administratörer). Har du två alternativ. Första alternativet är att skapa Azure AD-konton som inte är synkroniserade med din lokala Active Directory-instans. Anslut din arbetsstation till Azure AD, som du kan hantera och korrigera med hjälp av Microsoft Intune. Andra alternativ är att använda befintliga konton som synkroniseras till din lokala Active Directory-instans. Använd befintliga arbetsstationer i Active Directory-domänen för hantering och säkerhet.

## <a name="manage-connected-tenants"></a>Hantera anslutna klienter
Organisationen säkerhet måste synlighet att utvärdera risker och fastställa om principer för din organisation och eventuella regelkrav följs. Du bör se till att organisationen säkerhet har insyn i alla prenumerationer som är ansluten till din produktionsmiljö och nätverk (via [Azure ExpressRoute](../expressroute/expressroute-introduction.md) eller [plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). En [Global administratör/företagsadministratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) i Azure AD kan utöka sin åtkomst till den [administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) roll och se alla prenumerationer och hanterade grupper som är ansluten till din miljö.

Se [utöka behörighet för att hantera alla Azure-prenumerationer och hanteringsgrupper](../role-based-access-control/elevate-access-global-admin.md) så att du och din säkerhetsgrupp kan visa alla prenumerationer eller hanteringsgrupper som är ansluten till din miljö. Du bör ta bort den här utökad åtkomst när du har utvärderat risker.

## <a name="enable-single-sign-on"></a>Aktivera enkel inloggning

I en mobil- och molnorienterade värld som du vill aktivera enkel inloggning (SSO) till enheter, appar och tjänster från var som helst så att användarna kan vara produktiva oavsett tid och plats. När du har flera identitetslösningar att hantera detta blir ett administrativa problem inte bara för IT utan även för användare som behöver komma ihåg flera lösenord.

Du kan få enkel inloggning med samma ID-lösning för alla dina appar och resurser. Och användarna kan använda samma uppsättning autentiseringsuppgifter för att logga in och komma åt resurser som de behöver, oavsett om resurserna kan finnas lokalt eller i molnet.

**Bästa praxis**: Aktivera enkel inloggning.  
**Information om**: Azure AD [utökar den lokala Active Directory](../active-directory/connect/active-directory-aadconnect.md) till molnet. Användare kan använda sina primära arbets- eller skolkonto för sina domänanslutna enheter, företagets resurser och alla webb- och SaaS-program som de behöver för att få sitt arbete. Användare inte längre komma ihåg flera uppsättningar med användarnamn och lösenord och deras åtkomst till ett program kan vara automatiskt etablerade (eller avetableras) baserat på deras organisation gruppmedlemskap och deras status som en anställd. Och du kan kontrollera den åtkomsten för galleriappar eller för dina egna lokala appar som du har utvecklat och publicerat genom [Azure AD-programproxy](../active-directory/active-directory-application-proxy-get-started.md).

Använda SSO för att ge användare åtkomst till sina [SaaS-program](../active-directory/active-directory-appssoaccess-whatis.md) baserat på deras arbets- eller skolkonto konto i Azure AD. Detta gäller inte bara för Microsoft SaaS-appar, men även andra appar som [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) och [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Du kan konfigurera programmet att använda Azure AD som en [SAML-baserad identitet](../active-directory/fundamentals-identity.md) provider. Som en säkerhetskontroll utfärdar inte Azure AD en token som tillåter användare att logga in till programmet om de har beviljats åtkomst via Azure AD. Du kan bevilja åtkomst direkt eller via en grupp att användare är medlem i.

Organisationer som inte skapar en gemensam identitet för att upprätta en enkel inloggning för användare och program exponeras mer för scenarier där användarna har flera lösenord. De här scenarierna öka sannolikheten för användare att lösenord eller med hjälp av svaga lösenord.

## <a name="turn-on-conditional-access"></a>Aktivera villkorlig åtkomst

Användare kan komma åt din organisations resurser med hjälp av en mängd olika enheter och appar från var som helst. Som IT-administratör, som du vill kontrollera att dessa enheter uppfyller dina krav för säkerhet och efterlevnad. Bara fokusera på vem som kan komma åt en resurs räcker inte längre.

För att jämna ut säkerhet och produktivitet, måste du tänka på hur en resurs används innan du kan fatta ett beslut om åtkomstkontroll. Med villkorsstyrd åtkoms i Azure AD kan du uppfylla det här kravet. Du kan göra automatiserade besluten om åtkomstkontroll baserat på villkor för att komma åt dina appar i molnet med villkorlig åtkomst.

**Bästa praxis**: Hantera och styr åtkomsten till företagets resurser.  
**Information om**: Konfigurera Azure AD [villkorlig åtkomst](../active-directory/active-directory-conditional-access-azure-portal.md) baserat på en grupp, plats och programmets känslighet för SaaS-appar och Azure AD-anslutna appar.

**Bästa praxis**: Blockera äldre autentiseringsprotokoll.
**Information om**: Angripare utnyttja svagheter i äldre protokoll varje dag, särskilt för lösenord besprutningsmedel attacker. Konfigurera villkorlig åtkomst för att blockera äldre protokoll. Se videon [Azure AD: Bra att veta](https://www.youtube.com/watch?v=wGk0J4z90GI) för mer information.

## <a name="enable-password-management"></a>Aktivera lösenordshantering

Om du har flera klienter eller om du vill att användarna kan [återställa sina egna lösenord](../active-directory/active-directory-passwords-update-your-own-password.md), är det viktigt att du använder lämpliga säkerhetsprinciper för att förhindra missbruk.

**Bästa praxis**: Konfigurera lösenordsåterställning via självbetjäning (SSPR) för dina användare.  
**Information om**: Använda Azure AD [lösenordsåterställning via självbetjäning](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funktionen.

**Bästa praxis**: Övervaka hur eller om SSPR som verkligen används.  
**Information om**: Övervaka användare som registrerar med hjälp av Azure AD [lösenord återställer registreringen aktivitetsrapporten](../active-directory/active-directory-passwords-get-insights.md). Rapporteringsfunktionen som Azure AD tillhandahåller kan du besvara frågor med hjälp av fördefinierade rapporter. Om du har korrekt licens, kan du också skapa egna frågor.

**Bästa praxis**: Utöka molnbaserade lösenordsprinciper till din lokala infrastruktur.
**Information om**: Förbättra lösenordsprinciper i din organisation genom att utföra samma kontroller för lokala lösenordsändringar som du gör ändringar av molnbaserade lösenord. Installera [Azure AD-lösenordsskydd](../active-directory/authentication/concept-password-ban-bad.md) för Windows Server Active Directory agenter lokalt att utöka listor med förbjudna lösenord till din befintliga infrastruktur. Användare och administratörer som ändrar, ange eller återställa lösenord på plats krävs för att följa principen med samma lösenord som endast molnbaserade användare.

## <a name="enforce-multi-factor-verification-for-users"></a>Framtvinga multifaktorverifiering för användare

Vi rekommenderar att du kräver tvåstegsverifiering för alla användare. Detta inkluderar administratörer och andra i organisationen som kan ha en betydande inverkan om sitt konto komprometteras (till exempel företagsriskkonsulter).

Det finns flera alternativ för att kräva tvåstegsverifiering. Det bästa alternativet för dig beror på dina mål och Azure AD-version du kör din licensprogram. Se [kräva tvåstegsverifiering för en användare](../active-directory/authentication/howto-mfa-userstates.md) att bestämma det bästa alternativet för dig. Se den [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) prissidor för mer information om licenser och priser.

Följande är alternativ och fördelar för att aktivera tvåstegsverifiering:

**Alternativ 1**: [Aktivera Multi-Factor Authentication genom att ändra användarens tillstånd](../active-directory/authentication/howto-mfa-userstates.md).   
**Förmånen**: Det här är den traditionella metoden för att kräva tvåstegsverifiering. Det fungerar med både [Azure Multi-Factor Authentication i molnet och Azure Multi-Factor Authentication Server](../active-directory/authentication/concept-mfa-whichversion.md). Med den här metoden kräver att användare utför en tvåstegsverifiering varje gång de loggar in och åsidosättningar principer för villkorlig åtkomst.

För att avgöra var Multifaktorautentisering måste vara aktiverat, se [vilken version av Azure MFA är rätt för min organisation?](../active-directory/authentication/concept-mfa-whichversion.md).

**Alternativ 2**: [Aktivera Multi-Factor Authentication med principen för villkorlig åtkomst](../active-directory/authentication/howto-mfa-getstarted.md).
**Förmånen**: Det här alternativet kan du fråga efter tvåstegsverifiering vissa villkor med hjälp av [villkorlig åtkomst](../active-directory/active-directory-conditional-access-azure-portal.md). Särskilda villkor kan vara användare logga in från olika platser, ej betrodda enheter eller program som du anser vara riskfyllda. Definiera särskilda villkor där du kräver tvåstegsverifiering kan du undvika konstant fråga om dina användare, vilket kan vara en otrevlig användarupplevelse.

Det här är det mest flexibla sättet att aktivera tvåstegsverifiering för dina användare. När en princip för villkorlig åtkomst fungerar endast för Azure Multi-Factor Authentication i molnet och är en premiumfunktion i Azure AD. Du hittar mer information om den här metoden i [distribuera molnbaserade Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

**Alternativ 3**: Aktivera Multi-Factor Authentication med principer för villkorlig åtkomst genom att utvärdera risken för användare och logga in för [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Förmånen**: Det här alternativet kan du:

- Identifiera potentiella sårbarheter som påverkar organisationens identiteter.
- Konfigurera automatiska svar till identifierade misstänkta åtgärder som är relaterade till din organisations identiteter.
- Undersöka misstänkta incidenter och vidta lämpliga åtgärder du kan åtgärda detta.

Den här metoden används Azure AD Identity Protection riskbedömningen att avgöra om tvåstegsverifiering krävs baserat på användare och inloggningsrisk för alla molnprogram. Den här metoden kräver Azure Active Directory P2 licensiering. Du hittar mer information om den här metoden i [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Alternativ 1, aktivera Multi-Factor Authentication genom att ändra användartillståndet, åsidosätter principer för villkorlig åtkomst. Eftersom alternativ 2 och 3 använder principer för villkorlig åtkomst, kan du inte använda alternativ 1 med dem.

Organisationer som inte lägger till extra skyddslager för identitet, till exempel tvåstegsverifiering, är mer sårbara för angrepp för stöld av autentiseringsuppgifter. En attack med stöld av autentiseringsuppgifter kan leda till kompromettering av data.

## <a name="use-role-based-access-control"></a>Använd rollbaserad åtkomstkontroll
Åtkomsthantering för molnresurser är viktigt för alla organisationer som använder molnet. [Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) hjälper dig att hantera vem som har åtkomst till Azure-resurser, vad de kan göra med dessa resurser och vilka områden som de har åtkomst till.

Utse grupper eller enskilda roller ansvarar för specifika funktioner i Azure hjälper dig att undvika förvirring som kan leda till mänskliga och Automatiseringsfel som skapar säkerhetsrisker. Begränsa åtkomst baserat på den [behöver veta](https://en.wikipedia.org/wiki/Need_to_know) och [lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är mycket viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst.

Ditt säkerhetsteam måste insyn i dina Azure-resurser för att utvärdera och åtgärda risker. Om security-teamet har operativa ansvarsområden, behöver de ytterligare behörighet att utföra sitt arbete.

Du kan använda [RBAC](../role-based-access-control/overview.md) att tilldela behörigheter till användare, grupper och program för ett visst omfång. Omfattningen för en rolltilldelning kan vara en prenumeration, en resursgrupp eller en enskild resurs.

**Bästa praxis**: Hålla isär uppgifter i ditt team och bevilja endast mängden åtkomst till användare som de behöver för att utföra sitt arbete. Istället för att ge alla kan obegränsade behörigheter i din Azure-prenumeration eller resurser, endast vissa åtgärder i ett visst omfång.
**Information om**: Använd [inbyggda RBAC-roller](../role-based-access-control/built-in-roles.md) i Azure för att tilldela behörigheter till användare.

> [!Note]
> Specifika behörigheter skapar onödig komplexitet och förvirring kommer innehåll kan sparas i ett ”äldre” konfiguration som är svårare att lösa utan att behöva betala något. Undvik att resurs-specifika behörigheter. Använd istället hanteringsgrupper för företagsomfattande behörigheter och resursgrupper för behörigheter i prenumerationer. Undvik att användarspecifika behörigheter. I stället tilldela åtkomst till grupper i Azure AD.

**Bästa praxis**: Bevilja security team med Azure ansvarsområden åtkomst till finns i Azure-resurser så att de kan utvärdera och åtgärda risker.
**Information om**: Bevilja security team i RBAC [Säkerhetsläsare](../role-based-access-control/built-in-roles.md#security-reader) roll. Du kan använda roten hanteringsgruppen eller hanteringsgruppen segment, beroende på omfattningen av ansvarsområden:

- **Rot-hanteringsgruppen** för team som ansvarar för alla företagsresurser
- **Segment-hanteringsgruppen** för team med begränsad omfattning (ofta på grund av regler eller andra organisatoriska gränser)

**Bästa praxis**: Bevilja behörighet så att security team som har direkt operativa ansvarsområden.
**Information om**: Granska de inbyggda RBAC-rollerna för lämpliga rolltilldelningen. Om de inbyggda rollerna inte uppfyller de specifika behoven i din organisation, kan du skapa [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md). Som du kan tilldela anpassade roller till användare, grupper och tjänstens huvudnamn på prenumerationen, resursgruppen och resurs-scope med inbyggda roller.

**Bästa praxis**: Bevilja Azure Security Center åtkomst till säkerhetsroller som behöver den. Security Center hjälper att snabbt identifiera och åtgärda risker.
**Information om**: Lägg till security team utan dessa behov i RBAC [säkerhetsadministratör](../role-based-access-control/built-in-roles.md#security-admin) rollen så att de kan visa säkerhetsprinciper, security tillstånd, redigera säkerhetsprinciper, Visa aviseringar och rekommendationer och stänga aviseringar och rekommendationer. Du kan göra detta med hjälp av hanteringsgruppen roten eller hanteringsgruppen segment, beroende på omfattningen av ansvarsområden.

Organisationer som inte framtvingar åtkomstkontroll för data med hjälp av funktionerna som RBAC ger fler behörigheter än vad som krävs till sina användare. Detta kan leda till kompromettering av data genom att tillåta användare att komma åt typer av data (till exempel stora marknadsfördelar) som de inte ska ha.

## <a name="lower-exposure-of-privileged-accounts"></a>Lägre exponering av Privilegierade konton

Att skydda privilegierad är åtkomst ett viktigt första steg för att skydda företagets tillgångar. Minimera antalet personer som har åtkomst till säker information eller resurser minskar risken för att en obehörig användare få åtkomst eller en auktoriserad användare oavsiktligt påverkar en känsliga resurs.

Privilegierade konton är konton som administrerar och hanterar IT-system. Cyberhot angripare siktar in dessa konton för att få åtkomst till organisationens data och system. Om du vill skydda privilegierad åtkomst, bör du isolera konton och system från risk att utsättas för en obehörig användare.

Vi rekommenderar att du utvecklar och följer en översikt över för att skydda privilegierad åtkomst från cyberhot angripare. Information om hur du skapar en detaljerad översikt över för att skydda identiteter och åtkomst som hanteras eller som rapporteras i Azure AD, Microsoft Azure, Office 365 och andra molntjänster, granska [referensmaterialet för att skydda privilegierad åtkomst för hybrid- och distributioner i Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Följande sammanfattar de rekommenderade metoder som finns i [referensmaterialet för att skydda privilegierad åtkomst för hybrid- och distributioner i Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Bästa praxis**: Hantera, kontrollera och övervaka åtkomst till Privilegierade konton.   
**Information om**: Aktivera [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). När du har aktiverat Privileged Identity Management kan du ett meddelande om e-postmeddelanden för privilegierad åtkomst rollen ändringar. Dessa meddelanden ge en tidig varning när ytterligare användare läggs till i mycket Privilegierade roller i din katalog.

**Bästa praxis**: Se till att alla kritiska administratörskonton hanteras Azure AD-konton.
**Information om**: Ta bort några konsument-konton från kritiska administratörsroller (till exempel Microsoft-konton som hotmail.com, live.com och outlook.com).

**Bästa praxis**: Se till att alla kritiska administratörsroller ha ett separat konto för administrativa uppgifter för att undvika nätfiske och andra attacker för att angripa administrativa privilegier.
**Information om**: Skapa ett separat administratörskonto som har tilldelats behörighet att utföra administrativa uppgifter. Blockera användning av dessa administrativa konton för dagliga produktivitetsverktyg som Microsoft Office 365 e-post godtyckliga webbsurfning.

**Bästa praxis**: Identifiera och klassificera konton som är mycket Privilegierade roller.   
**Information om**: Visa användare som ingår i den globala administratören privilegierad rolladministratör och andra mycket Privilegierade roller efter att aktivera Azure AD Privileged Identity Management. Ta bort alla konton som inte längre behövs i de här rollerna och kategorisera de återstående konton som tilldelas till administrativa roller:

- Individuellt tilldelas till administrativa användare och kan användas för icke-administrativa syften (till exempel personliga e-post)
- Individuellt tilldelade till administrativa användare och avsedda för administrativa syften
- Delas mellan flera användare
- För åtkomst vid akutfall scenarier
- För automatiserade skript
- För externa användare

**Bästa praxis**: Implementera just-in-time ”(JIT) åtkomst till ytterligare sänka exponeringstiden för privilegier och öka din synlighet användningen av Privilegierade konton.   
**Information om**: Azure AD Privileged Identity Management kan du:

- Begränsa användare till att endast utföra på sina privilegier JIT.
- Tilldela roller för en förkortad varaktighet tryggt att privilegier återkallas automatiskt.

**Bästa praxis**: Definiera minst två konton för åtkomst vid akutfall.   
**Information om**: För åtkomst vid akutfall hjälpa organisationer begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket Privilegierade och tilldelas inte till enskilda individer. För åtkomst vid akutfall är begränsade till scenarier där normala administrativa konton inte kan användas. Organisationer måste begränsa användningen av nödfall konton endast nödvändiga lång tid.

Utvärdera de konton som är tilldelade eller berättigad för rollen som global administratör. Om du inte ser några endast molnbaserade konton med hjälp av den `*.onmicrosoft.com` domän (avsett för åtkomst vid akutfall), skapa dem. Mer information finns i [hantera åtkomst vid akutfall administratörskonton i Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md).

**Bästa praxis**: Ha en ”Bryt om” på plats i ett nödfall.
**Information om**: Följ stegen i [referensmaterialet för att skydda privilegierad åtkomst för hybrid- och distributioner i Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Bästa praxis**: Kräver alla kritiska administratörskonton vara lösenord utan (rekommenderas) eller kräva Multifaktorautentisering.
**Information om**: Använd den [Microsoft Authenticator-appen](../active-directory/authentication/howto-authentication-phone-sign-in.md) att logga in på alla Azure AD-konto utan lösenord. Som [Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator använder nyckel-baserad autentisering för att aktivera en autentiseringsuppgift för användare som är kopplad till en enhet och använder biometrisk autentisering eller en PIN-kod.

Kräv Azure Multi-Factor Authentication vid inloggning för alla enskilda användare som tilldelas permanent till en eller flera av Azure AD-administratörsroller: Global administratör, privilegierad Rolladministratör, Exchange Online-administratör och SharePoint Online-administratör. Aktivera [Multifaktorautentisering för dina administratörskonton](../active-directory/authentication/howto-mfa-userstates.md) och se till att du har registrerat konto administratörsanvändare.

**Bästa praxis**: Ha en administratörsarbetsstation där uppgifter inte är tillåtet (till exempel, bläddra och e-post) för kritiska administratörskonton. Detta skyddar dina administratörskonton från angreppsmetoder som använder Bläddra och e-post och avsevärt minska risken för att en större incident.
**Information om**: Använd en administratörsarbetsstation. Välj en säkerhetsnivå för arbetsstation:

- Mycket säker produktivitet enheter ger avancerad säkerhet för att bläddra bland och andra produktivitetsuppgifter.
- [Privilegierad åtkomst arbetsstationer (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) ger ett dedikerat operativsystem som är skyddat mot internetattacker och hotvektorer för känsliga uppgifter.

**Bästa praxis**: Avetablera administratörskonton när en medarbetare lämnar organisationen.
**Information om**: Har en process som inaktiverar eller tar bort administratörskonton när en medarbetare lämnar organisationen.

**Bästa praxis**: Testa regelbundet administratörskonton med hjälp av aktuella attackteknikerna.
**Information om**: Använda Office 365-Attack simulatorn eller en tredje part erbjuder att köra realistisk attackscenarier i din organisation. Detta kan hjälpa dig hitta sårbara användare innan en verklig attack inträffar.

**Bästa praxis**: Vidta åtgärder för att minimera de mest använda angripna teknikerna.  
**Information om**: [Identifiera Microsoft-konton i administrativa roller som måste stängas för att arbets- eller skolkonton](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Se till att separata användarkonton och e-post vidarebefordran för globala administratörskonton](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Kontrollera att lösenorden för administrativa konton nyligen har ändrats](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Aktivera synkronisering av lösenordshash](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Kräv Multifaktorautentisering för användare i alla Privilegierade roller samt exponerade användare](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Hämta din Office 365 skyddar Score (om du använder Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Läser du igenom informationen för Office 365 säkerhets- och (om du använder Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurera aktivitetsövervakning för Office 365 (om du använder Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Upprätta incident/emergency response plan ägare](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Skydda lokala Privilegierade administrativa konton](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Om du inte skyddar privilegierad åtkomst, kanske du upptäcker att du har för många användare i mycket Privilegierade roller och är mer sårbara för attacker. Skadliga aktörer, inklusive cyberhot angripare, ofta mål-administratörskonton och andra element för privilegierad åtkomst för att få åtkomst till känsliga data och system med hjälp av stöld av autentiseringsuppgifter.

## <a name="control-locations-where-resources-are-created"></a>Kontrollera platser där resurser skapas

Det är mycket viktigt att aktivera molnoperatörer att utföra uppgifter samtidigt som hindrar dem från icke-bakåtkompatibel konventioner som behövs för att hantera din organisations resurser. Organisationer som vill styra de platser där resurser skapas bör hårt koda de här platserna.

Du kan använda [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) att skapa säkerhetsprinciper vars definitioner beskriver åtgärder eller resurser som uttryckligen har nekats. Du kan tilldela dessa principdefinitioner i det önskade omfånget som prenumerationen, resursgruppen eller en enskild resurs.

> [!NOTE]
> Säkerhetsprinciper är inte samma som RBAC. De kan faktiskt använda RBAC för att auktorisera användare att skapa dessa resurser.
>
>

Organisationer som inte styr hur resurser skapas är svårare att användare som kan utnyttja tjänsten genom att skapa fler resurser än de behöver. Härdning av skapandeprocessen resurs är ett viktigt steg för att skydda ett scenario med flera innehavare.

## <a name="actively-monitor-for-suspicious-activities"></a>Övervaka aktivt för misstänkta aktiviteter

En aktiv identitet övervakningen kan snabbt identifiera misstänkt beteende och utlösa en avisering för vidare studier. I följande tabell visas två Azure AD-funktioner som hjälper organisationer att övervaka deras identiteter:

**Bästa praxis**: Har en metod för att identifiera:

- Försöker logga in [utan spåras](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Brute force](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) attacker mot ett visst konto.
- Försök att logga in från flera platser.
- Inloggningar från [enheter](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Misstänkta IP-adresser.

**Information om**: Använda Azure AD Premium [avvikelseidentifiering rapporter](../active-directory/active-directory-view-access-usage-reports.md). Har processer och procedurer på plats för IT-administratörer kan köra dessa rapporter dagligen eller på begäran (vanligtvis i ett scenario med incidenthantering).

**Bästa praxis**: Ha en aktiv övervakningssystemet som meddelar dig om riskerna och kan justera risknivå (hög, medel eller låg) för ditt företags behov.   
**Information om**: Använd [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), som flaggar aktuellt risker på sin egen instrumentpanel och skickar daglig sammanfattning av meddelanden via e-post. För att skydda din organisations identiteter, kan du konfigurera riskbaserade principer som automatiskt svarar på identifierade problem när en angiven risknivå har uppnåtts.

Organisationer som inte aktivt övervakar sina identitetssystem finns risken att användarens autentiseringsuppgifter har komprometterats. Organisationer kan inte utan kännedom att misstänkta aktiviteter som äger rum via dessa autentiseringsuppgifter kan minimera den här typen av hot.

## <a name="use-azure-ad-for-storage-authentication"></a>Använd Azure AD för autentisering för lagring
[Azure Storage](../storage/common/storage-auth-aad.md) stöder autentisering och auktorisering med Azure AD för Blob storage och Queue storage. Du kan använda den rollbaserad åtkomstkontrollen i Azure med Azure AD-autentisering, tilldela specifika behörigheter till användare, grupper och program ned till området för en enskild blob-behållare eller kön.

Vi rekommenderar att du använder [Azure AD för att autentisera åtkomst till lagring](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Nästa steg

Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.
