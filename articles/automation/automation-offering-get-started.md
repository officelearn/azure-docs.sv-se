---
title: "Komma igång med Azure Automation | Microsoft Docs"
description: "Den här artikeln ger en översikt över Azure Automation-tjänsten och beskriver grundläggande koncept, implementeringsdetaljer och förberedelser inför distribution från Azure Marketplace."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 9b4982ffece9283304ad3ab3c82a471ac1dbd463
ms.contentlocale: sv-se
ms.lasthandoff: 05/11/2017

---

## <a name="getting-started-with-azure-automation"></a>Komma igång med Azure Automation

Den här guiden för att komma igång beskriver grundläggande begrepp för distribution av Azure Automation. Om Automation i Azure är nytt för dig eller om du har erfarenhet av programvara för automatiserat arbetsflöde (till exempel System Center Orchestrator) kan den här guiden hjälpa dig att komma igång. Guiden innehåller information om begrepp och distribution.

## <a name="key-concepts"></a>Viktiga begrepp

### <a name="automation-service"></a>Automation-tjänsten
Automation är en Azure-tjänst som använder Windows PowerShell och Azure-tekniker för att kontrollera din hantering av Azure, molnet, din lokala infrastruktur och dina program.  Azure Automation hjälper dig att hantera hela livscykeln för tjänster och program med automatisk distribution med hjälp av processautomatisering, konfiguration av operativsystem med PowerShell Desired State Configuration, löpande uppdatering och övervakning med hantering av uppdateringar och spårning av ändringar samt automatisk felsökning och reparation.

### <a name="automation-account"></a>Automation-konto
Ett Automation-konto är en Azure-resurs som du skapar.  Du kan hantera alla dina Azure-resurser, molnresurser och lokala resurser med ett enda Automation-konto.  Ett Automation-konto är en behållare för objekt som du behöver för automatiseringsjobb, till exempel runbooks, moduler, tillgångar som autentiseringsuppgifter och scheman samt konfigurationer. Du kan använda flera Automation-konton för att dela in resurserna i olika logiska miljöer som utveckling, testning och produktion eller i olika geografiska områden.  

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Om du kör runbooks på fysiska eller virtuella datorer i ditt lokala datacenter, på Azure eller hos någon annan molntjänstleverantör kan du komma åt och hantera dessa lokala resurser med funktionen Hybrid Runbook Worker.     

### <a name="automation-desired-state-configuration"></a>Automation – önskad tillståndskonfiguration
Automation Desired State Configuration (DSC), som bygger på PowerShell DSC, konfigurerar, övervakar och uppdaterar automatiskt önskad status för de operativsystem du har på Azure, lokalt eller i ett annat moln.  

### <a name="management-solutions"></a>Hanteringslösningar
Hanteringslösningar, som uppdateringshantering och spårning av ändringar, utökar funktionerna i Azure Automation och används med Log Analytics.  Lösningarna kan innefatta flera resurser som stöder ett visst hanteringsscenario, till exempel Automation-runbooks, fördefinierade Log Analytics-sökfrågor, aviseringar och visualiseringar.  

## <a name="architecture-overview"></a>Översikt över arkitekturen

Azure Automation är ett SaaS-program (programvara som en tjänst) som erbjuder en skalbar, tillförlitlig miljö för flera innehavare för att automatisera processer med runbooks och hantera konfigurationsändringar i Windows- och Linux-system med Desired State Configuration (DSC) på Azure, på andra molntjänster eller lokalt. Enheter i ditt Automation-konto, till exempel runbooks, tillgångar och Kör som-konton är isolerade från andra Automation-konton i din prenumeration och andra prenumerationer.  

De runbooks som du kör i Azure körs i Automation-sandboxar på virtuella Azure PaaS-datorer (plattform som en tjänst).  Automation-sandboxar ger innehavare isolering för alla aspekter av runbook-körningen – moduler, lagring, minne, nätverkskommunikation, jobbströmmar m.m. Den här rollen hanteras av tjänsten och kan inte nås eller kontrolleras från ditt Azure- eller Azure Automation-konto.         

