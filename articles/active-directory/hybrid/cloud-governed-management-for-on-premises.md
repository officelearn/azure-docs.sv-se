---
title: Azure AD Cloud-styrd hantering för lokala arbets belastningar – Azure
description: I det här avsnittet beskrivs moln styrnings hantering för lokala arbets belastningar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec74b9391c780cf673fe47bd82cc6d92534eb56d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234088"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hur Azure AD levererar hantering av moln styrning för lokala arbets belastningar

Azure Active Directory (Azure AD) är en omfattande IDaaS-lösning (Identity as a Service) som används av miljon tals organisationer som omfattar alla aspekter av identitet, åtkomst hantering och säkerhet. Azure AD innehåller mer än en miljard användar identiteter och hjälper användarna att logga in och få säker åtkomst till båda:

* Externa resurser, till exempel Microsoft Office 365, Azure Portal och tusentals andra program vara som en tjänst (SaaS).
* Interna resurser, till exempel program i företagets företags nätverk och intranät, tillsammans med alla moln program som utvecklats av organisationen.

Organisationer kan använda Azure AD om de är "rent moln" eller "hybrid" distribution om de har lokala arbets belastningar. En hybrid distribution av Azure AD kan ingå i en strategi för att en organisation ska migrera sina IT-tillgångar till molnet eller fortsätta att integrera befintlig lokal infrastruktur tillsammans med nya moln tjänster.

Tidigare har hybrid organisationer sett Azure AD som en utökning av den befintliga lokala infrastrukturen. I dessa distributioner är den lokala identitets styrnings administrationen, Windows Server Active Directory eller andra lokala katalog system, kontroll punkterna och användare och grupper synkroniseras från dessa system till en moln katalog, till exempel Azure AD. När dessa identiteter finns i molnet kan de göras tillgängliga för Office 365, Azure och andra program.

