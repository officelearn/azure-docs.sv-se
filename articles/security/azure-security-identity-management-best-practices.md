---
title: "Azure identitet och säkerhetsmetoder | Microsoft Docs"
description: "Den här artikeln innehåller en uppsättning av bästa praxis för Identitetshantering och kontroll med hjälp av inbyggda funktioner i Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: 50f9073d3c35bd9dcfd826ff44e767fb69558757
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identitetshantering och åtkomst kontroll säkerhetsmetoder
Många Överväg identitet till att det nya lagret gräns för säkerhet kan ta över rollen ur ett traditionellt nätverk till Central. Den här utvecklingen av primära pivot för säkerhet kontrolleras och investeringar kommer från det faktum att nätverket ytgränser har blivit allt porös och den perimeterskydd får inte vara så effektiv som de en gång var före expanderingen av [BYOD ](http://aka.ms/byodcg) enheter och molnprogram.

I den här artikeln diskuteras en samling Azure Identitetshantering och säkerhetsmetoder för access control. Följande rekommendationer härleds från våra erfarenhet av [Azure AD](../active-directory/active-directory-whatis.md) och erfarenheter från kunder som dig själv.

För varje rekommenderar förklarar vi:

* Vad som är bästa praxis
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att aktivera bästa praxis

Den här Azure Identitetshantering och säkerhet för åtkomstkontroll artikel om bästa praxis är baserad på en konsensus åsikt och Azure plattformsfunktioner och funktioner, eftersom de finns vid tidpunkten som den här artikeln skrevs. Åsikter och tekniker ändras med tiden och den här artikeln kommer att uppdateras regelbundet så att dessa ändringar.

Azure identity management och åtkomst kontroll säkerhetsmetoder i den här artikeln omfattar:

* Centralisera din Identitetshantering
* Aktivera enkel inloggning (SSO)
* Distribuera lösenordshantering
* Använda multifaktorautentisering (MFA) för användare
* Använd rollbaserad åtkomstkontroll (RBAC)
* Kontrollen platser där resurser skapas med resource manager
* Guide för utvecklare kan utnyttja identitetsfunktioner för SaaS-appar
* Övervaka aktivt för misstänkta aktiviteter

## <a name="centralize-your-identity-management"></a>Centralisera din Identitetshantering
Ett viktigt steg mot att skydda din identitet är att säkerställa att IT kan hantera konton från en enda plats om där det här kontot har skapats. Även flesta företag IT-organisationer har sina primära konto directory lokalt, hybrid cloud distributioner är ökar och det är viktigt att du förstår hur du integrerar lokalt och molnet kataloger och ger en sömlös upplevelse för slutanvändaren.

Att åstadkomma detta [hybrididentitet](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) scenariot rekommenderar vi två alternativ:

* Synkronisera din lokala katalog med din molnkatalog med Azure AD Connect
* Federera din lokala identitet med en molnbaserad katalog med hjälp av [Active Directory Federation Services](https://msdn.microsoft.com/library/bb897402.aspx) (AD FS)

Organisationer som misslyckas att integrera sin lokala identitet med sina molnidentitet får bättre administrativa kostnader i Hantera konton, vilket ökar sannolikheten för fel och säkerhetsintrång.

Mer information om Azure AD-synkronisering finns i artikel [integrera dina lokala identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Aktivera enkel inloggning (SSO)
När du har flera kataloger för att hantera detta blir ett administrativa problem inte bara för IT-avdelningen, utan även för användare som behöver komma ihåg flera lösenord. Med hjälp av [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) du ger användarna möjligheten för använder samma uppsättning autentiseringsuppgifter för att logga in och komma åt de resurser som de behöver, oavsett om den här resursen finnas lokalt eller i molnet.

Använda SSO för att ge användare åtkomst till sina [SaaS-program](../active-directory/active-directory-appssoaccess-whatis.md) baserat på deras organisationens konto i Azure AD. Detta gäller inte bara för Microsoft SaaS-appar, men även andra appar som [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) och [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Programmet kan konfigureras för att använda Azure AD som en [SAML-baserade identitet](../active-directory/fundamentals-identity.md) provider. Azure AD utfärdar inte en token så att de kan logga in på programmet, om de har beviljats åtkomst med hjälp av Azure AD som en säkerhetskontroll. Du kan bevilja åtkomst direkt eller via en grupp att de är medlem i.

> [!NOTE]
> beslutet att använda enkel inloggning påverkar hur du integrerar din lokala katalog med din molnkatalog. Om du vill SSO, behöver du använda federation, eftersom katalogsynkronisering ger endast [samma inloggning](../active-directory/active-directory-aadconnect.md).
>
>

Organisationer som inte behöver använda enkel inloggning för sina användare och program visas fler scenarier där användarna har flera lösenord som direkt ökar sannolikheten för användare återanvända lösenord eller med svaga lösenord.

Du kan lära dig mer om Azure AD SSO genom att läsa artikeln [AD FS-hantering och anpassning med Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Distribuera lösenordshantering
I scenarier där du har flera innehavare eller där du vill att användarna ska kunna [återställa sina egna lösenord](../active-directory/active-directory-passwords-update-your-own-password.md), är det viktigt att du använder lämpliga säkerhetsprinciper för att förhindra missbruk. I Azure kan du utnyttja återställning av lösenord för självbetjäning och anpassa säkerhetsalternativen för att uppfylla dina affärsbehov.

Det är särskilt viktigt att få feedback från dessa användare och sig av sina erfarenheter när de försöker utföra dessa steg. Baserat på dessa upplevelser, utveckla en plan för att minimera potentiella problem som kan uppstå under distributionen av en större grupp. Det rekommenderas också att du använder den [lösenord återställer registrering aktivitetsrapport](../active-directory/active-directory-passwords-get-insights.md) att övervaka de användare som registrerar.

Organisationer som vill undvika lösenord ändra supportsamtal men att användarna ska kunna återställa sina lösenord är svårare att ett ökat anrop till helpdesk på grund av lösenordsproblem med. I organisationer som har flera klienter, är det viktigt att du implementera den här typen av kapacitet och användarna kan utföra i säkerhetsgränser som fastställdes i säkerhetsprincipen för återställning av lösenord.

Du kan lära dig mer om lösenordsåterställning genom att läsa artikeln [distribuera lösenordshantering och utbilda användare att använda den](../active-directory/active-directory-passwords-best-practices.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Använda multifaktorautentisering (MFA) för användare
För organisationer som måste vara kompatibel med branschstandarder, t.ex [PCI DSS version 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), Multi-Factor authentication är ett måste ha funktionen för att autentisera användare. Utöver att vara kompatibel med branschstandarder, tillämpa MFA för att autentisera användare kan också hjälpa organisationer att minska stöld autentiseringstypen av angrepp, t.ex [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Genom att aktivera Azure MFA för dina användare kan du lägger till ett andra säkerhetslager till användarinloggningar och transaktioner. I det här fallet en transaktion kan att komma åt ett dokument som finns på en filserver eller i SharePoint Online. Azure MFA hjälper även IT att minska sannolikheten att avslöjade autentiseringsuppgifter kommer att ha åtkomst till organisationens data.

Exempel: du införa Azure MFA för användarna och konfigurera den att använda ett telefonsamtal eller SMS som verifiering. Om användarens autentiseringsuppgifter komprometteras kan angripare inte åtkomst till alla resurser eftersom han inte har åtkomst till användarens telefon. Organisationer som inte lägga till extra skyddslager identitet är mer känslig för autentiseringsuppgifter attack med lösenordsstöld, vilket kan leda till röjande av data.

Ett alternativ för organisationer som vill ha hela autentiseringen kontrollen lokal är att använda [Azure Multi-Factor Authentication-servern](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), kallas även MFA lokala. Med den här metoden kan du fortfarande att kunna använda multifaktorautentisering, samtidigt som den MFA-server lokalt.

Mer information om Azure MFA finns i artikel [komma igång med Azure Multi-Factor Authentication i molnet](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Använd rollbaserad åtkomstkontroll (RBAC)
Begränsa åtkomst baserat på de [behöver](https://en.wikipedia.org/wiki/Need_to_know) och [minsta privilegium](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Azure rollbaserad åtkomstkontroll (RBAC) kan användas för att tilldela behörigheter till användare, grupper och program för ett visst område. Omfånget för en rolltilldelning kan vara en prenumeration, resursgrupp eller en enskild resurs.

Du kan utnyttja [inbyggda RBAC](../active-directory/role-based-access-built-in-roles.md) roller i Azure för att tilldela behörigheter till användare. Överväg att använda *Storage-konto deltagare* för molnoperatörer som behöver kunna hantera storage-konton och *klassiska Storage-konto deltagare* att hantera klassiska lagringskonton. Överväg att lägga till dem för molnoperatörer som behöver hantera virtuella datorer och storage-kontot, *Virtual Machine-deltagare* roll.

Organisationer som inte behöver använda data åtkomstkontroll genom att utnyttja funktioner, till exempel RBAC kan ger fler behörigheter än vad som krävs för sina användare. Detta kan leda till data röjande av ge användarna åtkomst till vissa typer av datatyper (t.ex. stora marknadsfördelar) som de inte borde ha i första hand.

Du kan lära dig mer om Azure RBAC genom att läsa artikeln [rollbaserad åtkomstkontroll i](../active-directory/role-based-access-control-configure.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Kontrollen platser där resurser skapas med resource manager
Det är mycket viktigt att aktivera molnoperatörer att utföra uppgifter samtidigt som hindrar dem från att bryta konventioner som behövs för att hantera din organisations resurser. Organisationer som vill styra de platser där resurser skapas bör hård code dessa platser.

För att uppnå kan organisationer skapa säkerhetsprinciper som har definitioner som beskriver åtgärder eller resurser som uttryckligen har nekats. Du tilldelar dessa principdefinitioner i det önskade omfånget, till exempel prenumerationen, resursgruppen eller en enskild resurs.

> [!NOTE]
> Det är inte samma som RBAC, den faktiskt använder RBAC för att autentisera användare som har behörighet att skapa dessa resurser.
>
>

Utnyttjar [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) att skapa anpassade principer också för scenarier där organisationen vill tillåter åtgärder endast när lämpliga kostnadsstället associeras, annars kommer de att neka begäran.

Organisationer som inte styr hur du skapar resurser som är svårare att användare som kan missbruka tjänsten genom att skapa fler resurser än de behöver. Härdning av resurs-processen är ett viktigt steg i att skydda ett scenario med flera innehavare.

Du kan lära dig mer om hur du skapar principer med Azure Resource Manager genom att läsa artikeln [använda princip för att hantera resurser och åtkomstkontroll](../azure-resource-manager/resource-manager-policy.md).

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Guide för utvecklare kan utnyttja identitetsfunktioner för SaaS-appar
Användaridentitet ska utnyttjas i många situationer när användare har åtkomst till [SaaS-appar](https://azure.microsoft.com/marketplace/active-directory/all/) som kan integreras med lokala eller molnbaserade directory. Först och främst måste vi rekommenderar att utvecklare använda en säker metod för att utveckla apparna, t.ex [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Azure AD förenklar autentisering för utvecklare genom att tillhandahålla identitet som en tjänst med stöd för standardiserade protokoll som [OAuth 2.0](http://oauth.net/2/) och [OpenID Connect](http://openid.net/connect/), samt öppen källkod bibliotek för olika plattformar.

Se till att registrera alla program som outsources autentisering till Azure AD, detta är en obligatorisk procedur. Det beror på bakom detta eftersom Azure AD kräver att samordna kommunikationen med programmet när du hanterar inloggning (SSO) eller byta ut tokens. Användarens session upphör att gälla när livslängd för token som utfärdas av Azure AD upphör att gälla. Utvärdera alltid om programmet ska använda den här gången eller om du kan minska den här gången. Minska livstid kan fungera som en säkerhetsåtgärd som kan tvinga användare att logga ut baserat på en period av inaktivitet.

Organisationer som inte behöver använda identity-kontroll för att komma åt appar och inte leder utvecklarens om hur du integrerar appar på ett säkert sätt med deras identity management-system kan vara svårare att autentiseringstyp stöld av angrepp, t.ex [svaga autentisering och session management som beskrivs i öppna Web Application säkerhet projekt (OWASP) topp 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Du kan lära dig mer om autentiseringsscenarier för SaaS-appar genom att läsa [Autentiseringsscenarier för Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Övervaka aktivt för misstänkta aktiviteter
Enligt [Verizon 2016 Data intrång rapporten](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), avslöjade autentiseringsuppgifter är fortfarande i ökar och blir en av de mest lönsamma företag för cyber tjuvar. Därför är det viktigt att har en aktiv identitet övervakaren system som snabbt kan identifiera misstänkt beteendeaktivitet och utlösa en avisering om ytterligare undersökning. Azure AD har två viktiga funktioner som hjälper organisationer att övervaka sina identiteter: Azure AD Premium [avvikelseidentifiering rapporter](../active-directory/active-directory-view-access-usage-reports.md) och Azure AD [identitetsskydd](../active-directory/active-directory-identityprotection.md) kapaciteten.

Se till att använda rapporter för avvikelseidentifiering för att identifiera försöker logga in [utan som spåras](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [brute force](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) attacker mot ett särskilt konto, försöker logga in från flera platser, logga in från [infekterade enheter](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) och misstänkta IP-adresser. Tänk på att de är rapporter. Du måste alltså ha processer och procedurer för IT-administratörer att köra rapporterna på daglig basis eller på begäran (vanligtvis i ett scenario för incidenter).

Däremot Azure AD identity protection är en aktiv övervakningssystemet och den kommer flaggan aktuella risker på sin egen instrumentpanel. Förutom att får du även dagliga sammanfattning meddelanden via e-post. Vi rekommenderar att du justerar risknivån enligt dina affärsbehov. Risknivå för en risk är en indikation (hög, medel eller låg) på allvarlighetsgrad för händelsen risk. Risknivån hjälper användarna att identitetsskydd prioritera de åtgärder som de måste göra för att minska risken för organisationen.

Organisationer som inte aktivt övervakar sina identitetssystem finns risk för att användarens autentiseringsuppgifter komprometteras. Placera utan vetskap misstänkta aktiviteter tar med dessa autentiseringsuppgifter, organisationer kommer inte att kunna undvika den här typen av hot.
Du kan lära dig mer om Azure Identity protection genom att läsa [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