Om du vill automatisera distributionen och hanteringen av resurser i ditt lokala datacenter eller andra molntjänster kan du utse en eller flera datorer som kör Hybrid Runbook Worker-rollen.  Varje HRW behöver en Microsofts-hanteringsagent (MMA) med en anslutning till en Log Analytics-arbetsyta och ett Automation-konto.  Log Analytics används för att starta installationen, underhålla MMA-agenten och övervaka HRW-funktioner.  Azure Automation tar hand om leveransen av runbooks och instruktionen för att köra dem.

Du kan distribuera flera HRW för att tillhandahålla hög tillgänglighet för dina runbooks, belastningsutjämna runbookjobb, och i vissa fall dedikera dem till specifika arbetsbelastningar eller miljöer.  HRW kommunicerar med Automation-tjänsten via den utgående porten 443 (TCP).  Om du har en runbook som körs på en HRW i ditt datacenter och vill att runbooken ska utföra hanteringsuppgifter mot andra datorer eller tjänster i datacentret kan runbooken behöva åtkomst till andra portar.  Om dina IT-säkerhetsprinciper inte tillåter att datorer i nätverket ansluter till Internet kan du läsa artikeln [OMS-gateway](../log-analytics/log-analytics-oms-gateway.md), som agerar som proxy för HRW för insamling av jobbstatus och mottagning av konfigurationsinformation från ditt Automation-konto.

Runbooks som körs på en HRW körs i kontexten för det lokala systemkontot på datorn, vilket är den säkerhetskontext som rekommenderas när du utför administrativa åtgärder på den lokala Windows-datorn. Om du vill att en runbook ska köra aktiviteter mot resurser utanför den lokala datorn måste du definiera säkra inloggningstillgångar i Automation-kontot. Du kan komma åt dessa från runbooken och använda dem för autentisering med den externa resursen. Du kan använda tillgångar som [autentiseringsuppgifter](automation-credentials.md), [certifikat](automation-certificates.md) och [anslutning](automation-connections.md) i din runbook med cmdlets. På så sätt kan du ange autentiseringsuppgifter så att du kan autentisera dem.

DSC-konfigurationer som lagras i Azure Automation kan tillämpas direkt på virtuella datorer i Azure. Andra fysiska och virtuella datorer kan begära konfigurationer från Azure Automation DSC-hämtningsservern.  När det gäller hantering av konfigurationer för dina lokala fysiska eller virtuella Windows- och Linux-system behöver du inte distribuera någon infrastruktur för att kunna använda Automation DSC-hämtningsservern, endast utgående Internet-åtkomst från varje system som ska hanteras av Automation DSC med kommunikation via port 443 (TCP) till OMS-tjänsten.   

![Översikt över Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

## <a name="prerequisites"></a>Krav

### <a name="automation-dsc"></a>Automation DSC
Azure Automation DSC kan användas för att hantera olika datorer:

* Virtuella Azure-datorer (klassisk) som kör Windows eller Linux
* Virtuella Azure-datorer som kör Windows eller Linux
* Virtuella AWS-datorer (Amazon Web Services) som kör Windows eller Linux
* Fysiska/virtuella Windows-datorer som körs lokalt, eller i ett annat moln än Azure eller AWS
* Fysiska/virtuella Linux-datorer som körs lokalt, eller i ett annat moln än Azure eller AWS

Den senaste versionen av WMF 5 måste vara installerad för PowerShell DSC-agenten för att Windows ska kunna kommunicera med Azure Automation. Den senaste versionen av [PowerShell DSC-agenten för Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) måste vara installerad för att Linux ska kunna kommunicera med Azure Automation.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
När du anger att en dator ska köra hybrid runbook-jobb måste datorn ha följande:

* Windows Server 2012 eller senare
* Windows PowerShell 4.0 eller senare.  Vi rekommenderar att du installerar Windows PowerShell 5.0 på datorn för ökad tillförlitlighet. Du kan ladda ned den senaste versionen från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=50395)
* Minst två kärnor
* Minst 4 GB RAM-minne

