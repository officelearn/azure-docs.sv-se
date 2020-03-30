---
title: Planera moln-HR-program till Azure Active Directory-användaretablering
description: I den här artikeln beskrivs distributionsprocessen för att integrera moln-HR-system, till exempel Workday och SuccessFactors, med Azure Active Directory. Integrering av Azure AD med ditt moln-HR-system resulterar i ett komplett identitetslivscykelhanteringssystem.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522440"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planera moln-HR-program till Azure Active Directory-användaretablering

Historiskt sett har IT-personal förlitat sig på manuella metoder för att skapa, uppdatera och ta bort medarbetare. De har använt metoder som att ladda upp CSV-filer eller anpassade skript för att synkronisera medarbetardata. Dessa etableringsprocesser är felbenägna, osäkra och svåra att hantera.

För att hantera identitetslivscyklerna för anställda, leverantörer eller betingade arbetare erbjuder [Azure Active Directory (Azure AD) användaretableringstjänsten](../app-provisioning/user-provisioning.md) integrering med molnbaserade HR-program.To manage the identity lifecycles of employees, vendors, or contingent workers, Azure Active Directory (Azure AD) user provisioning service offers integration with cloud-based personal resources (HR) applications. Exempel på program är Workday eller SuccessFactors.

Azure AD använder den här integreringen för att aktivera följande arbetsflöden för moln-HR-program (app):

- **Etablera användare i Active Directory:** Etablera valda uppsättningar användare från en moln-HR-app till en eller flera Active Directory-domäner.
- **Etablera molnanvändare till Azure AD:** I scenarier där Active Directory inte används etablerar du användare direkt från molnet HR-appen till Azure AD.
- **Skriv tillbaka till appen för moln-HR:** Skriv tillbaka e-postadresser och användarnamnsattribut från Azure AD till appen för moln-HR.

