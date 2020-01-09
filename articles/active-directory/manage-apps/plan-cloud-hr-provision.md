---
title: Planera molnet HR Application för att Azure Active Directory användar etablering
description: Den här artikeln beskriver distributions processen för att integrera moln HR-system, till exempel Workday och Sucessfactors med Azure Active Directory. Genom att integrera Azure AD med ditt moln HR-system resulterar det i ett komplett hanterings system för identitets livs cykeln.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 6f72371077aab813cc22c9bbbe755fdfaa9ac00a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433826"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planera molnet HR Application för att Azure Active Directory användar etablering

Tidigare IT-personal har förlitat sig på manuella metoder för att skapa, uppdatera och ta bort medarbetare som att ladda upp CSV-filer eller anpassade skript för att synkronisera medarbetar data. Dessa etablerings processer är fel känsliga, oskyddade och svåra att hantera.

För att sömlöst hantera slut punkt till slut punkt för anställda, leverantörer eller eventualtillgångar, [Azure Active Directory (Azure AD) användar etablerings tjänsten](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) erbjuder integrering med MOLNbaserade HR-program (personal) som Workday eller SuccessFactors.

Azure AD använder denna integrering för att aktivera följande moln-arbets flöden för HR (app):

- **Etablera användare till AD** -etablera-valda uppsättningar med användare från en Cloud HR-app till en eller flera Active Directory-domäner (AD).
- **Etablering av enbart moln användare till Azure AD** – i scenarier där AD inte används, etablera användare direkt från Cloud HR-appen till Azure AD.
- **Skriv tillbaka till Cloud HR-appen.** – Skriv e-postadresserna och attributen för användar namn från Azure AD tillbaka till Cloud HR-appen.

