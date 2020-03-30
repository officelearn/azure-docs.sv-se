---
title: Fyra steg till en stark identitetsgrund - Azure AD
description: I det här avsnittet beskrivs fyra steg som hybrididentitetskunder kan vidta för att skapa en stark identitetsgrund.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206784"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Fyra steg till en stark identitetsgrund med Azure Active Directory

Att hantera åtkomst till appar och data kan inte längre förlita sig på de traditionella gränsstrategierna för nätverkssäkerhet, till exempel perimeternätverk och brandväggar på grund av den snabba förflyttningen av appar till molnet. Nu måste organisationer lita på sin identitetslösning för att styra vem och vad som har åtkomst till organisationens appar och data. Fler organisationer tillåter anställda att ta med sina egna enheter för att arbeta och använda sina enheter var de än kan ansluta till Internet. Att se till att dessa enheter är kompatibla och säkra har blivit en viktig faktor i den identitetslösning som en organisation väljer att implementera. På dagens digitala arbetsplats [är identitet det primära kontrollplanet](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) för alla organisationer som flyttar till molnet.

När organisationer använder en Azure Active Directory-hybrididentitetslösning (Azure Active Directory) får de åtkomst till premiumfunktioner som låser upp produktiviteten genom automatisering, delegering, självbetjäning och enkel inloggningsfunktioner. Det gör det möjligt för dina anställda att komma åt företagets resurser var de än behöver göra sitt arbete samtidigt som ditt IT-team kan styra den åtkomsten genom att se till att rätt personer har rätt tillgång till rätt resurser för att skapa säker produktivitet.

Baserat på våra lärdomar hjälper den här checklistan med metodtips dig att snabbt distribuera rekommenderade åtgärder för att skapa en *stark* identitetsgrund i din organisation:

* Anslut enkelt till appar
* Upprätta en identitet för varje användare automatiskt
* Ge användarna möjlighet att göra det på ett säkert sätt
* Operationalisera dina insikter

## <a name="step-1---connect-to-apps-easily"></a>Steg 1 - Anslut till appar enkelt

Genom att ansluta dina appar till Azure AD kan du förbättra slutanvändarens produktivitet och säkerhet genom att aktivera enkel inloggning (SSO) och göra användaretablering. Genom att hantera dina appar på ett enda ställe, Azure AD, kan du minimera administrativa kostnader och uppnå en enda kontrollpunkt för dina säkerhets- och efterlevnadsprinciper.

I det här avsnittet beskrivs dina alternativ för att hantera användaråtkomst till appar, aktivera säker fjärråtkomst till interna appar och fördelarna med att migrera dina appar till Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Göra appar tillgängliga för användarna sömlöst