> [!NOTE]
> Den här distributionsplanen visar hur du distribuerar dina arbetsflöden för moln-HR-appar med Azure AD-användares etablering. Information om hur du distribuerar automatisk användaretablering till saaS-appar (Software as a Service) finns i [Planera en automatisk distribution av användaretablering](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Aktiverade HR-scenarier

Azure AD-tjänsten för användaretablering möjliggör automatisering av följande SCENARIER för hantering av pulsbaserad identitetslivscykel:

- **Nyanställd anställning:** När en ny medarbetare läggs till i molnet HR-app, skapas ett användarkonto automatiskt i Active Directory och Azure AD med möjlighet att skriva tillbaka e-postadress och användarnamn attribut till molnet HR-app.
- **Medarbetarattribut och profiluppdateringar:** När en medarbetarpost som namn, titel eller chef uppdateras i molnet HR-app, uppdateras deras användarkonto automatiskt i Active Directory och Azure AD.
- **Uppsägningar av anställda:** När en medarbetare avslutas i molnet HR-app, är deras användarkonto automatiskt inaktiveras i Active Directory och Azure AD.
- **Anställd återanställer:** När en medarbetare återanställs i molnet HR-app, kan deras gamla konto automatiskt återaktiveras eller återetableras till Active Directory och Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Vem är denna integration bäst lämpad för?

Integrering av moln-HR-appar med Azure AD-användare är idealisk för organisationer som:

- Vill ha en fördefinierad, molnbaserad lösning för moln-HR-användaretablering.
- Kräv direkt användaretablering från moln-HR-appen till Active Directory eller Azure AD.
- Kräv att användare etableras med hjälp av data som hämtas från appen för moln-HR.
- Kräv att ansluta, flytta och låta användare synkroniseras till en eller flera Active Directory-skogar, domäner och OUs baserat endast på ändringsinformation som identifieras i molnet HR-appen.
- Använd Office 365 för e-post.

## <a name="learn"></a>Läs mer

Tillhandahållande av användare skapar en grund för pågående identitetsstyrning. Det förbättrar kvaliteten på affärsprocesser som är beroende av auktoritativa identitetsdata.

### <a name="terms"></a>Villkor

I den här artikeln används följande termer:

- **Källsystem**: Lagringsplatsen för användare som Azure AD avsättningar från. Ett exempel är en moln-HR-app som Workday eller SuccessFactors.
- **Målsystem**: Lagringsplatsen för användare som Azure AD-avsättningar till. Exempel är Active Directory, Azure AD, Office 365 eller andra SaaS-appar.
- **Joiners-Movers-Leavers process**: En term som används för nyanställda, överföringar och uppsägning med hjälp av en moln-HR-app som ett system med poster. Processen slutförs när tjänsten har avstÃ¤ndest de nödvändiga attributen till målsystemet.

### <a name="key-benefits"></a>Viktiga fördelar

Den här kapaciteten hos HR-driven IT-etablering erbjuder följande betydande affärsfördelar:

- **Öka produktiviteten:** Nu kan du automatisera tilldelningen av användarkonton och Office 365-licenser och ge åtkomst till nyckelgrupper. Automatisera uppdrag ger nyanställda omedelbar tillgång till sina jobbverktyg och ökar produktiviteten.
- **Hantera risker:** Du kan öka säkerheten genom att automatisera ändringar baserat på medarbetarstatus eller gruppmedlemskap med data som flödar in från molnet HR-appen. Genom att automatisera ändringarna säkerställs att användaridentiteter och åtkomst till nyckelappar uppdateras automatiskt när användare övergår eller lämnar organisationen.
- **Ta itu med efterlevnad och styrning:** Azure AD stöder inbyggda granskningsloggar för användaretableringsbegäranden som utförs av appar för både käll- och målsystem. Med granskning kan du spåra vem som har åtkomst till apparna från en enda skärm.
- **Hantera kostnad:** Automatisk avsättning minskar kostnaderna genom att undvika ineffektivitet och mänskliga fel i samband med manuell etablering. Det minskar behovet av specialutvecklade lösningar för användaretablering som skapats över tid med hjälp av äldre och föråldrade plattformar.

### <a name="licensing"></a>Licensiering

För att konfigurera moln-HR-appen till Azure AD-integrering av användare behöver du en giltig [Azure AD Premium-licens](https://azure.microsoft.com/pricing/details/active-directory/) och en licens för moln-HR-appen, till exempel Workday eller SuccessFactors.

Du behöver också en giltig Azure AD Premium P1 eller högre prenumerationslicens för varje användare som kommer från molnet HR-appen och etableras till antingen Active Directory eller Azure AD. Eventuellt felaktigt antal licenser som ägs i molnet HR-app kan leda till fel under användarens etablering.

### <a name="prerequisites"></a>Krav

- Global azure AD-administratörsåtkomst för att konfigurera Azure AD Connect-etableringsagenten.
- En test- och produktionsinstans av moln-HR-appen.
- Administratörsbehörigheter i molnet HR-appen för att skapa en användare av systemintegration och göra ändringar för att testa medarbetardata för testning.
- För användaretablering till Active Directory krävs en server som kör Windows Server 2012 eller senare med .NET 4.7.1+ runtime för att vara värd för [Azure AD Connect-etableringsagenten](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) för synkronisering av användare mellan Active Directory och Azure AD.

### <a name="training-resources"></a>Utbildningsresurser

| **Resurser** | **Länk och beskrivning** |
|:-|:-|
| Videoklipp | [Vad är användaretablering i Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Distribuera användaretablering i Active Azure Directory](https://youtu.be/pKzyts6kfrw) |
| självstudiekurserna | [Lista över självstudier om hur du integrerar SaaS-appar med Azure AD](../saas-apps/tutorial-list.md) |
| | [Självstudiekurs: Konfigurera arbetsdag för automatisk etablering av användare](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| VANLIGA FRÅGOR OCH SVAR | [Automatiserad etablering av användare](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Etablering från arbetsdag till Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Lösningsarkitektur

I följande exempel beskrivs lösningsarkitekturen för etablering från till användare för vanliga hybridmiljöer och följande:

- **Auktoritära HR-data flödar från moln-HR-app till Active Directory.** I det här flödet initieras HR-händelsen (Joiners-Movers-Leavers-process) i molnet HR-appklienten. Azure AD-etableringstjänsten och Azure AD Connect-etableringsagenten etablerar användardata från cloud HR-appklienten till Active Directory. Beroende på händelsen kan det leda till att du skapar, uppdaterar, aktiverar och inaktiverar åtgärder i Active Directory.
- **Synkronisera med Azure AD och skriv tillbaka e-post och användarnamn från lokala Active Directory till cloud HR-appen.** När kontona har uppdaterats i Active Directory synkroniseras de med Azure AD via Azure AD Connect. E-postadresserna och användarnamnsattributen kan skrivas tillbaka till molnet HR-appklienten.

![Arbetsflödesdiagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beskrivning av arbetsflödet

Följande viktiga steg visas i diagrammet:  

1. **HR-teamet** utför transaktionerna i molnet HR-app klienten.
2. **Azure AD-etableringstjänsten** kör schemalagda cykler från cloud HR-appklienten och identifierar ändringar som måste bearbetas för synkronisering med Active Directory.
3. **Azure AD-etableringstjänsten** anropar Azure AD Connect-etableringsagenten med en begäranhetstjänst som innehåller Active Directory-konto skapa, uppdatera, aktivera och inaktivera åtgärder.
4. **Azure AD Connect-etableringsagenten** använder ett tjänstkonto för att hantera Active Directory-kontodata.
5. **Azure AD Connect** kör [deltasynkronisering](../hybrid/how-to-connect-sync-whatis.md) för att hämta uppdateringar i Active Directory.
6. **Active Directory-uppdateringar** synkroniseras med Azure AD.
7. **Azure AD-etableringstjänsten** skriver tillbaka e-postattribut och användarnamn från Azure AD till cloud HR-appklientinnehavaren.

## <a name="plan-the-deployment-project"></a>Planera distributionsprojektet

Tänk på dina organisationsbehov medan du bestämmer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas gör de det vanligtvis på grund av felaktiga förväntningar på effekter, resultat och ansvar. För att undvika dessa fallgropar, [se till att du engagerar rätt intressenter.](https://aka.ms/deploymentplans) Se också till att intressentrollerna i projektet är väl förstådda. Dokumentera intressenterna och deras projektinmatning och kontobesiktning.

Inkludera en representant från HR-organisationen som kan tillhandahålla indata på befintliga HR-affärsprocesser och arbetaridentitet samt krav på databearbetning av jobb.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Kommunicera proaktivt med användarna om när och hur deras upplevelse kommer att förändras. Låt dem veta hur de får stöd om de får problem.

### <a name="plan-a-pilot"></a>Planera en pilot

Integrera HR affärsprocesser och arbetsflöden identitet från molnet HR-app till målsystem kräver en betydande mängd dataverifiering, dataomvandling, datarensning och end-to-end testning innan du kan distribuera lösningen i produktion.

Kör den första konfigurationen i en [pilotmiljö](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) innan du skalar den till alla användare i produktion.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Välj moln-HR-etableringsappar

Om du vill underlätta Azure AD-etableringsarbetsflöden mellan molnet HR-appen och Active Directory kan du lägga till flera etableringsappar från Azure AD-appgalleriet:

- **Cloud HR-app till Active Directory-användaretablering:** Den här etableringsappen underlättar etablering av användarkonto från moln-HR-appen till en enda Active Directory-domän. Om du har flera domäner kan du lägga till en instans av den här appen från Azure AD-appgalleriet för varje Active Directory-domän som du behöver etablera till.
- **Cloud HR-app till Azure AD-användaretablering:** Medan Azure AD Connect är det verktyg som ska användas för att synkronisera Active Directory-användare till Azure AD, kan den här etableringsappen användas för att underlätta etablering av molnanvändare från moln-HR-appen till en enda Azure AD-klientorganisation.
- **Cloud HR app tillbakaskrivning:** Denna etablering anslutning app underlättar avskrivning av användarens e-postadresser från Azure AD till molnet HR-app.

I följande bild visas till exempel de Workday-anslutningsappar som är tillgängliga i Azure AD-appgalleriet.

![Azure Active Directory-portalappgalleri](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Beslutsflödesdiagram

Använd följande beslutsflödesdiagram för att identifiera vilka moln-HR-etableringsappar som är relevanta för ditt scenario.

![Beslutsflödesdiagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Utforma azure AD Connect-etableringsagentens distributionstopologi

Etableringsintegreringen mellan molnet HR-appen och Active Directory kräver fyra komponenter:

- Cloud HR-appklient
- Etablering av anslutningsapp
- Etablerande agent för Azure AD Connect
- Active Directory-domän

Azure AD Connect-etableringsagentens distributionstopologi beror på antalet klienter i molnets HR-app och underordnade Active Directory-domäner som du planerar att integrera. Om du har flera Active Directory-domäner beror det på om Active Directory-domänerna är sammanhängande eller [osammanhängande](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

Välj ett av distributionsscenarierna baserat på ditt beslut:

- Endatorappklientorganisation i molnet -> målbaserade underordnade domäner för Active Directory eller flera underordnade Active Directory-domäner i en betrodd skog
- Endatorappklientorganisation i molnet -> rikta in sig på flera underordnade domäner i en osammanhängande Active Directory-skog

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Endatorappklientorganisation i molnet -> målbaserade underordnade domäner för Active Directory eller flera underordnade Active Directory-domäner i en betrodd skog

Vi rekommenderar följande produktionskonfiguration:

|Krav|Rekommendation|
|:-|:-|
|Antal Azure AD Connect-etableringsagenter som ska distribueras|Två (för hög tillgänglighet och redundans)
|Antal etableringsappar som ska konfigureras|En app per underordnad domän|
|Servervärd för Azure AD Connect-etableringsagent|Windows 2012 R2+ med siktlinje till geolokaliserade Active Directory-domänkontrollanter</br>Kan samexistera med Azure AD Connect-tjänsten|

![Flöde till lokala agenter](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Endatorappklientorganisation i molnet -> rikta in sig på flera underordnade domäner i en osammanhängande Active Directory-skog

Det här scenariot innebär att etablera användare från molnet HR-app till domäner i osammanhängande Active Directory-skogar.

Vi rekommenderar följande produktionskonfiguration:

|Krav|Rekommendation|
|:-|:-|
|Antal Azure AD Connect-etableringsagenter för att distribuera lokala|Två per osammanhängande Active Directory-skog|
|Antal etableringsappar som ska konfigureras|En app per underordnad domän|
|Servervärd för Azure AD Connect-etableringsagent|Windows 2012 R2+ med siktlinje till geolokaliserade Active Directory-domänkontrollanter</br>Kan samexistera med Azure AD Connect-tjänsten|

![Enstaka moln HR-appklienterar osammanhängande Active Directory-skog](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect-etableringsagentkrav

Moln-HR-appen till Active Directory-användaretableringslösning kräver att du distribuerar en eller flera Azure AD Connect-etableringsagenter på servrar som kör Windows 2012 R2 eller senare. Servrarna måste ha minst 4 GB RAM och .NET 4.7.1+ körningstid. Kontrollera att värdservern har nätverksåtkomst till active directory-måldomänen.

För att förbereda den lokala miljön registrerar konfigurationsguiden för Azure AD Connect-etableringsagenten agenten med din Azure AD-klientorganisation, [öppnar portar,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [ger åtkomst till webbadresser](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)och stöder [utgående HTTPS-proxykonfiguration](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Etableringsagenten använder ett tjänstkonto för att kommunicera med Active Directory-domänerna. Innan du installerar agenten skapar du ett tjänstkonto i Active Directory – användare och datorer som uppfyller följande krav:

- Ett lösenord som inte upphör att gälla
- Delegerade kontrollbehörigheter för att läsa, skapa, ta bort och hantera användarkonton

Du kan välja domänkontrollanter som ska hantera etableringsbegäranden. Om du har flera geografiskt distribuerade domänkontrollanter installerar du etableringsagenten på samma plats som de domänkontrollanter som föredras. Den här placeringen förbättrar tillförlitligheten och prestandan hos lösningen från på sluten tid.

För hög tillgänglighet kan du distribuera mer än en Azure AD Connect-etableringsagent. Registrera agenten för att hantera samma uppsättning lokala Active Directory-domäner.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planera omfångsfilter och attributmappning

När du aktiverar etablering från moln-HR-appen till Active Directory eller Azure AD styr Azure-portalen attributvärdena via attributmappning.

### <a name="define-scoping-filters"></a>Definiera omfångsfilter

Använd [omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) för att definiera de attributbaserade regler som avgör vilka användare som ska etableras från molnet HR-appen till Active Directory eller Azure AD.

När du initierar anslutningsprocessen samlar du in följande krav:

- Används appen cloud HR för att ta med både anställda och betingade arbetare?
- Planerar du att använda moln-HR-appen till Azure AD-användareetablering för att hantera både anställda och betingade arbetare?
- Planerar du att distribuera cloud HR-appen till Azure AD-användaretablering endast för en delmängd av moln-HR-appanvändarna? Ett exempel kan bara vara medarbetare.

Beroende på dina krav kan du när du konfigurerar attributmappningar ange fältet **Källobjektomfattning** så att det väljer vilka uppsättningar användare i molnet HR-appen ska vara i omfånget för etablering till Active Directory. Mer information finns i självstudiekursen för appen för moln-HR-appen för vanliga omfångsfilter.

### <a name="determine-matching-attributes"></a>Fastställa matchande attribut

Med etablering får du möjlighet att matcha befintliga konton mellan käll- och målsystemet. När du integrerar cloud HR-appen med Azure AD-etableringstjänsten kan du [konfigurera attributmappning](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) för att avgöra vilka användardata som ska flöda från moln-HR-appen till Active Directory eller Azure AD.

När du initierar anslutningsprocessen samlar du in följande krav:

- Vilket är det unika ID:t i den här moln-HR-appen som används för att identifiera varje användare?
- Ur ett identitetslivscykelperspektiv, hur hanterar du återanställare? Har återanställer behålla sina gamla anställda id?
- Behandlar du framtida-daterade hyror och skapar Active Directory-konton för dem i förväg?
- Ur ett identitetslivscykelperspektiv, hur hanterar du medarbetare till kontingentar arbetsarkonvertering, eller på annat sätt?
- Har konverterade användare behålla sina gamla Active Directory-konton eller får de nya?

Beroende på dina krav stöder Azure AD direkt attribut-till-attributmappning genom att tillhandahålla konstanta värden eller [skrivuttryck för attributmappningar](../app-provisioning/functions-for-customizing-application-data.md). Den här flexibiliteten ger dig ultimat kontroll över vad som är befolkat i det riktade appattributet. Du kan använda [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) och Graph Explorer för att exportera attributmappningar och schema för användare till en JSON-fil och importera den tillbaka till Azure AD.

Som standard används attributet i molnet HR-appen som representerar det unika medarbetar-ID som det matchande *attributet mappas till det unika attributet i Active Directory.* I scenariot Workday-appen mappas till exempel **attributet Workday** **WorkerID** till Active Directory **employeeID-attributet.**

Du kan ange flera matchande attribut och tilldela matchande prioritet. De utvärderas på matchande prioritet. Så snart en matchning hittas utvärderas inga ytterligare matchande attribut.

Du kan också [anpassa standardattributmappningarna,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)till exempel ändra eller ta bort befintliga attributmappningar. Du kan också skapa nya attributmappningar enligt dina affärsbehov. Mer information finns i självstudiekursen för appen för moln-HR -appen (till exempel [Arbetsdag)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)för en lista över anpassade attribut som ska mappas.

### <a name="determine-user-account-status"></a>Fastställ kontostatus

Som standard mappar etableringsappen hr-användarprofilstatusen till användarkontostatusen i Active Directory eller Azure AD för att avgöra om användarkontot ska aktiveras eller inaktiveras.

När du startar joiners-leavers-processen samlar du in följande krav.

| Process | Krav |
| - | - |
| **Snickare** | Ur ett identitetslivscykelperspektiv, hur hanterar du återanställare? Har återanställer behålla sina gamla anställda id? |
| | Behandlar du framtida-daterade hyror och skapar Active Directory-konton för dem i förväg? Skapas dessa konton i ett aktiverat eller inaktiverat tillstånd? |
| | Ur ett identitetslivscykelperspektiv, hur hanterar du medarbetare till kontingentar arbetsarkonvertering, eller på annat sätt? |
| | Behåller konverterade användare sina gamla Active Directory-konton eller får de nya? |
| **Avhoppare** | Hanteras uppsägningar på olika sätt för anställda och betingade arbetare i Active Directory? |
| | Vilka giltighetsdatum beaktas för bearbetning av användarens avslutning? |
| | Hur påverkar konverteringar av medarbetare och villkorade arbetare befintliga Active Directory-konton? |
| | Hur bearbetar du åtgärden Återkalla i Active Directory? Återaktiverade åtgärder måste hanteras om framtida daterade hyror skapas i Active Directory som en del av Joiner-processen. |

Beroende på dina krav kan du anpassa mappningslogiken med hjälp av [Azure AD-uttryck](../app-provisioning/functions-for-customizing-application-data.md) så att Active Directory-kontot aktiveras eller inaktiveras baserat på en kombination av datapunkter.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mappa cloud HR-app till Active Directory-användarattribut

Varje moln HR-app levereras med standard moln HR-app till Active Directory-mappningar.

När du startar joiners-Movers-Leavers-processen samlar du in följande krav.

| Process | Krav |
| - | - |
| **Snickare** | Är processen för att skapa active directory-konton manuell, automatiserad eller delvis automatiserad? |
| | Planerar du att sprida anpassade attribut från molnet HR-app till Active Directory? |
| **Movers** | Vilka attribut vill du bearbeta när en Movers-åtgärd sker i appen för moln-HR? |
| | Utför du några specifika attributvalider vid tidpunkten för användaruppdateringar? Om ja, lämna information. |
| **Avhoppare** | Hanteras uppsägningar på olika sätt för anställda och betingade arbetare i Active Directory? |
| | Vilka giltighetsdatum beaktas för bearbetning av användarens avslutning? |
| | Hur påverkar konverteringar av medarbetare och villkorade arbetare befintliga Active Directory-konton? |

Beroende på dina behov kan du ändra mappningarna för att uppfylla dina integrationsmål. Mer information finns i den specifika självstudiekursen för moln-HR-appen (till exempel [Arbetsdag)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)för en lista över anpassade attribut som ska mappas.

### <a name="generate-a-unique-attribute-value"></a>Generera ett unikt attributvärde

När du initierar anslutningsprocessen kan du behöva generera unika attributvärden när du anger attribut som CN, samAccountName och UPN, som har unika begränsningar.

Azure AD-funktionen [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) utvärderar varje regel och kontrollerar sedan det värde som genereras för unikhet i målsystemet. Ett exempel finns i [Generera unikt värde för attributet userPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Den här funktionen stöds för närvarande endast för Workday to Active Directory-användaretablering. Det kan inte användas med andra etableringsappar.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurera tilldelning av Active Directory OU-behållare

Det är ett vanligt krav att placera Active Directory-användarkonton i behållare baserat på affärsenheter, platser och avdelningar. När du initierar en Movers-process, och om det sker en ändring av tillsynsorganisation, kan du behöva flytta användaren från en organisationsenhet till en annan i Active Directory.

Använd funktionen [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) för att konfigurera affärslogiken för organisationsenhetstilldelningen och mappa den till det överordnade Active Directory-attributetDistinguishedName . **parentDistinguishedName**

Om du till exempel vill skapa användare i organisationsenheten baserat på hr-attributet **Kommun**kan du använda följande uttryck:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Med det här uttrycket, om kommunvärdet är Dallas, Austin, Seattle eller London, skapas användarkontot i motsvarande organisationsenhet. Om det inte finns någon matchning skapas kontot i standardenheten.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planera för lösenordsleverans av nya användarkonton

När du startar anslutningsprocessen måste du ange och leverera ett tillfälligt lösenord för nya användarkonton. Med moln-HR till Azure AD-användaretablering kan du distribuera Azure AD-funktionen för återställning av lösenord för [självbetjäning](../authentication/quickstart-sspr.md) (SSPR) för användaren dag ett.

SSPR är ett enkelt sätt för IT-administratörer att göra det möjligt för användare att återställa sina lösenord eller låsa upp sina konton. Du kan etablera **attributet Mobilnummer** från molnet HR-appen till Active Directory och synkronisera det med Azure AD. När **attributet Mobilnummer** finns i Azure AD kan du aktivera SSPR för användarens konto. På dag ett kan den nya användaren använda det registrerade och verifierade mobilnumret för autentisering.

## <a name="plan-for-initial-cycle"></a>Planera för inledande cykel

När Azure AD-etableringstjänsten körs för första gången utför den en [inledande cykel](../app-provisioning/how-provisioning-works.md#initial-cycle) mot molnet HR-appen för att skapa en ögonblicksbild av alla användarobjekt i molnet HR-appen. Den tid det tar för inledande cykler är direkt beroende av hur många användare som finns i källsystemet. Den inledande cykeln för vissa moln HR-app klienter med över 100.000 användare kan ta lång tid.

**För stora klienter i hr-appen i molnet (>30 000 användare)** kör du den inledande cykeln i progressiva steg. Starta de inkrementella uppdateringarna först när du har verifierat att rätt attribut har angetts i Active Directory för olika scenarier för användaretablering. Följ beställningen här.

1. Kör den inledande cykeln endast för en begränsad uppsättning användare genom att ställa in [omfångsfiltret](#plan-scoping-filters-and-attribute-mapping).
2. Verifiera etablering av Active Directory-konton och attributvärden som angetts för de användare som valts för den första körningen. Om resultatet uppfyller dina förväntningar expanderar du filtret för omfång för att successivt inkludera fler användare och verifiera resultaten för den andra körningen.

När du är nöjd med resultatet av den inledande cykeln för testanvändare startar du de [inkrementella uppdateringarna](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planera testning och säkerhet

Kontrollera att du testar att resultaten är som förväntat och granskar etableringscyklerna i varje steg av den första piloten genom att aktivera användaretablering.

### <a name="plan-testing"></a>Planera testning

När du har konfigurerat cloud HR-appen till Azure AD-användares etablering kör du testfall för att kontrollera om den här lösningen uppfyller organisationens krav.

|Scenarier|Förväntat resultat|
|:-|:-|
|Ny medarbetare anställs i appen för moln-HR.| - Användarkontot etableras i Active Directory.</br>- Användaren kan logga in på Active Directory-domänappar och utföra önskade åtgärder.</br>- Om Azure AD Connect-synkronisering är konfigurerad skapas användarkontot också i Azure AD.
|Användaren avslutas i molnet HR-appen.|- Användarkontot är inaktiverat i Active Directory.</br>- Användaren kan inte logga in på några företagsappar som skyddas av Active Directory.
|Användartillsynsorganisation uppdateras i appen för moln-HR.|Baserat på attributmappningen flyttas användarkontot från en organisationsenhet till en annan i Active Directory.|
|HR uppdaterar användarens chef i molnet HR-app.|Cheffältet i Active Directory uppdateras för att återspegla den nya chefens namn.|
|HR återanställer en medarbetare till en ny roll.|Beteendet beror på hur molnet HR-appen är konfigurerad för att generera medarbetar-ID:</br>- Om det gamla medarbetar-ID:et återanvänds för en återanställad, aktiverar kopplingen det befintliga Active Directory-kontot för användaren.</br>- Om återanställa ett nytt medarbetar-ID skapar kopplingen ett nytt Active Directory-konto för användaren.|
|HR konverterar medarbetaren till en kontraktsanställd eller vice versa.|Ett nytt Active Directory-konto skapas för den nya personen och det gamla kontot inaktiveras på konverteringens giltighetsdatum.|

Använd föregående resultat för att avgöra hur du överför implementeringen för automatisk användaretablering till produktion baserat på dina etablerade tidslinjer.

> [!TIP]
> Använd tekniker som datareducering och dataskrubbning när du uppdaterar testmiljön med produktionsdata för att ta bort eller maskera känsliga personuppgifter för att uppfylla sekretess- och säkerhetsstandarder. 

### <a name="plan-security"></a>Planera säkerhet

Det är vanligt att en säkerhetsgranskning krävs som en del av distributionen av en ny tjänst. Om en säkerhetsgranskning krävs eller inte har genomförts läser du de många Azure [AD-faktablad](https://www.microsoft.com/download/details.aspx?id=36391) som ger en översikt över identiteten som en tjänst.

### <a name="plan-rollback"></a>Återställ plan

Implementeringen av användare i molnet kan misslyckas med att fungera som önskat i produktionsmiljön. Om så är fallet kan följande återställningssteg hjälpa dig att återgå till ett tidigare fungerande tillstånd.

1. Granska [komredovisningsrapporten](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) och [etableringsloggarna](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) för att ta reda på vilka felaktiga åtgärder som utfördes för de berörda användarna eller grupperna. Mer information om komredovisningsrapporten och loggarna för etablering finns i [Hantera användaretablering i molnet.](#manage-your-configuration)
2. Det senast fungerande tillståndet för de berörda användarna eller grupperna kan bestämmas genom granskningsloggarna för etablering eller genom att granska målsystemen (Azure AD eller Active Directory).
3. Arbeta med appens ägare för att uppdatera de användare eller grupper som påverkas direkt i appen med hjälp av de senast kända bra tillståndsvärdena.

## <a name="deploy-the-cloud-hr-app"></a>Distribuera appen för moln-HR

Välj den moln-HR-app som anpassar till dina lösningskrav.

**Arbetsdag:** Om du vill importera arbetarprofiler från Arbetsdag till Active Directory och Azure AD läser du [Självstudiekurs: Konfigurera arbetsdag för automatisk användaretablering](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Du kan också skriva tillbaka e-postadressen och användarnamnet till Workday.

## <a name="manage-your-configuration"></a>Hantera konfigurationen

Azure AD kan ge ytterligare insikter om organisationens användaretableringsanvändning och driftshälsa via granskningsloggar och rapporter.

### <a name="gain-insights-from-reports-and-logs"></a>Få insikter från rapporter och loggar

Efter en lyckad [inledande cykel](../app-provisioning/how-provisioning-works.md#initial-cycle)fortsätter Azure AD-etableringstjänsten att köra inkrementella uppdateringar från rygg mot rygg på obestämd tid, med intervall som definierats i de självstudier som är specifika för varje app, tills någon av följande händelser inträffar:

- Tjänsten stoppas manuellt. En ny inledande cykel utlöses med hjälp av [Azure-portalen](https://portal.azure.com/) eller lämpligt [Microsoft Graph API-kommando.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- En ny inledande cykel utlöses på grund av en ändring av attributmappningar eller omfångsfilter.
- Etableringsprocessen går i karantän på grund av en hög felfrekvens. Den förblir i karantän i mer än fyra veckor, då den inaktiveras automatiskt.

Om du vill granska dessa händelser och alla andra aktiviteter som utförs av etableringstjänsten [läser du om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Alla aktiviteter som utförs av etableringstjänsten registreras i Azure AD-granskningsloggarna. Du kan dirigera Azure AD-granskningsloggar till Azure Monitor-loggar för vidare analys. Med Azure Monitor-loggar (kallas även Log Analytics-arbetsyta) kan du fråga data för att hitta händelser, analysera trender och utföra korrelation mellan olika datakällor. Titta på det här [videoklippet](https://youtu.be/MP5IaCTwkQg) om du vill lära dig fördelarna med att använda Azure Monitor-loggar för Azure AD-loggar i praktiska användarscenarier.

Installera [logganalysvyerna för Azure AD-aktivitetsloggar för](../reports-monitoring/howto-install-use-log-analytics-views.md) att få åtkomst till [fördefinierade rapporter](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) kring etableringshändelser i din miljö.

Mer information finns i hur du [analyserar Azure AD-aktivitetsloggarna med dina Azure Monitor-loggar](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Hantera personliga data

Azure AD Connect-etableringsagenten som är installerad på Windows-servern skapar loggar i Windows-händelseloggen som kan innehålla personuppgifter beroende på din moln-HR-app till Active Directory-attributmappningar. För att uppfylla användarnas sekretesskrav ställer du in en schemalagd aktivitet i Windows för att rensa händelseloggen och se till att inga data lagras längre än 48 timmar.

Azure AD-etableringstjänsten genererar inte rapporter, utför analyser eller ger insikter efter 30 dagar eftersom tjänsten inte lagrar, bearbetar eller behåller data efter 30 dagar.

### <a name="troubleshoot"></a>Felsöka

Information om hur du felsöker eventuella problem som kan uppstå under etableringen finns i följande artiklar:

- [Problem med att konfigurera användaretablering till ett Azure AD Gallery-program](application-provisioning-config-problem.md)
- [Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program](user-provisioning-sync-attributes-for-mapping.md)
- [Användaretablering till ett Azure AD Gallery-program tar timmar eller mer](application-provisioning-when-will-provisioning-finish.md)
- [Problem med att spara administratörsuppgifter när du konfigurerar användaretablering till ett Azure Active Directory Gallery-program](application-provisioning-config-problem-storage-limit.md)
- [Inga användare etableras i ett Azure AD Gallery-program](application-provisioning-config-problem-no-users-provisioned.md)
- [Fel uppsättning användare etableras i ett Azure AD Gallery-program](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Konfigurera Windows Loggboken för agentfelsökning](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Konfigurera Granskningsloggar för Azure Portal Audit för felsökning av tjänster](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Förstå loggar för AD-användarkonto skapa åtgärder](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Förstå loggar för hanterarens uppdateringsåtgärder](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Lösa vanliga fel påträffades](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Nästa steg

- [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
- [Api-översikt för Azure AD-synkronisering](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Hoppa över radering av användarkonton som inte omfattas](skip-out-of-scope-deletions.md)
- [Etablerande agent för Azure AD Connect: Versionsversionshistorik](provisioning-agent-release-version-history.md)