## <a name="security"></a>Säkerhet
Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders.  För att en runbook ska kunna utföra sina åtgärder måste den ha behörighet att komma åt resurserna på ett säkert sätt med den minsta behörighet som krävs i prenumerationen.  

### <a name="automation-account"></a>Automation-konto
Alla automatiseringsaktiviteter som du utför mot resurser med hjälp av Azure-cmdletar i Azure Automation autentiserar till Azure med hjälp av autentiseringsuppgiftsbaserad autentisering med organisationens Azure Active Directory-ID.  Ett Automation-konto är ett annat konto än det som du använder för att logga in på portalen för att konfigurera och använda Azure-resurser.  

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men Automation-konton kan hantera alla resurser i din prenumeration. Skapa Automation-konton i olika regioner om du har principer som kräver att data och resurser är isolerade i en specifik region.

> [!NOTE]
> Automation-konton, och de resurser som de innehåller, som skapats på Azure-portalen kan inte nås på den klassiska Azure-portalen. Om du vill hantera dessa konton eller deras resurser med Windows PowerShell måste du använda Azure Resource Manager-modulerna.
>

När du skapar ett Automation-konto på Azure Portal skapar du automatiskt två autentiseringsenheter:

* Ett Kör som-konto. Det här kontot skapar ett tjänstobjekt i Azure Active Directory (AD Azure) och ett certifikat. Det tilldelar också den rollbaserade åtkomstkontrollen (RBAC) för deltagare, som hanterar Resource Manager-resurser med hjälp av runbookflöden.
* Ett klassiskt Kör som-konto. Det här kontot överför ett hanteringscertifikat som används för att hantera klassiska resurser med hjälp av runbooks.

Rollbaserad åtkomstkontroll är tillgängligt i Azure Resource Manager-läge för att bevilja tillåtna åtgärder för ett Azure AD-användarkonto och Kör som-konto för att autentisera tjänstobjektet.  Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Autentiseringsmetoder
Följande tabell sammanfattar de olika autentiseringsmetoderna för varje miljö som stöds av Azure Automation.

| Metod | Miljö
| --- | --- |
| Azure Kör som-konto och klassiskt Kör som-konto |Azure Resource Manager och klassisk Azure-distribution |  
| Azure AD-användarkonto |Azure Resource Manager och klassisk Azure-distribution |  
| Windows-autentisering |Lokalt datacenter eller annan molnprovider som använder Hybrid Runbook Worker |  
| AWS-autentiseringsuppgifter |Amazon Web Services |  

I avsnittet **How to\Authentication and Security** (Anvisningar\Autentisering och säkerhet) finns artiklar som innehåller en översikt och implementeringssteg för att konfigurera autentisering för dessa miljöer, antingen med ett befintligt eller ett nytt konto som du anger för den miljön.  I [Uppdatera ett Automation-konto med hjälp av PowerShell](automation-update-account-powershell.md) beskrivs hur du uppdaterar ditt befintliga Automation-konto med Kör som-konton med PowerShell, om inte kontot ursprungligen konfigurerades med ett Kör som-konto eller ett klassiskt Kör som-konto.   

## <a name="network"></a>Nätverk
För att Hybrid Runbook Worker ska kunna ansluta till och registreras med Microsoft Operations Management Suite (OMS) måste den ha åtkomst till portnumret och URL:en som anges nedan.  Detta gäller utöver de [portar och URL:er som krävs för att Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agents.md) ska kunna ansluta till OMS. Om du använder en proxyserver för kommunikation mellan agenten och OMS-tjänsten måste du se till att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till Internet måste du konfigurera brandväggen att tillåta åtkomst.

Nedan anges porten och de URL:er som krävs för att Hybrid Runbook Worker ska kunna kommunicera med Automation.

* Port: Endast TCP 443 krävs för utgående Internet-åtkomst
* Global URL:  *.azure-automation.net

Om du har ett Automation-konto som har definierats för en specifik region och vill begränsa kommunikationen med det regionala datacentret finns DNS-posten för varje region i följande tabell.

