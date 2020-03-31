---
title: Azure AD Cloud Governed Management för lokala arbetsbelastningar – Azure
description: I det här avsnittet beskrivs molnstyrd hantering för lokala arbetsbelastningar.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109526"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Så här levererar Azure AD molnstyrd hantering för lokala arbetsbelastningar

Azure Active Directory (Azure AD) är en omfattande IDaaS-lösning (Identity as a Service) som används av miljontals organisationer som sträcker sig över alla aspekter av identitet, åtkomsthantering och säkerhet. Azure AD innehåller mer än en miljard användaridentiteter och hjälper användare att logga in och få åtkomst på båda:

* Externa resurser, till exempel Microsoft Office 365, Azure-portalen och tusentals andra SaaS-program (Software-as-a-Service).
* Interna resurser, till exempel program i en organisations företagsnätverk och intranät, tillsammans med alla molnprogram som utvecklats av den organisationen.

Organisationer kan använda Azure AD om de är "rent moln" eller som en hybriddistribution om de har lokala arbetsbelastningar. En hybriddistribution av Azure AD kan vara en del av en strategi för en organisation att migrera sina IT-resurser till molnet eller fortsätta att integrera befintlig lokal infrastruktur tillsammans med nya molntjänster.

Historiskt sett har "hybrid" organisationer sett Azure AD som en förlängning av sin befintliga lokala infrastruktur. I dessa distributioner är den lokala identitetsstyrningsadministrationen, Windows Server Active Directory eller andra interna katalogsystem, kontrollpunkterna och användare och grupper synkroniseras från dessa system till en molnkatalog som Azure AD. När dessa identiteter finns i molnet kan de göras tillgängliga för Office 365, Azure och andra program.

