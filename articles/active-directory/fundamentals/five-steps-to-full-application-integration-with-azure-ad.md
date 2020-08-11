---
title: Fem steg för att integrera alla dina appar med Azure AD
description: Den här guiden förklarar hur du integrerar alla dina program med Azure AD. I varje steg förklarar vi värdet och tillhandahåller länkar till resurser som förklarar den tekniska informationen.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057379"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Fem steg för att integrera alla dina appar med Azure AD

Azure Active Directory (Azure AD) är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst. Azure AD tillhandahåller säkra lösningar för autentisering och auktorisering så att kunder, partners och anställda kan komma åt de program som de behöver. Med Azure AD, [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks), [enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)och [Automatisk användar etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) blir identitets-och åtkomst hantering enkel och säker.

Om ditt företag har en Microsoft 365 prenumeration använder du förmodligen [redan](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) Azure AD. Azure AD kan dock användas för alla dina program och genom att [centralisera din program hantering](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) kan du använda samma funktioner för identitets hantering, verktyg och principer i hela program portföljen. På så sätt får du en enhetlig lösning som förbättrar säkerheten, minskar kostnaderna, ökar produktiviteten och gör det möjligt att kontrol lera efterlevnaden. Och du får fjärråtkomst till lokala appar.

Den här guiden förklarar hur du integrerar alla dina program med Azure AD. I varje steg förklarar vi värdet och tillhandahåller länkar till resurser som förklarar den tekniska informationen. Vi presenterar de här stegen i en ordning som vi rekommenderar. Du kan dock gå till valfri del av processen för att komma igång med det som ger dig det mest önskade värdet.

Andra resurser i det här avsnittet, inklusive djupgående fakta blad för affärs processer, som finns på våra [resurser för att migrera program till Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) sida.

## <a name="1-use-azure-ad-for-new-applications"></a>1. Använd Azure AD för nya program

Börja med att fokusera på nyligen förvärvade program. När ditt företag börjar använda ett nytt program kan du [lägga till det i Azure AD-klienten](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) direkt. Konfigurera en företags princip så att du kan lägga till nya appar i Azure AD som standard praxis i din organisation. Detta är minimalt störande för befintliga affärs processer och gör att du kan undersöka och bevisa värdet som du får från att integrera appar utan att ändra hur man gör affärer i din miljö idag.

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program för att göra det enkelt att komma igång. Du kan [lägga till en Galleri-app i Azure AD-organisationen](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) med stegvisa [självstudier](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) för integrering med populära appar som:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Dessutom kan du [integrera program som inte finns i galleriet](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), inklusive program som redan finns i din organisation eller program från tredje part från en leverantör som inte redan ingår i Azure AD-galleriet. Du kan också [lägga till din app i galleriet](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) om den inte finns där.

Slutligen kan du även integrera de appar som du utvecklar internt. Detta beskrivs i steg fem i den här hand boken.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. Bestäm användning av befintliga program och prioritera arbete

Sedan identifierar du de program anställda ofta använder och prioriterar ditt arbete för att integrera dem med Azure AD.

Du kan börja med att använda Microsoft Cloud App Security&#39;s [Cloud Discovery-verktyg](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) för att identifiera och hantera &quot; skugg- &quot; IT i nätverket (det vill säga appar som inte hanteras av IT-avdelningen). Du kan [använda Microsoft Defender Avancerat skydd (ATP)](https://docs.microsoft.com/cloud-app-security/wdatp-integration) för att förenkla och utöka identifierings processen.

Dessutom kan du använda [rapporten AD FS program aktivitet](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) i Azure Portal för att identifiera alla AD FS appar i organisationen, antalet unika användare som har loggat in på dem och kompatibilitet för att integrera dem med Azure AD.

När du har identifierat ditt befintliga landskap vill du [skapa en plan](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) och prioritera appar för högsta prioritet att integrera. Några exempel frågor som du kan ställa för att hjälpa dig:

- Vilka appar används mest?
- Vilka är riskiest?
- Vilka appar kommer att inaktive ras i framtiden, vilket gör flytten onödig?
- Vilka appar måste vara lokala och kan inte flyttas till molnet?

Du får de största fördelarna och kostnads besparingarna när alla dina appar är integrerade och du inte längre förlitar dig på flera identitets lösningar. Du kommer dock att uppleva enklare identitets hantering och ökad säkerhet när du flyttar stegvis mot det här målet. Du vill använda den här tiden för att prioritera ditt arbete och bestämma vad som passar din situation.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. integrera appar som är beroende av andra identitets leverantörer

Under identifierings processen kan du ha hittat program som inte spåras av IT-avdelningen, vilket innebär att dina data och resurser är sårbara. Du kan också ha program som använder alternativa identitets lösningar, inklusive Active Directory Federation Services (AD FS) (ADFS) eller andra identitets leverantörer. Överväg hur du kan konsolidera din identitets-och åtkomst hantering för att spara pengar och öka säkerheten. Genom att minska antalet identitets lösningar som du har:

- Spara pengar genom att eliminera behovet av lokal användar etablering och-autentisering samt licens avgifter som betalas till andra moln identitets leverantörer för samma tjänst.
- Minska den administrativa belastningen och ge bättre säkerhet med färre uppsägningar i identitets-och åtkomst hanterings processen.
- Ge anställda möjlighet att få säker åtkomst till enkel inloggning till alla program som de behöver via portalen för mina [appar](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).
- Förbättra intelligensen i Azure AD&#39;s [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) -relaterade tjänster som villkorlig åtkomst genom att öka mängden data som den får från din app-användning och utöka fördelarna med de nyligen tillagda apparna.