Azure AD gör det möjligt för administratörer att lägga till [program](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) i enterprise-programgalleriet i [Azure-portalen](https://portal.azure.com/). Genom att lägga till program i företagsprogramgalleriet blir det enklare för dig att konfigurera program som använder Azure AD som identitetsprovider. Du kan också hantera användarnas åtkomst till programmet med principer för villkorlig åtkomst och konfigurera enkel inloggning (SSO) till program så att användarna inte behöver ange sina lösenord upprepade gånger och automatiskt loggas in på både lokala och molnbaserade program.

När program har lagts till i Azure AD-galleriet kan användarna se appar som har tilldelats dem och söka efter och begära andra appar efter behov. Azure AD innehåller [flera metoder](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) för användare att komma åt sina appar:

* Åtkomstpanel/Mina appar
* Startprogram för Office 365-app
* Direkt inloggning till federerade appar
* Direkt inloggningslänkar

Mer information om användaråtkomst till appar finns i **Steg 3 – Stärk användarna** i den här artikeln.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrera appar från Active Directory Federation Services till Azure AD

Migrera enkel inloggningskonfiguration från Active Directory Federation Services (ADFS) till Azure AD möjliggör ytterligare funktioner för säkerhet, en mer konsekvent hanterbarhet och samarbete. För bästa resultat rekommenderar vi att du migrerar dina appar från AD FS till Azure AD. Genom att ta med dig programautentisering och auktorisering till Azure AD får du följande fördelar:

* Hantera kostnader
* Hantera risker
* Ökad produktivitet
* Ta itu med efterlevnad och styrning

Mer information finns i faktablad [för migrera dina program till Azure Active Directory.](https://aka.ms/migrateapps/whitepaper)

### <a name="enable-secure-remote-access-to-apps"></a>Aktivera säker fjärråtkomst till appar

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) är en enkel lösning för organisationer att publicera lokala appar i molnet för fjärranvändare som behöver åtkomst till interna appar på ett säkert sätt. Efter en enda inloggning till Azure AD kan användare komma åt både moln- och lokala program via externa webbadresser eller en intern programportal.

Azure AD Application Proxy erbjuder följande fördelar:

* Utöka Azure AD till lokala resurser
  * Säkerhet och skydd i molnskala
  * Funktioner som villkorlig åtkomst och multifaktorautentisering som är lätta att aktivera
* Inga komponenter i perimeternätverket som VPN och traditionella omvända proxylösningar
* Inga inkommande anslutningar krävs
* Enkel inloggning (SSO) på olika enheter, resurser och appar i molnet och lokalt
* Ger slutanvändarna möjlighet att vara produktiva när som helst och var som helst

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Upptäck Shadow IT med Microsoft Cloud App Security

I moderna företag är IT-avdelningar ofta inte medvetna om alla molnprogram som används av användarna för att utföra sitt arbete. När IT-administratörer tillfrågas om hur många molnappar de tror att deras anställda använder säger de i genomsnitt 30 eller 40. I själva verket är genomsnittet över 1 000 separata appar som används av anställda i organisationen. 80 % av de anställda använder icke-sanktionerade appar som ingen har granskat och kanske inte följer dina säkerhets- och efterlevnadsprinciper.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) kan hjälpa dig att identifiera användbara appar som är populära bland användare som IT kan sanktionera och lägga till i enterprise-programgalleriet så att användarna kan dra nytta av funktioner som SSO och villkorlig åtkomst.

