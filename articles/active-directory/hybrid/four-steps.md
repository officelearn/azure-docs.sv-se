---
title: Fyra steg till en stark identitet foundation med Azure Active Directory
description: Det här avsnittet beskrivs fyra steg hybrid identity kunder kan vidta för att bygga en stark identitet grund.
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
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96b5e8ab63c1784ff073c7ba38cd4a6319db43c5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452740"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Fyra steg till en stark identitet foundation med Azure Active Directory

Hantera åtkomst till appar och data kan inte längre förlita dig på traditionella gräns säkerhetsstrategier som perimeternätverk och brandväggar på grund av att snabbt flytta appar till molnet. Organisationer måste nu lita på deras ID-lösning för att styra vem och vad har åtkomst till organisationens appar och data. Fler och fler organisationer tillåter att anställda med sina egna enheter att fungera och använda sina enheter från valfri plats som de kan ansluta till Internet. Försäkra dig om enheterna är kompatibla och säker har blivit ett viktigt övervägande i ID-lösning som en organisation väljer att implementera. I dagens digitala arbetsplats [identiteten är primär kontrollplanet](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) i alla organisationer flytt till molnet.

I använda en hybrididentitetslösning i Azure Active Directory (AD Azure), tillgång organisationer till premium-funktioner som låser upp produktiviteten med automation, delegering, självbetjäning och enkel inloggning för funktioner. Det gör att dina medarbetare att få åtkomst till företagsresurser från oavsett var de behöver för att utföra sitt arbete samtidigt som IT-teamet att styra den åtkomsten genom att se till att rätt personer har rätt åtkomst till rätt resurser för att upprätta säker produktivitet.

Baserat på våra erfarenheter, den här checklistan för bästa praxis hjälper dig att snabbt distribuera rekommenderade åtgärder för att skapa en *stark* identity foundation i din organisation:

* Anslut enkelt till appar
* Upprätta en identitet för varje användare automatiskt
* Underlätta för dina användare på ett säkert sätt
* Operationalisera dina insikter

## <a name="step-1---connect-to-apps-easily"></a>Steg 1 – Anslut till appar enkelt

Genom att ansluta dina appar med Azure AD kan du förbättra slutanvändarnas produktivitet och säkerhet genom att aktivera enkel inloggning (SSO) och gör etableringen av användare. Du kan minska administrativa kostnader och uppnå en enda åtkomstpunkt för åtkomstkontroll för dina principer för säkerhet och efterlevnad genom att hantera dina appar i en och samma plats, Azure AD.

Det här avsnittet beskriver dina alternativ för att hantera användarnas åtkomst till appar, att aktivera säker fjärråtkomst till interna appar och fördelarna med att migrera dina appar till Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Göra appar tillgängliga för användarna sömlöst

Azure AD låter administratörer [lägga till program](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) till galleriet för Enterprise-program i den [Azure-portalen](https://portal.azure.com/). Att lägga till program i galleriet för Enterprise-program gör det enklare för dig att konfigurera program för att använda Azure AD som identitetsprovider. Du kan även hantera användarnas åtkomst till programmet med principer för villkorlig åtkomst och konfigurera enkel inloggning (SSO) till program så att användarna inte behöver ange sina lösenord flera gånger och automatiskt loggas in både lokalt och molnbaserade program.

När program har lagts till i Azure AD-galleriet kan se användare appar som har tilldelats till dem och söka efter och begära andra appar efter behov. Azure AD tillhandahåller [flera metoder](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) för användare att komma åt sina appar:

* Åtkomstpanelen / Mina appar
* Startprogram för Office 365-app
* Direkt inloggning till federerade appar
* Inloggnings-länkar

Läs mer om användaråtkomst till appar i **steg3 – ge användarna** i den här artikeln.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrera appar från Active Directory Federation Services till Azure AD

Migrera enkel inloggningkonfigurationen från Active Directory Federation Services (ADFS) till Azure AD kan ytterligare funktioner på säkerhet, en mer konsekvent hanterbarhet och samarbete. För bästa resultat rekommenderar vi att du migrerar dina appar från AD FS till Azure AD. Få ut dina program autentisering och auktorisering till Azure AD ger dig följande fördelar:

* Hantera kostnader
* Riskhantering
* Öka produktiviteten
* Efterlevnad och styrning

Mer information finns i den [migrera din program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper) White Paper.

### <a name="enable-secure-remote-access-to-apps"></a>Aktivera säker fjärråtkomst till appar

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) tillhandahåller en enkel lösning för organisationer att publicera lokala appar till molnet för fjärranslutna användare som behöver åtkomst till interna appar på ett säkert sätt. När du har en enkel inloggning till Azure AD, kan användare komma åt både i molnet och lokala program via externa URL: er eller en intern App-portal.