| **Region** | **DNS-post** |
| --- | --- |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net |
| Östra USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Västra centrala USA | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Västra Europa |we-jobruntimedata-prod-su1.azure-automation.net |
| Norra Europa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Centrala Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net |
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net |
| Östra Japan |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net |
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net |
| Virginia (USA-förvaltad region) | usge-jobruntimedata-prod-su1.azure-automation.us |

Om du behöver en lista med IP-adresser istället för namn kan du ladda ned och läsa igenom XML-filen med [IP-adresser från Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) från Microsoft Download Center.

> [!NOTE]
> Den här filen innehåller de IP-adressintervall (inklusive Compute, SQL och Storage-intervall) som används i Microsoft Azure-datacentren. Varje vecka publiceras en uppdaterad fil med aktuella intervall och eventuella kommande ändringar av IP-adressintervallen. De nya intervall som anges i filen kommer inte att börja användas i datacentren förrän om minst en vecka. Ladda ned den nya XML-filen varje vecka och gör nödvändiga ändringar på din webbplats för att kunna identifiera vilka tjänster som körs i Azure. ExpressRoute-användare minns kanske att den här filen användes för att uppdatera Azures BGP-annonser under den första veckan varje månad.
>


## <a name="implementation"></a>Implementering

### <a name="creating-an-automation-account"></a>Skapa ett Automation-konto

Du kan skapa ett Automation-konto på Azure Portal på olika sätt.  I följande tabell visas varje typ av distribution och skillnaderna mellan dem.  

|Metod | Beskrivning |
|-------|-------------|
| Välj Automatisering och kontroll från Marketplace | Ett erbjudande som skapar både ett Automation-konto och en OMS-arbetsyta som är länkade till varandra i samma resursgrupp och region.  Denna metod distribuerar också lösningar för spårning av ändringar och uppdateringshantering (aktiverat som standard). |
| Välj Automation från Marketplace | Skapar ett Automation-konto i en ny eller befintlig resursgrupp som inte är länkad till en OMS-arbetsyta och som inte innehåller några tillgängliga lösningar från erbjudandet Automatisering och kontroll. Detta är en grundläggande konfiguration som ger en introduktion till Automation. Med den kan du lära dig hur du skriver runbooks, DSC-konfigurationer och använder funktionerna i tjänsten. |
| Valda hanteringslösningar | Om du väljer en lösning (**[Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md)**, **[Starta/stoppa virtuella datorer utanför arbetstid](automation-solution-vm-management.md)** eller **[Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md)**) uppmanas du att välja en befintlig Automation- och OMS-arbetsyta, eller så erbjuds du möjligheten att skapa dessa om det behövs för den lösning som ska distribueras i din prenumeration. |

