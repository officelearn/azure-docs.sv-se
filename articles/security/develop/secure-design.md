---
title: Utforma säkra program i Microsoft Azure
description: Den här artikeln beskrivs bästa praxis att överväga under krav och designstegen för ditt program webbprojekt.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653288"
---
# <a name="design-secure-applications-on-azure"></a>Utforma säkra program på Azure
I den här artikeln visar vi säkerhetsaktiviteter och kontroller att tänka på när du utformar program för molnet. Utbildningsresurser tillsammans med säkerhetsfrågor och begrepp att överväga under kraven och utforma faser i Microsofts [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att utforma en säkrare program.

Följande faser för SDL-processen beskrivs i den här artikeln:

- Utbildning
- Krav
- Design

## <a name="training"></a>Utbildning
Innan du börjar utveckla ditt molnprogram, ta reda på säkerhet och sekretess i Azure. Genom att utföra det här steget kan minska du antalet och deras allvarlighetsgrad exploaterbar sårbarheter i ditt program. Du kommer att tur att reagera på lämpligt sätt på hotbild föränderliga.

Använd följande resurser under valet utbildning för att bekanta dig med Azure-tjänster som är tillgängliga för utvecklare och säkerhetsmetoder på Azure:

  - [Utvecklarens guide till Azure](https://azure.microsoft.com/campaigns/developer-guide/) visar hur du kommer igång med Azure. Guiden visar vilka tjänster som du kan använda för att köra dina program, datalagring, införlivar intelligens, skapa IoT-appar och distribuera dina lösningar på ett mer effektivt och säkert sätt.

  - [Startguide för Azure-utvecklare](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) innehåller viktig information för utvecklare som vill komma igång med Azure-plattformen för sin utveckling behöver.

  - [SDK: er och verktyg](https://docs.microsoft.com/azure/index#pivot=sdkstools) beskrivs de verktyg som är tillgängliga på Azure.

  - [Azure DevOps-tjänster](https://docs.microsoft.com/azure/devops/) ger utveckling samarbetsverktyg. Verktygen innehåller högpresterande pipelines, kostnadsfri Git-lagringsplatser, konfigurerbara Kanban-kort och omfattande automatiserad och molnbaserad belastningstestning.
    Den [DevOps Resurscenter](https://docs.microsoft.com/azure/devops/learn/) kombinerar våra inlärningsresurser för DevOps metoder, Git versionskontroll, agila metoder, hur vi arbetar med DevOps vid Microsoft och hur du kan utvärdera dina egna DevOps-förlopp.

  - [Viktigaste 5 security saker att tänka på innan du skickar till produktion](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) visar hur du kan skydda dina webbprogram på Azure och dina appar mot de vanligaste och farliga web application attacker.

  - [Secure DevOps Kit för Azure](https://azsk.azurewebsites.net/index.html) är en samling av skript, verktyg, tillägg och automatiseringar som caters till omfattande Azure-prenumeration och resursgrupp säkerhetsbehov DevOps-team som använder omfattande automation. Secure DevOps Kit för Azure kan visar hur du smidigt integrera säkerhet i inbyggda DevOps-arbetsflöden. I kit adresser verktyg som security verifieringstest (SVTs), som kan hjälpa utvecklare att skriva säkra kod och testa den säkra konfigurationen sina molnprogram i stegen för kodning och tidig utveckling.

  - [Rekommenderade säkerhetsmetoder för Azure-lösningar](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) innehåller en uppsättning security bästa praxis att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.

## <a name="requirements"></a>Krav
Utvecklingsfasen krav är ett viktigt steg för att definiera vad ditt program är och vad den gör när det har släppts. Den krav är ett viktigt att tänka på säkerhetskontrollerna som du skapar i ditt program. Under den här fasen kan börja du också med stegen som du kommer att utföras i hela SDL-processen så att du släpper och distribuera ett säkert program.

### <a name="consider-security-and-privacy-issues"></a>Överväg att problem med säkerhet och sekretess
Den här fasen är den bästa tidpunkten att tänka på grundläggande säkerhet och sekretessproblem. Definiera acceptabel nivå av säkerhet och sekretess i början av ett projekt kan ett team:

- Förstå riskerna med säkerhetsproblem.
- Identifiera och åtgärda buggar på säkerhet vid utveckling.
- Använd etablerade nivåer av säkerhet och sekretess i hela projektet.

När du skriver kraven för ditt program bör du överväga att säkerhetskontroller som kan hjälpa dig att skydda dina program och data.

### <a name="ask-security-questions"></a>Be säkerhetsfrågor
Be säkerhetsfrågor som:

  - Innehåller känsliga data i mitt program?

  - Varken mitt program samlar in eller lagra data som kräver att jag följer branschstandarder och efterlevnadsprogram som den [Federal finansiella Institution undersökning rådet (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) eller [Payment Card Industry Data Security Standards (PCI-DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - Mitt program samlar in eller innehåller känsliga data för personal eller kund som kan användas, antingen på egen hand eller med andra information, identifiera, kontakta eller leta upp en enskild person?

  - Varken mitt program samlar in eller innehåller data som kan användas för att få åtkomst till en enskild persons medicin, utbildning, finansiella institutioner eller anställningsinformation? Identifiera dina datas känslighet under fasen krav hjälper dig att klassificera dina data och identifiera dataskyddsmetod du ska använda för ditt program.

  - Hur och var lagras mina data? Överväg hur du övervakar de storage-tjänster som programmet använder för eventuella oväntade ändringar (till exempel längre svarstider). Kommer du att kunna påverka loggning för att samla in mer detaljerade data och analysera ett problem i detalj?

  - Mitt program kommer vara tillgängliga för allmänheten (på internet) eller internt bara? Om ditt program är tillgängliga för allmänheten, hur kan du skydda de data som samlas in används på fel sätt? Om ditt program är tillgängligt internt endast kan du vem i din organisation ska ha åtkomst till programmet och hur länge de ska ha åtkomst.

  - Förstår du din modell identitet innan du börjar utforma ditt program? Hur du avgör vilka användare som de säger att de är och vad en användare har behörighet att göra?

  - Utför mitt program känslig eller viktig aktiviteter (till exempel överföra pengar, att låsa upp dörrar eller leverera medicin)?
    Fundera över hur du ska kontrollera att användaren som utför en känslig uppgift har behörighet att utföra uppgiften och hur du ska autentisera att personen är de säger att de är. Auktorisering (AuthZ) är handling för att bevilja en autentiserad huvudnamn behörighet att göra något. Autentisering (AuthN) är av en utmaning en part för giltiga autentiseringsuppgifter.

  - Utför riskfyllda programvara-aktiviteter, till exempel så att användarna kan överföra eller hämta filer eller andra data i mitt program? Om ditt program utför riskfyllda aktiviteter, kan du överväga hur ditt program skyddar användare från hantering av skadliga filer eller data.

### <a name="review-owasp-top-10"></a>Granska OWASP topp 10
Överväg att granska den [ <span class="underline">OWASP Top 10 programmet säkerhetsrisker</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
De OWASP 10 adresser kritiska säkerhetsrisker för webbprogram.
Medvetenhet om dessa säkerhetsrisker kan hjälpa dig att fatta beslut om krav och design som minimerar riskerna i ditt program.

Det är viktigt att tänka på säkerhetskontroller så att inga uppstår.
Men du även vill [förutsätt en överträdelse](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) sker. Om vi antar att ett intrång hjälper dig att besvara några viktiga frågor om säkerhet i förväg så att de inte behöver besvaras i en nödsituation:

  - Hur kommer jag för att identifiera en attack?

  - Vad ska jag göra om det är en attack eller intrång?

  - Hur ska jag återställa från angrepp som data läcker från Microsoft eller manipulera?

## <a name="design"></a>Design

Designfasen är viktigt för att upprätta Metodtips för design och funktionella specifikationer. Det är också viktigt för att utföra riskanalys som hjälper till att minska problem för säkerhet och sekretess i ett projekt.

När du har krav på säkerhet på plats och använder säker designbegrepp, kan du undvika eller minimera möjligheter för en säker. En säker är en tillsyn i utformningen av det program som kan låta en användare att utföra skadliga eller oväntat åtgärder när ditt program har släppts.

Under designfasen, också tänka på hur du kan använda säkerhet i lager. en nivå av skydd är inte nödvändigtvis tillräckligt. Vad händer om en angripare får de senaste din brandvägg för webbaserade program (WAF)? Du vill ha en annan security kontroll på plats för att skydda mot den attacken.

Med detta i åtanke diskuterar vi följande säker designbegrepp och de säkerhetskontroller som du bör hantera när du utformar säkra program:

- Använd en säker kodning bibliotek och ett ramverk för programvara.
- Söka efter sårbara komponenter.
- Använd threat modeling under programmets design.
- Minska din attackyta.
- Anta en princip av identitet som primär säkerhetsperimeter.
- Kräv omautentisering för viktiga transaktioner.
- Använda en lösning för hantering av nycklar för att skydda nycklar, autentiseringsuppgifter och andra hemligheter.
- Skydda känsliga data.
- Implementera felsäker mått.
- Dra nytta av fel och undantagshantering.
- Använd loggning och aviseringar.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Använd en säker kodning bibliotek och ett ramverk för programvara

Använd en säker kodning bibliotek och ett ramverk för programvara som har inbäddat säkerhet för utveckling. Utvecklare kan använda befintliga, beprövade funktioner (kryptering, sanering indata, utdatakodning, nycklar eller anslutningssträngar och allt annat som kan betraktas som en säkerhetskontroll) istället för att utveckla säkerhetskontroller från grunden. Detta hjälper att skydda mot säkerhetsrelaterade design och implementering fel.

Var noga med att du använder den senaste versionen av ditt ramverk och alla säkerhetsfunktioner som är tillgängliga inom ramen för. Microsoft erbjuder en omfattande [uppsättning utvecklingsverktyg](https://azure.microsoft.com/product-categories/developer-tools/) för alla utvecklare, arbetar på valfri plattform eller språk, molnappar. Du kan koda med valfritt språk genom att välja bland olika [SDK: er](https://azure.microsoft.com/downloads/).
Du kan dra nytta av fullständig integrated development Environment (IDE: er) och redigerare som har avancerade funktioner för felsökning och inbyggt stöd för Azure.

Microsoft erbjuder en mängd olika [språk, ramverk och verktyg](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) som du kan använda för att utveckla program på Azure. Ett exempel är [Azure för .NET och .NET Core-utvecklare](https://docs.microsoft.com/dotnet/azure/). För varje språk och ramverk som vi erbjuder, hittar du snabbstarter, självstudier och API-referenser hjälper dig att komma igång snabbt.

Azure erbjuder en mängd olika tjänster som du kan använda som värd för webbplatser och webbprogram program. De här tjänsterna kan du utveckla i språket du föredrar, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python.
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (Webbappar) är en av dessa tjänster.

Web Apps lägger till kraften hos Microsoft Azure i ditt program. Den innehåller säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan också dra nytta av DevOps-funktioner i Web Apps som pakethantering, för att mellanlagra miljöer, anpassade domäner, SSL/TLS-certifikat och kontinuerlig distribution från Azure DevOps, GitHub, Docker Hub och andra källor.

Azure erbjuder andra tjänster som du kan använda som värd för webbplatser och webbprogram program. I de flesta fall är Web Apps det bästa alternativet. Överväg att för en arkitektur för micro service [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Om du behöver större kontroll över de virtuella datorer koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Mer information om hur du väljer mellan dessa Azure-tjänster finns i en [jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Tillämpa uppdateringar för komponenter

Om du vill förhindra säkerhetsrisker, bör du kontinuerligt inventera både dina på klientsidan och serversidan komponenter (till exempel ramverk och bibliotek) och deras beroenden efter uppdateringar. Säkerhetsproblem med nya och uppdaterade programvaruversioner släpps kontinuerligt. Kontrollera att du har en pågående plan för att övervaka, hantera och tillämpa uppdateringar eller konfigurationsändringar till de bibliotek och komponenter som du använder.

Se den [OWASP Open Web Application Security Project ()](https://www.owasp.org/index.php/Main_Page) sidan på [med hjälp av komponenter med kända säkerhetsrisker](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) för förslag. Du kan även prenumerera på e-postaviseringar för säkerhetsproblem som är relaterade till komponenter som du använder.

### <a name="use-threat-modeling-during-application-design"></a>Använda threat modeling under programmets design

Hotmodellering är processen att identifiera potentiella säkerhetshot på din verksamhet och programmet, och att säkerställa att rätt åtgärder är uppfyllda. SDL-processen anger att grupper ska delta i threat modeling under designfasen när lösa potentiella problem är relativt enkelt och kostnadseffektivt. Med hjälp av threat modellering under designfasen kan avsevärt minska den totala kostnaden för utveckling.

För att underlätta hotet modellera processen, vi har utformat den [SDL Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) med icke-säkerhetsexperter i åtanke. Det här verktyget gör hotmodellering enklare för alla utvecklare genom att ange tydliga instruktioner om hur du skapar och analysera hotmodeller.

Modellering programmets design och räkna upp [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) hot – Spoofing, Tampering, Repudiation, avslöjande av Information, Denial of Service och utökade rättigheter – över alla förtroende gränser har visat ett effektivt sätt att fånga upp design fel tidigt. I följande tabell visas STRIDE hot och ger vissa exempel åtgärder som använder funktioner som tillhandahålls av Azure. Dessa åtgärder fungerar inte i alla situationer.

| Hot | Egenskapen för säkerhet | Minskning av potentiella Azure-plattformen |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Förfalskning               | Authentication        | [Kräver HTTPS klientanslutningar](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manipulation              | Integritet             | Verifiera SSL/TLS-certifikat. Program som använder SSL/TLS måste fullständigt Kontrollera X.509-certifikat för de entiteter som de ansluter till. Använda Azure Key Vault-certifikat till [hantera din x509 certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| Repudiation            | Non-repudiation       | Aktivera Azure [övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Avslöjande av information | Sekretess       | Kryptera känsliga data [vilande](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) och [under överföring](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Denial of Service-attack      | Tillgänglighet          | Övervaka prestandamått potentiella DOS av tjänsten villkor. Implementera anslutningsfilter. [Azure DDoS protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), i kombination med metodtips för design av program, ger skydd mot DDoS-attacker.|
| Upphöjning av rättigheter | Authorization         | Använd Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>Minska din attackyta

En risken för angrepp är det totala antalet summan av där potentiella problem som kan uppstå. I det här dokumentet fokusera vi på ett programs risken för angrepp.
Fokus ligger på att skydda ett program mot angrepp. Ett snabbt och enkelt sätt att minimera risken för angrepp är att ta bort oanvända resurser och kod från ditt program. Det mindre ditt program, det mindre angrepp. Till exempel ta bort:

- Kod för funktioner som ännu inte har släppts.
- Felsökning supportkod.
- Nätverksgränssnitt och protokoll som inte används eller som har blivit inaktuella.
- Virtuella datorer och andra resurser som du inte använder.

Göra reguljära Rensa dina resurser och försäkra dig om att du tar bort oanvända kod är bra sätt att se till att det finns färre möjligheter för skadliga aktörer för angrepp.

En mer detaljerad och djupgående sätt att minska din attackyta är att slutföra en attack surface-analys. En attack surface analys kan du mappa delarna av ett system som behöver granskas och testas för säkerhetsrisker.

Syftet med en attack surface analys är att förstå riskområden i ett program så att utvecklare och specialister inom företagssäkerhet är medveten om vilka delar av programmet är öppna för angrepp. Du kan sedan hitta sätt att minimera det här potentiella, spåra när och hur risken för angrepp ändras och vad det innebär en risk ur.

En attack surface analys hjälper dig att identifiera:

- Funktioner och delar av systemet måste du granska och testa för säkerhetsrisker.
- Med hög risk delar av kod som kräver skydd på djupet skydd (delar av systemet som du behöver för att skydda).
- När du alter risken för angrepp och måste du uppdatera en hotbedömning.

Minska möjligheter för angripare att utnyttja en potentiell svaga plats eller säkerhetsproblem måste du noggrant analysera programmets övergripande risken för angrepp. Den innehåller också inaktivera eller begränsa åtkomst till systemtjänster, använda principen om lägsta behörighet och använda försvar i flera lager där så är möjligt.

Vi går igenom [genomför en attack surface granskning](secure-develop.md#conduct-attack-surface-review) under fasen för verifiering av SDL-processen.

> [!NOTE]
> **Vad är skillnaden mellan hotmodellering och attack surface analys?**
Hotmodellering är processen att identifiera potentiella säkerhetshot mot ditt program och att säkerställa att rätt åtgärder mot hot är på plats. Attack surface analysis identifierar med hög risk delar av kod som är öppna för angrepp. Det innebär att hitta sätt att försvara med hög risk delar av programmet och granska och testa dessa delar av kod innan du distribuerar programmet.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Anta en princip av identitet som primär säkerhetsperimeter

Det är viktigt att utöka security perimeter fokus från ett nätverk arbetssätt till en identity-centric metod när du utformar molnbaserade program. Historiskt sett har säkerhetsperimeter primära lokala en organisations nätverk. De flesta lokala security-Designer använder nätverket som primär security pivot. För program i molnet betjänas du bättre av överväger identitet som primär säkerhetsperimeter.

Saker du kan göra för att utveckla en identity-centric metod för att utveckla webbprogram:

- Använda multifaktorautentisering för användare.
- Använd stark autentisering och auktorisering plattformar.
- Tillämpa principen om lägsta behörighet.
- Implementera just-in-time-åtkomst.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Använda multifaktorautentisering för användare

Använda tvåfaktorsautentisering. Tvåfaktorsautentisering är den aktuella standarden för autentisering och auktorisering eftersom de undviker security-svagheter som ingår i användarnamnet och lösenordet typer av autentisering. Åtkomst till Azure-hanteringsgränssnitt (Azure portal/fjärråtkomst PowerShell) och till kundinriktad tjänster bör utformade och konfigurerade för att använda [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Använda stark autentisering och auktorisering plattformar

Använd plattform anger mekanismer för autentisering och auktorisering istället för anpassad kod. Det beror på att utveckla anpassade Autentiseringskod kan antas vara felbenägna. Kommersiella kod (till exempel från Microsoft) ofta granskas stor utsträckning för säkerhet. [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är Azure-lösning för identitets- och åtkomsthantering. De här Azure AD-verktyg och tjänster du underlätta säker utveckling:

- [Azure AD identity-plattformen (Azure AD för utvecklare)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) är en molnbaserad identitetstjänst som utvecklare använder för att skapa appar som på ett säkert sätt logga in användare. Azure AD hjälper utvecklare som skapar en enda klient, line-of-business (LOB) appar och utvecklare som vill utveckla appar för flera klienter. Förutom grundläggande inloggning, kan appar som skapats med hjälp av Azure AD anropa Microsoft APIs och anpassade API: er som bygger på Azure AD-plattformen. Azure AD identity-plattformen stöder branschstandardprotokollen som OAuth 2.0 och OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) är en identitetshanteringstjänst som du kan använda för att anpassa och styra hur kunderna registrerar jag mig, logga in och hanterar sina profiler när de använder dina program. Detta innefattar program som utvecklas för iOS, Android och .NET, bland annat. Azure AD B2C möjliggör dessa åtgärder samtidigt som kundernas identiteter skyddas.

#### <a name="apply-the-principle-of-least-privilege"></a>Tillämpa principen om lägsta behörighet

Begreppet [lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) innebär att ge användare den exakta nivån av åtkomst till och kontroll som de behöver för att göra sitt arbete och inget mer.

Behöver programutvecklare administratörsrättigheter i domänen? En administrativ assistent skulle behöver åtkomst till administrativa kontroller på sina datorer? Utvärdering av åtkomst till program fungerar på samma. Om du använder [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) för att ge användare olika funktioner och utfärdare i ditt program, skulle du ge alla åtkomst till allt. Genom att begränsa åtkomst till vad som krävs för varje roll kan begränsa du risken för en security problemet inträffar.

Se till att ditt program tillämpar [lägsta behörighet](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) under hela dess mönster i databasåtkomst.

> [!NOTE]
>  Reglerna för lägsta behörighet måste tillämpas för programvaran och för de personer som skapar programvara. Programutvecklare kan vara en stor säkerhetsrisk IT om de får för mycket åtkomst. Konsekvenserna kan vara allvarligt om en utvecklare har skadligt syfte eller får för mycket åtkomst. Vi rekommenderar att reglerna för lägsta behörighet tillämpas för utvecklare genom hela livscykeln.

#### <a name="implement-just-in-time-access"></a>Implementera just-in-time-åtkomst

Implementera *just-in-time* (JIT) åtkomst till ytterligare sänka exponeringstiden för privilegier. Använd [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) till:

- Ge användare behörighet de behöver bara JIT.
- Tilldela roller för en förkortad varaktighet tryggt att privilegier återkallas automatiskt.

### <a name="require-re-authentication-for-important-transactions"></a>Kräv omautentisering för viktiga transaktioner

[Förfalskning av begäran mellan webbplatser](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (även kallat *XSRF* eller *CSRF*) är en attack mot webbaserat appar som påverkar en skadlig webbapp interaktionen mellan en klientens webbläsare och en webbplats App som litar på den webbläsaren. Attacker med förfalskning av begäran är möjligt eftersom webbläsare skickar vissa typer av autentiseringstoken automatiskt med varje begäran till en webbplats.
Den här typen av utnyttjande kallas även en *klick attack* eller *session kör* eftersom angreppet drar nytta av användaren tidigare är autentiserad session.

Det bästa sättet att skydda mot den här typen av angrepp är att be användaren ange något som bara användaren kan ange innan alla viktiga transaktioner, till exempel ett köp, inaktivering av konto eller en lösenordsändring. Du kan be användaren att ange lösenordet igen, Slutför en captcha eller skicka en hemlig token som bara användaren själv. Den vanligaste metoden är hemlig token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Använd en lösning för hantering av nycklar för att skydda nycklar, autentiseringsuppgifter och andra hemligheter

Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. Enda sämre resultat än att förlora dina nycklar och autentiseringsuppgifter som har en obehörig part få åtkomst till dem. Angripare kan dra nytta av automatisk och manuell tekniker för att hitta nycklar och hemligheter som lagras i källkodsdatabaser som GitHub. Placera inte nycklar och hemligheter i de här offentliga lagringsplatser eller på andra servrar.

Placera alltid dina nycklar, certifikat, hemligheter och anslutningssträngar i en lösning för hantering av nycklar. Du kan använda en central tillsammans som nycklar och hemligheter är lagrade i maskinvarusäkerhetsmoduler (HSM). Azure ger dig en HSM i molnet med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

Key Vault är en *Arkiv för hemligheter*: det är en centraliserad molntjänst för att lagra programhemligheter. Key Vault håller dina känsliga data säkert genom att hålla programhemligheter i en enskild central plats och ger säker åtkomst, kontrollen behörighet och åtkomst-loggning.

Hemligheter är lagrade på enskilda *valv*. Varje valv har sin egen konfigurations- och IPSec-principer för åtkomstkontroll. Du kommer till dina data via ett REST-API eller klient-SDK som är tillgänglig för de flesta programmeringsspråk.

> [!IMPORTANT]
> Azure Key Vault har utformats för att lagra konfigurationen hemligheter för serverprogram. Det är inte avsedd för att lagra data som hör till app-användare. Detta återges i dess prestandaegenskaper, API och kostnadsmodell.
>
> Användardata bör lagras på annan plats, som i en Azure SQL Database-instans som har Transparent datakryptering (TDE) eller i ett lagringskonto som använder Azure Storage Service Encryption. Hemligheter som används av ditt program för att få åtkomst till dessa datalager kan hållas i Azure Key Vault.

### <a name="protect-sensitive-data"></a>Skydda känsliga data

Skydda data är en viktig del av Säkerhetsstrategin.
Klassificera dina data och identifiera dataskyddet måste hjälper dig att skapa din app med säkerhet i åtanke. Klassificera (kategorisering) lagras data av känslighet och inverkan på företaget hjälper utvecklare att avgöra de risker som är associerade med data.

Märka alla tillämpliga data som känsligt när du utformar din dataformat. Se till att programmet behandlar tillämpliga data som känslig. Med hjälp av dessa metoder kan du skydda känsliga data:

- Använda kryptering.
- Undvik att hårdkoda hemligheter som nycklar och lösenord.
- Se till att åtkomstkontroller och granskning finns på plats.

#### <a name="use-encryption"></a>Använd kryptering

Skydda data ska vara en väsentlig del av Säkerhetsstrategin.
Om dina data lagras i en databas eller om den flyttas fram och tillbaka mellan platser, använder kryptering av [data i vila](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (när den är i databasen) och kryptering av [data under överföring](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (på väg till och från den här användaren. databasen, ett API eller service-slutpunkt). Vi rekommenderar att du alltid använder SSL/TLS-protokollen för att utbyta data. Kontrollera att du använder den senaste versionen av TLS för kryptering (för närvarande är det här är version 1.2).

#### <a name="avoid-hard-coding"></a>Undvik att hårdkoda

Några saker ska aldrig vara hårdkodade i din programvara. Några exempel är värdnamn eller IP-adresser, URL: er, e-postadresser, användarnamn, lösenord, nycklar för lagringskonton och andra kryptografiska nycklar. Överväg att implementera krav avseende vad kan eller kan inte hårdkodade i din kod, inklusive i avsnitten kommentar av din kod.

Se till att du inte sparar känslig information när du placerar kommentarer i din kod. Detta inkluderar din e-postadress, lösenord, anslutningssträngar, information om ditt program som bara känner av någon i din organisation och allt annat som kan ge en angripare fördelar med att attackera ditt program eller din organisation .

I praktiken, förutsätter att allt innehåll i ditt projekt är allmänt kända när det distribueras. Undvik inklusive känsliga data av något slag i projektet.

Tidigare beskrivits [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Du kan använda Key Vault för att lagra hemligheter som nycklar och lösenord i stället för att hårdkoda dem. När du använder Key Vault i kombination med hanterade identiteter för Azure-resurser, Azure-webbappen kan komma åt hemliga konfigurationsvärden enkelt och säkert utan att lagra hemligheter i källkontroll eller konfiguration. Mer information finns i [hantera hemligheter i dina serverappar med Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementera felsäker mått

Programmet måste kunna hantera [fel](https://docs.microsoft.com/dotnet/standard/exceptions/) som uppstår vid körning på ett konsekvent sätt. Programmet bör se alla fel och antingen misslyckas säkra eller stängd.

Du bör också se till att fel är inloggad med tillräcklig användarkontext för att identifiera misstänkt eller skadlig aktivitet. Loggar ska behållas tillräckligt länge att tillåta fördröjd kriminalteknisk analys. Loggar ska vara i ett format som enkelt kan användas av en hanteringslösning för loggen. Se till att aktiveras aviseringar för fel som är relaterade till säkerhet. Inte tillräckligt med loggning och övervakning gör att angripare kan ytterligare angrepp system och underhåll persistence.

### <a name="take-advantage-of-error-and-exception-handling"></a>Dra nytta av fel och undantagshantering

Implementera rätt fel och [undantagshantering](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) är en viktig del av skydden kodning. Fel och undantagshantering är viktiga för att ett system, tillförlitlig och säker. Fel i felhantering kan leda till olika typer av säkerhetsproblem, till exempel läcker information för attacker och för att hjälpa angripare mer information om din plattform och design.

Se till att:

- Du kan hantera undantag på ett centraliserat sätt att undvika dubbletter [try-/ catch-block](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) i koden.

- Alla oväntade beteenden hanteras i programmet.

- Meddelanden som visas för användarna läcka inte kritiska data men ger tillräckligt med information för att förklara problemet.

- Undantag loggas, och de ger tillräckligt med information för datautredning eller incident response team att undersöka.

[Med Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) ger en förstklassig upplevelse för [hantering av fel och undantag](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) som orsakas av beroende system. Du kan använda Logikappar för att skapa arbetsflöden för att automatisera uppgifter och processer som integrerar appar, data, system och tjänster åt företag och organisationer.

### <a name="use-logging-and-alerting"></a>Använd loggning och aviseringar

[Log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) din säkerhet utfärdar för säkerhet undersökningar och utlöser aviseringar om problem så som man vet om problem i tid. Aktivera granskning och loggning på samtliga komponenter. Granskningsloggar ska avbilda användarkontext och identifiera alla viktiga händelser.

Kontrollera att du inte logga alla känsliga data som användaren har skickat till din webbplats. Exempel på känsliga data:

- Autentiseringsuppgifter för användare
- Personnummer eller annan information
- Kreditkortsnummer eller andra finansiell information
- Information om hälsa
- Privata nycklar eller andra data som kan användas för att dekryptera krypterad information
- System- eller information som kan användas för effektivare angrepp programmet

Se till att programmet övervakar användaren management händelser, till exempel lyckade och misslyckade användarinloggningar, antalet återställningar av lösenord, lösenordsändringar, kontoutelåsning och användarregistrering. Loggning för dessa händelser kan du identifiera och reagera på potentiellt misstänkt beteende. Du kan också samla in data för åtgärder, t.ex. vem som åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
I följande artiklar, rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utveckla och distribuera säkra program.

- [Utveckla säkra program](secure-develop.md)
- [Distribuera säkra program](secure-deploy.md)