> [!NOTE]
> I den här distributions planen lär du dig hur du distribuerar dina Cloud HR app-arbetsflöden med Azure AD-användar etablering. Information om hur du distribuerar automatisk användar etablering till SaaS-appar finns i [Planera en automatisk distribution av användar etablering](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Aktiverade HR-scenarier

Azure AD-tjänsten för användar etablering möjliggör automatisering av följande scenarier för HR-baserade identitets livs cykel hantering:

- **Anställning av nya anställda** – när en ny medarbetare läggs till i Cloud HR-appen skapas ett användar konto automatiskt i AD och Azure AD med alternativet att skriva tillbaka attributen e-postadress och användar namn till Cloud HR-appen.
- **Uppdateringar av anställda och profiler** – när en medarbetar post uppdateras i Cloud HR-appen (till exempel namn, titel eller chef) uppdateras deras användar konto automatiskt i AD och Azure AD.
- **Anställdas uppsägningar** – när en medarbetare avslutas i Cloud HR-appen inaktive ras deras användar konto automatiskt i AD och Azure AD.
- **Anställdas återställningar** – när en medarbetare återställs i Cloud HR-appen kan deras gamla konto automatiskt återaktiveras eller etableras till AD och Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Vem är den här integreringen bäst lämpad för?

Cloud HR app-integrering med Azure AD-användar etablering passar utmärkt för organisationer som:

- vill ha en förbyggd, molnbaserad lösning för molnets användar etablering i molnet
- Kräv direkt användar etablering från Cloud HR-appen till AD eller Azure AD
- Kräv att användare ska tillhandahållas med hjälp av data som hämtas från Cloud HR-appen
- Kräv anslutning till, flytta och lämna användare till att synkroniseras till en eller flera AD-skogar, domäner och organisationsenheter baserat enbart på ändrings information som identifieras i Cloud HR-appen
- Använd Office 365 för e-post

## <a name="learn"></a>Inlärning

Användar etablering skapar en grund för pågående identitets styrning och förbättrar kvaliteten på affärs processer som förlitar sig på auktoritativa identitets data.

### <a name="terms"></a>Villkor

I den här artikeln används följande villkor:

- **Käll system** – databasen med användare som Azure AD etablerar från (till exempel en Cloud HR-app som Workday och SuccessFactors.)
- **Mål system** – databasen med användare som Azure AD tillhandahåller till (till exempel AD, Azure AD, Office365 eller andra SaaS-appar.)
- **Kopplingar – flyttnings** behållnings process – en term som används för nya anställningar, överföringar och uppsägningar med hjälp av en Cloud HR-app som ett system med poster. Processen slutförs när tjänsten har angett de attribut som krävs för mål systemet.

### <a name="key-benefits"></a>Viktiga fördelar

Den här funktionen för HR-drivna IT-etablering ger avsevärda affärs förmåner enligt listan nedan:

- **Öka produktiviteten** – nu kan du automatisera tilldelningen av användar konton, Office365-licenser och ge åtkomst till nyckel grupper. Automatiserade tilldelningar ger nya anställda omedelbar till gång till sina jobb verktyg och ökar produktiviteten.
- **Hantera risk** – du kan öka säkerheten genom att automatisera ändringar baserat på medarbetarnas status eller grupp medlemskap med data som flödar in från Cloud HR-appen. Automatisering av ändringar säkerställer att användar identiteter och åtkomst till viktiga appar uppdateras automatiskt när användare övergår till eller lämnar organisationen.
- **Hantera efterlevnad och styrning** – Azure AD stöder interna gransknings loggar för användar etablerings begär Anden som utförs av appar av både käll-och mål system. Med granskning kan du spåra vem som har åtkomst till apparna från en enda skärm.
- **Hantera kostnad** – automatisk etablering minskar kostnaderna genom att undvika ineffektivitet och mänskligt fel som är kopplat till manuell etablering. Det minskar behovet av anpassade användar etablerings lösningar som skapats med tiden med hjälp av äldre och inaktuella plattformar.

### <a name="licensing"></a>Licensiering

Om du vill konfigurera Cloud HR-appen till Azure AD-integrering av användar etablering måste du ha en giltig [Azure AD Premium-licens](https://azure.microsoft.com/pricing/details/active-directory/) och en licens för Cloud HR-appen, till exempel Workday eller SuccessFactors.

Du måste också ha en giltig Azure AD Premium P1 eller en högre prenumerations licens för varje användare som kommer att skickas från Cloud HR-appen och tillhandahålls antingen till AD eller Azure AD. Ett felaktigt antal licenser som ägs i Cloud HR-appen kan leda till fel vid användar etablering.

### <a name="prerequisites"></a>Krav

- Global administratörs åtkomst till Azure AD för att konfigurera Azure AD Connect etablerings agenten.
- En test-och produktions instans av Cloud HR-appen.
- Administratörs behörighet i Cloud HR-appen för att skapa en system integrations användare och göra ändringar för att testa medarbetar data i test syfte.
- För användar etablering till AD krävs en server som kör Windows Server 2012 eller senare med .NET 4.7.1 + runtime för att vara värd för [Azure AD Connect etablerings agenten](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) för synkronisering av användare mellan AD och Azure AD.

### <a name="training-resources"></a>Utbildnings resurser

| **Resurser** | **Länk och beskrivning** |
|:-|:-|
| Videor | [Vad är användar etablering i Active Azure-katalogen?](https://youtu.be/_ZjARPpI6NI) |
| | [Hur distribuerar jag användar etablering i Active Azure-katalogen?](https://youtu.be/pKzyts6kfrw) |
| Självstudiekurser | Se [listan med självstudier om hur du integrerar SaaS-appar med Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Självstudie: Konfigurera arbets dag för automatisk användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| FAQ | [Automatiserad användar etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Etablering från Workday till Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Lösningsarkitektur

I följande exempel beskrivs den kompletta lösningen för användar etablerings lösningen för vanliga hybrid miljöer och inkluderar:

- **Auktoritativt data flöde i molnet – från Cloud HR-appen till AD** – i det här flödet initieras tjänsten HR event (joinr-Movers-processer) i Cloud HR App-klienten. Azure AD Provisioning-tjänsten och Azure AD Connect etablerings agenten etablerar användar data från Cloud HR App-klienten i AD. Beroende på händelsen kan det leda till att du skapar/uppdaterar/aktiverar/inaktiverar-åtgärder i AD.
- **Synkronisera med Azure AD och tillbakaskrivning e-post och användar namn från den lokala AD till Cloud HR-appen** – när kontona har uppdaterats i AD, synkroniseras de med Azure ad via Azure AD Connect, och attributen e-post adresser och användar namn kan skrivas tillbaka till Cloud HR App-klienten.

![Arbets flödes diagram](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beskrivning av arbets flöde

De viktigaste stegen som anges i diagrammet är:  

1. **HR team** utför transaktionerna i Cloud HR App-klienten.
2. **Azure AD Provisioning-tjänsten** kör de schemalagda cyklerna från Cloud HR App-klienten och identifierar ändringar som behöver bearbetas för synkronisering med AD.
3. **Azure AD Provisioning-tjänsten** anropar Azure AD Connect etablerings agenten med en nytto last för begäran som innehåller AD-konto skapa/uppdatera/aktivera/inaktivera åtgärder.
4. **Azure AD Connect etablerings agenten** använder ett tjänst konto för att hantera AD-konto data.
5. **Azure AD Connect** kör delta- [synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) för att hämta uppdateringar i AD.
6. **AD** -uppdateringar synkroniseras med Azure AD.
7. **Azure AD Provisioning-tjänsten** tillbakaskrivningar e-postattribut och användar namn från Azure AD till Cloud HR App-klienten.

## <a name="plan-the-deployment-project"></a>Planera distributions projektet

Överväg organisationens behov medan du fastställer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du engagerar rätt intressenter](https://aka.ms/deploymentplans) och att från intressenter-rollerna i projektet är väl förstå genom att dokumentera intressenterna och deras ingångs-och Accountabilities i projektet.

Du måste inkludera en representant från den HR-organisation som kan tillhandahålla indata på befintliga arbets processer för företag och arbets identitet + jobb data bearbetnings krav.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Du bör proaktivt kommunicera med dina användare hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om de drabbas av problem.

### <a name="plan-a-pilot"></a>Planera en pilot

Att integrera HR-affärsprocesser och identitets arbets flöden från molnet HR-appen till mål systemen kräver en stor mängd data verifiering, data omvandling, rengöring av data och slut punkt till slut punkt innan du distribuerar lösningen i produktionen.

Vi rekommenderar att den inledande konfigurationen ska köras i en [pilot miljö](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) innan den skalas till alla användare i produktionen.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Välj Cloud HR Provisioning Connector-appar

För att under lätta Azure AD etablering av arbets flöden mellan Cloud HR-appen och AD finns det flera Provisioning Connector-appar som du kan lägga till från Azure AD App-galleriet:

- **Cloud HR-appen till AD-användar etablering** – den här etablerings anslutnings appen underlättar användar konto etablering från Cloud HR-appen till en enda AD-domän. Om du har flera domäner kan du lägga till en instans av den här appen från Azure AD App-galleriet för varje AD-domän som du måste etablera till.
- **Cloud HR-appen till Azure AD-användar etablering** – medan Azure AD Connect är det verktyg som ska användas för att synkronisera AD-användare till Azure AD, kan den här etablerings anslutnings appen användas för att under lätta etableringen av endast molnbaserade användare från molnets HR-app till en enda Azure AD-klient.
- **Cloud HR app-tillbakaskrivning** – den här etablerings anslutnings appen gör att användarens e-postadresser kan skrivas tillbaka från Azure AD till Cloud HR-appen.

Bilden nedan visar till exempel de Workday Connector-appar som finns tillgängliga i Azure AD App-galleriet.

![Azure Active Directory portalens app-Galleri](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Flödesschema för beslut

Använd diagrammet för besluts flöde nedan för att identifiera vilka Cloud HR-etablerings appar som är relevanta för ditt scenario.

![Flödesschema för beslut](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Design Azure AD Connect distributions topologi för etablerings agent

Etableringen av integreringen mellan Cloud HR-appen och AD kräver följande fyra komponenter:

- Cloud HR app-klient
- Provisioning Connector-appen
- Azure AD Connect etablerings agent
- AD-domän

Den Azure AD Connect etablerings agentens distributions topologi är beroende av antalet Cloud HR-appar och underordnade AD-domäner som du planerar att integrera. Om du har flera AD-domäner beror det på om AD-domänerna är sammanhängande eller [åtskilda](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

Välj ett av distributions scenarier baserat på ditt beslut:

- En enda moln HR-app-klient – > rikta in sig på en eller flera underordnade AD-domäner i en betrodd skog
- En enda molnbaserad app-klient för HR-> rikta in sig på flera underordnade domäner i en osammanhängande AD-skog

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>En enda molnbaserad app-klient för PERSONALAVDELNINGEN – > en eller flera underordnade AD-domäner i en betrodd skog

Vi rekommenderar följande produktions konfiguration:

|Krav|Rekommendation|
|:-|:-|
|Antal Azure AD Connect etablerings agenter som ska distribueras|2 (för hög tillgänglighet och redundans)
|Antal etablerings anslutnings program som ska konfigureras|En app per underordnad domän|
|Server värd för Azure AD Connect etablerings agent|Windows 2012 R2 + med en rad syn för geo-lokaliserade AD-domänkontrollanter</br>Kan samverka med Azure AD Connect tjänst|

![Flöda till lokala agenter](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>En enda molnbaserad app-klient för HR-> rikta in sig på flera underordnade domäner i en osammanhängande AD-skog

I det här scenariot ingår etablering av användare från Cloud HR-appen till domäner i åtskilda AD-skogar.

Vi rekommenderar följande produktions konfiguration:

|Krav|Rekommendation|
|:-|:-|
|Antal Azure AD Connect etablerings agenter som ska distribueras lokalt|2 per åtskild AD-skog|
|Antal etablerings anslutnings program som ska konfigureras|En app per underordnad domän|
|Server värd för Azure AD Connect etablerings agent|Windows 2012 R2 + med en rad syn för geo-lokaliserade AD-domänkontrollanter</br>Kan samverka med Azure AD Connect tjänst|

![Molnets gemensamma AD-skog från en moln HR-app](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect krav för etablerings agenten

Cloud HR-appen till AD-etablerings lösningen kräver att du distribuerar en eller flera Azure AD Connect etablerings agenter på servrar som kör Windows 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1 + Runtime. Se till att värd servern har nätverks åtkomst till mål AD-domänen.

För att förbereda lokal-Azure AD Connect miljön registrerar konfigurations guiden för konfigurations agenten med Azure AD-klienten, [öppnar portar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [tillåter åtkomst till URL: er](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)och stöder [utgående https-proxykonfiguration](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Etablerings agenten använder ett tjänst konto för att kommunicera med AD-domänerna. Innan du installerar agenten rekommenderar vi att du skapar ett tjänst konto i AD-användare och datorer som uppfyller följande krav:

- Ett lösen ord som inte upphör att gälla
- Delegerad kontroll behörighet att läsa, skapa, ta bort och hantera användar konton

Du kan välja domänkontrollanter som ska hantera etablerings begär Anden. Om du har flera geografiskt distribuerade domänkontrollanter installerar du etablerings agenten på samma plats som den önskade domänkontrollanten för att förbättra tillförlitligheten och prestandan för lösningen från slut punkt till slut punkt.

För hög tillgänglighet kan du distribuera fler än en Azure AD Connect etablerings agent och registrera den för att hantera samma uppsättning lokala AD-domäner.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planera omfångs filter och attributmappning

När du aktiverar etablering från Cloud HR-appen till AD eller Azure AD, styr Azure Portal attributvärdena genom mappning av attribut.

### <a name="define-scoping-filters"></a>Definiera omfångs filter

Använd [omfångs filter](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) för att definiera de attributbaserade regler som avgör vilka användare som ska tillhandahållas från Cloud HR-appen till AD eller Azure AD.

När du initierar anslutnings processen samlar du in följande krav:

- Används Cloud HR-appen för både anställda och eventualtillgångar?
- Planerar du att använda Cloud HR-appen till användar etablering i Azure AD för att hantera både anställda och eventualtillgångar?
- Planerar du att bara distribuera Cloud HR-appen till Azure AD-användar etablering för en delmängd av molnet för HR-appar (till exempel anställda)?

Beroende på dina behov, när du konfigurerar attribut mappningar, kan du ange fältet **käll objekt omfånget** för att välja vilka uppsättningar av användare i Cloud HR-appen som ska finnas i omfånget för etablering till AD. Mer information om vanliga omfångs filter hittar du i kursen om Cloud HR-appen.

### <a name="determine-matching-attributes"></a>Bestäm matchande attribut

Med hjälp av etableringen får du möjlighet att matcha befintliga konton mellan käll-och mål systemet. När du integrerar Cloud HR-appen med Azure AD Provisioning-tjänsten kan du [konfigurera mappning av mappar](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) för att avgöra vilka användar data som ska flöda från molnet för molnet till AD eller Azure AD.

När du initierar anslutnings processen samlar du in följande krav:

- Vad är det unika ID: t i Cloud HR-appen som används för att identifiera varje användare?
- Hur hanterar du återställningar från ett livs cykel perspektiv för identiteter? Behåller återställningen kvar sitt gamla medarbetar-ID?
- Bearbetar du framtida anställda och skapade AD-konton för dem i förväg?
- Hur hanterar man medarbetare till konverteringen av en identitets livs cykel eller på annat sätt från ett livs cykel perspektiv för identiteter?
- Behåller konverterade användare sitt gamla AD-konto eller får de nya?

Beroende på ditt krav stöder Azure AD mappning av direkt attribut-till-attribut, vilket ger konstanta värden eller [skriver uttryck för mappningar av attribut](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Den här flexibiliteten ger dig den ultimata kontrollen över vad som kommer att fyllas i det riktade app-attributet. Du kan använda [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) och Graph Explorer för att exportera mappningar och schemat för användar etablerings attribut till en JSON-fil och importera tillbaka dem till Azure AD.

Som **standard**används attributet i Cloud HR-appen som representerar det unika medarbetar-ID: t som matchande attribut *mappat till det unika attributet i AD.* I app-scenariot för arbets dagar mappas exempelvis attributet *WorkerID* för *Workday* till AD *Anställningsnr* -attributet.

Du kan ange flera matchande attribut och tilldela matchande prioritet. De utvärderas utifrån matchnings prioritet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut.

Du kan också [Anpassa standardattribut – mappningar](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) , till exempel ändra eller ta bort befintliga attribut-mappningar, eller skapa nya attribut-mappningar enligt dina affärs behov. I guiden för Cloud HR-appen (till exempel [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) hittar du en lista över anpassade attribut att mappa.

### <a name="determine-user-account-status"></a>Ange status för användar konto

Som standard mappar Provisioning Connector-appen **användar profil** status till **användar konto status** i AD/Azure AD för att avgöra om användar kontot ska aktive ras eller inaktive ras.

När du initierar processen för kopplingar/Lämnare samlar du in följande krav:

| Process | Krav |
| - | - |
| **Kopplingar** | Hur hanterar du återställningar från ett livs cykel perspektiv för identiteter? Behåller återställningen kvar sitt gamla medarbetar-ID? |
| | Bearbetar du framtida anställda och skapar AD-konton för dem i förväg? Har de här kontona skapats i aktiverat/inaktiverat tillstånd? |
| | Hur hanterar man medarbetare till konverteringen av en identitets livs cykel eller på annat sätt från ett livs cykel perspektiv för identiteter? |
| | Behåller konverterade användare sitt gamla AD-konto eller får de nya? |
| **Lämnare** | Hanteras uppsägningar på olika sätt för anställda och eventualtillgångar i AD? |
| | Vilka effektiva datum beaktas vid bearbetning av användar terminering? |
| | Hur påverkas befintliga AD-konton av anställda och eventualtillgångar |
| | Hur bearbetar du åtgärden "återkalla" i AD? Återställnings åtgärder måste hanteras om framtida daterad anställda skapas i AD som en del av anslutnings processen. |

Beroende på dina behov kan du anpassa mappnings logiken med hjälp av [Azure AD-uttryck](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) så att AD-kontot aktive ras eller inaktive ras baserat på en kombination av data punkter.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Mappa molnets HR-app till AD-användarattribut

Varje Cloud HR-app levereras med standard molnets HR-app till AD-mappningar.

Samla in följande krav när du initierar processen Joinr/Movers/Leaveer:

| Process | Krav |
| - | - |
| **Kopplingar** | Är AD-kontot skapandet manuell, automatiserad eller delvis automatiserad? |
| | Planerar du att sprida anpassade attribut från Cloud HR-appen till AD? |
| **Flytter** | Vilka attribut vill du bearbeta när en "flyttal"-åtgärd äger rum i Cloud HR-appen? |
| | Utför du några speciella attributändringar vid tidpunkten för användar uppdateringar? Om ja, ange information. |
| **Lämnare** | Hanteras uppsägningar på olika sätt för anställda och eventualtillgångar i AD? |
| | Vilka effektiva datum beaktas vid bearbetning av användar terminering? |
| | Hur påverkas befintliga AD-konton av anställda och eventualtillgångar |

Beroende på dina krav kan du ändra mappningarna så att de passar dina integrations mål. Mer information om en lista över anpassade attribut som ska mappas hittar du i den aktuella själv studie kursen om Cloud HR app (till exempel [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)).

### <a name="generate-unique-attribute-value"></a>Generera unikt attributvärde

När du startar anslutnings processen kan du behöva generera unika attributvärden när du ställer in attribut som CN, samAccountName och UPN med unika begränsningar.

Azure AD Function- [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) utvärderar varje regel och kontrollerar sedan värdet som genereras för unikhet i mål systemet. Se exempel [som genererar unika värden för attributet userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Den här funktionen stöds för närvarande endast för "arbets dag för Active Directory användar etablering". Den kan inte användas med andra etablerings program.

### <a name="configure-ad-ou-container-assignment"></a>Konfigurera tilldelning av AD OU-behållare

Det är ett vanligt krav att placera AD-användarkonton i behållare utifrån affär senheter, platser och avdelningar. När du initierar en flyttnings process och om det finns en ändring av organisations administratören kan du behöva flytta användaren från en ORGANISATIONSENHET till en annan i AD.

Använd funktionen [switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) för att konfigurera affärs logiken för OU-tilldelningen och mappa den till AD-attributet *parentDistinguishedName*.

Om du till exempel vill skapa användare i OU baserat på HR-attributet "kommun" kan du använda följande uttryck.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Med det här uttrycket, om Orts värdet är Borås, Austin, Seattle eller London, kommer användar kontot att skapas i motsvarande ORGANISATIONSENHET. Om det inte finns någon matchning kommer kontot att skapas i standard ORGANISATIONSENHETen.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planera för lösen ords leverans för nya användar konton

När du startar anslutnings processen måste du ange och leverera ett tillfälligt lösen ord för nya användar konton. Med Azure AD-användar etablering för Azure AD kan du Distribuera Azure AD-funktionen för [återställning av lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) för användaren dag 1.

SSPR är ett enkelt sätt för IT-administratörer att göra det möjligt för användare att återställa sina lösen ord eller låsa upp sina konton. Du kan etablera attributet för **mobil nummer** från Cloud HR-appen till AD och synkronisera det med Azure AD. När attributet **mobil nummer** är i Azure AD kan du aktivera SSPR för användarens konto så att de nya användarna kan använda det registrerade och verifierade mobiltelefon numret för autentisering på dag 1.

## <a name="plan-for-initial-cycle"></a>Planera för första cykeln

När Azure AD Provisioning-tjänsten körs för första gången utför den en [första cykel](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle) mot Cloud HR-appen för att skapa en ögonblicks bild av alla användar objekt i Cloud HR-appen. Den tid det tar för inledande cykler är direkt beroende av hur många användare som finns i käll systemet. Den första cykeln för vissa Cloud HR app-klienter med över 100 000 användare kan ta lång tid.

**För stora Cloud HR app-klienter (> 30 000-användare) rekommenderar vi** att du kör den första cykeln i stegvisa steg och bara startar de stegvisa uppdateringarna när du har verifierat att rätt attribut är inställda i AD för olika användar etablerings scenarier. Följ ordningen nedan:

1. Kör bara den första cykeln för en begränsad uppsättning användare genom att ange [omfångs filtret](#plan-scoping-filters-and-attribute-mapping).
2. Verifiera AD-kontots etablerings-och attributvärden som angetts för de användare som valts för första körningen. Om resultatet uppfyller dina förväntningar expanderar du omfångs filtret till progressivt innehåller fler användare och kontrollerar resultatet för den andra körningen.

När du är nöjd med resultatet från den första cykeln för test användare kan du starta de [stegvisa uppdateringarna](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planera testning och säkerhet

I varje steg i distributionen från inledande pilot genom att aktivera användar etablering, se till att du testar att resultatet är som förväntat och granskar etablerings cyklerna.

### <a name="plan-testing"></a>Planera testning

När du har konfigurerat Cloud HR-appen till Azure AD-etableringen måste du köra test ärenden för att kontrol lera om den här lösningen uppfyller organisationens krav.

|Scenarier|Förväntat resultat|
|:-|:-|
|Ny medarbetare anställning i Cloud HR-appen| -Användar kontot har tillhandahållits i AD.</br>– Användarna kan logga in på AD-Domain-appar och utföra önskade åtgärder.</br>– Om AAD Connect-synkronisering har kon figurer ATS så skapas även användar kontot i Azure AD.
|Användaren har avbrutits i Cloud HR-appen|-Användar kontot är inaktiverat i AD.</br>– Användaren kan inte logga in på alla företags program som skyddas av AD.
|Den användar ansvariga organisationen har uppdaterats i Cloud HR-appen|Baserat på mappningen av attribut, flyttas användar kontot från en ORGANISATIONSENHET till en annan i AD.|
|HR uppdaterar användarens chef i Cloud HR-appen|Fältet chef i AD uppdateras för att återspegla den nya chefens namn.|
|HR återanställer en medarbetare till en ny roll.|Beteendet beror på hur Cloud HR-appen är konfigurerad att generera anställnings-ID:</br>– Om det gamla medarbetar-ID: t återanvänds för återställningen aktiverar anslutnings programmet det befintliga AD-kontot för användaren.</br>– Om återställningar får ett nytt medarbetar-ID skapar anslutningen ett nytt AD-konto för användaren.|
|HR konverterar medarbetaren till kontrakts arbetare eller vice versa|Ett nytt AD-konto skapas för den nya personen och det gamla kontot inaktive ras på konverteringens giltighets datum.|

Använd resultaten ovan för att avgöra hur du ska överföra den automatiska användar etablerings implementeringen till produktion utifrån dina etablerade tids linjer.

> [!TIP]
> Vi rekommenderar att du använder metoder som data minskning och data rensning när du uppdaterar test miljön med produktions data för att ta bort/maskera känslig PII (personligt identifierbar information) för att följa sekretess-och säkerhets standarder.

### <a name="plan-security"></a>Säkerhetsplanering

Det är vanligt att en säkerhets granskning krävs som en del av distributionen av en ny tjänst. Om en säkerhets granskning krävs eller ännu inte har utförts, kan du läsa mer i de många Azure AD- [Whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) som ger en översikt över identiteten som en tjänst.

### <a name="plan-rollback"></a>Planera återställning

Om den molnbaserade användar etablerings implementeringen inte fungerar som du vill i produktions miljön kan följande återställnings steg hjälpa dig att återställa till ett tidigare fungerande tillstånd:

1. Granska [sammanfattnings rapporten](check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) och etablerings [loggarna](check-status-user-account-provisioning.md#provisioning-logs-preview) (se [Hantera Cloud HR app User etablering](#manage-your-configuration)) för att avgöra vilka felaktiga åtgärder som utförts på berörda användare och/eller grupper.
2. Det senaste fungerande läget för användare och/eller grupper som påverkas kan bestämmas genom etableringen av gransknings loggar eller genom att granska mål systemen (Azure AD eller AD).
3. Arbeta med appens ägare för att uppdatera användare och/eller grupper som påverkas direkt i appen med hjälp av senast fungerande tillstånds värden.

## <a name="deploy-the-cloud-hr-app"></a>Distribuera Cloud HR-appen

Välj den Cloud HR-app som passar dina lösnings krav.

**Arbets dag** – hänvisa till [självstudie: Konfigurera arbets dag för automatisk användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) för de steg du behöver utföra för att importera Worker-profiler från arbets dagar till både AD och Azure AD, med valfri Skriv åtgärd för e-postadressen och användar namnet till arbets dagen.

## <a name="manage-your-configuration"></a>Hantera konfigurationen

Azure AD kan ge ytterligare insikter om din organisations användar etablering och drifts hälsa genom gransknings loggar och-rapporter.

### <a name="gain-insights-from-reports-and-logs"></a>Få insikter från rapporter och loggar

Efter en lyckad [första cykel](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle)fortsätter Azure AD Provisioning-tjänsten att fortsätta att köra stegvisa uppdateringar på en obestämd tid, med intervall som definierats i självstudierna som är särskilda för varje app, tills någon av följande händelser inträffar:

- Tjänsten stoppas manuellt och en ny första cykel utlöses med hjälp av [Azure Portal](https://portal.azure.com/) eller med lämpligt [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) -kommando.
- En ny första cykel utlöses på grund av en ändring av attributens mappningar eller omfångs filter.
- Etablerings processen går i karantän på grund av en hög fel frekvens och hålls kvar i karantän i mer än fyra veckor då den automatiskt kommer att inaktive ras.

Om du vill granska dessa händelser och alla andra aktiviteter som utförs av etablerings tjänsten, [lär du dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Alla aktiviteter som utförs av etablerings tjänsten registreras i **gransknings loggarna för Azure AD**. Du kan skicka Azure AD audit-loggar till Azure Monitor loggar för vidare analys. **Azure Monitor loggar (kallas även Log Analytics arbets yta)** gör att du kan ställa frågor till data för att hitta händelser, analysera trender och utföra korrelationer mellan olika data källor. Titta på den här [videon](https://youtu.be/MP5IaCTwkQg) och lär dig fördelarna med att använda Azure Monitor loggar för Azure AD-loggar i praktiska användar scenarier.

Du kan installera [Log Analytics-vyer för Azure AD-aktivitets loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) för att få åtkomst till [färdiga rapporter](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) kring etablering av händelser i din miljö.

Mer information finns i [analysera Azure AD-aktivitets loggarna med dina Azure Monitor loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>Hantera personliga data

Azure AD Connect etablerings agenten som är installerad på Windows Server skapar loggar i Windows-händelseloggen som kan innehålla personliga data beroende på din moln-HR-app till AD-attribut mappningar. Om du vill följa användar sekretess kraven kan du konfigurera en schemalagd schemalagd aktivitet i Windows för att rensa händelse loggen och se till att inga data bevaras efter 48 timmar.

Azure AD Provisioning-tjänsten genererar inte rapporter, utför analyser eller ger insikter mer än 30 dagar. Därför lagrar inte Azure AD Provisioning-tjänsten, bearbetar eller behåller några data längre än 30 dagar. 

### <a name="troubleshoot"></a>Felsökning

Se följande länkar för att felsöka eventuella problem som kan uppstå under etableringen:

- [Problem med att konfigurera användar etablering i ett Azure AD Gallery-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Användar etablering i ett Azure AD Gallery-program tar flera timmar eller mer](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Problem med att spara administratörsautentiseringsuppgifter när du konfigurerar användar etablering till ett Azure Active Directory Galleri program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Inga användare tillhandahålls till ett Azure AD Gallery-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [Fel uppsättning användare tillhandahålls till ett Azure AD Gallery-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Konfigurera Windows Loggboken för agent fel sökning](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Konfigurera Azure Portal gransknings loggar för fel sökning av tjänst](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Förstå loggar för skapande åtgärder i AD-användarkontot](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Förstå loggar för uppdaterings åtgärder i Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Lösa vanliga fel som har påträffats](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Nästa steg

- [Skriver uttryck för mappningar av attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Översikt över Azure AD Synchronization API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Hoppa över borttagning av användar konton som ingår i omfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Azure AD Connect etablerings agent: versions historik](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