I det här avsnittet visas hur du skapar ett Automation-konto och en OMS-arbetsyta genom att integrera Automatisering och kontroll.  Om du vill skapa ett fristående Automation-konto för testning eller för att förhandsgranska tjänsten kan du läsa artikeln [Skapa fristående Automation-konto](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-log-analytics"></a>Skapa Automation-konto integrerat med Log Analytics
Den rekommenderade metoden för att integrera Automation är att välja erbjudandet Automatisering och kontroll från Marketplace.  Detta skapar både ett Automation-konto och en integrering med en OMS-arbetsyta, inklusive alternativet att installera hanteringslösningar som är tillgängliga med erbjudandet.  

>[!NOTE]
>För att kunna skapa ett Automation-konto måste du vara medlem i rollen Tjänstadministratörer eller vara medadministratör för den prenumeration som ger åtkomst till prenumerationen. Du måste också läggas till som användare i prenumerationens Active Directory-standardinstans. Kontot behöver inte tilldelas en privilegierad roll.
>
>Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen som medadministratör för prenumerationen läggs du till i Active Directory som gäst. I så fall visas varningen ”Du har inte behörighet att skapa ...” på bladet **Lägg till Automation-konto**.
>
>Användare som har lagts till i rollen som medadministratör kan först tas bort från prenumerationens Active Directory-instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.

2. Klicka på **Ny**.<br><br> ![Välj alternativet Ny på Azure Portal](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Sök efter **Automation** och välj sedan **Automatisering och kontroll*** i sökresultaten.<br><br> ![Sök efter och välj Automatisering och kontroll från Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Klicka på **Skapa** när du har läst beskrivningen för erbjudandet.  

5. Välj **OMS-arbetsyta** på inställningsbladet **Automatisering och kontroll**.  På bladet **OMS-arbetsytor** kan du välja en OMS-arbetsyta som är länkad till samma Azure-prenumeration som Automation-kontot eller skapa en ny OMS-arbetsyta.  Om du inte har en OMS-arbetsyta väljer du **Skapa ny arbetsyta** och utför följande på bladet **OMS-arbetsyta**:
   - Ange ett namn för den nya **OMS-arbetsytan**.
   - Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   - Du kan skapa en **resursgrupp** eller välja en befintlig resursgrupp.  
   - Välj en **Plats**.  För närvarande är endast regionerna **Australien, sydöstra**,  **USA, östra**, **Sydostasien**, **USA, västra centrala** och **Europa, västra** tillgängliga.
   - Välj en **Prisnivå**.  Lösningen erbjuds i två nivåer: Kostnadsfri eller Per nod (OMS).  Den kostnadsfria nivån har en gräns för mängden information som samlas in varje dag, kvarhållningsperioden och körtid för runbook-jobb.  Per nod (OMS) har ingen daglig gräns för insamlad data.  
   - Välj **Automation-konto**.  Om du skapar en ny OMS-arbetsyta måste du också skapa ett Automation-konto som ska associeras med den nya OMS-arbetsytan, inklusive din Azure-prenumeration, resursgrupp och region.  Du kan välja **Skapa ett Automation-konto** och ange följande på bladet **Automation-konto**:
  - I fältet **namn** anger du namnet på Automation-kontot.

    Alla andra alternativ fylls i automatiskt baserat på den valda OMS-arbetsytan. Dessa alternativ kan inte ändras.  Ett Azure Kör som-konto är standardmetoden för autentisering för erbjudandet.  När du klickar på **OK** verifieras konfigurationsalternativen och Automation-kontot skapas.  Du kan spåra förloppet under **Meddelanden** på menyn.

    Annars väljer du ett befintligt Automation Kör som-konto.  Kontot du väljer får inte redan vara länkat till en annan OMS-arbetsyta. I så fall visas ett meddelande i bladet.  Om det redan är länkat måste du välja ett annat Automation Kör som-konto eller skapa ett nytt.

    När du har angett informationen som behövs klickar du på **Skapa**.  Informationen verifieras och Automation-kontot och Kör som-kontona skapas.  Du återgår automatiskt till bladet **OMS-arbetsyta**.  

6. När du har angett informationen som krävs på bladet **OMS-arbetsyta**, klickar du på **Skapa**.  När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn.  Du kommer tillbaka till bladet **Lägg till lösning**.  

7. På inställningsbladet **Automatisering och kontroll** bekräftar du att du vill installera de rekommenderade förvalda lösningarna. Om du avmarkerar någon av dessa kan du installera dem individuellt senare.  

8. Klicka på **Skapa** för att fortsätta med integreringen av Automation och OMS-arbetsytan. Alla inställningar verifieras och sedan distribueras lösningen i din prenumeration.  Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **Meddelanden** på menyn.

När erbjudandet har integrerats kan du börja skapa runbooks, arbeta med hanteringslösningarna som du har aktiverat eller börjar arbeta med [Log Analytics](https://docs.microsoft.com/azure/log-analytics) för att samla in data som genereras av resurser i molnet eller som finns lokalt.   

## <a name="next-steps"></a>Nästa steg
* Du kan bekräfta att det nya Automation-kontot kan autentisera mot Azure-resurser genom att granska [testa autentisering av Azure Automation Kör som-konto](automation-verify-runas-authentication.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

