---
title: Metodtips för Azure-identitet & åtkomstsäkerhet | Microsoft-dokument
description: Den här artikeln innehåller en uppsättning metodtips för identitetshantering och åtkomstkontroll med hjälp av inbyggda Azure-funktioner.
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73053346"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management och regelverk kring åtkomstkontroll och säkerhet

I den här artikeln diskuterar vi en samling azure-identitetshantering och åtkomstkontroll säkerhetspraxis. Dessa metodtips härleds från vår erfarenhet av [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) och erfarenheter från kunder som du själv.

För varje bästa praxis förklarar vi:

* Vad bästa praxis är
* Varför du vill aktivera den bästa metoden
* Vad kan bli resultatet om du inte aktiverar bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att göra det bästa sättet att öva

Den här azure-identitetshantering och åtkomstkontroll säkerhet bästa praxis artikeln bygger på en konsensus yttrande och Azure plattform funktioner och funktionsuppsättningar, som de finns när den här artikeln skrevs.

Avsikten med att skriva den här artikeln är att tillhandahålla en allmän färdplan för en mer robust säkerhetsposition efter driftsättning som styrs av vår checklista "[5 för att skydda din identitetsinfrastruktur](steps-secure-identity.md)" som går igenom några av våra kärnfunktioner och tjänster.

Åsikter och teknik förändras med tiden och denna artikel kommer att uppdateras regelbundet för att återspegla dessa förändringar.

Azure identity management and access control security best practices som beskrivs i den här artikeln är:

* Behandla identitet som den primära säkerhetsperimetern
* Centralisera identitetshantering
* Hantera anslutna klienter
* Aktivera enkel inloggning
* Aktivera villkorlig åtkomst
* Planera för rutinmässiga säkerhetsförbättringar
* Aktivera lösenordshantering
* Framtvinga flerfaktorsverifiering för användare
* Använd rollbaserad åtkomstkontroll
* Lägre exponering av privilegierade konton
* Styra platser där resurser finns
* Använda Azure AD för lagringsautentisering

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Behandla identitet som den primära säkerhetsperimetern