Azure AD Application Proxy ger följande fördelar:

* Utöka Azure AD till lokala resurser
  * Skalbar säkerhet och skydd
  * Funktioner som villkorlig åtkomst och autentisering med flera faktorer som är lätta att aktivera
* Inga komponenter i perimeternätverket, till exempel VPN och traditionella omvänd proxy-lösningar
* Inga inkommande anslutningar som krävs
* Enkel inloggning (SSO) mellan enheter, resurser och appar i molnet och lokalt
* Slutanvändarna att vara produktiva när som helst och var som helst

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Upptäcka skugg-IT med Microsoft Cloud App Security

IT-avdelningar är ofta inte medvetna om alla molnprogram som används av användarna som utför arbetet i moderna företag. När IT-administratörer är och hur många molnbaserade använda appar som de tänker sina anställda, i genomsnitt de anta 30 eller 40. I själva verket är medelvärdet över 1 000 separata appar som används av anställda i din organisation. 80% av anställda använda icke-sanktionerade appar att ingen har granskat och kanske inte är kompatibla med principerna säkerhet och efterlevnad.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) kan hjälpa dig att identifiera användbara appar som är populära bland användarna att den kan sanktionera och lägga till i galleriet för Enterprise-program så att användarna dra nytta av funktioner som enkel inloggning och villkorlig åtkomst.