![Identitetslivscykel](media/cloud-governed-management-for-on-premises//image1.png)

När organisationer flyttar mer av sin IT-infrastruktur tillsammans med sina program till molnet letar många efter de förbättrade säkerhetsfunktionerna och förenklade hanteringsfunktioner för identitetshantering som en tjänst. De molnlevererade IDaaS-funktionerna i Azure AD påskyndar övergången till molnstyrd hantering genom att tillhandahålla de lösningar och funktioner som gör det möjligt för organisationer att snabbt anta och flytta mer av sin identitetshantering från traditionella lokala azure AD, samtidigt som de fortsätter att stödja befintliga och nya program.

Det här dokumentet beskriver Microsofts strategi för hybrid-IDaaS och beskriver hur organisationer kan använda Azure AD för sina befintliga program.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD-metoden för molnstyrd identitetshantering

När organisationer övergår till molnet behöver de garantier för att de har kontroller över sin fullständiga miljö – mer säkerhet och mer insyn i aktiviteter, med stöd av automatisering och proaktiva insikter. "**Molnstyrd hantering**" beskriver hur organisationer hanterar och styr sina användare, program, grupper och enheter från molnet.

I denna moderna värld måste organisationer kunna hantera effektivt i stor skala, på grund av spridningen av SaaS-applikationer och den ökande rollen för samarbete och externa identiteter. Det nya risklandskapet i molnet innebär att en organisation måste vara mer lyhörd - en skadlig aktör som äventyrar en molnanvändare kan påverka moln- och lokala program.

I synnerhet måste hybridorganisationer kunna delegera och automatisera uppgifter, vilket historiskt sett it gjorde manuellt. För att automatisera uppgifter behöver de API:er och processer som dirigerar livscykeln för de olika identitetsrelaterade resurserna (användare, grupper, program, enheter), så att de kan delegera den dagliga hanteringen av dessa resurser till fler individer utanför it-personal. Azure AD åtgärdar dessa krav via hantering av användarkonton och inbyggd autentisering för användare utan att kräva lokal identitetsinfrastruktur. Att inte bygga ut lokal infrastruktur kan gynna organisationer som har nya grupper av användare, till exempel affärspartners, som inte har sitt ursprung i deras lokala katalog, men vars åtkomsthantering är avgörande för att uppnå affärsresultat.

Dessutom är ledningen inte fullständig utan styrning --- och styrning i denna nya värld är en integrerad del av identitetssystemet snarare än ett tillägg. Identitetsstyrning ger organisationer möjlighet att hantera identitets- och åtkomstlivscykeln för anställda, affärspartners och leverantörer samt tjänster och program.

Genom att införliva identitetsstyrning blir det enklare att göra det möjligt för organisationen att övergå till molnstyrd hantering, gör det möjligt för IT att skalas upp, tar itu med nya utmaningar med gäster och ger djupare insikter och automatisering än vad kunderna hade med infrastruktur. Styrning i den här nya världen innebär möjligheten för en organisation att ha insyn, synlighet och korrekta kontroller av åtkomsten till resurser inom organisationen. Med Azure AD har säkerhetsåtgärder och granskningsgrupper insyn i vem som har --- och vem som ska ha - åtkomst till vilka resurser i organisationen (på vilka enheter), vad dessa användare gör med åtkomsten och om organisationen har och använder lämpliga kontroller för att ta bort eller begränsa åtkomsten i enlighet med företagets eller regleringspolicyer.

Den nya hanteringsmodellen gynnar organisationer med både SaaS- och line-of-business-program (LOB), eftersom de lättare kan hantera och skydda åtkomsten till dessa program. Genom att integrera program med Azure AD kan organisationer använda och hantera åtkomst över både moln- och lokala identiteter konsekvent. Livscykelhanteringen för program blir mer automatiserad och Azure AD ger omfattande insikter om programanvändning som inte var lätt att uppnå i lokal identitetshantering. Genom Azure AD, Office 365-grupper och Teams självbetjäningsfunktioner kan organisationer enkelt skapa grupper för åtkomsthantering och samarbete och lägga till eller ta bort användare i molnet för att aktivera samarbets- och åtkomsthanteringskrav.

Välja rätt Azure AD-funktioner för molnstyrd hantering beror på vilka program som ska användas och hur dessa program ska integreras med Azure AD. I följande avsnitt beskrivs metoderna för AD-integrerade program och program som använder federationsprotokoll (till exempel SAML, OAuth eller OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Molnstyrd hantering för AD-integrerade program

Azure AD förbättrar hanteringen för en organisations lokala Active Directory-integrerade program via säker fjärråtkomst och villkorlig åtkomst till dessa program. Dessutom tillhandahåller Azure AD även hantering av kontolivscykeln och hantering av autentiseringsuppgifter för användarens befintliga AD-konton, inklusive:

* **Säker fjärråtkomst och villkorlig åtkomst för lokala program**

För många organisationer är det första steget i hanteringen av åtkomst från molnet för lokala AD-integrerade webb- och fjärrskrivbordsbaserade program att distribuera [programproxyn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) framför dessa program för att ge säker fjärråtkomst.

Efter en enda inloggning till Azure AD kan användare komma åt både moln- och lokala program via en extern URL eller en intern programportal. Programproxy ger till exempel fjärråtkomst och enkel inloggning till Fjärrskrivbord, SharePoint, samt appar som Tableau och Qlik och LOB-program (Line of Business). Dessutom kan principer för villkorlig åtkomst omfatta visning [av användarvillkoren](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) och [se till att användaren har gått med på dem](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) innan han eller hon kan komma åt ett program.

![Arkitektur för appproxy](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatisk livscykelhantering för Active Directory-konton**

Identitetsstyrning hjälper organisationer att uppnå en balans mellan *produktivitet* --- hur snabbt kan en person ha tillgång till de resurser de behöver, till exempel när de går med i organisationen? --- och *säkerhet* --- hur ska deras tillgång förändras över tid, till exempel när den personens anställningsstatus ändras? Identitetslivscykelhantering är grunden för identitetsstyrning, och effektiv styrning i stor skala kräver modernisering av identitetslivscykelhanteringsinfrastrukturen för program.

För många organisationer är identitetslivscykel för anställda knuten till representationen av den användaren i ett HCM-system (Human Capital Management). För organisationer som använder Workday som hcm-system kan Azure AD se till att användarkonton i AD [automatiskt etableras och avetableras för arbetare i Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Detta leder till förbättrad användarproduktivitet genom automatisering av födslorättskonton och hanterar risker genom att se till att programåtkomsten uppdateras automatiskt när en användare byter roller eller lämnar organisationen. Den workday-driven användaretablering [distributionsplanen](https://aka.ms/WorkdayDeploymentPlan) är en steg-för-steg guide som går organisationer genom bästa praxis genomförandet av Workday till Active Directory User Provisioning lösning i en femstegsprocess.

Azure AD Premium innehåller även Microsoft Identity Manager, som kan importera poster från andra lokala HCM-system, inklusive SAP, Oracle eBusiness och Oracle PeopleSoft.

Samarbete mellan företag kräver i allt högre grad att personer utanför organisationen får åtkomst. [Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/b2b/) gör det möjligt för organisationer att på ett säkert sätt dela sina program och tjänster med gästanvändare och externa partner samtidigt som de behåller kontrollen över sina egna företagsdata.

Azure AD kan [automatiskt skapa konton i AD för gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) efter behov, vilket gör det möjligt för företagsgäster att komma åt lokala AD-integrerade program utan att behöva ett annat lösenord. Organisationer kan ställa in [MFA-principer (Multi Factor Authentication) för gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)så att MFA-kontroller görs under programproxyautentisering. Alla [åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) som görs på moln-B2B-användare gäller även lokala användare. Om molnanvändaren till exempel tas bort via livscykelhanteringsprinciper tas även den lokala användaren bort.

**Hantering av autentiseringsuppgifter för Active Directory-konton** Azure AD:s lösenordsåterställning med självbetjäning gör att användare som har glömt sina lösenord kan omauktoriseras och återställa sina lösenord, med de ändrade lösenorden [skrivna till lokala Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Processen för återställning av lösenord kan också använda de lokala lösenordsprinciperna i Active Directory: När en användare återställer sitt lösenord kontrolleras det för att säkerställa att den uppfyller den lokala Active Directory-principen innan den begår den till den katalogen. Distributionsplanen för [återställning av](https://aka.ms/deploymentplans/sspr) lösenordsåterbeställning av självbetjäning beskriver metodtips för att distribuera återställning av lösenord för självbetjäning till användare via webb- och Windows-integrerade upplevelser.

![Azure AD SSPR-arkitektur](media/cloud-governed-management-for-on-premises/image3.png)

Slutligen, för organisationer som tillåter användare att ändra sina lösenord i AD, kan AD konfigureras för att använda samma lösenordsprincip som organisationen använder i Azure AD via [Azure AD-lösenordsskyddsfunktionen](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), som för närvarande är i offentlig förhandsversion.

När en organisation är redo att flytta ett AD-integrerat program till molnet genom att flytta operativsystemet som är värd för programmet till Azure, tillhandahåller [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) AD-kompatibla domäntjänster (till exempel domänanslutning, grupprincip, LDAP och Kerberos/NTLM-autentisering). Azure AD Domain Services integreras med organisationens befintliga Azure AD-klientorganisation, vilket gör det möjligt för användare att logga in med sina företagsautentiseringsuppgifter. Dessutom kan befintliga grupper och användarkonton användas för att skydda åtkomst till resurser, vilket säkerställer en smidigare "lift-and-shift" av lokala resurser till Azure-infrastrukturtjänster.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Molnstyrd hantering för lokala federationsbaserade program

För en organisation som redan använder en lokal identitetsprovider möjliggör om du flyttar program till Azure AD säkrare åtkomst och enklare administrativ upplevelse för federationshantering. Azure AD gör det möjligt att konfigurera detaljerade åtkomstkontroller per program, inklusive Azure Multi-Factor Authentication, med hjälp av Azure AD Conditional Access. Azure AD stöder fler funktioner, inklusive programspecifika tokensigneringscertifikat och konfigurerbara certifikatförfallodatum. Dessa funktioner, verktyg och vägledning gör det möjligt för organisationer att dra tillbaka sina lokala identitetsleverantörer. Microsofts egen IT, till exempel, har flyttat 17 987 program från Microsofts interna Active Directory Federation Services (AD FS) till Azure AD.

![Azure AD-utveckling](media/cloud-governed-management-for-on-premises/image5.png)

Om du vill börja migrera federerade program till Azure https://aka.ms/migrateapps AD som identitetsprovider läser du som innehåller länkar till:

* Faktabladet [Migrera dina program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper), som presenterar fördelarna med migrering och beskriver hur du planerar för migrering i fyra tydligt skisserade faser: identifiering, klassificering, migrering och pågående hantering. Du kommer att guidas genom hur man tänker på processen och bryta ner ditt projekt i lätt att konsumera bitar. I hela dokumentet finns länkar till viktiga resurser som hjälper dig på vägen.

* Lösningsguiden [Migrera programautentisering från Active Directory Federation Services till Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) utforskar mer i detalj samma fyra faser av planering och körning av ett programmigreringsprojekt. I den här guiden får du lära dig hur du tillämpar dessa faser på det specifika målet att flytta ett program från Active Directory Federation Services (AD FS) till Azure AD.

* [Skriptet för migreringsberedskap](https://aka.ms/migrateapps/adfstools) för Active Directory Services kan köras på befintliga lokala AD FS-servrar (Active Directory Federation Services) för att avgöra om program för migrering till Azure AD är redo för migrering.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Löpande åtkomsthantering över moln- och lokala program

Organisationer behöver en process för att hantera åtkomst som är skalbar. Användarna fortsätter att samla åtkomsträttigheter och slutar med utöver vad som ursprungligen var avstämmt för dem. Dessutom måste företagsorganisationer kunna skala effektivt för att utveckla och genomdriva åtkomstpolicy och kontroller kontinuerligt.

Vanligtvis får IT-ombud åtkomst till godkännandebeslut till beslutsfattare för företag. Dessutom kan IT involvera användarna själva. Användare som har åtkomst till konfidentiella kunddata i ett företags marknadsföringsprogram i Europa behöver till exempel känna till företagets policyer. Gästanvändare kan också vara omedvetna om hanteringskraven för data i en organisation som de har bjudits in till.

Organisationer kan automatisera åtkomstlivscykelprocessen med hjälp av tekniker som [dynamiska grupper,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)tillsammans med användaretablering till [SaaS-program](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)eller [program som är integrerade med scim-standarden (System for Cross-Domain Identity Management).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Organisationer kan också styra vilka [gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)som har åtkomst till lokala program . Dessa åtkomsträttigheter kan sedan granskas regelbundet med hjälp av återkommande [Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Framtida riktningar

I hybridmiljöer är Microsofts strategi att aktivera distributioner där **molnet är kontrollplanet för identitet**, och lokala kataloger och andra identitetssystem, till exempel Active Directory och andra lokala program, är målet för etablering av användare med åtkomst. Den här strategin kommer att fortsätta att säkerställa rättigheter, identiteter och åtkomst i de program och arbetsbelastningar som är beroende av dem. I det här sluttillståndet kan organisationer driva slutanvändarens produktivitet helt från molnet.

![Azure AD-arkitekturen](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med den här färden finns i Azure AD-distributionsplanerna som finns på <https://aka.ms/deploymentplans> . De ger heltäckande vägledning om hur du distribuerar Azure Active Directory (Azure AD) funktioner. Varje plan förklarar affärsvärdet, planeringsöverväganden, design och operativa procedurer som krävs för att framgångsrikt distribuera vanliga Azure AD-funktioner. Microsoft uppdaterar kontinuerligt distributionsplanerna med bästa praxis som vi har lärt oss från kunddistributioner och annan feedback när vi lägger till nya funktioner för att hantera från molnet med Azure AD.