<em>"**Cloud App Security** hjälper oss att se till att våra medarbetare använder våra moln- och SaaS-program på rätt sätt, på ett sätt som stöder grundläggande säkerhetsprinciper som hjälper till att skydda Accenture."</em> --- [John Blasi, VD, Informationssäkerhet, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Förutom att identifiera skugg-IT kan MCAS också bestämma risknivån för appar, förhindra obehörig åtkomst till företagsdata, eventuellt dataläckage och andra säkerhetsrisker som är förknippade med programmen.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Steg 2 - Upprätta en identitet för varje användare automatiskt

Genom att sammanföra lokala och molnbaserade kataloger i en Azure AD-hybrididentitetslösning kan du återanvända din befintliga lokala Active Directory-investering genom att etablera dina befintliga identiteter i molnet. Lösningen synkroniserar lokala identiteter med Azure AD, medan IT håller den lokala Active Directory som körs med alla befintliga styrningslösningar som den primära sanningens källa för identiteter. Microsofts Azure AD-hybrididentitetslösning sträcker sig över lokala och molnbaserade funktioner, vilket skapar en gemensam användaridentitet för autentisering och auktorisering till alla resurser oavsett var de befinner sig.

Genom att integrera dina lokala kataloger med Azure AD blir användarna mer produktiva och användarna inte använder flera konton för appar och tjänster genom att tillhandahålla en gemensam identitet för åtkomst till både molnresurser och lokala resurser. Använda flera konton är en smärtpunkt för slutanvändare och IT lika. Från ett slutanvändarperspektiv innebär det att ha flera konton att behöva komma ihåg flera lösenord. För att undvika detta, många användare återanvända samma lösenord för varje konto, vilket är dåligt ur ett säkerhetsperspektiv. Ur ett IT-perspektiv leder återanvändning ofta till fler lösenordsåterställningar och helpdesk-kostnader tillsammans med slutanvändarklagomålen.

Azure AD Connect är det verktyg som används för att synkronisera dina lokala identiteter till Azure AD, som sedan kan användas för att komma åt molnprogram. När identiteterna finns i Azure AD kan de etablera till SaaS-program som Salesforce eller Concur.

I det här avsnittet listar vi rekommendationer för att tillhandahålla hög tillgänglighet, modern autentisering för molnet och minska ditt lokala fotavtryck.

> [!NOTE]
> Om du vill veta mer om Azure AD Connect läser du [Vad är Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Konfigurera en mellanlagringsserver för Azure AD Connect och hålla den uppdaterad

Azure AD Connect spelar en nyckelroll i etableringsprocessen. Om synkroniseringsservern av någon anledning kopplas ändringarna till lokala inte i molnet och orsakar åtkomstproblem för användarna. Det är viktigt att definiera en redundansstrategi som gör att administratörer snabbt kan återuppta synkroniseringen när synkroniseringsservern är offline.

Om du vill ge hög tillgänglighet i händelse av att din primära Azure AD Connect-server kopplas från rekommenderar vi att du distribuerar en separat [mellanlagringsserver](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) för Azure AD Connect. Om du distribuerar en server kan administratören "befordra" mellanlagringsservern till produktion med en enkel konfigurationsväxel. Med en standby-server konfigurerad i mellanlagringsläge kan du också testa och distribuera nya konfigurationsändringar och införa en ny server om du inaktiverar den gamla.

> [!TIP]
> Azure AD Connect uppdateras regelbundet. Därför rekommenderar vi starkt att du håller mellanlagringsservern aktuell för att dra nytta av prestandaförbättringar, buggfixar och nya funktioner som varje ny version tillhandahåller.

### <a name="enable-cloud-authentication"></a>Aktivera molnautentisering

Organisationer med lokal Active Directory bör utöka sin katalog till Azure AD med Azure AD Connect och konfigurera lämplig autentiseringsmetod. [Att välja rätt autentiseringsmetod](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) för din organisation är det första steget i din resa för att flytta appar till molnet. Det är en viktig komponent eftersom den styr åtkomsten till alla molndata och resurser.

Den enklaste och rekommenderade metoden för att aktivera molnautentisering för lokala katalogobjekt i Azure AD är att aktivera [PHS (Password Hash Synchronization).](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) Alternativt kan vissa organisationer överväga att aktivera [direktautentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Oavsett om du väljer PHS eller PTA, glöm inte att aktivera [Seamless Single Sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) så att användarna kan komma åt molnappar utan att ständigt ange sitt användarnamn och lösenord i appen när du använder Windows 7- och 8-enheter i företagets nätverk. Utan enkel inloggning måste användarna komma ihåg programspecifika lösenord och logga in på varje program. På samma sätt måste IT-personal skapa och uppdatera användarkonton för varje program som Office 365, Box och Salesforce. Användarna måste komma ihåg sina lösenord, plus spendera tid att logga in på varje program. Att tillhandahålla en standardiserad enda inloggningsmekanism till hela företaget är avgörande för bästa användarupplevelse, minskad risk, förmåga att rapportera och styrning.

För organisationer som redan använder AD FS eller en annan lokal autentiseringsleverantör kan en flytt till Azure AD eftersom din identitetsleverantör kan minska komplexiteten och förbättra tillgängligheten. Om du inte har specifika användningsfall för att använda federationen rekommenderar vi att du migrerar från federerad autentisering till antingen PHS och Seamless SSO eller PTA och Seamless SSO för att dra nytta av fördelarna med ett minskat lokalt fotavtryck och den flexibilitet som molnet erbjuder med förbättrade användarupplevelser. Mer information finns i [Migrera från federation till lösenordshã¤nde för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Aktivera automatisk avetablering av konton

Att aktivera automatisk etablering och avetablering till dina program är den bästa strategin för att styra identiteternas livscykel i flera system. Azure AD stöder [automatiserad, principbaserad etablering och avetablering](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) av användarkonton till en mängd populära SaaS-program som ServiceNow och Salesforce och andra som implementerar [SCIM 2.0-protokollet](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Till skillnad från traditionella etableringslösningar, som kräver anpassad kod eller manuell överföring av CSV-filer, finns etableringstjänsten i molnet och har förintegrerade anslutningsappar som kan konfigureras och hanteras med Azure-portalen. En viktig fördel med automatisk avetablering är att det hjälper till att skydda din organisation genom att omedelbart ta bort användarnas identiteter från viktiga SaaS-appar när de lämnar organisationen.

Mer information om automatisk etablering av användarkonton och hur det fungerar finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Steg 3 – Ge användarna möjlighet att skydda sig på ett säkert sätt

På dagens digitala arbetsplats är det viktigt att balansera säkerheten med produktiviteten. Slutanvändare kan dock ofta trycka tillbaka säkerhetsåtgärder som gör att deras produktivitet och åtkomst till molnappar gör det långsammare. Azure AD tillhandahåller självbetjäningsfunktioner som gör det möjligt för användare att förbli produktiva samtidigt som administrativa kostnader minimeras.

I det här avsnittet visas rekommendationer för att ta bort friktion från organisationen genom att ge användarna möjlighet att vara vaksamma.

### <a name="enable-self-service-password-reset-for-all-users"></a>Aktivera lösenordsåterställning av självbetjäning för alla användare

Azures [självbetjäningslösenordsåterställning](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) är ett enkelt sätt för IT-administratörer att tillåta användare att återställa och låsa upp sina lösenord eller konton utan administratörsingripande. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

Som standard låser Azure AD upp konton när de utför en återställning av lösenord. Men när du aktiverar Azure AD [Connect-integrering lokalt](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)har du också möjlighet att separera dessa två åtgärder, vilket gör det möjligt för användare att låsa upp sitt konto utan att behöva återställa lösenordet.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Se till att alla användare är registrerade för MFA och SSPR

Azure tillhandahåller rapporter som kan användas av dig och din organisation för att säkerställa att användarna är registrerade för MFA och SSPR. Användare som inte har registrerat sig kan behöva utbildas i processen.

[MFA-inloggningsrapporten](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) innehåller information om MFA-användning och ger dig insikter om hur MFA fungerar i din organisation. Att ha åtkomst till inloggningsaktivitet (och granskningar och riskidentifieringar) för Azure AD är avgörande för felsökning, användningsanalys och kriminaltekniska undersökningar.

På samma sätt kan [självbetjäningsrapporten för lösenordshantering](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) användas för att avgöra vem som har (eller inte har) registrerat sig för SSPR.

### <a name="self-service-app-management"></a>Apphantering med självbetjäning

Innan användarna själv kan upptäcka program från åtkomstpanelen måste du aktivera [självbetjäningsprogramåtkomst](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) till alla program som du vill tillåta användare att upptäcka och begära åtkomst till. Självbetjäningsprogramåtkomst är ett bra sätt att tillåta användare att självupptäcka program och eventuellt tillåta företagsgruppen att godkänna åtkomst till dessa program. Du kan tillåta att företagsgruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare för [lösenordsbaserade enkel inloggningsprogram](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) direkt från sina åtkomstpaneler.

### <a name="self-service-group-management"></a>Självbetjäning, grupphantering

Att tilldela användare till program mappas bäst när du använder grupper, eftersom de ger stor flexibilitet och möjlighet att hantera i stor skala:

* Attributbaserat med dynamiskt gruppmedlemskap
* Delegering till appägare

Azure AD ger möjlighet att hantera åtkomst till resurser med hjälp av säkerhetsgrupper och Office 365-grupper. Dessa grupper kan hanteras av en gruppägare som kan godkänna eller neka medlemsförfrågningar och delegera kontroll över gruppmedlemskap. Den här funktionen kallas [grupphantering med självbetjäning](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)och sparar tid genom att låta gruppägare som inte har tilldelats en administrativ roll skapa och hantera grupper utan att behöva förlita sig på administratörer för att hantera sina begäranden.

## <a name="step-4---operationalize-your-insights"></a>Steg 4 - Operationalisera dina insikter

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en effektiv strategi för att säkerställa att användarna förblir produktiva och att din organisation är säker. Säkerhetsloggar och rapporter kan hjälpa till att svara på frågor som:

* Använder du det du betalar för?
* Finns det något misstänkt eller skadligt händer i min hyresgäst?
* Vem påverkades vid en säkerhetsincident?

Säkerhetsloggar och rapporter ger dig en elektronisk post över misstänkta aktiviteter och hjälper dig att identifiera mönster som kan tyda på försök eller lyckad extern penetration av nätverket och interna attacker. Du kan använda granskning för att övervaka användaraktivitet, dokumentefterlevnad, göra kriminalteknisk analys med mera. Aviseringar ger meddelanden om säkerhetshändelser.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Tilldela minst privilegierade administratörsroller för åtgärder

När du tänker på din inställning till verksamheten, det finns ett par nivåer av administration att överväga. Den första nivån lägger administrationen på dina globala administratörer. Att alltid använda den globala administratörsrollen kan vara lämpligt för mindre företag. Men för större organisationer med helpdesk personal och administratörer som ansvarar för specifika uppgifter, tilldela rollen som global administratör kan vara en säkerhetsrisk eftersom det ger dessa individer med möjlighet att hantera uppgifter som är utöver vad de bör kunna göra.

I det här fallet bör du överväga nästa administrationsnivå. Med Hjälp av Azure AD kan du ange slutanvändare som "begränsade administratörer" som kan hantera uppgifter i mindre privilegierade roller. Du kan till exempel tilldela din helpdesk-personal rollen för [säkerhetsläsaren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) för att ge dem möjlighet att hantera säkerhetsrelaterade funktioner med skrivskyddad åtkomst. Eller kanske det är vettigt att tilldela [rollen autentiseringsadministratör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) till enskilda personer för att ge dem möjlighet att återställa autentiseringsuppgifter som inte är lösenord eller läsa och konfigurera Azure Service Health.

Mer information finns [i Administratörsrollbehörigheter i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Övervaka hybridkomponenter (Azure AD Connect-synkronisering, AD FS) med Hjälp av Azure AD Connect Health

Azure AD Connect och AD FS är kritiska komponenter som potentiellt kan bryta livscykelhantering och autentisering och i slutändan leda till avbrott. Därför bör du distribuera Azure AD Connect Health för övervakning och rapportering av dessa komponenter.

Mer information finns i [Övervaka AD FS med Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Använda Azure Monitor för att samla in dataloggar för analys

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) är en enhetlig övervakningsportal för alla Azure AD-loggar, som ger djupa insikter, avancerad analys och smart maskininlärning. Med Azure Monitor kan du använda mått och loggar i portalen och via API:er för att få mer insyn i tillståndet och prestandan för dina resurser. Det möjliggör en enda glasruta inom portalen samtidigt som ett brett utbud av produktintegreringar via API:er och dataexportalternativ som stöder traditionella SIEM-system från tredje part. Azure Monitor ger dig också möjlighet att konfigurera varningsregler för att få meddelanden eller vidta automatiserade åtgärder i problem som påverkar dina resurser.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Skapa anpassade instrumentpaneler för ditt ledarskap och din dag till dag

Organisationer som inte har en SIEM-lösning kan hämta [Power BI Content Pack](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) för Azure AD. Power BI-innehållspaketet innehåller färdiga rapporter som hjälper dig att förstå hur användarna använder och använder Azure AD-funktioner, vilket gör att du kan få insikter om alla aktiviteter i katalogen. Du kan också skapa en egen [anpassad instrumentpanel](https://docs.microsoft.com/power-bi/service-dashboards) och dela med din ledningsgrupp för att rapportera om dagliga aktiviteter. Instrumentpaneler är ett bra sätt att övervaka ditt företag och se alla dina viktigaste mätvärden på ett ögonblick. Visualiseringarna på en instrumentpanel kan komma från en underliggande datauppsättning eller flera, eller från en underliggande rapport eller flera. En instrumentpanel kombinerar lokala och molndata, vilket ger en samlad vy oavsett var dessa data finns.

![Anpassad Power BI-instrumentpanel](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Förstå dina drivrutiner för supportsamtal

När du implementerar en hybrididentitetslösning enligt beskrivningen i den här artikeln bör du i slutändan märka en minskning av dina supportsamtal. Vanliga problem som glömda lösenord och kontoutelåsningar mildras genom att implementera Azures lösenordsåterställning med självbetjäning, samtidigt som självbetjäningsprogramåtkomst aktiveras kan användare självupptäcka och begära åtkomst till program utan att förlita sig på på din IT-personal.

Om du inte observerar en minskning av supportsamtalen rekommenderar vi att du analyserar dina supportsamtalsdrivrutiner i ett försök att bekräfta om åtkomsten till SSPR eller självbetjäningsprogram har konfigurerats korrekt eller om det finns andra nya problem som systematiskt kan behandlas.

*"I vår digitala omvandlingsresa behövde vi en pålitlig leverantör av identitets- och åtkomsthantering för att underlätta sömlös men säker integration mellan oss, partners och molntjänstleverantörer, för ett effektivt ekosystem; Azure AD var det bästa alternativet som erbjöd oss de funktioner och den synlighet som krävdes och som gjorde det möjligt för oss att upptäcka och reagera på risker."* --- [Yazan Almasri, global informationssäkerhetschef, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Övervaka din användning av appar för att öka insikterna

Förutom att upptäcka Shadow IT kan övervakning av appanvändning i hela organisationen med [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) hjälpa din organisation när du flyttar för att dra full nytta av löftet om molnprogram. Det kan hjälpa dig att hålla dig i kontroll över dina tillgångar genom förbättrad insyn i aktivitet och öka skyddet av kritiska data över molnprogram. Genom att övervaka appanvändningen i organisationen med MCAS kan du svara på följande frågor:

* Vilka oordnade appar använder anställda för att lagra data i?
* Var och när lagras känsliga data i molnet?
* Vem får åtkomst till känsliga data i molnet?

*"Med Cloud App Security kan vi snabbt upptäcka avvikelser och vidta åtgärder."* --- [Eric LePenske, Senior Manager, Informationssäkerhet, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Sammanfattning

Det finns många aspekter på att implementera en hybrididentitetslösning, men den här checklistan i fyra steg hjälper dig att snabbt utföra en identitetsinfrastruktur som gör det möjligt för användare att bli mer produktiva och säkra.

* Anslut enkelt till appar
* Upprätta en identitet för varje användare automatiskt
* Ge användarna möjlighet att göra det på ett säkert sätt
* Operationalisera dina insikter

Vi hoppas att detta dokument är en användbar färdplan för att skapa en stark identitetsgrund för din organisation.

## <a name="identity-checklist"></a>Checklista för identitet

Vi rekommenderar att du skriver ut följande checklista som referens när du börjar din resa till en mer solid identitetsgrund i din organisation.

### <a name="today"></a>Today

|Klart?|Objekt|
|:-|:-|
||Återställning av självbetjäning (SSPR) för en grupp|
||Övervaka hybridkomponenter med Azure AD Connect-hälsotillståndet|
||Tilldela minst privilegierade administratörsroller för åtgärden|
||Upptäck Shadow IT med Microsoft Cloud App Security|
||Använd Azure Monitor för att samla in dataloggar för analys|

### <a name="next-two-weeks"></a>De närmaste två veckorna

|Klart?|Objekt|
|:-|:-|
||Göra en app tillgänglig för användarna|
||Pilot Azure AD-etablering för en SaaS-app som du väljer|
||Konfigurera en mellanlagringsserver för Azure AD Connect och hålla den uppdaterad|
||Börja migrera appar från ADFS till Azure AD|
||Skapa anpassade instrumentpaneler för ditt ledarskap och din dag till dag|

### <a name="next-month"></a>Nästa månad

|Klart?|Objekt|
|:-|:-|
||Övervaka din användning av appar för att öka insikterna|
||Pilot säker fjärråtkomst till appar|
||Se till att alla användare är registrerade för MFA och SSPR|
||Aktivera molnautentisering|

### <a name="next-three-months"></a>De kommande tre månaderna

|Klart?|Objekt|
|:-|:-|
||Aktivera självbetjäningsapphantering|
||Aktivera grupphantering med självbetjäning|
||Övervaka din användning av appar för att öka insikterna|
||Förstå dina drivrutiner för supportsamtal|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan öka din säkra hållning med hjälp av funktionerna i Azure Active Directory och den här femstegschecklistan - [Fem steg för att skydda din identitetsinfrastruktur](https://aka.ms/securitysteps).

Lär dig hur identitetsfunktionerna i Azure AD kan hjälpa dig att påskynda övergången till molnstyrd hantering genom att tillhandahålla de lösningar och funktioner som gör det möjligt för organisationer att snabbt anta och flytta mer av sin identitetshantering från traditionella lokala system till Azure AD - [Hur Azure AD levererar molnstyrd hantering för lokala arbetsbelastningar](https://aka.ms/cloudgoverned).