Många anser att identitet är den primära perimetern för säkerhet. Detta är en övergång från det traditionella fokuset på nätverkssäkerhet. Nätverksper perimeterer blir allt mer porösa, och att perimeterförsvaret inte kan vara lika effektivt som det var innan explosionen av [BYOD-enheter](https://aka.ms/byodcg) och molnprogram.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) är Azure-lösningen för identitets- och åtkomsthantering. Azure AD är en multitenant, molnbaserad katalog- och identitetshanteringstjänst från Microsoft. Den kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd i en och samma lösning.

I följande avsnitt visas metodtips för identitets- och åtkomstsäkerhet med Hjälp av Azure AD.

**Bästa praxis:** Centrera säkerhetskontroller och identifieringar kring användar- och tjänstidentiteter.
**Detalj**: Använd Azure AD för att samlokalisering av kontroller och identiteter.

## <a name="centralize-identity-management"></a>Centralisera identitetshantering

I ett [hybrididentitetsscenario](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) rekommenderar vi att du integrerar dina lokala kataloger och molnkataloger. Integrering gör det möjligt för IT-teamet att hantera konton från en plats, oavsett var ett konto skapas. Integrering hjälper också användarna att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst till både molnresurser och lokala resurser.

**Bästa praxis:** Upprätta en enda Azure AD-instans. Konsekvens och en enda auktoritativa källor kommer att öka tydligheten och minska säkerhetsriskerna från mänskliga fel och konfigurationskomplexitet.
**Detalj**: Ange en enda Azure AD-katalog som auktoritär källa för företags- och organisationskonton.

**Bästa praxis:** Integrera dina lokala kataloger med Azure AD.  
**Detalj:** Använd [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) för att synkronisera din lokala katalog med din molnkatalog.

> [!Note]
> Det finns [faktorer som påverkar prestanda för Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Se till att Azure AD Connect har tillräcklig kapacitet för att hålla underpresterande system från att hindra säkerhet och produktivitet. Stora eller komplexa organisationer (organisationer som etablerar mer än 100 000 objekt) bör följa [rekommendationerna](../../active-directory/hybrid/whatis-hybrid-identity.md) för att optimera deras Azure AD Connect-implementering.

**Bästa praxis:** Synkronisera inte konton till Azure AD som har hög behörighet i din befintliga Active Directory-instans.
**Detalj:** Ändra inte standardkonfigurationen för [Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) som filtrerar bort dessa konton. Den här konfigurationen minskar risken för att motståndare svänger från molnet till lokala tillgångar (vilket kan skapa en större incident).

**Bästa praxis:** Aktivera synkronisering av lösenord hash-synkronisering.  
**Detalj:** Synkronisering av lösenordshã¤nde är en funktion som används för att synkronisera användarlösenordshÃ¤nde från en lokal Active Directory-instans till en molnbaserad Azure AD-instans. Den här synkroniseringen hjälper till att skydda mot läckta autentiseringsuppgifter som spelas upp igen från tidigare attacker.

Även om du bestämmer dig för att använda federation med AD FS (Active Directory Federation Services) eller andra identitetsleverantörer kan du eventuellt ställa in synkronisering av lösenordshÃ¤ngning som en säkerhetskopia om dina lokala servrar misslyckas eller blir tillfälligt otillgängliga. Med den här synkroniseringen kan användare logga in på tjänsten med samma lösenord som de använder för att logga in på sin lokala Active Directory-instans. Det gör det också möjligt för Identity Protection att identifiera komprometterade autentiseringsuppgifter genom att jämföra synkroniserade lösenord hashar med lösenord som är kända för att äventyras, om en användare har använt samma e-postadress och lösenord på andra tjänster som inte är anslutna till Azure AD.

Mer information finns i [Implementera synkronisering av lösenordsh hash med Azure AD Connect-synkronisering](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Bästa praxis**: Använd Azure AD för autentisering för ny programutveckling.
**Detalj**: Använd rätt funktioner för att stödja autentisering:

  - Azure AD för anställda
  - [Azure AD B2B](../../active-directory/b2b/index.yml) för gästanvändare och externa partner
  - [Azure AD B2C](../../active-directory-b2c/index.yml) för att styra hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program

Organisationer som inte integrerar sin lokala identitet med sin molnidentitet kan ha mer information om hur du hanterar konton. Denna overhead ökar sannolikheten för misstag och säkerhetsöverträdelser.

> [!Note]
> Du måste välja vilka kataloger kritiska konton ska finnas i och om administratörsarbetsstationen som används hanteras av nya molntjänster eller befintliga processer. Om du använder befintliga hanterings- och identitetsetableringsprocesser kan vissa risker minska, men det kan också skapa en risk för att en angripare äventyrar ett lokalt konto och pivoterar till molnet. Du kanske vill använda en annan strategi för olika roller (till exempel IT-administratörer jämfört med affärsenhetsadministratörer). Du har två alternativ. Det första alternativet är att skapa Azure AD-konton som inte är synkroniserade med din lokala Active Directory-instans. Gå med i administratörsarbetsstationen till Azure AD, som du kan hantera och korrigera med hjälp av Microsoft Intune. Det andra alternativet är att använda befintliga administratörskonton genom att synkronisera till den lokala Active Directory-instansen. Använd befintliga arbetsstationer i Active Directory-domänen för hantering och säkerhet.

## <a name="manage-connected-tenants"></a>Hantera anslutna klienter
Din säkerhetsorganisation behöver synlighet för att bedöma risker och för att avgöra om organisationens principer och eventuella regelkrav följs. Du bör se till att din säkerhetsorganisation har insyn i alla prenumerationer som är anslutna till din produktionsmiljö och ditt nätverk (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) eller [plats-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). En [global administratör/företagsadministratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) i Azure AD kan öka deras åtkomst till rollen [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) och se alla prenumerationer och hanterade grupper som är anslutna till din miljö.

Se [öka åtkomsten för att hantera alla Azure-prenumerationer och hanteringsgrupper](../../role-based-access-control/elevate-access-global-admin.md) för att säkerställa att du och din säkerhetsgrupp kan visa alla prenumerationer eller hanteringsgrupper som är anslutna till din miljö. Du bör ta bort den här förhöjda åtkomsten när du har bedömt riskerna.

## <a name="enable-single-sign-on"></a>Aktivera enkel inloggning

I en första värld med mobilt, första molnet vill du aktivera enkel inloggning (SSO) på enheter, appar och tjänster var som helst så att användarna kan vara produktiva var och när som helst. När du har flera identitetslösningar att hantera blir detta ett administrativt problem inte bara för IT utan även för användare som måste komma ihåg flera lösenord.

Genom att använda samma identitetslösning för alla dina appar och resurser kan du uppnå SSO. Och användarna kan använda samma uppsättning autentiseringsuppgifter för att logga in och komma åt de resurser som de behöver, oavsett om resurserna finns lokalt eller i molnet.

**Bästa praxis**: Aktivera SSO.  
**Detalj**: Azure AD [utökar lokal Active Directory](/azure/active-directory/connect/active-directory-aadconnect) till molnet. Användare kan använda sitt primära arbets- eller skolkonto för sina domänanslutna enheter, företagsresurser och alla webb- och SaaS-program som de behöver för att få sina jobb gjorda. Användare behöver inte komma ihåg flera uppsättningar användarnamn och lösenord, och deras programåtkomst kan automatiskt etableras (eller avetableras) baserat på deras organisationsgruppmedlemskap och deras status som anställd. Och du kan kontrollera den åtkomsten för galleriappar eller för dina egna lokala appar som du har utvecklat och publicerat genom [Azure AD-programproxy](/azure/active-directory/active-directory-application-proxy-get-started).

Använd SSO för att göra det möjligt för användare att komma åt sina [SaaS-program](/azure/active-directory/active-directory-appssoaccess-whatis) baserat på deras arbets- eller skolkonto i Azure AD. Detta gäller inte bara för Microsoft SaaS-appar, utan även för andra appar, till exempel [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) och [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). Du kan konfigurera ditt program så att det använder Azure AD som en [SAML-baserad](/azure/active-directory/fundamentals-identity) identitetsprovider. Som en säkerhetskontroll utfärdar Azure AD inte en token som tillåter användare att logga in på programmet om de inte har beviljats åtkomst via Azure AD. Du kan bevilja åtkomst direkt eller via en grupp som användarna är medlemmar i.

Organisationer som inte skapar en gemensam identitet för att upprätta SSO för sina användare och program är mer utsatta för scenarier där användare har flera lösenord. Dessa scenarier ökar sannolikheten för att användare återanvänder lösenord eller använder svaga lösenord.

## <a name="turn-on-conditional-access"></a>Aktivera villkorlig åtkomst

Användare kan komma åt organisationens resurser med hjälp av en mängd olika enheter och appar var som helst. Som IT-administratör vill du se till att dessa enheter uppfyller dina standarder för säkerhet och efterlevnad. Bara fokusera på vem som kan komma åt en resurs är inte tillräckligt längre.

För att balansera säkerhet och produktivitet måste du tänka på hur en resurs nås innan du kan fatta ett beslut om åtkomstkontroll. Med Azure AD Villkorlig åtkomst kan du åtgärda detta krav. Med villkorlig åtkomst kan du fatta beslut om automatisk åtkomstkontroll baserat på villkoren för åtkomst till dina molnappar.

**Bästa praxis**: Hantera och kontrollera åtkomsten till företagets resurser.  
**Detalj:** Konfigurera Azure [AD-villkorlig åtkomst](/azure/active-directory/active-directory-conditional-access-azure-portal) baserat på en grupp, plats och programkänslighet för SaaS-appar och Azure AD-anslutna appar.

**Bästa praxis**: Blockera äldre autentiseringsprotokoll.
**Detalj**: Angripare utnyttja svagheter i äldre protokoll varje dag, särskilt för lösenord spray attacker. Konfigurera villkorlig åtkomst för att blockera äldre protokoll. Mer information finns i videon [Azure AD: Do's and Don'ts.](https://www.youtube.com/watch?v=wGk0J4z90GI)

## <a name="plan-for-routine-security-improvements"></a>Planera för rutinmässiga säkerhetsförbättringar

Säkerhet utvecklas alltid och det är viktigt att bygga in ett sätt att regelbundet visa tillväxt och upptäcka nya sätt att skydda din miljö i ditt ramverk för moln- och identitetshantering.

Identity Secure Score är en uppsättning rekommenderade säkerhetskontroller som Microsoft publicerar som fungerar för att ge dig en numerisk poäng för att objektivt mäta din säkerhetsposition och hjälpa till att planera framtida säkerhetsförbättringar. Du kan också visa dina poäng i jämförelse med dem i andra branscher samt dina egna trender över tiden.

**Bästa praxis**: Planera rutinmässiga säkerhetsgranskningar och förbättringar baserat på bästa praxis i din bransch.
**Detalj:** Använd funktionen Identitetssäker poäng för att rangordna dina förbättringar över tid.

## <a name="enable-password-management"></a>Aktivera lösenordshantering

Om du har flera klienter eller om du vill att användarna ska [kunna återställa sina egna lösenord](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)är det viktigt att du använder lämpliga säkerhetsprinciper för att förhindra missbruk.

**Bästa praxis:** Konfigurera självbetjäningslösenordsåterställning (SSPR) för användarna.  
**Detalj:** Använd azure [AD-funktionen för återställning av lösenord för självbetjäning.](/azure/active-directory-b2c/active-directory-b2c-reference-sspr)

**Bästa praxis**: Övervaka hur eller om SSPR verkligen används.  
**Detalj:** Övervaka de användare som registrerar sig med hjälp av azure AD [password reset registration activity.](/azure/active-directory/active-directory-passwords-get-insights) Rapporteringsfunktionen som Azure AD tillhandahåller hjälper dig att svara på frågor med hjälp av färdiga rapporter. Om du är licensierad på rätt sätt kan du också skapa anpassade frågor.

**Bästa praxis**: Utöka molnbaserade lösenordsprinciper till din lokala infrastruktur.
**Detalj:** Förbättra lösenordsprinciperna i organisationen genom att utföra samma kontroller för lokala lösenordsändringar som du gör för molnbaserade lösenordsändringar. Installera [Azure AD-lösenordsskydd](/azure/active-directory/authentication/concept-password-ban-bad) för Windows Server Active Directory-agenter lokalt för att utöka förbjudna lösenordslistor till din befintliga infrastruktur. Användare och administratörer som ändrar, anger eller återställer lösenord lokalt måste följa samma lösenordsprincip som användare som endast är molnet.

## <a name="enforce-multi-factor-verification-for-users"></a>Framtvinga flerfaktorsverifiering för användare

Vi rekommenderar att du behöver tvåstegsverifiering för alla dina användare. Detta inkluderar administratörer och andra i organisationen som kan ha en betydande inverkan om deras konto äventyras (till exempel ekonomiansvariga).

Det finns flera alternativ för att kräva tvåstegsverifiering. Det bästa alternativet för dig beror på dina mål, Azure AD-utgåvan du kör och ditt licensprogram. Se [Så här kräver du tvåstegsverifiering för att en användare](/azure/active-directory/authentication/howto-mfa-userstates) ska kunna avgöra vilket alternativ som är bäst för dig. Mer information om licenser och priser finns på prissidorna för [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) och Azure Multi [Factor Authentication.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Följande är alternativ och fördelar för att aktivera tvåstegsverifiering:

**Alternativ 1:** Aktivera MFA för alla användare och inloggningsmetoder med Azure AD Security Defaults **Benefit:** Med det här alternativet kan du enkelt och snabbt tillämpa MFA för alla användare i din miljö med en strikt princip för att:

* Utmana administrativa konton och mekanismer för administrativ inloggning
* Kräv MFA-utmaning via Microsoft Authenticator för alla användare
* Begränsa äldre autentiseringsprotokoll.

Den här metoden är tillgänglig för alla licensieringsnivåer men kan inte blandas med befintliga principer för villkorlig åtkomst. Du hittar mer information i Azure AD Security Defaults

**Alternativ 2:** [Aktivera multifaktorautentisering genom att ändra användartillstånd](../../active-directory/authentication/howto-mfa-userstates.md).   
**Fördel**: Detta är den traditionella metoden för att kräva tvåstegsverifiering. Det fungerar med både [Azure Multi-Factor Autentisering i molnet och Azure Multi-Factor Authentication Server](/azure/active-directory/authentication/concept-mfa-whichversion). Med den här metoden krävs att användare utför tvåstegsverifiering varje gång de loggar in och åsidosätter principer för villkorlig åtkomst.

Information om var multifaktorautentisering behöver aktiveras finns i [Vilken version av Azure MFA som är rätt för min organisation?](/azure/active-directory/authentication/concept-mfa-whichversion).

**Alternativ 3:** [Aktivera multifaktorautentisering med principen villkorlig åtkomst](/azure/active-directory/authentication/howto-mfa-getstarted).
**Fördel**: Med det här alternativet kan du fråga efter tvåstegsverifiering under särskilda förhållanden med hjälp av [villkorlig åtkomst](/azure/active-directory/active-directory-conditional-access-azure-portal). Specifika villkor kan vara användarloggning från olika platser, ej betrodda enheter eller program som du anser vara riskfyllda. Genom att definiera specifika villkor där du behöver tvåstegsverifiering kan du undvika konstant förfrågande för användarna, vilket kan vara en obehaglig användarupplevelse.

Det här är det mest flexibla sättet att aktivera tvåstegsverifiering för användarna. Aktivera en princip för villkorlig åtkomst fungerar bara för Azure Multi-Factor Authentication i molnet och är en premiumfunktion i Azure AD. Du hittar mer information om den här metoden i [Distribuera molnbaserad Azure Multi-Factor Authentication](/azure/active-directory/authentication/howto-mfa-getstarted).

**Alternativ 4:** Aktivera multifaktorautentisering med principer för villkorlig åtkomst genom att utvärdera användar- och inloggningsrisken för [Azure AD](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)Identity Protection .   
**Fördel**: Med det här alternativet kan du:

* Identifiera potentiella sårbarheter som påverkar organisationens identiteter.
* Konfigurera automatiska svar till identifierade misstänkta åtgärder som är relaterade till organisationens identiteter.
* Undersök misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem.

Den här metoden använder riskutvärderingen av Azure AD Identity Protection för att avgöra om tvåstegsverifiering krävs baserat på användar- och inloggningsrisk för alla molnprogram. Den här metoden kräver Azure Active Directory P2-licensiering. Du hittar mer information om den här metoden i [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Alternativ 1, som aktiverar multifaktorautentisering genom att ändra användartillståndet, åsidosätter principer för villkorlig åtkomst. Eftersom alternativ 2 och 3 använder principer för villkorlig åtkomst kan du inte använda alternativ 1 med dem.

Organisationer som inte lägger till extra lager av identitetsskydd, till exempel tvåstegsverifiering, är mer mottagliga för stöldskydd för autentiseringsuppgifter. En stöldattack för autentiseringsuppgifter kan leda till datakompromiss.

## <a name="use-role-based-access-control"></a>Använd rollbaserad åtkomstkontroll

Åtkomsthantering för molnresurser är avgörande för alla organisationer som använder molnet. [Rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/overview)hjälper dig att hantera vem som har åtkomst till Azure-resurser, vad de kan göra med dessa resurser och vilka områden de har åtkomst till.

Att ange grupper eller enskilda roller som ansvarar för specifika funktioner i Azure hjälper till att undvika förvirring som kan leda till mänskliga fel och automatiseringsfel som skapar säkerhetsrisker. Att begränsa åtkomsten baserat på [behovet](https://en.wikipedia.org/wiki/Need_to_know) av att känna till och [minst privilegier](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är absolut nödvändigt för organisationer som vill genomdriva säkerhetsprinciper för dataåtkomst.

Ditt säkerhetsteam behöver insyn i dina Azure-resurser för att kunna bedöma och åtgärda risker. Om säkerhetsgruppen har operativt ansvar behöver de ytterligare behörigheter för att utföra sitt jobb.

Du kan använda [RBAC](/azure/role-based-access-control/overview) för att tilldela behörigheter till användare, grupper och program i ett visst omfång. Omfånget för en rolltilldelning kan vara en prenumeration, en resursgrupp eller en enskild resurs.

**Bästa praxis:** Segregera uppgifter inom ditt team och bevilja endast den mängd tillgång till användare som de behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter i din Azure-prenumeration eller dina Azure-resurser tillåter du bara vissa åtgärder i ett visst omfång.
**Detalj**: Använd [inbyggda RBAC-roller](/azure/role-based-access-control/built-in-roles) i Azure för att tilldela privilegier till användare.

> [!Note]
> Specifika behörigheter skapar onödiga komplexitet och förvirring, ackumuleras i en "äldre" konfiguration som är svårt att fixa utan rädsla för att bryta något. Undvik resursspecifika behörigheter. Använd i stället hanteringsgrupper för företagsomfattande behörigheter och resursgrupper för behörigheter inom prenumerationer. Undvik användarspecifika behörigheter. Tilldela istället åtkomst till grupper i Azure AD.

**Bästa praxis:** Bevilja säkerhetsteam med Azure-ansvarsåtkomst för att se Azure-resurser så att de kan bedöma och åtgärda risker.
**Detalj**: Bevilja säkerhetsteam rollen RBAC [Security Reader.](/azure/role-based-access-control/built-in-roles#security-reader) Du kan använda rothanteringsgruppen eller segmenthanteringsgruppen, beroende på ansvarsområdet:

* **Rothanteringsgrupp** för team som ansvarar för alla företagsresurser
* **Segmenthanteringsgrupp** för team med begränsad omfattning (vanligen på grund av regulatoriska eller andra organisatoriska gränser)

**Bästa praxis**: Bevilja lämpliga behörigheter till säkerhetsteam som har direkt operativt ansvar.
**Detalj**: Granska de inbyggda RBAC-rollerna för rätt rolltilldelning. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa [anpassade roller för Azure-resurser](/azure/role-based-access-control/custom-roles). Precis som med inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänsthuvudnamn vid prenumerations-, resursgrupps- och resursomfattningar.

**Metodtips:** Bevilja Azure Security Center åtkomst till säkerhetsroller som behöver det. Security Center gör det möjligt för säkerhetsteam att snabbt identifiera och åtgärda risker.
**Detalj:** Lägg till säkerhetsteam med dessa behov i [rbac-säkerhetsadministratörsrollen](/azure/role-based-access-control/built-in-roles#security-admin) så att de kan visa säkerhetsprinciper, visa säkerhetstillstånd, redigera säkerhetsprinciper, visa aviseringar och rekommendationer och avvisa aviseringar och rekommendationer. Du kan göra detta genom att använda rothanteringsgruppen eller segmenthanteringsgruppen, beroende på ansvarsområdet.

Organisationer som inte tillämpar dataåtkomstkontroll med hjälp av funktioner som RBAC kan ge fler privilegier än nödvändigt till sina användare. Detta kan leda till datakompromettering genom att tillåta användare att komma åt typer av data (till exempel hög affärspåverkan) som de inte borde ha.

## <a name="lower-exposure-of-privileged-accounts"></a>Lägre exponering av privilegierade konton

Att skydda privilegierad åtkomst är ett viktigt första steg för att skydda affärstillgångar. Om du minimerar antalet personer som har åtkomst till säker information eller resurser minskar risken för att en obehörig användare får åtkomst eller en behörig användare oavsiktligt påverkar en känslig resurs.

Privilegierade konton är konton som administrerar och hanterar IT-system. Cyberangripare riktar in sig på dessa konton för att få åtkomst till en organisations data och system. För att skydda privilegierad åtkomst bör du isolera konton och system från risken att utsättas för en obehörig användare.

Vi rekommenderar att du utvecklar och följer en färdplan för att skydda privilegierad åtkomst mot cyberattacker. Information om hur du skapar en detaljerad översikt för att skydda identiteter och åtkomst som hanteras eller rapporteras i Azure AD, Microsoft Azure, Office 365 och andra molntjänster finns i [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

Följande sammanfattar de bästa metoderna för [att skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD:](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Bästa praxis:** Hantera, kontrollera och övervaka åtkomst till privilegierade konton.   
**Detalj:** Aktivera [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). När du har aktiverat Privilegierad identitetshantering får du e-postmeddelanden om privilegierade rolländringar. Dessa meddelanden ger tidig varning när ytterligare användare läggs till i mycket privilegierade roller i katalogen.

**Bästa praxis:** Se till att alla kritiska administratörskonton hanteras Azure AD-konton.
**Information**: Ta bort alla konsumentkonton från kritiska administratörsroller (till exempel Microsoft-konton som hotmail.com, live.com och outlook.com).

**Bästa praxis**: Se till att alla kritiska administratörsroller har ett separat konto för administrativa uppgifter för att undvika nätfiske och andra attacker för att äventyra administrativa privilegier.
**Detalj**: Skapa ett separat administratörskonto som har tilldelats de privilegier som behövs för att utföra de administrativa uppgifterna. Blockera användningen av dessa administrativa konton för dagliga produktivitetsverktyg som Microsoft Office 365-e-post eller godtycklig surfning.

**Bästa praxis:** Identifiera och kategorisera konton som är i mycket privilegierade roller.   
**Detalj:** När du har aktiverat Azure AD Privileged Identity Management kan du visa de användare som finns i den globala administratören, administratören för privilegierade roller och andra mycket privilegierade roller. Ta bort alla konton som inte längre behövs i dessa roller och kategorisera de återstående konton som har tilldelats administratörsroller:

* Individuellt tilldelade till administrativa användare och kan användas för icke-administrativa ändamål (till exempel personlig e-post)
* Individuellt tilldelade till administrativa användare och endast avsedda för administrativa ändamål
* Delas mellan flera användare
* För scenarier för nödåtkomst
* För automatiserade skript
* För externa användare

**Bästa praxis:** Implementera "just in time" (JIT) åtkomst för att ytterligare sänka exponeringstiden för privilegier och öka din insyn i användningen av privilegierade konton.   
**Detalj:** Azure AD Privileged Identity Management kan du:

* Begränsa användare till att bara ta på sig sina privilegier JIT.
* Tilldela roller för en förkortad varaktighet med tillförsikt om att privilegierna återkallas automatiskt.

**Bästa praxis**: Definiera minst två konton för nödåtkomst.   
**Detalj:** Konton för nödåtkomst hjälper organisationer att begränsa privilegierad åtkomst i en befintlig Azure Active Directory-miljö. Dessa konton är mycket privilegierade och tilldelas inte till specifika personer. Konton för nödåtkomst är begränsade till scenarier där normala administrativa konton inte kan användas. Organisationer måste begränsa nödkontots användning till endast den tid som krävs.

Utvärdera de konton som har tilldelats eller är berättigade till den globala administratörsrollen. Om du inte ser några molnkonton med `*.onmicrosoft.com` hjälp av domänen (avsedd för nödåtkomst) skapar du dem. Mer information finns [i Hantera administrativa konton för nödåtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Bästa praxis**: Ha en "krossa glas" process på plats i händelse av en nödsituation.
**Detalj:** Följ stegen för [att skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

**Bästa praxis:** Kräv att alla kritiska administratörskonton ska vara lösenordslösa (föredras) eller kräva multifaktorautentisering.
**Detalj:** Använd [Microsoft Authenticator-appen](/azure/active-directory/authentication/howto-authentication-phone-sign-in) för att logga in på ett Azure AD-konto utan att använda ett lösenord. Precis som [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification)använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera en användarautentiseringsidentifiering som är knuten till en enhet och använder biometrisk autentisering eller en PIN-kod.

Kräv Azure Multi Factor-autentisering vid inloggning för alla enskilda användare som permanent tilldelas en eller flera av Azure AD-administratörsrollerna: Global administratör, Administratör för privilegierad roll, Exchange Online-administratör och SharePoint Online Administratör. Aktivera [multifaktorautentisering för dina administratörskonton](/azure/active-directory/authentication/howto-mfa-userstates) och se till att användare av administratörskonton har registrerat sig.

**Bästa praxis**: Ha en administratörsarbetsstation för kritiska administratörskonton där produktionsuppgifter inte är tillåtna (till exempel surfning och e-post). Detta skyddar dina administratörskonton från angreppsvektorer som använder surfning och e-post och avsevärt minskar risken för en större incident.
**Detalj**: Använd en administratörsarbetsstation. Välj en nivå av arbetsstationssäkerhet:

- Mycket säkra produktivitetsenheter ger avancerad säkerhet för surfning och andra produktivitetsuppgifter.
- [Arbetsstationer (Privileged Access Workstations)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) tillhandahåller ett dedikerat operativsystem som är skyddat mot internetattacker och hotvektorer för känsliga uppgifter.

**Bästa praxis:** Avetablera administratörskonton när anställda lämnar organisationen.
**Detalj**: Har en process på plats som inaktiverar eller tar bort administratörskonton när anställda lämnar organisationen.

**Bästa praxis:** Testa regelbundet administratörskonton med hjälp av aktuella attacktekniker.
**Detalj:** Använd Office 365 Attack Simulator eller ett erbjudande från tredje part för att köra realistiska attackscenarier i din organisation. Detta kan hjälpa dig att hitta sårbara användare innan en verklig attack inträffar.

**Bästa praxis:** Vidta åtgärder för att minska de mest använda attackerade teknikerna.  
**Detalj:** [Identifiera Microsoft-konton i administrativa roller som måste växlas till arbets- eller skolkonton](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Se till att separata användarkonton och vidarebefordran av e-post för globala administratörskonton](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Se till att lösenorden för administrativa konton nyligen har ändrats](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Aktivera synkronisering av lösenordsh hash](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Kräv multifaktorautentisering för användare i alla privilegierade roller samt exponerade användare](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Skaffa ditt säkra Office 365-resultat (om du använder Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Granska säkerhets- och efterlevnadsvägledningen för Office 365 (om du använder Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurera Aktivitetsövervakning för Office 365 (om office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Upprätta ägare av incident-/beredskapsplan](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Säkra lokala privilegierade administrativa konton](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Om du inte skyddar privilegierad åtkomst kan det hända att du har för många användare i mycket privilegierade roller och är mer sårbara för attacker. Skadliga aktörer, inklusive cyberattacker, riktar ofta in sig på administratörskonton och andra element med privilegierad åtkomst för att få åtkomst till känsliga data och system med hjälp av stöld av autentiseringsuppgifter.

## <a name="control-locations-where-resources-are-created"></a>Styra platser där resurser skapas

Det är mycket viktigt att göra det möjligt för molnoperatörer att utföra uppgifter samtidigt som de hindras från att bryta mot konventioner som behövs för att hantera organisationens resurser. Organisationer som vill styra de platser där resurser skapas bör hårdkoda dessa platser.

Du kan använda [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) för att skapa säkerhetsprinciper vars definitioner beskriver de åtgärder eller resurser som uttryckligen nekas. Du tilldelar dessa principdefinitioner i önskat omfång, till exempel prenumerationen, resursgruppen eller en enskild resurs.

> [!NOTE]
> Säkerhetsprinciper är inte samma sak som RBAC. De använder faktiskt RBAC för att tillåta användare att skapa dessa resurser.
>
>

Organisationer som inte kontrollerar hur resurser skapas är mer mottagliga för användare som kan missbruka tjänsten genom att skapa fler resurser än de behöver. Härdning av processen för att skapa resurser är ett viktigt steg för att skydda ett scenario med flera liggande.

## <a name="actively-monitor-for-suspicious-activities"></a>Övervakar aktivt misstänkta aktiviteter

Ett aktivt identitetsövervakningssystem kan snabbt identifiera misstänkt beteende och utlösa en avisering för vidare undersökning. I följande tabell visas två Azure AD-funktioner som kan hjälpa organisationer att övervaka sina identiteter:

**Bästa praxis**: Ha en metod för att identifiera:

- Försöker logga in [utan att spåras](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- [Brute force](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) attacker mot ett visst konto.
- Försöker logga in från flera platser.
- Inloggningar från [infekterade enheter](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Misstänkta IP-adresser.

**Detalj:** Använd Azure AD [Premium-avvikelserapporter](/azure/active-directory/active-directory-view-access-usage-reports). Ha processer och procedurer på plats för IT-administratörer att köra dessa rapporter dagligen eller på begäran (vanligtvis i ett incidentsvarsscenario).

**Bästa praxis**: Har ett aktivt övervakningssystem som meddelar dig om risker och kan justera risknivån (hög, medel eller låg) till dina affärsbehov.   
**Detalj**: Använd [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), som flaggar de aktuella riskerna på sin egen instrumentpanel och skickar dagliga sammanfattningsmeddelanden via e-post. För att skydda organisationens identiteter kan du konfigurera riskbaserade principer som automatiskt svarar på identifierade problem när en angiven risknivå har uppnåtts.

Organisationer som inte aktivt övervakar sina identitetssystem riskerar att komprometterade användaruppgifter. Utan vetskap om att misstänkta aktiviteter äger rum via dessa autentiseringsuppgifter kan organisationer inte minska den här typen av hot.

## <a name="use-azure-ad-for-storage-authentication"></a>Använda Azure AD för lagringsautentisering
[Azure Storage](/azure/storage/common/storage-auth-aad) stöder autentisering och auktorisering med Azure AD för Blob-lagring och kölagring. Med Azure AD-autentisering kan du använda Azure-rollbaserad åtkomstkontroll för att bevilja specifika behörigheter till användare, grupper och program ned till omfattningen av en enskild blob-behållare eller kö.

Vi rekommenderar att du använder [Azure AD för att autentisera åtkomst till lagring](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Nästa steg

Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.
