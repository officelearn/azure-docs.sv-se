--- 
title: "Komma igång med Azure Automation | Microsoft Docs"
description: "Den här artikeln ger en översikt över Azure Automation-tjänsten och beskriver design, implementeringsdetaljer och förberedelser inför distribution från Azure Marketplace."
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
ms.date: 08/18/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 54f137b26bf1c8f966e8ef110dcf3d25abf7ac5b
ms.contentlocale: sv-se
ms.lasthandoff: 08/19/2017

---

# <a name="getting-started-with-azure-automation"></a>Komma igång med Azure Automation

Den här guiden för att komma igång beskriver grundläggande begrepp för distribution av Azure Automation. Om Automation i Azure är nytt för dig eller om du har erfarenhet av programvara för automatiserade arbetsflöden (till exempel System Center Orchestrator) kan den här guiden hjälpa dig att förstå hur du ska förbereda och publicera Automation.  Sedan kommer du att vara redo att börja utveckla runbooks som stöd i din processautomation. 


## <a name="automation-architecture-overview"></a>Översikt över arkitekturen i Automation

![Översikt över Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation är ett SaaS-program (programvara som en tjänst) som erbjuder en skalbar, tillförlitlig miljö för flera innehavare för att automatisera processer med runbooks och hantera konfigurationsändringar i Windows- och Linux-system med Desired State Configuration (DSC) på Azure, på andra molntjänster eller lokalt. Enheter i ditt Automation-konto, till exempel runbooks, tillgångar och Kör som-konton är isolerade från andra Automation-konton i din prenumeration och andra prenumerationer.  

De runbooks som du kör i Azure körs i Automation-sandboxar på virtuella Azure PaaS-datorer (plattform som en tjänst).  Automation-sandboxar ger innehavare isolering för alla aspekter av runbook-körningen – moduler, lagring, minne, nätverkskommunikation, jobbströmmar m.m. Den här rollen hanteras av tjänsten och kan inte nås eller kontrolleras från ditt Azure- eller Azure Automation-konto.         

Om du vill automatisera distributionen och hanteringen av resurser i ditt lokala datacenter eller andra molntjänster när du har skapat ett Automation-konto, kan du välja en eller flera datorer som kör [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)-rollen.  För varje Hybrid Runbook Worker (HRW) behövs en Microsofts-hanteringsagent (MMA) med anslutning till en Log Analytics-arbetsyta och ett Automation-konto.  Log Analytics används för att starta installationen, underhålla MMA-agenten och övervaka funktionerna i Hybrid Runbook Worker.  Azure Automation tar hand om leveransen av runbooks och instruktionen för att köra dem.

Du kan distribuera flera HRW för att tillhandahålla hög tillgänglighet för dina runbooks, belastningsutjämna runbookjobb, och i vissa fall dedikera dem till specifika arbetsbelastningar eller miljöer.  Microsofts-hanteringsagent på Hybrid Runbook Worker startar kommunikationen med Automation-tjänsten via TCP-port 443, och det finns inga krav på brandvägg för inkommande trafik.  Om du har en runbook som körs på en Hybrid Runbook Worker i ditt datacenter och vill att runbooken ska utföra hanteringsuppgifter mot andra datorer eller tjänster i miljön, kan runbooken behöva åtkomst till andra portar.  Om dina IT-säkerhetsprinciper inte tillåter att datorer i nätverket ansluter till Internet kan du läsa artikeln [OMS-gateway](../log-analytics/log-analytics-oms-gateway.md), som agerar som proxy för HRW för insamling av jobbstatus och mottagning av konfigurationsinformation från ditt Automation-konto.

Runbooks som körs på en HRW körs i kontexten för det lokala systemkontot på datorn, vilket är den säkerhetskontext som rekommenderas när du utför administrativa åtgärder på den lokala Windows-datorn. Om du vill att en runbook ska köra aktiviteter mot resurser utanför den lokala datorn måste du definiera säkra inloggningstillgångar i Automation-kontot. Du kan komma åt dessa från runbooken och använda dem för autentisering med den externa resursen. Du kan använda tillgångar som [autentiseringsuppgifter](automation-credentials.md), [certifikat](automation-certificates.md) och [anslutning](automation-connections.md) i din runbook med cmdlets. På så sätt kan du ange autentiseringsuppgifter så att du kan autentisera dem.

DSC-konfigurationer som lagras i Azure Automation kan tillämpas direkt på virtuella datorer i Azure. Andra fysiska och virtuella datorer kan begära konfigurationer från Azure Automation DSC-hämtningsservern.  När det gäller hantering av konfigurationer för dina lokala fysiska eller virtuella Windows- och Linux-system behöver du inte distribuera någon infrastruktur för att kunna använda Automation DSC-hämtningsservern, endast utgående Internet-åtkomst från varje system som ska hanteras av Automation DSC med kommunikation via port 443 (TCP) till OMS-tjänsten.   

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

### <a name="permissions-required-to-create-automation-account"></a>Behörighet som krävs för att skapa Automation-konton
För att kunna skapa eller uppdatera Automation-kontot och slutföra det här avsnittet måste du ha vissa behörigheter.   
 
* Om du ska kunna skapa ett Automation-konto måste ditt AD-användarkonto tilldelas en roll med behörigheter motsvarande ägarrollen för Microsoft.Automation-resurser enligt beskrivningen i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  
* Om **Ja** har angetts för inställningen Appregistreringar kan användare som inte är administratörer i din Azure AD-klient [registrera AD-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Om **Nej** har angetts för inställningen Appregistreringar måste användaren som utför den här åtgärden vara global administratör i Azure AD. 

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen som global administratör/medadministratör för prenumerationen läggs du till i Active Directory som gäst. I så fall visas varningen ”Du har inte behörighet att skapa ...” på bladet **Lägg till Automation-konto**. Användare som har tilldelats rollen som global administratör/medadministratör kan tas bort från prenumerationens Active Directory-instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

## <a name="authentication-planning"></a>Planera för autentisering
Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders.  För att en runbook ska kunna utföra sina åtgärder måste den ha behörighet att komma åt resurserna på ett säkert sätt med den minsta behörighet som krävs i prenumerationen.  

### <a name="what-is-an-automation-account"></a>Vad är ett Automation-konto? 
Alla automatiseringsaktiviteter som du utför mot resurser med hjälp av Azure-cmdletar i Azure Automation autentiserar till Azure med hjälp av autentiseringsuppgiftsbaserad autentisering med organisationens Azure Active Directory-ID.  Ett Automation-konto är ett annat konto än det som du använder för att logga in på portalen för att konfigurera och använda Azure-resurser.  Följande Automation-resurser ingår i ett konto:

* **Certifikat** – innehåller ett certifikat som används för autentisering från en runbook eller DSC-konfiguration eller lägger till dem.
* **Anslutningar** – innehåller information om autentisering och konfiguration som krävs för att ansluta till en extern tjänst eller ett externt program från en runbook eller DSC-konfiguration.
* **Autentiseringsuppgifter** – är ett PSCredential-objekt som innehåller säkerhetsreferenser, som användarnamn och lösenord, och som krävs för att autentisera från en runbook eller DSC-konfiguration.
* **Integreringsmoduler** – är PowerShell-moduler som ingår i ett Azure Automation-konto för användning av cmdlets i runbooks och DSC-konfigurationer.
* **Scheman** – innehåller scheman som startar eller stoppar en runbook vid en angiven tidpunkt, även med återkommande frekvens.
* **Variabler** – innehåller värden som är tillgängliga från en runbook eller DSC-konfiguration.
* **DSC-konfigurationer** – är PowerShell-skript som beskriver hur du konfigurerar en funktion eller inställning för operativsystem eller installerar ett program på en Windows- eller Linux-dator.  
* **Runbooks** – är en uppsättning uppgifter som kör automatiserade processer i Azure Automation baserat på Windows PowerShell.    

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

I avsnittet **How to\Authentication and Security** (Anvisningar\Autentisering och säkerhet) finns artiklar som innehåller en översikt och implementeringssteg för att konfigurera autentisering för dessa miljöer, antingen med ett befintligt eller ett nytt konto som du anger för den aktuella miljön.  I [Uppdatera ett Automation-konto](automation-create-runas-account.md) beskrivs hur du uppdaterar ditt befintliga Automation-konto med Kör som-konton från portalen eller med PowerShell, om inte kontot ursprungligen konfigurerades med ett Kör som-konto eller ett klassiskt Kör som-konto. Om du vill skapa ett Kör som- och ett klassiskt Kör som-konto med ett certifikat utfärdat av din utfärdare av företagscertifikat (CA) kan du läsa om att skapa kontona med den här konfigurationen i den här artikeln.     
 
## <a name="network-planning"></a>Planera för nätverk
För att Hybrid Runbook Worker ska kunna ansluta till och registreras med Microsoft Operations Management Suite (OMS) måste den ha åtkomst till portnumret och URL:en som anges nedan.  Detta gäller utöver de [portar och URL:er som krävs för att Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agents.md#network) ska kunna ansluta till OMS. Om du använder en proxyserver för kommunikation mellan agenten och OMS-tjänsten måste du se till att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till Internet måste du konfigurera brandväggen att tillåta åtkomst.

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

## <a name="creating-an-automation-account"></a>Skapa ett Automation-konto

Du kan skapa ett Automation-konto på Azure Portal på olika sätt.  I följande tabell visas varje typ av distribution och skillnaderna mellan dem.  

|Metod | Beskrivning |
|-------|-------------|
| Välj Automatisering och kontroll från Marketplace | Ett erbjudande som skapar både ett Automation-konto och en OMS-arbetsyta som är länkade till varandra i samma resursgrupp och region.  Vid integrering med OMS kan du även använda Log Analytics till att övervaka och analysera statusen för runbook-jobb och jobbströmmar över tid och använda avancerade funktioner till att eskalera eller undersöka problem. I erbjudandet distribueras också lösningar för spårning av ändringar och uppdateringshantering (aktiverat som standard). |
| Välj Automation från Marketplace | Skapar ett Automation-konto i en ny eller befintlig resursgrupp som inte är länkad till en OMS-arbetsyta och som inte innehåller några tillgängliga lösningar från erbjudandet Automatisering och kontroll. Detta är en grundläggande konfiguration som ger en introduktion till Automation. Med den kan du lära dig hur du skriver runbooks, DSC-konfigurationer och använder funktionerna i tjänsten. |
| Valda hanteringslösningar | Om du väljer en lösning (**[Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md)**, **[Starta/stoppa virtuella datorer utanför arbetstid](automation-solution-vm-management.md)** eller **[Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md)**) uppmanas du att välja en befintlig Automation- och OMS-arbetsyta, eller så erbjuds du möjligheten att skapa dessa om det behövs för den lösning som ska distribueras i din prenumeration. |

I det här avsnittet visas hur du skapar ett Automation-konto och en OMS-arbetsyta genom att integrera Automatisering och kontroll.  Om du vill skapa ett fristående Automation-konto för testning eller för att förhandsgranska tjänsten kan du läsa artikeln [Skapa fristående Automation-konto](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-oms"></a>Skapa Automation-konto integrerat med OMS
Den rekommenderade metoden för att integrera Automation är att välja erbjudandet Automatisering och kontroll från Marketplace.  Detta skapar både ett Automation-konto och en integrering med en OMS-arbetsyta, inklusive alternativet att installera hanteringslösningar som är tillgängliga med erbjudandet.  

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

När erbjudandet har integrerats kan du börja skapa runbooks, arbeta med hanteringslösningarna som du har aktiverat, distribuera rollen [Hybrid Runbook worker](automation-hybrid-runbook-worker.md) eller börja arbeta med [Log Analytics](https://docs.microsoft.com/azure/log-analytics) och samla in data som genereras av resurser i molnet eller som finns lokalt.   

## <a name="next-steps"></a>Nästa steg
* Du kan bekräfta att det nya Automation-kontot kan autentisera mot Azure-resurser genom att granska [testa autentisering av Azure Automation Kör som-konto](automation-verify-runas-authentication.md).
* Om du vill komma igång med att skapa runbooks ska du först kontrollera vilka [typer av Automation-runbooks](automation-runbook-types.md) som stöds och liknande information innan du börjar skriva.