*”** Cloud App Security** hjälper oss att kontrollera att vår personer korrekt använder vårt moln- och SaaS-program, på ett sätt som stöd för de grundläggande säkerhetsprinciper som skyddar Accenture”. *--- [John Blasi, hantera Director, informationssäkerhet, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Förutom att upptäcka skugg-IT vet också MCAS risknivån för appar, kan du förhindra obehörig åtkomst till företagsdata, möjliga dataläckage och andra säkerhetsrisker i programmen.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Steg 2 – upprätta en identitet för varje användare automatiskt

Sammanför lokala och molnbaserade kataloger i en hybrididentitetslösning i Azure AD kan du återanvända din befintliga lokala Active Directory-investering genom att etablera dina befintliga identiteter i molnet. Lösningen synkroniserar lokala identiteter med Azure AD, samtidigt som IT är fortfarande en lokal Active Directory som körs med alla befintliga lösningar för styrning som den primära källan för sanningen för identiteter. Microsofts Azure AD-hybrididentitetslösning omfattar både lokala och molnbaserade funktioner, skapa en vanlig användaridentitet för autentisering och auktorisering för alla resurser, oavsett deras plats.

Integrera dina lokala kataloger med Azure AD gör användarna mer produktiva och förhindrar att användare kan använda flera konton i alla appar och tjänster genom att tillhandahålla en gemensam identitet för åtkomst till både moln och lokala resurser. Använda flera konton är ett omfattande problem för slutanvändare och IT både. Har flera konton innebär att behöva komma ihåg flera lösenord från en slutanvändare perspektiv. Undvik detta genom återanvända många användare samma lösenord för varje konto, vilket är dåligt ur säkerhetssynpunkt. Ur ett IT-perspektiv leder återanvändning ofta till fler lösenordsåterställning och supportkostnaderna tillsammans med slutanvändarens klagomål.

Azure AD Connect är det verktyg som används för att synkronisera dina lokala identiteter till Azure AD, som sedan kan användas för åtkomst till molnprogram. När identiteterna som är i Azure AD, kan de etablera till SaaS-program som Salesforce och Concur.

I det här avsnittet vi en lista med rekommendationer för att tillhandahålla hög tillgänglighet, modern autentisering för molnet, och minskar storleken på plats.

> [!NOTE]
> Om du vill veta mer om Azure AD Connect finns i [vad är Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Konfigurera en fristående server för Azure AD Connect och hålla den uppdaterad

Azure AD Connect spelar en viktig roll i etableringsprocessen. Om synkroniseringsservern försätts offline av någon anledning, ändringar i lokala uppdateras inte i molnet och orsaka problem med åtkomst till användare. Det är viktigt att definiera en strategi för växling vid fel som gör att administratörer kan snabbt återuppta synkroniseringen när sync-servern går offline.

För att tillhandahålla hög tillgänglighet i händelsen din primära Azure AD Connect-servern går offline, rekommenderar vi att du distribuerar en separat [mellanlagring server](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) för Azure AD Connect. Distribuera en server kan administratören till ”” mellanlagringsserver till produktion genom en enkel konfiguration-växel. Att ha en reservserver som konfigurerats i mellanlagringsläge kan du också att testa och distribuera nya konfigurationsändringar och introducera en ny server om inaktivering av den gamla servern.

> [!TIP]
> Azure AD Connect uppdateras regelbundet. Vi rekommenderar därför starkt att du behåller testservern aktuella för att kunna dra nytta av den prestandaförbättringar, felkorrigeringar och nya funktioner som ger varje ny version.

### <a name="enable-cloud-authentication"></a>Aktivera autentisering i molnet

Organisationer med lokal Active Directory ska utöka sin katalog till Azure AD med Azure AD Connect och konfigurera en passande autentiseringsmetod. [Välja rätt autentiseringsmetod](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) för din organisation är det första steget i din resa med att flytta appar till molnet. Det är en kritisk komponent eftersom den styr åtkomst till alla data i molnet och resurser.

Den enklaste och rekommenderade metoden för att aktivera autentisering i molnet för den lokala katalogobjekt i Azure AD är att aktivera [Lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Vissa organisationer kan också överväga att aktivera [direktautentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Om du väljer PHS eller PTA, Glöm inte att aktivera [sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) att ge användare åtkomst till appar i molnet utan ständigt att ange sitt användarnamn och lösenord i appen när du använder Windows 7 och 8-enheter på ditt företags nätverk. Utan enkel inloggning, användare måste komma ihåg programspecifika lösenord och logga in varje program. På samma sätt måste IT-personal att skapa och uppdatera användarkonton för varje program som Office 365, Box och Salesforce. Användare måste komma ihåg sina lösenord, samt lägga tid att logga in på varje program. Att tillhandahålla en standardiserad enkel inloggning mekanism för att hela företaget är avgörande för bästa användarupplevelsen, minskning av risk, möjlighet att rapportera och styrning.

För organisationer som redan använder AD FS eller en annan lokal autentiseringsprovider flyttar till Azure AD som identitetsprovider kan minska komplexiteten och förbättra tillgängligheten. Om du inte har specifika användningsfall för att använda federation, rekommenderar vi att du migrerar från federerad autentisering till antingen PHS och sömlös enkel inloggning eller PTA och sömlös SSO att dra nytta av fördelarna med ett lägre lokala fotavtryck och flexibilitet som molnet erbjuder med Förbättrad användarupplevelser. Mer information finns i [migrera från federation till synkronisering av lösenordshash för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Aktivera automatisk borttagning av konton

Att aktivera automatisk etablering och avetablering för dina program är den bästa strategin för reglerar livscykeln för identiteter över flera system. Azure AD stöder [automatiserad, grupprincipbaserad etablering och borttagning](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) av användarkonton till en mängd olika populära SaaS-program som ServiceNow och Salesforce som implementerar den [SCIM 2.0 protokollet](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Till skillnad från traditionella etablering lösningar som kräver anpassad kod eller manuell överföring av CSV-filer, etableringstjänsten finns i molnet och funktioner förintegrerade anslutningar som kan konfigureras och hanteras med hjälp av Azure-portalen. En viktig fördel med automatisk avetablering är att den kan du skydda din organisation genom att direkt ta bort användarnas identiteter från viktiga SaaS-appar när de lämnar organisationen.

Läs mer om automatisk användarkontoetablering och hur det fungerar i [automatisera Användaretablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Steg 3 – stärka dina användare på ett säkert sätt

I dagens digitala arbetsplats är det viktigt att belastningsutjämna säkerhet med produktivitet. Men skicka slutanvändare ofta tillbaka på säkerhetsåtgärder som försämrar deras produktivitet och åtkomst till molnappar. För att lösa det, Azure AD innehåller självbetjäning som kan användas att förbli produktiva och minimerar administrativa kostnader.

Det här avsnittet innehåller rekommendationer för att ta bort friktionen från din organisation genom att förse dina användare samtidigt som de behåller vaksam.

### <a name="enable-self-service-password-reset-for-all-users"></a>Aktivera självbetjäningsportalen för lösenordsåterställning för alla användare

Azures [lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) erbjuder ett enkelt sätt för IT-administratörer att tillåta användare att återställa och låsa upp sina lösenord eller sina konton utan inblandning av administratören. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

Som standard låser Azure AD upp konton när den utför en återställning av lösenord. Men när du aktiverar Azure AD Connect [integrering på lokala](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), du har också möjlighet att dela dessa två åtgärder som användarna kan låsa upp sitt konto utan att behöva återställa lösenordet.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Se till att alla användare har registrerats för MFA och SSPR

Azure tillhandahåller rapporter som kan användas av dig och din organisation att se till att användarna har registrerats för MFA och SSPR. Användare som inte har registrerat kan måste utbildas om processen.

MFA [rapporten inloggningar](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) innehåller information om MFA-användningen och ger dig insikter om hur MFA fungerar i din organisation. Att ha åtkomst till inloggning aktivitet (och granskningar och riskhändelser) för Azure AD är avgörande för felsökning, användningsanalys och stöda utredningar.

På samma sätt kan den [Self-service lösenordshantering rapporten](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) kan användas för att fastställa vem som har (eller inte) som har registrerats för SSPR.

### <a name="self-service-app-management"></a>Självbetjäning apphantering

Innan användarna kan själva upptäcka program från sina åtkomstpaneler, måste du aktivera [självbetjäningsåtkomsten](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) för program som du vill tillåta användare att identifiera själv och begära åtkomst till. Självbetjäning för programåtkomst är ett bra sätt att låta användare själva utforska program och om du vill tillåta affärsgrupp att godkänna åtkomst till dessa program. Du kan tillåta affärsgrupp att hantera de autentiseringsuppgifter som är tilldelade till de användarna för [lösenord för enkel inloggning på program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-the-application-for-password-single-sign-on) direkt från sina åtkomstpaneler.

### <a name="self-service-group-management"></a>Självbetjäning, grupphantering

Tilldela användare till program mappas bäst när du använder grupper, eftersom de låter stor flexibilitet och möjlighet att hantera i stor skala:

* Attributbaserad med dynamiska gruppmedlemskap
* Delegering till app-ägare

Azure AD tillhandahåller möjligheten att hantera åtkomst till resurser med hjälp av säkerhetsgrupper och Office 365-grupper. Dessa grupper kan hanteras av en gruppägare som kan godkänna eller neka förfrågningar om medlemskap och delegera kontrollen av gruppmedlemskap. Kallas även [självbetjäningsgrupphantering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), den här funktionen sparar tid genom att låta gruppägare som inte har tilldelats en administrativ roll för att skapa och hantera grupper utan att behöva förlita dig på administratörer att hantera sina begäranden.

## <a name="step-4---operationalize-your-insights"></a>Steg 4 – Operationalisera dina insikter

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en effektiv strategi för att säkerställa att de vara fortsatt produktiva och din organisation är säker. Säkerhetsloggar och rapporter kan hjälpa att besvara fråga som:

* Använder du vad du betalar för?
* Finns det något misstänkt eller skadliga sker i min klient?
* Vem som har påverkats under en säkerhetsincident?

Säkerhetsloggar och rapporter ger dig en elektronisk post av misstänkta aktiviteter och hjälper dig att du upptäcka mönster som kan indikera ett försök har gjorts eller lyckad externa penetrationstester av nätverket och interna attacker. Du kan använda granskning för att övervaka användaraktivitet, dokumentet regelefterlevnad, göra kriminalteknisk analys med mera. Aviseringar tillhandahåller meddelanden av säkerhetshändelser.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Tilldela minst Privilegierade administrativa roller för åtgärder

När du funderar din metod för att operations finns det ett par nivåer av administration att tänka på. Den första nivån placerar arbetet med administration på dina globala administratörer. Alltid använder rollen global administratör, kan vara lämpligt för mindre företag. Men större organisationer med supportpersonal och administratörer som är ansvarig för specifika uppgifter kan tilldela rollen global administratör kan utgöra en säkerhetsrisk eftersom den ger möjlighet att hantera uppgifter som ligger ovanför och utanför de personer vad de ska kunna göra.

I det här fallet bör du överväga nästa nivå i administration. Du kan använda Azure AD för att ange slutanvändare som ”begränsade administratörer” som kan hantera uppgifter i mindre privilegierade roller. Exempel: du kan tilldela din hjälp för supportavdelningens personal i [säkerhetsläsare](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) roll att ge dem möjlighet att hantera säkerhetsrelaterade funktioner med skrivskyddad åtkomst. Eller kanske det vara bra att tilldela den [administratören authentication](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) rollen för enskilda användare att ge dem möjlighet att återställa autentiseringsuppgifter för icke-password eller läsa och konfigurera Azure Service Health.

Mer information finns i [behörigheter för administratör i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Övervaka hybrid komponenter (Azure AD Connect sync AD FS) med Azure AD Connect Health

Azure AD Connect och AD FS är viktiga komponenter som kan bryta potentiellt livscykelhantering och autentisering och slutligen leder till avbrott. Därför bör du distribuera Azure AD Connect Health för övervakning och rapportering av dessa komponenter.

Om du vill veta mer går du Läs [övervaka AD FS med hjälp av Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Använda Azure Monitor för att samla in loggar för analys

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) är en enhetlig övervakning portal för alla Azure AD-loggar som ger fördjupad kunskap, avancerad analys och smart maskininlärning. Med Azure Monitor kan använda du mått och loggar i portalen och API: er för att få mer detaljerad information om tillstånd och prestanda för dina resurser. Det gör att ett fönster för glas upplevelse i portalen vid aktivering av en mängd olika produktintegreringar via API: er och data exportalternativ som har stöd för traditionella SIEM-system från tredje part. Azure Monitor får du också möjligheten att konfigurera Varningsregler att få ett meddelande eller för att vidta automatiska åtgärder på problem som påverkar dina resurser.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Skapa anpassade instrumentpaneler för din ledarskap och din dag till dag

Organisationer som inte har en SIEM-lösning kan ladda ned den [Power BI-Innehållspaketet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) för Azure AD. Power BI content pack innehåller fördefinierade rapporter som hjälper dig att förstå hur dina användare införa och använda Azure AD-funktioner, där du kan få insikter om alla aktiviteter i din katalog. Du kan också skapa egna [anpassad instrumentpanel](https://docs.microsoft.com/power-bi/service-dashboards) och dela med dina ledning för att rapportera om dagliga aktiviteter. Instrumentpaneler är ett bra sätt att övervaka din verksamhet och se alla dina viktigaste mätdata på ett ögonblick. Visualiseringarna på en instrumentpanel kan komma från en underliggande datauppsättning eller flera och från en underliggande rapport eller flera. En instrumentpanel kombinerar lokala och molnbaserade data, vilket ger en samlad vy oavsett var data finns.

![Anpassad instrumentpanel i Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Förstå din drivrutiner för anrop

När du implementerar en hybrididentitetslösning som beskrivs i den här artikeln bör du slutligen märka en minskning i din supportsamtal. Vanliga problem som glömda lösenord och kontoutelåsningar begränsas genom att implementera Azures lösenordsåterställning via självbetjäning, samtidigt som självbetjäningsåtkomsten tillåter användare att identifiera själv och begära åtkomst till program utan att behöva på din IT-personal.

Om du inte ser en minskning av supportsamtal kan rekommenderar vi att du analysera dina anrop drivrutiner i ett försök att bekräfta om SSPR eller självbetjäningsåtkomsten har konfigurerats korrekt eller om det finns några nya problem som kan vara systematiskt åtgärdas.

*”I vår digitala omställning vi behövde en pålitlig identitets- och access management provider som underlättar sömlös ännu säker integrering mellan USA, partners och leverantörer av molntjänster, för en effektiv ekosystemet. Azure AD var det bästa alternativet erbjuder oss nödvändiga funktioner och synlighet som vi kunnat identifiera och svara på risker ”.* --- [Yazan Almasri, Global Information Security Director, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Övervaka din användning av appar för att få bättre insikt

Förutom att upptäcka skugg-IT, övervakning av användning i din organisation med hjälp av [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) kan hjälpa din organisation när du migrerar till dra full nytta av molnapparnas potential. Det kan hålla dig kontroll över dina tillgångar med ökad insyn i aktiviteten och öka skyddet av viktiga data i molnappar. Övervaka användning i din organisation med MCAS kan hjälpa dig att besvara följande frågor:

* Vilka osanktionerade appar anställda använder för att lagra data i?
* Var och när känsliga data som lagras i molnet?
* Vem har åtkomst till känsliga data i molnet?

*”Med Cloud App Security, vi kan snabbt upptäcka avvikelser och vidta åtgärder”.* --- [Eric LePenske, Senior Manager, informationssäkerhet, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Sammanfattning

Det finns många sätt att implementera en hybrid Identity-lösning, men den här checklistan i fyra steg hjälper dig att snabbt utföra en identitetsinfrastruktur som gör att användare kan bli mer produktiv och säker.

* Anslut enkelt till appar
* Upprätta en identitet för varje användare automatiskt
* Underlätta för dina användare på ett säkert sätt
* Operationalisera dina insikter

Vi hoppas att det här dokumentet är en bra översikt över att upprätta en stark identitet grund för din organisation.

## <a name="identity-checklist"></a>Checklista för identitet

Vi rekommenderar att du skriver ut följande checklista som referens när du börjar din resa till en mer stabil grund identitet i din organisation.

### <a name="today"></a>I dag

|Klart?|Objekt|
|:-|:-|
||Pilot - Self Service lösenord återställa (SSPR) för en grupp|
||Övervaka hybrid komponenter med hjälp av Azure AD Connect Health|
||Tilldela minst Privilegierade administrativa roller för åtgärden|
||Upptäcka skugg-IT med Microsoft Cloud App Security|
||Använda Azure Monitor för att samla in loggar för analys|

### <a name="next-two-weeks"></a>Kommande två veckorna

|Klart?|Objekt|
|:-|:-|
||Skapa en app som är tillgängliga för dina användare|
||Azure AD-etablering för en SaaS-app för val av pilotprojekt|
||Konfigurera en fristående server för Azure AD Connect och hålla den uppdaterad|
||Börja migrera appar från AD FS till Azure AD|
||Skapa anpassade instrumentpaneler för din ledarskap och din dag till dag|

### <a name="next-month"></a>Nästa månad

|Klart?|Objekt|
|:-|:-|
||Övervaka din användning av appar för att få bättre insikt|
||Pilot säker fjärråtkomst till appar|
||Se till att alla användare har registrerats för MFA och SSPR|
||Aktivera autentisering i molnet|

### <a name="next-three-months"></a>Kommande tre månaderna

|Klart?|Objekt|
|:-|:-|
||Aktivera självbetjäning apphantering|
||Aktivera grupphantering|
||Övervaka din användning av appar för att få bättre insikt|
||Förstå din drivrutiner för anrop|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan öka din säkerhetsställning med hjälp av funktionerna i Azure Active Directory och checklistan fem steg - [fem steg för att skydda din infrastruktur för Identitetshantering](https://aka.ms/securitysteps).

Lär dig hur identitetsfunktioner i Azure AD kan hjälpa dig snabbare övergång till molnet styrda management genom att tillhandahålla lösningar och funktioner som gör att organisationer snabbt anta och flytta flera av deras Identitetshantering från traditionella lokala system till Azure AD - [hur Azure AD tillhandahåller regleras Molnhantering för lokala arbetsbelastningar](https://aka.ms/cloudgoverned).
