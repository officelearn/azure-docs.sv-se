---
title: Azure AD-molnet regleras av lokala arbetsbelastningar – Azure
description: Det här avsnittet beskriver regleras molnhantering för lokala arbetsbelastningar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109526"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hur Azure AD ger Cloud regleras av lokala arbetsbelastningar

Azure Active Directory (Azure AD) är en omfattande identitet som en tjänst (IDaaS)-lösning som används av miljontals organisationer som omfattar alla aspekter av identitet, hantering och säkerhet. Azure AD innehåller fler än en miljard användaridentiteter och hjälper användarna att logga in och säker åtkomst till båda:

* Externa resurser, till exempel Microsoft Office 365, Azure-portalen och tusentals andra Software-as-a-Service (SaaS)-program.
* Interna resurser, till exempel program på en organisations företagsnätverket och intranät, tillsammans med alla molnbaserade program som har utvecklats av organisationen.

Organisationer kan använda Azure AD om de är ”renodlade molnlösningar”, eller som en hybrid distribution om de har lokala arbetsbelastningar. En hybriddistribution av Azure AD kan inte ingå i en strategi för en organisation att migrera sin IT-tillgångar till molnet eller fortsätta att integrera befintliga lokala infrastruktur tillsammans med nya molntjänster.

Historiskt sett har ' hybridorganisationer ' sett Azure AD som en utökning av den befintliga lokala infrastruktur. Windows Server Active Directory eller andra interna directory-system är kontrollpunkterna i distributionerna, lokala identitet styrning administration och användare och grupper synkroniseras från dessa system till en molnkatalog t.ex Azure AD. När det är dessa identiteter i molnet, kan de göras tillgängliga till Office 365, Azure och andra program.

