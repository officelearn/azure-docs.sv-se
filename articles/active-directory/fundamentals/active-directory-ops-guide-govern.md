---
title: Referensguide för Azure Active Directory-styrningsåtgärder
description: I referensguiden för åtgärder beskrivs de kontroller och åtgärder du bör vidta för att skydda styrningshantering
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535462"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Referensguide för Azure Active Directory-styrningsåtgärder

I det här avsnittet i [referensguiden för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskrivs de kontroller och åtgärder du bör vidta för att bedöma och intyga åtkomst som beviljats icke-privilegierade och privilegierade identiteter, granskning och kontrolländringar i miljön.

> [!NOTE]
> Dessa rekommendationer är aktuella från och med publiceringsdatumet men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina styrningsmetoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till nyckeluppgifter

För att hantera Azure Active Directory krävs kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett distributionsprojekt. Det är fortfarande viktigt att du ställer in dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Aktivitet | Ägare |
| :- | :- |
| Arkivera Azure AD-granskningsloggar i SIEM-systemet | InfoSec operationsteam |
| Upptäck program som hanteras av efterlevnad | IAM Operations Team |
| Granska regelbundet tillgången till ansökningar | InfoSec-arkitekturteam |
| Granska regelbundet åtkomsten till externa identiteter | InfoSec-arkitekturteam |
| Granska regelbundet vem som har privilegierade roller | InfoSec-arkitekturteam |
| Definiera säkerhetsportar för att aktivera privilegierade roller | InfoSec-arkitekturteam |
| Regelbundet granska samtyckesbidrag | InfoSec-arkitekturteam |
| Designkataloger och åtkomstpaket för program och resurser baserade för anställda i organisationen | App ägare |
| Definiera säkerhetsprinciper för att tilldela användare åtkomstpaket | InfoSec team + App Ägare |
| Om principer innehåller godkännandearbetsflöden granskar du regelbundet arbetsflödesgodkännanden | App ägare |
| Granska undantag i säkerhetsprinciper, till exempel principer för villkorlig åtkomst, med hjälp av åtkomstgranskningar | InfoSec operationsteam |

När du granskar listan kan du behöva tilldela en ägare för uppgifter som saknar en ägare eller justera ägarskapet för uppgifter med ägare som inte är anpassade till rekommendationerna ovan.

#### <a name="owner-recommended-reading"></a>Ägaren rekommenderade läsning

- [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Styrning i Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Testning av konfigurationsändringar

Det finns ändringar som kräver särskilda överväganden vid testning, från enkla tekniker som att distribuera en målundergrupp av användare till att distribuera en ändring i en parallell testklient. Om du inte har implementerat en teststrategi bör du definiera en testmetod som baseras på riktlinjerna i tabellen nedan:

| Scenario| Rekommendation |
|-|-|
|Ändra autentiseringstypen från federerad till PHS/PTA eller vice versa| Använd [stegvis distribution](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) för att testa effekten av att ändra autentiseringstypen.|
|Distribuera en ny certifikatutfärdarprincip eller identitetsskyddsprincip (Identity Protection Policy)|Skapa en ny ca-princip och tilldela testanvändare.|
|Introduktion till en testmiljö för ett program|Lägg till programmet i en produktionsmiljö, dölj det från panelen MyApps och tilldela det för att testa användare under kvalitetssäkringsfasen (QA).|
|Ändring av synkroniseringsregler|Utför ändringarna i ett test Azure AD Connect med samma konfiguration som för närvarande är i produktion, även känd som mellanlagringsläge, och analysera CSExport-resultat. Om du är nöjd, byt till produktion när den är klar.|
|Byte av varumärkesprofilering|Testa i en separat testklient.|
|Rulla ut en ny funktion|Om funktionen stöder utrullning till en måluppsättning användare identifierar du pilotanvändare och utbygger. Till exempel kan självbetjäningslösenordsåterställning och multifaktorautentisering rikta sig till specifika användare eller grupper.|
|Klippa över ett program från en lokal IDP (Active Directory), till Azure AD|Om programmet stöder flera IdP-konfigurationer, till exempel Salesforce, konfigurera både och testa Azure AD under ett ändringsfönster (om programmet introducerar HRD-sida). Om programmet inte stöder flera IdPs schemalägger du testningen under ett ändringskontrollfönster och programstopp.|
|Uppdatera dynamiska gruppregler|Skapa en parallell dynamisk grupp med den nya regeln. Jämför med det beräknade resultatet, till exempel kör PowerShell med samma villkor.<br>Om testet går igenom, byt ut de platser där den gamla gruppen användes (om möjligt).|
|Migrera produktlicenser|Se [Ändra licens för en enskild användare i en licensierad grupp i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Ändra AD FS-regler som auktorisering, emission, MFA|Använd gruppanspråk för att rikta in dig på delmängden användare.|
|Ändra AD FS-autentiseringsupplevelse eller liknande ändringar för hela servergruppen|Skapa en parallell servergrupp med samma värdnamn, implementera konfigurationsändringar, testa från klienter med HOSTS-fil, NLB-routningsregler eller liknande routning.<br>Om målplattformen inte stöder HOSTS-filer (till exempel mobila enheter) kontrollerar du ändringen.|

## <a name="access-reviews"></a>Åtkomstgranskningar

### <a name="access-reviews-to-applications"></a>Få tillgång till granskningar till program

Med tiden kan användare samla åtkomst till resurser när de rör sig i olika team och positioner. Det är viktigt att resursägare regelbundet granskar åtkomsten till program och tar bort privilegier som inte längre behövs under användarnas livscykel. Azure [AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) gör det möjligt för organisationer att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Resursägare bör regelbundet granska användarnas åtkomst för att se till att endast rätt personer har fortsatt åtkomst. Helst bör du överväga att använda Azure AD-åtkomstgranskningar för den här uppgiften.

![Startsida för Access-granskningar](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Varje användare som interagerar med åtkomstgranskningar måste ha en betald Azure AD Premium P2-licens.

### <a name="access-reviews-to-external-identities"></a>Få tillgång till granskningar till externa identiteter

Det är viktigt att endast begränsa åtkomsten till externa identiteter till resurser som behövs under den tid som behövs. Upprätta en regelbunden automatisk åtkomstgranskningsprocess för alla externa identiteter och programåtkomst med hjälp av Azure [AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview). Om det redan finns en process lokalt kan du överväga att använda Azure AD-åtkomstgranskningar. När ett program har dragits tillbaka eller inte längre används tar du bort alla externa identiteter som hade åtkomst till programmet.

> [!NOTE]
> Varje användare som interagerar med åtkomstgranskningar måste ha en betald Azure AD Premium P2-licens.

## <a name="privileged-account-management"></a>Hantering av privilegierade konton

### <a name="privileged-account-usage"></a>Användning av privilegierade konton

Hackare riktar ofta in sig på administratörskonton och andra element med privilegierad åtkomst för att snabbt få tillgång till känsliga data och system.Eftersom användare med privilegierade roller tenderar att ackumuleras med tiden är det viktigt att granska och hantera administratörsåtkomst regelbundet och ge just-in-time privilegierad åtkomst till Azure AD- och Azure-resurser.

Om det inte finns någon process i organisationen för att hantera privilegierade konton, eller om du för närvarande har administratörer som använder sina vanliga användarkonton för att hantera tjänster och resurser, bör du omedelbart börja använda separata konton, till exempel ett för vanliga dagliga verksamhet. den andra för privilegierad åtkomst och konfigurerad med MFA. Ännu bättre, om din organisation har en Azure AD Premium P2-prenumeration, bör du omedelbart distribuera [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). På samma sätt bör du också granska dessa privilegierade konton och [tilldela mindre privilegierade roller](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) om tillämpligt.

En annan aspekt av privilegierad kontohantering som bör implementeras är att definiera [åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) för dessa konton, antingen manuellt eller [automatiserat via PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review).

#### <a name="privileged-account-management-recommended-reading"></a>Rekommenderad läsning av privilegierad kontohantering

- [Roller i Azure AD-privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Konton för nödåtkomst

Organisationer måste skapa [nödkonton](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) för att vara förberedda för att hantera Azure AD för fall som autentiseringsfel som:

- Avbrottskomponenter i autentiseringsinfrastrukturer (AD FS, Lokal AD, MFA-tjänst)
- Administrativ personalomsättning

Om du vill förhindra att du oavsiktligt blir utelåst från din klientorganisation eftersom du inte kan logga in eller aktivera en befintlig enskild användares konto som administratör, bör du skapa två eller flera nödsituationskonton och se till att de implementeras och anpassas till [Microsofts metodtips](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) och [krossar glasprocedurer](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Privilegierad åtkomst till Azure EA-portal

[Azure Enterprise Agreement (Azure EA) portalen](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) kan du skapa Azure-prenumerationer mot en huvud enterprise agreement, vilket är en kraftfull roll inom företaget. Det är vanligt att bootstrap skapandet av denna portal innan ens få Azure AD på plats, så det är nödvändigt att använda Azure AD identiteter för att låsa den, ta bort personliga konton från portalen, se till att korrekt delegering är på plats, och minska risken för lockout .

Om EA-portalens auktoriseringsnivå för närvarande är inställd på "blandat läge" måste du ta bort alla [Microsoft-konton](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) från all privilegierad åtkomst i EA-portalen och konfigurera EA-portalen så att endast Azure AD-konton används. Om de EJ-portaldelegerade rollerna inte är konfigurerade bör du också söka efter och implementera delegerade roller för avdelningar och konton.

#### <a name="privileged-access-recommended-reading"></a>Privilegierad åtkomst rekommenderad läsning

- [Behörigheter för administratörsrollen i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Berättigandehantering

[Med RÄTTIGHETSHANTERING (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) kan appägare paketera resurser och tilldela dem till specifika personer i organisationen (både interna och externa). EM tillåter självbetjäningsanmälning och delegering till företagare samtidigt som styrningsprinciper för att bevilja åtkomst, ange åtkomstvaraktighet och tillåta arbetsflöden för godkännande. 

> [!NOTE]
> Azure AD-berättigandehantering kräver Azure AD Premium P2-licenser.

## <a name="summary"></a>Sammanfattning

Det finns åtta aspekter på en säker identitetsstyrning. Den här listan hjälper dig att identifiera de åtgärder du bör vidta för att bedöma och intyga åtkomsten som beviljas till icke-privilegierade och privilegierade identiteter, granskning och kontrolländringar i miljön.

- Tilldela ägare till nyckeluppgifter.
- Implementera en teststrategi.
- Använd Azure AD Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar.
- Upprätta en regelbunden, automatisk åtkomstgranskningsprocess för alla typer av externa identiteter och programåtkomst.
- Upprätta en åtkomstgranskningsprocess för att granska och hantera administratörsåtkomst regelbundet och ge just-in-time privilegierad åtkomst till Azure AD- och Azure-resurser.
- Etablera nödkonton som ska förberedas för att hantera Azure AD för oväntade avbrott.
- Lås åtkomsten till Azure EA-portalen.
- Implementera Rättighetshantering för att ge reglerad åtkomst till en samling resurser.

## <a name="next-steps"></a>Nästa steg

Kom igång med [Azure AD-driftkontroller och åtgärder](active-directory-ops-guide-ops.md).