Vi har publicerat vägledning för att hantera affärs processen för att integrera appar med Azure AD, inklusive en [affisch](https://aka.ms/AppOnePager) och [presentation](https://aka.ms/AppGuideline) som du kan använda för att göra affärs-och program ägare medvetna och intresserade. Du kan ändra dessa exempel med din egen anpassning och publicera dem i din organisation via företags portalen, nyhets brevet eller något annat medium i takt med att du går igenom processen.

En bra plats för att börja är genom att utvärdera din användning av Active Directory Federation Services (AD FS) (ADFS). Många organisationer använder ADFS för autentisering med SaaS-appar, anpassade branschspecifika appar och Office 365 och Azure AD-baserade appar:

![Diagrammet visar lokala appar, branschspecifika appar, SaaS-appar och, via Azure AD, Office 365 alla ansluter med prickade linjer till Active Directory och AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Du kan uppgradera den här konfigurationen genom att [ersätta ADFS med Azure AD som centrum](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) för din identitets hanterings lösning. Detta möjliggör inloggning för varje app som dina anställda vill ha åtkomst till och gör det enkelt för de anställda att hitta affärs program som de behöver via portalen för mina [appar](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access), utöver de andra förmåner som anges ovan.

![Diagrammet visar lokala appar via Active Directory och AD FS, branschspecifika appar, SaaS-appar och Office 365 alla ansluter med prickade linjer till Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

När Azure AD blir den centrala identitets leverantören kan du växla från ADFS fullständigt, i stället för att använda en federerad lösning. Appar som tidigare använde ADFS för autentisering kan nu endast använda Azure AD.

![Diagrammet visar lokala, branschspecifika appar, SaaS-appar och Office 365 alla ansluter med prickade linjer till Azure Active Directory. Active Directory och AD FS finns inte.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Du kan också migrera appar som använder en annan molnbaserad identitetsprovider till Azure AD. Din organisation kan ha flera lösningar för identitets åtkomst hantering (IAM) på plats. Att migrera till en Azure AD-infrastruktur är en möjlighet att minska beroenden för IAM-licenser (lokalt eller i molnet) och infrastruktur kostnader. I de fall där du redan har betalat för Azure AD via M365-licenser, finns det ingen anledning att betala den extra kostnaden för en annan IAM-lösning.

## <a name="4-integrate-on-premises-applications"></a>4. integrera lokala program

Traditionellt var programmen skyddade genom att tillåta åtkomst endast när du är ansluten till företags nätverket. I en alltmer ansluten värld vill vi dock tillåta åtkomst till appar för kunder, partners och/eller anställda, oavsett var de befinner sig i världen. [Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) är en funktion i Azure AD som ansluter dina befintliga lokala appar till Azure AD och kräver inte att du behåller Edge-servrar eller andra ytterligare infrastrukturer.

![Ett diagram visar tjänsten Application Proxy i praktiken. En användare kommer åt " https://sales.contoso.com " och deras begäran omdirigeras via " https://sales-contoso.msappproxy.net " i Azure Active Directory till den lokala adressen " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Du kan använda [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) för att aktivera programproxy och lägga till ett lokalt program till Azure AD-klienten.

Dessutom kan du integrera program leverans kontroller som F5 Big-IP APM eller Zscaler Private Access. Genom att integrera dessa med Azure AD får du modern autentisering och identitets hantering för Azure AD tillsammans med trafik hanterings-och säkerhetsfunktionerna i partner produkten. Vi kallar den här lösningen för [säker hybrid åtkomst](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Om du använder någon av följande tjänster idag har vi själv studie kurser som steg för steg hjälper dig att integrera dem med Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Citrix Application levererar styrenhet (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (tidigare kallat Citrix NetScaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. integrera appar som utvecklarna skapar

För appar som har skapats inom företaget kan utvecklarna använda [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) för att implementera autentisering och auktorisering. Program som är integrerade med plattformen och [registreras med Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) och hanteras precis som andra appar i din portfölj.

Utvecklare kan använda plattformen för både interna användnings appar och kund riktade appar, och det finns andra fördelar med att använda plattformen. [Microsoft Authentication libraries (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), som är en del av plattformen, gör det möjligt för utvecklare att aktivera moderna upplevelser som Multi-Factor Authentication och att använda säkerhets nycklar för att komma åt sina appar utan att behöva implementera dem själva. Dessutom kan appar som är integrerade med Microsoft Identity Platform komma åt [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) – en enhetlig API-slutpunkt som tillhandahåller Microsoft 365 data som beskriver mönstren för produktivitet, identitet och säkerhet i en organisation. Utvecklare kan använda den här informationen för att implementera funktioner som ökar produktiviteten för dina användare. Till exempel genom att identifiera de personer som användaren har interagerat med nyligen och visa dem i appen&#39;s UI.

Vi har en [video serie](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) som ger en omfattande introduktion till plattformen samt [många kod exempel](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) i språk och plattformar som stöds.

## <a name="next-steps"></a>Nästa steg

- [Resurser för att migrera program till Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