![Identitetslivscykel](media/cloud-governed-management-for-on-premises//image1.png)

När organisationer över flera av deras IT-infrastruktur tillsammans med sina program till molnet, behöver många bättre säkerhet och funktioner för Identitetshantering som en tjänst förenklad hantering. Molnlevererat IDaaS-funktioner i Azure AD snabbare övergång till molnet styrda management genom att tillhandahålla lösningar och funktioner som gör att organisationer snabbt anta och flytta flera av deras Identitetshantering från traditionella lokala system till Azure AD när du fortsätter att stödja befintliga samt nya program.

Det här dokumentet beskriver Microsofts strategi för hybrid IDaaS och beskriver hur organisationer kan använda Azure AD för befintliga program.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD-förhållningssätt gällande öppenhet och styrda Identitetshantering

Som organisationer övergång till molnet och de behöver garantier som de har kontroller över sina fullständig miljö - säkrare och mer insyn i aktiviteter som stöds av automation och proaktiv insikter. ”**Molnet regleras management**” beskriver hur organisationer hantera och styra sina användare, program, grupper och enheter från molnet.

Organisationer behöver för att kunna hantera effektivt i skala, på grund av ökningen av SaaS-program och ökande rollen och externa identiteter i den här moderna världen. Den nya risk liggande med molnet innebär en organisation måste vara snabbare – en skadliga aktörer som komprometterar en cloud-användare kan påverka molnprogram och lokala program.

I synnerhet hybrid organisationer behöver för att kunna delegera och automatisera uppgifter, som tidigare IT gjorde manuellt. För att automatisera uppgifter de behöver API: er och processer som dirigera livscykeln för de olika identitetsrelaterade resurserna (användare, grupper, program, enheter), så att de kan delegera den dagliga driften av dessa resurser till fler personer utanför viktiga IT-personal. Azure AD-adresser dessa krav med hjälp av hantering av användarkonton och interna authentication för användare utan att den lokala identitetsinfrastrukturen. Inte att bygga ut den lokala infrastrukturen kan hjälpa organisationer som har nya ideella grupper av användare, till exempel affärspartner som inte har sitt ursprung i sina lokala katalog, men vars åtkomsthantering är viktigt att uppnå affärsresultat.

Dessutom management är inte komplett utan styrning--- och styrning i den här nya världen är en integrerad del av ID-system i stället för ett tillägg. Identitetsstyrning ger organisationer möjlighet att hantera identitet och åtkomst livscykel i anställda, affärspartner och leverantörer, och tjänster och program.

Införliva identitetsstyrning gör det enklare att aktivera organisationen att övergå till styrda molnhantering, låter IT-avdelningen att skala, åtgärdar nya utmaningar med gäster och ger djupare insikter och automatisering än vad kunderna hade med den lokala infrastrukturen. Styrning i den här nya världen innebär möjligheten för en organisation att ha genomskinlighet, synlighet och rätt kontroller på åtkomst till resurser i organisationen. Med Azure AD har säkerhetsåtgärder och granska team insyn i vem som har--- och vem som ska ha - åtkomst till vilka resurser i organisationen (på vilka enheter), vad användarna gör med som åtkomst, och om organisationen har och använder lämpliga kontroller för att ta bort eller begränsa åtkomst i enlighet med företagets eller organisationsprinciper.

Den nya Hanteringsmodellen av förmåner organisationer med både SaaS och line-of-business (LOB) program, eftersom de enkelt kan hantera och skydda åtkomsten till dessa program. Genom att integrera program med Azure AD kan organisationer kommer att kunna använda och hantera åtkomst över både moln och lokala har sitt ursprung identiteter konsekvent. Livscykelhantering för program blir mer automatiserad och Azure AD tillhandahåller detaljerad information om program som körs som inte är enkelt realistiska i lokal Identitetshantering. Via Azure AD, Office 365-grupper och team självbetjäningsfunktioner kan organisationer enkelt skapa grupper för åtkomsthantering och samarbete och lägga till eller ta bort användare i molnet för att möjliggöra samarbete och få åtkomst till hanteringskrav.

Att välja rätt Azure AD-funktioner för molnet styrda management beror på de program som ska användas och hur dessa program kommer att integreras med Azure AD. I följande avsnitt beskrivs metoderna som ska vidtas för AD-integrerade program och program som använder federation-protokoll (till exempel SAML, OAuth eller OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Regleras molnhantering för AD-integrerade program

Azure AD förbättrar hantering för organisationens lokala Active Directory-integrerade program via säker fjärråtkomst och villkorlig åtkomst till dessa program. Azure AD tillhandahåller dessutom också Livscykelhantering för kontot och hantering av inloggningsuppgifter för användarens befintliga AD-konton, inklusive:

* **Säker fjärråtkomst och villkorlig åtkomst för lokala program**

Många organisationer är det första steget i att hantera åtkomst från molnet för en lokal AD-integrerade webb- och remote desktop-baserade program är att distribuera den [programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) framför dessa program för att tillhandahålla säker fjärråtkomst.

När du har en enkel inloggning till Azure AD, kan användare komma åt både i molnet och lokala program via en extern Webbadress eller en intern App-portal. Application Proxy ger fjärråtkomst och enkel inloggning till fjärrskrivbord, SharePoint, samt appar som Tableau, Qlik och verksamhetsspecifika (LOB) program. Principer för villkorlig åtkomst kan dessutom innehålla visar den [användningsvillkoren](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) och [att se till att användaren har godkänt dem](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) innan du kan komma åt ett program.

![App Proxy-arkitektur](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatisk Livscykelhantering för Active Directory-konton**

Identitetsstyrning hjälper organisationer att få en balans mellan *produktivitet* ---hur snabbt kan en person har tillgång till resurserna de behöver, till exempel när de gå med i organisationen? --- och *security* ---hur bör deras åtkomst ändras med tiden, till exempel när den personen anställningsstatus ändras? Identitetslivcykelhantering är grunden för Identitetshantering och effektiva styrning i stor skala kräver modernisering av hanteringsinfrastrukturen identitet livscykel för program.

Många organisationer är användaridentiteternas livscykel för anställda kopplad till en representation av användaren i ett system för mänskliga kapitalhantering (HCM-system). För organisationer som använder Workday som deras HCM-system, Azure AD kan kontrollera att användarkonton i AD är [automatiskt etableras och avetableras för arbetare i Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Gör så leder till bättre användarproduktivitet automatiseringen av birthright användarkonton och hanterar risken genom att kontrollera att programmet åtkomst uppdateras automatiskt när en användare ändrar roller eller lämnar organisationen. Etableringen av Workday-driven användare [distributionsplan](https://aka.ms/WorkdayDeploymentPlan) är en stegvis guide som hjälper organisationer via den bästa praxis-implementeringen av Workday till Active Directory-användare och etablera lösningen i fem steg.

Azure AD Premium innehåller även Microsoft Identity Manager, som sedan kan importera poster från andra lokala HCM-system, inklusive SAP, Oracle eBusiness och Oracle PeopleSoft.

Samarbete mellan företag kräver allt högre grad bevilja åtkomst till personer utanför organisationen. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) samarbete gör det möjligt för organisationer att på ett säkert sätt kan dela sina program och tjänster med gästanvändare och externa partners samtidigt som de behåller kontrollen över sina egna företagets data.

Azure AD kan [skapa automatiskt konton i AD för gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) efter behov, aktivera företagsgäster att få åtkomst till lokala AD-integrerade program utan att behöva ett annat lösenord. Organisationer kan ställa in [multifaktorautentisering (MFA)-principer för gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s så MFA kontrollerar görs under application proxy-autentisering. Dessutom kommer alla [åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) som görs i molnet B2B användare gäller för lokala användare. Till exempel raderas molnet användaren tas bort via principer för hantering av livscykeln, också lokala användare.

**Autentiseringsuppgifter för Active Directory-konton** Azure AD är Självbetjäning för återställning av lösenord kan användare som har glömt sitt lösenord för att autentiseras och återställa sina lösenord med ändrade lösenord [skrivs till lokala Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Processen för lösenordsåterställning kan också använda principer för en lokal Active Directory-lösenord: När en användaren återställer sitt lösenord, kontrolleras det garanterar att den når en lokal Active Directory-principen innan du genomför den till katalogen. Återställning av lösenord via självbetjäning [distributionsplan](https://aka.ms/deploymentplans/sspr) beskriver de bästa metoderna för att distribuera lösenordsåterställning via Självbetjäning för användare via webb- och Windows-integrerad upplevelse.

![Azure AD SSPR-arkitektur](media/cloud-governed-management-for-on-premises/image3.png)

Slutligen, för organisationer som tillåter användare att ändra sina lösenord i AD AD kan konfigureras för att använda samma lösenordsprincip som organisationen använder i Azure AD via den [funktionen för skydd av Azure AD-lösenord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)för närvarande i offentlig förhandsversion.

När en organisation är redo att flytta ett AD-integrerade program till molnet genom att flytta operativsystem som är värd för programmet till Azure, [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) får AD-kompatibel domäntjänster (till exempel domänanslutning, Grupprincip, LDAP och Kerberos/NTLM-autentisering). Azure AD Domain Services kan integreras med organisationens befintliga Azure AD-klient, vilket gör det möjligt för användare att logga in med sina företagsuppgifter. Dessutom kan befintliga grupper och användarkonton användas för att säker åtkomst till resurser, säkerställer en jämnare '-flytta ”av lokala resurser till Azure infrastructure services.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Regleras molnhantering för lokal federation-baserade program

För organisationer som redan använder en lokal identitetsprovider, möjliggör flytta program till Azure AD säkrare åtkomst och ett enklare administrativt perspektiv för federation. Azure AD kan konfigurera detaljerade programspecifika åtkomstkontroller, däribland Azure Multi-Factor Authentication med hjälp av Azure AD villkorlig åtkomst. Azure AD stöder fler funktioner, inklusive programspecifika certifikat för tokensignering och förfallodatum för certifikat som kan konfigureras. Dessa funktioner, verktyg och vägledning kan organisationer att dra tillbaka sin lokala identitetsleverantörer. Microsofts egen IT-avdelningen, exempelvis en har flyttat 17,987 program från Microsofts interna Active Directory Federation Services (AD FS) till Azure AD.

![Azure AD-utveckling](media/cloud-governed-management-for-on-premises/image5.png)

Att börja migrera federerade program till Azure AD som identitetsprovider, referera till https://aka.ms/migrateapps som innehåller länkar till:

* Dokumentet [migrera din program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper), som visar fördelarna med migreringen och beskriver hur du planerar för migrering i fyra faser som tydligt beskrivs: identifiering, klassificering, migrering och kontinuerlig hantering. Du får guidas genom hur du tycker om processen och dela upp ditt projekt i lätt att förstå delar. Är länkar till viktiga resurser som hjälper dig på vägen i hela dokumentet.

* I lösningsguiden [migrera program autentisering från Active Directory Federation Services till Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) utforskar mer detaljerat samma fyra stegen för planering och körning av ett programprojekt för migrering . I den här guiden får du lära dig hur du använder dessa faser för den specifika mål för att flytta ett program från Active Directory Federation Services (AD FS) till Azure AD.

* Den [Active Directory Federation Services beredskap Migreringsskriptet](https://aka.ms/migrateapps/adfstools) kan köras på befintliga lokala Active Directory Federation Services (AD FS) servrar för att fastställa beredskap för program för migrering till Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Pågående åtkomsthantering i molnet och lokala program

Organisationer behöver en process för att hantera åtkomst som är skalbar. Det fortsätter användarna att ackumuleras åtkomsträttigheter och avslutas med utöver vad har etablerats för dessa. Dessutom måste företag för att kunna skala effektivt för att utveckla och upprätthålla åtkomstprincip och kontroller med jämna mellanrum.

Vanligtvis IT delegerar åtkomst till godkännande beslut och beslutsfattare. Dessutom IT kan omfatta användare själva. Till exempel behöver användare som har åtkomst till konfidentiell kundinformation i ett företags marknadsföring program i Europa veta företagets principer. Gästanvändare kan också vara ovetande om hantering av kraven för data i en organisation som de har blivit inbjuden.

Organisationer kan automatisera processen för åtkomst-livscykeln via tekniker som [dynamiska grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), kopplade med användaretablering för [SaaS-program](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), eller [program integrerad med systemet för domänerna Identity Management (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) standard. Organisationer kan också styra som [gästanvändare har åtkomst till lokala program](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Dessa komma åt rättigheter kan sedan granskas regelbundet med återkommande [Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Framtida trender

I hybridmiljöer, Microsofts strategi är att aktivera distributioner där den **molnet är kontrollplanet för identiteten**, och lokala kataloger och andra identitetssystem som Active Directory och andra platser program, är målet för etablering av användare med åtkomst. Den här strategin fortsätter att säkerställa de rättigheter, identiteter och åtkomst i dessa program och arbetsbelastningar som förlitar sig på den. I den här sluttillstånd kan organisationer att enheten slutanvändarnas produktivitet helt och hållet från molnet.

![Azure AD-arkitekturen](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång på resan finns i Azure AD-distribution planer finns på <https://aka.ms/deploymentplans> . De ger slutpunkt till slutpunkt rekommendationer om hur du distribuerar Azure Active Directory (Azure AD)-funktioner. Varje plan beskriver affärsvärde, planera överväganden, design och operativa procedurer som krävs för att distribuera vanliga Azure AD-funktioner. Microsoft uppdaterar kontinuerligt distribution-planer med bästa praxis som registrerats från kunddistributioner och annan feedback när vi lägger till nya funktioner för hantering från molnet med Azure AD.