![Identitets livs cykel](media/cloud-governed-management-for-on-premises//image1.png)

När organisationer flyttar mer av sin IT-infrastruktur tillsammans med sina program till molnet, letar många efter den förbättrade säkerheten och de förenklade hanterings funktionerna i identitets hantering som en tjänst. De molnbaserade IDaaS-funktionerna i Azure AD påskyndar över gången till moln styrnings hantering genom att tillhandahålla lösningar och funktioner som gör det möjligt för organisationer att snabbt komma åt och flytta mer av sin identitets hantering från traditionella lokala system till Azure AD, samtidigt som de fortsätter att stödja befintliga och nya program.

Det här dokumentet beskriver Microsofts strategi för Hybrid IDaaS och beskriver hur organisationer kan använda Azure AD för sina befintliga program.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD-metoden för Cloud-styrd identitets hantering

När organisationer övergår till molnet måste de se till att de har kontroller över hela miljön – mer säkerhet och mer insyn i aktiviteter, stöd för automatisering och proaktiva insikter. "**Cloud styrd Management**" beskriver hur organisationer hanterar och styr sina användare, program, grupper och enheter från molnet.

I den här moderna världen behöver organisationer kunna hantera effektivt i skala på grund av spridningen av SaaS-program och den ökande rollen för samarbete och externa identiteter. Det nya riskkapitalet i molnet innebär att en organisation måste vara mer besvarad – en obehörig aktör som äventyrar en moln användare kan påverka molnet och lokala program.

I synnerhet måste hybrid organisationer kunna delegera och automatisera uppgifter som tidigare var manuellt. För att automatisera uppgifter behöver de API: er och processer som dirigerar livs cykeln för de olika identitets relaterade resurserna (användare, grupper, program och enheter), så att de kan delegera den dagliga hanteringen av dessa resurser till fler personer utanför den grundläggande IT-personalen. Azure AD löser dessa krav genom hantering av användar konton och inbyggd autentisering för användare utan krav på lokal identitets infrastruktur. Att inte skapa en lokal infrastruktur kan dra nytta av organisationer som har nya användar grupper, till exempel affärs partner, som inte har sitt ursprung i sin lokala katalog, men vars åtkomst hantering är avgörande för att uppnå affärs resultat.

Dessutom slutförs inte hanteringen utan styrning---och styrning i den nya världen är en integrerad del av identitets systemet i stället för ett tillägg. Identitets styrning ger organisationer möjlighet att hantera identitets-och åtkomst livs cykeln över anställda, affärs partner och leverantörer och tjänster och program.

Genom att införliva identitets styrning blir det enklare att låta organisationen gå över till moln styrnings hantering, vilket gör det möjligt att skala, korrigera nya utmaningar med gäster och ger djupare insikter och automatisering än vad kunderna hade haft med lokal infrastruktur. Styrning i den nya världen innebär att en organisation kan ha genomskinlighet, synlighet och lämpliga kontroller av åtkomsten till resurser i organisationen. Med Azure AD har säkerhets åtgärder och gransknings team en insyn i som har---och som ska ha åtkomst till vilka resurser i organisationen (på vilka enheter), vad dessa användare gör med den åtkomsten och om organisationen har och använder lämpliga kontroller för att ta bort eller begränsa åtkomst i enlighet med företagets eller reglerade principer.

Den nya hanterings modellen fördelar organisationer med både SaaS-och branschspecifika program, eftersom de enkelt kan hantera och säkra åtkomst till dessa program. Genom att integrera program med Azure AD kommer organisationer att kunna använda och hantera åtkomst över både molnet och lokala identiteter. Hantering av program livs cykeln blir mer automatiserad och Azure AD ger omfattande insikter om program användning som inte är lätt att nå i lokal identitets hantering. Med hjälp av självbetjänings funktionerna i Azure AD, Office 365-grupper och team kan organisationer enkelt skapa grupper för åtkomst hantering och samarbete och lägga till eller ta bort användare i molnet för att möjliggöra samarbete och åtkomst hanterings krav.

Att välja rätt Azure AD-funktioner för moln styrnings hantering beror på vilka program som ska användas och hur dessa program kommer att integreras med Azure AD. I följande avsnitt beskrivs de metoder som ska vidtas för AD-integrerade program och program som använder Federations protokoll (till exempel SAML, OAuth eller OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Molnbaserad hantering av appar för AD-integrerade program

Azure AD förbättrar hanteringen av en organisations lokala Active Directory-integrerade program genom säker fjärråtkomst och villkorlig åtkomst till dessa program. Dessutom tillhandahåller Azure AD även hantering av konto livs cykel och hantering av autentiseringsuppgifter för användarens befintliga AD-konton, inklusive:

* **Säker fjärråtkomst och villkorlig åtkomst för lokala program**

För många organisationer är det första steget i att hantera åtkomst från molnet för lokala AD-integrerade webb-och fjärr skrivbords program att distribuera [programproxyn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) framför dessa program för att tillhandahålla säker fjärråtkomst.

Efter en enkel inloggning till Azure AD kan användare komma åt både molnet och lokala program via en extern URL eller en intern applikations Portal. Till exempel ger Application Proxy fjärråtkomst och enkel inloggning till fjärr skrivbord, SharePoint, samt appar som Tableau och Qlik, samt LOB-program (Line of Business). Dessutom kan principer för villkorlig åtkomst inkludera att visa [användnings villkoren](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) och [Se till att användaren har kommit överens om att](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) få åtkomst till ett program.

![Arkitektur för App-proxy](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatisk livs cykel hantering för Active Directory-konton**

Identitets styrning hjälper organisationer att uppnå balans mellan *produktiviteten* ---hur snabbt kan en person ha åtkomst till de resurser som de behöver, till exempel när de ansluter till organisationen? ---och *säkerhet* ---hur ändras åtkomsten över tid, till exempel när personens anställnings status ändras? Hantering av identitets livs cykeln är grunden för identitets styrning, och effektiva styrningar i skalan kräver att infrastrukturen för identitets livs cykels hantering används för program.

För många organisationer är identitets livs cykeln för anställda knuten till åter givning av användaren i ett HCM-system (Human kapital Management). För organisationer som använder Workday som HCM system kan Azure AD se till att användar konton i AD [etableras automatiskt och avetableras för arbetare i arbets dagen](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Detta leder till förbättrad användar produktivitet genom automatisering av Birthright-konton och hanterar risker genom att säkerställa att program åtkomsten uppdateras automatiskt när en användare ändrar roller eller lämnar organisationen. [Distributions planen för arbets Plans](https://aka.ms/WorkdayDeploymentPlan) driven användar etablering är en steg-för-steg-guide som vägleder organisationer genom implementeringen av arbets dagar med metod tips för att Active Directory användar etablerings lösningen i en fem stegs process.

Azure AD Premium innehåller även Microsoft Identity Manager, som kan importera poster från andra lokala HCM-system, inklusive SAP, Oracle eBusiness och Oracle, Oracle.

Samarbete mellan företag i allt större utsträckning kräver att du beviljar åtkomst till personer utanför organisationen. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) -samarbete gör det möjligt för organisationer att på ett säkert sätt dela sina program och tjänster med gäst användare och externa partners samtidigt som de behåller kontrollen över sina egna företags data.

Azure AD kan [automatiskt skapa konton i AD för gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) vid behov, vilket gör det möjligt för företags gäster att komma åt lokala AD-integrerade program utan att behöva ett annat lösen ord. Organisationer kan ställa in [Multi-Factor Authentication-principer (MFA) för gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s så att MFA-kontroller görs vid autentisering med programproxy. Dessutom gäller alla [åtkomst granskningar](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) som görs i Cloud B2B-användare för lokala användare. Om till exempel moln användaren tas bort med hjälp av livs cykel hanterings principer, raderas även den lokala användaren.

**Hantering av autentiseringsuppgifter för Active Directory konton** Med lösen ords återställning via självbetjäning i Azure AD kan användare som har glömt sina lösen ord autentiseras igen och återställa sina lösen ord, med ändrade lösen ord [som skrivits till lokala Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Processen för lösen ords återställning kan också använda lokala Active Directory lösen ords principer: när en användare återställer sitt lösen ord, kontrol leras det för att säkerställa att det uppfyller den lokala Active Directorys principen innan den allokeras till den katalogen. [Distributions planen](https://aka.ms/deploymentplans/sspr) för lösen ords återställning via självbetjäning beskriver metod tips för att distribuera lösen ords återställning via självbetjäning till användare via webb-och Windows-integrerade upplevelser.

![Azure AD SSPR-arkitektur](media/cloud-governed-management-for-on-premises/image3.png)

Slutligen, för organisationer som tillåter användare att ändra sina lösen ord i AD, kan AD konfigureras att använda samma lösen ords princip som organisationen använder i Azure AD via funktionen för [lösen ords skydd i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), för närvarande i offentlig för hands version.

När en organisation är redo att flytta ett AD-integrerat program till molnet genom att flytta det operativ system som är värd för programmet till Azure, tillhandahåller [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) AD-kompatibla domän tjänster (till exempel domän anslutning, grup princip, LDAP och KERBEROS/NTLM-autentisering). Azure AD Domain Services integreras med organisationens befintliga Azure AD-klient, vilket gör det möjligt för användarna att logga in med sina företags uppgifter. Dessutom kan befintliga grupper och användar konton användas för att skydda åtkomsten till resurser, vilket säkerställer en smidigare "lyft och Shift" av lokala resurser till Azures infrastruktur tjänster.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Moln hanterings hantering för lokala Federations program

För en organisation som redan använder en lokal identitetsprovider, ger flytt av program till Azure AD säkrare åtkomst och en enklare administrativ upplevelse för Federations hantering. Med Azure AD kan du konfigurera detaljerad åtkomst kontroll per program, inklusive Azure Multi-Factor Authentication, genom att använda villkorlig åtkomst för Azure AD. Azure AD har stöd för fler funktioner, inklusive programspecifika token signerings certifikat och konfigurerbara förfallo datum för certifikat. Dessa funktioner, verktyg och vägledning gör det möjligt för organisationer att dra tillbaka sina lokala identitets leverantörer. Microsofts egen IT-organisation har flyttat 17 987-program från Microsofts interna Active Directory Federation Services (AD FS) (AD FS) till Azure AD.

![Azure AD-utveckling](media/cloud-governed-management-for-on-premises/image5.png)

För att börja migrera federerade program till Azure AD som identitetsprovider, se till https://aka.ms/migrateapps att innehåller länkar till:

* White paper [migrera dina program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper), som visar fördelarna med migreringen och beskriver hur du planerar migreringen i fyra tydligt sammansatta faser: identifiering, klassificering, migrering och kontinuerlig hantering. Du kommer att vägleda dig genom processen och dela upp ditt projekt i lätt att förbruka delar. I hela dokumentet finns länkar till viktiga resurser som hjälper dig längs vägen.

* Lösnings guiden [för att migrera programautentisering från Active Directory Federation Services (AD FS) till Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) utforska mer i detalj i samma fyra faser för att planera och köra ett programmigrerings projekt. I den här guiden får du lära dig hur du tillämpar dessa faser på det specifika målet att flytta ett program från Active Directory Federation Services (AD FS) (AD FS) till Azure AD.

* [Skriptet för Active Directory Federation Services (AD FS) migration readiness](https://aka.ms/migrateapps/adfstools) kan köras på befintliga lokala Active Directory Federation Services (AD FS)-servrar (AD FS) för att fastställa om program är tillgängliga för migrering till Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Kontinuerlig åtkomst hantering i molnet och lokala program

Organisationer behöver en process för att hantera åtkomst som är skalbar. Användarna fortsätter att samla in åtkomst rättigheter och få mer än vad som ursprungligen etablerades. Dessutom måste företags organisationer kunna skala effektivt för att utveckla och tillämpa åtkomst principer och kontroller kontinuerligt.

Vanligt vis delegerar den åtkomst till godkännande beslut till besluts fattare i företaget. Dessutom kan de involvera själva användarna. Till exempel behöver användare som har åtkomst till konfidentiell kund information i ett företags marknadsförings program i Europa känna till företagets principer. Gäst användare kan också vara medvetna om hanterings kraven för data i en organisation som de har bjudits in till.

Organisationer kan automatisera livs cykel processen för Access genom tekniker som [dynamiska grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), tillsammans med användar etablering för SaaS- [program](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)eller program som [integreras med systemet för scim-standarden (Cross-Domain Identity Management](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)). Organisationer kan också styra vilka [gäst användare som har åtkomst till lokala program](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Dessa behörigheter kan sedan regelbundet granskas med hjälp av återkommande [granskningar av Azure AD-åtkomst](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Framtida anvisningar

I hybrid miljöer är Microsofts strategi att aktivera distributioner där **molnet är kontroll planet för identitet**, och lokala kataloger och andra identitets system, till exempel Active Directory och andra lokala program, är målet för etablering av användare med åtkomst. Den här strategin kommer att fortsätta att säkerställa rättigheter, identiteter och åtkomst i de program och arbets belastningar som förlitar sig på dem. I det här läget kommer organisationer att kunna driva slutanvändarens produktivitet helt från molnet.

![Azure AD-arkitekturen](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med den här resan finns i distributions planerna för Azure AD som finns på <https://aka.ms/deploymentplans> . De ger fullständig vägledning om hur du distribuerar Azure Active Directory (Azure AD)-funktioner. I varje plan förklaras affärs värde, planerings överväganden, design och operativa procedurer som krävs för att distribuera vanliga funktioner i Azure AD. Microsoft uppdaterar kontinuerligt distributions planer med bästa praxis som lärts från kund distributioner och annan feedback när vi lägger till nya funktioner för hantering från molnet med Azure AD.
