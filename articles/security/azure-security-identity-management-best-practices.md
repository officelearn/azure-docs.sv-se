---
title: Azure identitet och åtkomst säkerhetsmetoder | Microsoft Docs
description: Den här artikeln innehåller en uppsättning Metodtips för Identitetshantering och åtkomstkontroll med inbyggda funktioner i Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 210781b36f6215afc925266e597031d772a94002
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060560"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identitetshantering och åtkomstkontroll säkerhetsmetoder

Många Överväg identitet ska vara det nya gräns-lagret för säkerhet, tar över rollen traditionella nätverk-centric perspektiv. Den här utvecklingen av den primära pivot för säkerhet uppmärksamhet och investeringar kommer från det faktum att nätverket perimetrar har blivit allt högre grad porös och den perimeterskydd får inte vara så effektiv som de en gång var innan expanderingen av [BYOD ](http://aka.ms/byodcg) enheter och molnprogram.

I den här artikeln diskuterar vi en samling Azure identity management och säkerhetsmetoder för access control. Dessa metodtips härleds från vår erfarenhet av [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) och erfarenheter från kunder som dig själv.

För varje rekommenderar förklarar vi:

* Vad den bästa metoden är
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera den bästa metoden
* Möjliga alternativ till den bästa praxis
* Hur du kan lära dig att aktivera ett metodtips

Den här Azure identity management och säkerhet för åtkomstkontroll artikel om bästa praxis baseras på en konsensus åsikter och funktioner för Azure-plattformen och funktioner, som de finns när den här artikeln skrevs. Andras åsikter och tekniker som ändras med tiden och den här artikeln kommer att uppdateras regelbundet att återspegla dessa ändringar.

Azure identitet och kontroll säkerhetsmetoder beskrivs i den här artikeln är:

* Centralisera dina Identitetshantering
* Aktivera enkel inloggning (SSO)
* Distribuera lösenordshantering
* Använda multifaktorautentisering (MFA) för användare
* Använda rollbaserad åtkomstkontroll (RBAC)
* Kontrollera platser där resurser skapas med Resource Manager
* Guide för utvecklare kan utnyttja funktioner för Identitetshantering för SaaS-appar
* Övervaka aktivt för misstänkta aktiviteter

## <a name="centralize-your-identity-management"></a>Centralisera dina Identitetshantering

Ett viktigt steg mot att skydda din identitet är att säkerställa att IT-avdelningen kan hantera konton från en enda plats om där det här kontot har skapats. Även om flesta företag IT-organisationer har sina primära konto directory lokalt, hybridmoln är ökar och det är viktigt att du förstår hur du integrerar lokala och kataloger i molnet och ger en sömlös upplevelse för slutanvändaren.

Att åstadkomma detta [hybrididentitet](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) scenario rekommenderar vi två alternativ:

* Synkronisera din lokala katalog med din molnkatalog med Azure AD Connect
* Aktivera enkel inloggning med [lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) eller samköra dina lokala identiteten med din cloud directory med hjälp av [Active Directory Federationstjänster](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

Organisationer som inte vill integrera sin lokala identitet med sina molnidentitet uppleva ökad administrationskostnader hantera konton, vilket ökar sannolikheten för fel och säkerhetsintrång.

Mer information om Azure AD-synkronisering, finns i artikeln [integrera dina lokala identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Aktivera enkel inloggning (SSO)

När du har flera kataloger att hantera detta blir ett administrativa problem inte bara för IT-avdelningen, utan även för slutanvändare som behöver komma ihåg flera lösenord. Med hjälp av [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) du ge användare möjlighet att använda samma uppsättning autentiseringsuppgifter att logga in och komma åt resurser som de behöver, oavsett om den här resursen är finnas lokalt eller i molnet.

Använda SSO för att ge användare åtkomst till sina [SaaS-program](../active-directory/manage-apps/what-is-single-sign-on.md) baserat på deras organisationskonto i Azure AD. Detta gäller inte bara för Microsoft SaaS-appar, men även andra appar som [Google Apps](../active-directory/saas-apps/google-apps-tutorial.md) och [Salesforce](../active-directory/saas-apps/salesforce-tutorial.md). Ditt program kan konfigureras för att använda Azure AD som en [SAML-baserad identitet](../active-directory/fundamentals-identity.md) provider. Som en säkerhetskontroll utfärdar Azure AD inte en token så att de kan logga in på programmet, såvida inte de har beviljats åtkomst med hjälp av Azure AD. Du kan bevilja åtkomst direkt eller via en grupp att de är medlem i.

> [!NOTE]
> beslutet att använda SSO påverkar hur du integrerar din lokala katalog med din molnkatalog. Om du vill att enkel inloggning, måste du använder federation, eftersom katalogsynkronisering ger endast [samma inloggning](../active-directory/active-directory-aadconnect.md).
>
>

Organisationer som inte behöver använda enkel inloggning för användare och program exponeras mer för scenarier där användarna har flera lösenord, vilket ökar sannolikheten för användare att lösenord eller med hjälp av svaga lösenord direkt.

Du kan lära dig mer om Azure AD SSO genom att läsa artikeln [AD FS-hantering och anpassning med Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Distribuera lösenordshantering

I scenarier där du har flera klienter eller om du vill att användarna kan [återställa sina egna lösenord](../active-directory/user-help/active-directory-passwords-update-your-own-password.md), är det viktigt att du använder lämpliga säkerhetsprinciper för att förhindra missbruk. I Azure, kan du använda funktionen för återställning av lösenord för självbetjäning och anpassa säkerhetsalternativ som uppfyller företagets krav.

Det är viktigt att få feedback från användarna och lär dig från deras upplevelser när de försöker att utföra dessa steg. Baserat på dessa upplevelser, utveckla en plan för att förebygga potentiella problem som kan uppstå under distributionen för en större grupp. Det rekommenderas också att du använder den [lösenord återställer registreringen aktivitetsrapporten](../active-directory/active-directory-passwords-get-insights.md) att övervaka de användare som registrerar.

Organisationer som vill undvika supportsamtal för lösenordet ändras, men att användarna kan återställa sina egna lösenord är svårare att ett ökat samtal till supportavdelningen på grund av lösenord. I organisationer som har flera klienter, är det viktigt att du har implementerat den här typen av funktionen och användarna kan utföra lösenordsåterställning i säkerhetsgränser som fastställdes i säkerhetsprincipen.

Du kan läsa mer om lösenordsåterställning genom att läsa artikeln [distribuera lösenordshantering och utbilda användare att använda den](../active-directory/authentication/howto-sspr-deployment.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Använda multifaktorautentisering (MFA) för användare

För organisationer som måste vara kompatibla med branschstandarder, t.ex [PCI DSS version 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), Multi-Factor authentication är ett måste ha funktion för att autentisera användare. Utöver som följer branschstandarder, tillämpa MFA för att autentisera användare kan också hjälpa organisationer att minska typ av autentiseringsuppgift stöld av angrepp, till exempel [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Genom att aktivera Azure MFA för dina användare får du lägger till ett andra säkerhetslager till användarinloggningar och transaktioner. I det här fallet en transaktion kan att ha åtkomst till ett dokument som finns på en filserver eller i en SharePoint Online. Azure MFA hjälper också till att IT-avdelningen att minska sannolikheten att en komprometterad autentiseringsuppgift har åtkomst till organisationsdata.

Till exempel: du använda Azure MFA för dina användare och konfigurera den för att använda ett telefonsamtal eller SMS som verifiering. Om användarens autentiseringsuppgifter skulle skadas kan kan angriparen inte åtkomst till alla resurser eftersom de inte har åtkomst till användarens telefon. Organisationer som inte lägger till extra skyddslager för identitet är svårare för autentiseringsuppgifter identitetsstöld, vilket kan leda till kompromettering av data.

Ett alternativ för organisationer som vill behålla de hela autentiseringen kontroll på plats är att använda [Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-deploy.md), även kallat MFA lokalt. Med den här metoden kommer du fortfarande att kunna använda multifaktorautentisering, samtidigt som de MFA server lokalt.

Mer information om Azure MFA finns i artikeln [komma igång med Azure Multi-Factor Authentication i molnet](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Använda rollbaserad åtkomstkontroll (RBAC)

Begränsa åtkomst baserat på den [behöver veta](https://en.wikipedia.org/wiki/Need_to_know) och [lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är mycket viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Azure rollbaserad åtkomstkontroll (RBAC) kan användas för att tilldela behörigheter till användare, grupper och program för ett visst omfång. Omfattningen för en rolltilldelning kan vara en prenumeration, en resursgrupp eller en enskild resurs.

Du kan utnyttja [inbyggda RBAC](../role-based-access-control/built-in-roles.md) roller i Azure för att tilldela behörigheter till användare. Överväg att använda *Lagringskontodeltagare* för molnoperatörer som behöver kunna hantera lagringskonton och *klassisk Lagringskontodeltagare* roll att hantera klassiska lagringskonton. Överväg att lägga till dem till för molnoperatörer som behöver hantera virtuella datorer och storage-konto, *virtuell Datordeltagare* roll.

Organisationer som inte behöver använda åtkomstkontroll för data med hjälp av funktionerna, till exempel RBAC kan ger fler behörigheter än vad som krävs till sina användare. Detta kan leda till data angrepp genom att ge användarna åtkomst till vissa typer av data (till exempel stora marknadsfördelar) som de inte ska ha i första hand.

Du kan lära dig mer om Azure RBAC genom att läsa artikeln [rollbaserad åtkomstkontroll i](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Kontrollera platser där resurser skapas med Resource Manager

Det är viktigt att aktivera molnoperatörer att utföra uppgifter samtidigt som hindrar dem från icke-bakåtkompatibel konventioner som behövs för att hantera din organisations resurser. Organisationer som vill styra de platser där resurser skapas bör hårt koda de här platserna.

För att uppnå detta, kan organisationer skapa säkerhetsprinciper som har definitioner som beskriver åtgärder eller resurser som inte är tillgängliga. Du kan tilldela dessa principdefinitioner i det önskade omfånget som prenumerationen, resursgruppen eller en enskild resurs.

> [!NOTE]
> Detta är inte samma som RBAC, den faktiskt använder RBAC för att autentisera användare som har behörighet att skapa dessa resurser.
>
>

Utnyttja [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) att skapa anpassade principer för scenarier där organisationen vill ge åtgärder endast när det lämpliga kostnadsstället associeras; i annat fall de neka begäran.

Organisationer som inte styr hur resurser skapas är svårare att användare som kan utnyttja tjänsten genom att skapa fler resurser än de behöver. Härdning av skapandeprocessen resurs är ett viktigt steg för att skydda ett scenario med flera innehavare.

Du kan lära dig mer om hur du skapar principer med Azure Resource Manager genom att läsa artikeln [vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Guide för utvecklare kan utnyttja funktioner för Identitetshantering för SaaS-appar

Användaridentitet utnyttjas i många scenarier när användare kommer åt [SaaS-appar](https://azure.microsoft.com/marketplace/active-directory/all/) som kan integreras med den lokala eller molnbaserade katalog. Först och främst rekommenderar vi att utvecklare använder en säker metod för att utveckla apparna, till exempel [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Azure AD förenklar autentisering för utvecklare genom att tillhandahålla identitet som en tjänst, med stöd för vanliga protokoll som [OAuth 2.0](http://oauth.net/2/) och [OpenID Connect](http://openid.net/connect/)samt som öppen källkod bibliotek för olika plattformar.

Se till att registrera alla program som outsources autentisering till Azure AD, det här är en obligatorisk procedur. Anledningen till detta beror på Azure AD behöver samordna kommunikationen med programmet när hantering av inloggning (SSO) eller byta ut tokens. Användarens session upphör att gälla när livslängd för token som utfärdas av Azure AD upphör att gälla. Utvärdera alltid om ditt program bör använda den här gången eller om du kan minska den här gången. Minska livslängd kan fungera som en säkerhetsåtgärd som kommer att tvinga användare att logga ut baserat på en period av inaktivitet.

Organisationer som inte behöver använda identitet kontrollen för att komma åt appar och inte hjälper utvecklarna om hur du på ett säkert sätt integrera appar med sina identitetshanteringssystem kan vara svårare att autentiseringsuppgifter stöld av typen av angrepp, till exempel [svaga hantering av autentisering och sessionen som beskrivs i OWASP Open Web Application Security Project ()-översta 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Du kan lära dig mer om autentiseringsscenarier för SaaS-appar genom att läsa [Autentiseringsscenarier för Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Övervaka aktivt för misstänkta aktiviteter

Enligt [Verizon 2016 Data intrång rapporten](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), avslöjade autentiseringsuppgifter är fortfarande i ökar och bli en av de mest lönsamma företagen för cyberhot cyberbrotten. Därför är det viktigt att ha ett system för övervakaren av aktiva identiteten som snabbt kan identifiera misstänkt beteende aktivitet och utlöser en avisering för vidare studier. Azure AD har två viktiga funktioner som hjälper organisationer att övervaka deras identiteter: Azure AD Premium [avvikelseidentifiering rapporter](../active-directory/active-directory-view-access-usage-reports.md) och Azure AD [identitetsskydd](../active-directory/active-directory-identityprotection.md) kapaciteten.

Se till att använda rapporter för avvikelseidentifiering för att identifiera försök att logga in [utan spåras](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [råstyrkeattacker](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) attacker mot ett visst konto, försöker logga in från flera platser, logga in från [ infekterade enheter och misstänkta IP-adresser. Tänk på att de är rapporter. Du måste alltså ha processer och procedurer för IT-administratörer kan köra dessa rapporter på daglig basis eller på begäran (vanligtvis i ett scenario med incidenthantering).

Däremot Azure AD identity protection är en aktiv övervakningssystemet och vilket det aktuella riskerna på sin egen instrumentpanel. Förutom att får du även daglig sammanfattning av meddelanden via e-post. Vi rekommenderar att du justerar risknivån enligt dina krav. För en riskhändelse är en indikation (hög, medel eller låg) på riskhändelsens allvarlighetsgrad. Risknivån hjälper användare att Identity Protection prioritera de åtgärder som de måste vidta för att minska risken för organisationen.

Organisationer som inte aktivt övervakar sina identitetssystem finns risken att användarens autentiseringsuppgifter har komprometterats. Placera utan vetskap som misstänkta aktiviteter har förekommit med dessa autentiseringsuppgifter, kan organisationer kommer inte att kunna åtgärda den här typen av hot.
Du kan lära dig mer om Azure Identity protection genom att läsa [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
