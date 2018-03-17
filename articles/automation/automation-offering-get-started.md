---
title: "Kom igång med Azure Automation"
description: "Den här artikeln innehåller en översikt över Azure Automation-tjänsten. Den granskar design och implementeringslösning information inför onboarding erbjudande från Azure Marketplace."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: dab404178b45828732e137835213046cedaf0d03
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="get-started-with-azure-automation"></a>Kom igång med Azure Automation

Den här artikeln introducerar grundläggande begrepp som rör distribution av Azure Automation. Om du har använt automatisering i Azure eller har erfarenhet av automation arbetsflödet program som System Center Orchestrator kan du lära dig hur du förbereder och publicera Automation. När du har läst den här artikeln vara du redo att börja utveckla runbooks för att stödja dina behov för automatisering av processen. 


## <a name="automation-architecture-overview"></a>Översikt över arkitekturen i Automation

![Översikt över Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation är en programvara som en tjänst (SaaS) som ger en skalbar och tillförlitlig multitenant miljö där du kan använda runbooks för att automatisera processer. Du kan använda önskad tillstånd Configuration (DSC) i Azure, andra molntjänster eller i en lokal miljö för att hantera konfigurationer för ändringar i Windows och Linux-system. Entiteter i ditt Automation-konto, inklusive runbooks, resurser och kör som-konton som är isolerad från andra Automation-konton i din prenumeration och från andra prenumerationer.  

Runbooks som körs i Azure körs på Automation sandboxar. Sandboxar finns i Azure-plattformen som en tjänst (PaaS) virtuella datorer. 

Automation sandboxar ange klientisolering för alla aspekter av runbook-körningen, inklusive moduler, lagring, minne, nätverkskommunikation och dataströmmar för jobbet. Den här rollen hanteras av tjänsten. Du kan inte komma åt eller hantera rollen från Azure eller Automation-konto.         

För att automatisera distribution och hantering av resurser i ditt lokala datacenter eller andra molntjänster, när du har skapat ett Automation-konto kan du ange en eller flera virtuella datorer att köra den [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) roll. Varje Runbook Worker-Hybrid kräver Microsoft-hanteringsagenten som ska installeras och ett Automation-konto. Agenten måste ha en anslutning till en Azure logganalys-arbetsyta. Du kan använda Log Analytics för att starta installationen, underhålla Microsoft-hanteringsagenten och övervaka funktionerna i Runbook Worker-hybriden. Azure Automation utför leveransen av runbooks och anvisningarna för att köra dem.

Du kan distribuera flera Hybrid Runbook Workers. Använd Hybrid Runbook Worker för att tillhandahålla hög tillgänglighet för dina runbooks och belastningsutjämning för runbook-jobb. I vissa fall kan tilldela du runbook-jobb för specifika arbetsbelastningar eller miljöer. Microsoft Monitoring Agent på Hybrid Runbook Worker initierar kommunikation med tjänsten Automation via TCP-port 443. Hybrid Runbook Worker har inga krav för inkommande brandväggen.  

Du kanske vill en runbook som körs på en Hybrid Runbook Worker att utföra hanteringsuppgifter mot andra datorer eller tjänster i din miljö. I det scenariot kan runbook också behöva åtkomst till andra portar. Om din IT-säkerhetsprinciper inte Tillåt datorer i nätverket för att ansluta till internet, granska [OMS Gateway](../log-analytics/log-analytics-oms-gateway.md). Operations Management Suite (OMS) Gateway fungerar som proxy för Hybrid Runbook Worker. Den samlar in jobbstatus och tar emot konfigurationsinformation från ditt Automation-konto.

Runbooks som körs på en Hybrid Runbook Worker körs i kontexten för det lokala systemkontot på datorn. Vi rekommenderar en säkerhetskontext när du utför administrativa åtgärder på den lokala Windows-datorn. Om du vill att runbook körs åtgärder mot resurser som ligger utanför den lokala datorn kan du behöva definiera säker inloggningstillgångar i Automation-kontot. Du kan komma åt säker inloggningstillgångar från runbook och använda dem för att autentisera med den externa resursen. Du kan använda [autentiseringsuppgifter](automation-credentials.md), [certifikat](automation-certificates.md), och [anslutning](automation-connections.md) tillgångar i din runbook. Använda tillgångar med cmdletar som du kan använda för att ange autentiseringsuppgifter för att autentisera dem.

Du kan använda DSC-konfigurationer som lagras i Azure Automation till virtuella datorer. Andra fysiska och virtuella datorer kan begära konfigurationer från hämtningsservern i Automation DSC. Du behöver inte distribuera en infrastruktur för att stödja hämtningsservern Automation DSC för att hantera konfigurationer av din lokala fysiska eller virtuella Windows- och Linux-system. Du behöver bara utgående Internetåtkomst från varje dator som du tänker hantera med hjälp av Automation DSC. Kommunikationen sker via TCP-port 443 till OMS-tjänsten.   

## <a name="prerequisites"></a>Förutsättningar

### <a name="automation-dsc"></a>Automation DSC
Du kan använda Automation DSC för att hantera dessa datorer:

* Virtuella Azure-datorer (klassisk) med Windows eller Linux.
* Virtuella Azure-datorer med Windows eller Linux.
* Amazon Web Services (AWS) virtuella datorer som kör Windows eller Linux.
* Fysiska och virtuella Windows-datorer som är lokalt eller i ett moln än Azure eller AWS.
* Fysiska och virtuella Linux-datorer som finns lokalt eller i ett moln än Azure eller AWS.

Den senaste versionen av Windows Management Framework (WMF) 5 måste installeras för Windows-datorer. För Linux-datorer, den senaste versionen av den [PowerShell DSC-agent för Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) måste vara installerad. PowerShell DSC-agenten använder WMF 5 för att kommunicera med Automation. 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
När du anger en dator att köra en hybrid runbook måste datorn uppfylla följande krav:

* Windows Server 2012 eller senare.
* Windows PowerShell 4.0 eller senare. För ökad tillförlitlighet rekommenderar vi Windows PowerShell 5.0. Du kan [hämta den nya versionen](https://www.microsoft.com/download/details.aspx?id=50395) från Microsoft Download Center.
* .NET framework 4.6.2 eller senare.
* Minst två kärnor.
* Minst 4 GB RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Behörigheter som krävs för att skapa ett Automation-konto
Du måste ha följande behörigheter och behörigheter att skapa eller uppdatera ett Automation-konto och slutföra de uppgifter som beskrivs i den här artikeln:   
 
* Om du vill skapa ett Automation-konto, ditt användarkonto i Azure Active Directory (AD Azure) måste läggas till en roll med behörigheter som motsvarar rollen ägare för **Microsoft.Automation** resurser. Mer information finns i [rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  
* I Azure-portalen under **Azure Active Directory** > **hantera** > **App registreringar**om **App registreringar**  är inställd på **Ja**, icke-administratörer i din Azure AD-klient kan [registrera Active Directory-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Om **App registreringar** är inställd på **nr**, användaren som utför den här åtgärden måste vara en global administratör i Azure AD. 

Om du inte är medlem i Active Directory-instans för prenumerationens innan du lade till rollen som global administratör/coadministrator prenumerationens läggs du till Active Directory som en gäst. I det här scenariot kan du se det här meddelandet på det **lägga till Automation-konto** sida: ”du har inte behörighet att skapa”. 

Om en användare läggs till rollen global administratör/coadministrator först, kan du ta bort dem från prenumerationens Active Directory-instans och nytt läggs till i användarrollen fullständig i Active Directory.

Att verifiera användarroller:
1. I Azure-portalen går du till den **Azure Active Directory** fönstret.
2. Välj **användare och grupper**.
3. Välj **alla användare**. 
4. När du väljer en viss användare, markerar du **profil**. Värdet för den **användartyp** attribut under användarens profil får inte vara **gäst**.

## <a name="authentication-planning"></a>Planera för autentisering
Du kan automatisera uppgifter mot resurser som finns i Azure, lokalt och i andra molntjänster i Azure Automation. För en runbook att utföra åtgärderna som krävs måste den ha behörighet att komma åt resurser på ett säkert sätt. Den måste ha de lägsta möjliga rättigheter som krävs i prenumerationen.  

### <a name="what-is-an-automation-account"></a>Vad är ett Automation-konto 
Alla automation-aktiviteter som du utför mot resurser med hjälp av cmdlets i Azure Automation autentisera till Azure med Azure AD organisationens identiteten autentiseringsuppgifterna-baserad autentisering.  Ett Automation-konto är separat från det konto som används för att logga in på portalen för att konfigurera och använda Azure-resurser. 

Följande resurser finns inkluderade med ett Automation-konto:

* **Certifikat**. Innehåller ett certifikat som används för autentisering från en runbook eller DSC-konfigurationen. Du kan också lägga till certifikat.
* **Anslutningar**. Innehåller information om autentisering och konfiguration som krävs för att ansluta till en extern tjänst eller program från en runbook eller DSC-konfigurationen.
* **Autentiseringsuppgifter**. Innehåller en **PSCredential** -objekt som har behörighet till exempel användarnamn och lösenord. Autentiseringsuppgifterna som krävs för att autentisera från en runbook eller DSC-konfigurationen.
* **Integreringsmoduler**. PowerShell-moduler som ingår i ett Automation-konto. Använd PowerShell-moduler för att köra cmdlet: ar i runbooks och DSC-konfigurationer.
* **Scheman**. Innehåller scheman som kan starta eller stoppa en runbook på en angiven tid, inklusive återkommande frekvenser.
* **Variabler**. Innehåller värden som är tillgängliga från en runbook eller DSC-konfigurationen.
* **DSC-konfigurationer**. PowerShell-skript som beskriver hur du konfigurerar en inställning eller funktion i operativsystemet eller hur du installerar ett program på en Windows- eller Linux-dator.  
* **Runbooks**. En uppsättning uppgifter som utför en automatiserad process i Automation baserat på Windows PowerShell.    

Automation-resurser för varje Automation-konto har associerats med en enda Azure-region. Du kan dock använda Automation-konton för att hantera alla resurser i din prenumeration. Skapa Automation-konton i olika regioner om du har principer som kräver att data och resurser är isolerade i en specifik region.

Två entiteter för autentisering skapas automatiskt när du skapar ett Automation-konto i Azure-portalen:

* **Kör som-konto**. Det här kontot har följande uppgifter:
  - Skapar ett huvudnamn för tjänsten i Azure AD.
  - Skapar ett certifikat.
  - Tilldelar den Contributor Role-Based åtkomstkontroll (RBAC), som hanterar Azure Resource Manager-resurser med hjälp av runbooks.
* **Klassiska kör som-konto**. Det här kontot Överför ett certifikat. Certifikatet används för att hantera klassiska resurser med hjälp av runbooks.

RBAC är tillgänglig med Resource Manager att bevilja tillåtna åtgärder till en Azure AD-användarkontot och kör som-konto. Du kan också använda RBAC för att verifiera att tjänstens huvudnamn. Mer information och hjälp med att utveckla en modell för att hantera behörigheter för Automation finns [rollbaserad åtkomstkontroll i Azure Automation-artikel](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Autentiseringsmetoder
I följande tabell sammanfattas de autentiseringsmetoder som du kan använda för varje miljö som har stöd för Azure Automation.

| Metod | Miljö 
| --- | --- | 
| Azure Kör som-konto och klassiskt Kör som-konto |Azure Resource Manager och klassisk Azure-distribution |  
| Azure AD-användarkonto |Azure Resource Manager och klassisk Azure-distribution |  
| Windows-autentisering |Lokala datacenter eller annan cloud services provider med hjälp av Hybrid Runbook Worker-rollen |  
| Amazon Web Services credentials |Amazon Web Services |  

Följande artiklar innehåller översikt och implementering steg för att konfigurera autentisering för dessa miljöer. Artiklarna beskriver använder en befintlig och använder ett nytt konto som du dedikerar för den miljön. 
* [Skapa en fristående Azure Automation-konto](automation-create-standalone-account.md)
* [Autentisera Runbooks med Azure klassiska distributionen och Resource Manager](automation-create-aduser-account.md)
* [Autentisera Runbooks med Amazon Web Services](automation-config-aws-account.md)
* [Uppdatera Automation kör som-konto](automation-create-runas-account.md)

För Azure kör som och klassiska kör som-konton, [uppdatera Automation kör som-konto](automation-create-runas-account.md) beskriver hur du uppdaterar ett befintligt Automation-konto med Kör som-konton från portalen. Det beskriver också hur du använder PowerShell om Automation-kontot inte ursprungligen konfigurerats med en Kör som- eller klassiska kör som-konto. Du kan skapa ett kör som-konto och klassiska kör som-konto med hjälp av ett certifikat som utfärdas av enterprise-certifikatutfärdare (CA). Granska [uppdatering Automation kör som-konto](automation-create-runas-account.md) information om hur du skapar konton med hjälp av den här konfigurationen.     
 
## <a name="network-planning"></a>Planera ditt nätverk
För Hybrid Runbook Worker kan ansluta till och registrera med OMS måste den ha åtkomst till portnumret och URL: erna som beskrivs i det här avsnittet. Detta är tillägg till den [portar och URL: er som krävs för Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) ska anslutas till OMS. 

Om du använder en proxyserver för kommunikation mellan agenten och OMS-tjänsten måste du kontrollera att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till internet, måste du konfigurera brandväggen att tillåta åtkomst till.

Följande porten och URL: er krävs att kommunicera med Automation Hybrid Runbook Worker rollen:

* Port: Endast TCP 443 krävs för utgående Internetåtkomst.
* Global URL: *.azure-automation.net.

Om du har ett Automation-konto som har definierats för en viss region, kan du begränsa kommunikation till det regionala datacentret. Följande tabell innehåller DNS-post för varje region.

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

En lista över region IP-adresser i stället för regionnamn, hämta den [Azure Datacenter-IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) XML-fil från Microsoft Download Center. 

> [!NOTE]
> Azure-Datacenter IP-adress XML-filen visas de IP-adressintervall som används i Microsoft Azure-datacenter. Beräkning, SQL och lagring adressintervall ingår i filen. 
>
>En uppdaterad fil skickas varje vecka. Filen visar för tillfället distribuerade intervall och eventuella kommande ändringar till IP-adressintervall. Nya adressintervall som visas i filen används inte i datacenter för minst en vecka. 
>
> Det är en bra idé att hämta den nya XML-filen varje vecka. Uppdatera din webbplats om du vill identifiera tjänster som körs i Azure. Azure ExpressRoute användare Observera att den här filen används för att uppdatera Border Gateway Protocol (BGP) annonsen Azure utrymme den första veckan i månaden. 
> 

## <a name="creating-an-automation-account"></a>Skapa ett Automation-konto

I följande tabell beskrivs metoder för att skapa ett Automation-konto i Azure-portalen. Tabellen innehåller information om varje typ av distribution och skillnader.  

|Metod | Beskrivning |
|-------|-------------|
| Välj **Automation- och kontrollservern** i Azure Marketplace | Ett Azure Marketplace-erbjudande skapas ett Automation-konto och OMS-arbetsyta som är länkade och i samma resursgrupp och region. Integrering med OMS även fördelen med att använda Log Analytics att övervaka och analysera runbook-jobbet status och jobbstatus dataströmmar över tid. Du kan också använda de avancerade funktionerna i logganalys eskalera eller undersöka problem. Erbjudandet distribuerar den **ändringsspårning** och **uppdateringshantering** lösningar, som är aktiverade som standard. |
| Välj **Automation** i Marketplace | Den här metoden skapar ett Automation-konto i en ny eller befintlig resursgrupp som inte är kopplad till en OMS-arbetsyta. Det innehåller inte några tillgängliga lösningar från den **Automation- och kontrollservern** erbjudande. Den här metoden är en grundläggande konfiguration som ger en introducerar till Automation. Det kan hjälpa dig att lära dig hur du skriver runbooks och DSC-konfigurationer och lär dig att använda funktionerna i tjänsten. |
| Välj **Management** lösningar | Om du väljer en **Management** lösning, inklusive [uppdateringshantering](../operations-management-suite/oms-solution-update-management.md), [Starta/stoppa virtuella datorer vid låg belastning](automation-solution-vm-management.md), eller [ändringsspårning](../log-analytics/log-analytics-change-tracking.md), lösningen uppmanas du att välja ett befintligt Automation-konto och OMS-arbetsyta. Lösningen kan du skapa ett Automation-konto och OMS-arbetsyta som krävs för lösningen ska distribueras i din prenumeration. |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>Skapa ett Automation-konto som är integrerad med OMS
Att publicera Automation, rekommenderar vi att du väljer den **Automation- och kontrollservern** erbjudande på Marketplace. Med den här metoden skapar ett Automation-konto och upprättar integrering med en OMS-arbetsyta. När du använder den här metoden har också möjlighet att installera hanteringslösningarna som är tillgängliga i erbjudandet.  

[Skapa en fristående Automation-konto](automation-create-standalone-account.md) vägleder dig genom processen att skapa ett Automation-konto och OMS-arbetsytan genom onboarding i **Automation- och kontrollservern** erbjudande. Du kan lära dig hur du skapar en fristående Automation-konto för att testa eller Förhandsgranska tjänsten.  

Skapa ett Automation-konto och OMS-arbetsyta med hjälp av den **Automation- och kontrollservern** Marketplace-erbjudande:

1. Logga in på Azure-portalen med ett konto som är medlem i rollen administratörer för prenumeration och en coadministrator för prenumerationen.
2. Välj **nya**.<br><br> ![Välj ny på Azure-portalen](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Sök efter **Automation**. I sökresultaten väljer **Automation- och kontrollservern**.<br><br> ![Sök efter och välj kontrollen & Automation i Azure Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Granska beskrivning för att erbjuda och markera **skapa**.  
5. Under **Automation- och kontrollservern**väljer **OMS-arbetsytan**. Under **OMS arbetsytor**, Välj en OMS-arbetsyta som är kopplad till Azure-prenumerationen som Automation-kontot. Om du inte har en OMS-arbetsyta, Välj **Skapa ny arbetsyta**. Under **OMS-arbetsytan**: 
  1. För **OMS-arbetsytan**, ange ett namn för det nya arbetsområdet.
  2. För **prenumeration**, välja en prenumeration att länka till. Om standardvalet inte den prenumeration som du vill använda, Välj prenumerationen från den nedrullningsbara listan.
  3. För **resursgruppen**, du kan skapa en resursgrupp eller välja en befintlig resursgrupp.  
  4. För **plats**, Välj en region. Mer information finns i [vilka regioner som Azure Automation finns i](https://azure.microsoft.com/regions/services/). Lösningar som erbjuds i två nivåer: gratis och per nod (OMS) tjänstnivån. Den kostnadsfria nivån har en gräns på mängden data som har samlats in varje dag, Bevarandeperiod och runbook-jobbet runtime minuter. Den per nod (OMS) nivån inte har en gräns på mängden data som samlas in varje dag.  
  5. Välj **Automation-konto**.  Om du skapar en ny OMS-arbetsyta, måste du också skapa ett Automation-konto som är kopplad till den nya OMS-arbetsytan. Ta din Azure-prenumeration, resursgrupp och region. 
    1. Välj **skapa ett Automation-konto**.
    2. Under **Automatiseringskontot**i den **namn** , ange namnet på Automation-kontot.
    Alla andra alternativ fylls i automatiskt baserat på OMS-arbetsyta som valts. Du kan inte ändra dessa alternativ. 
    3. Ett Azure Kör som-konto är standardmetoden för autentisering för erbjudandet. När du har valt **OK**konfigurationsalternativen verifieras och Automation-kontot har skapats. Om du vill spåra förloppet på menyn Välj **meddelanden**. 
    4. Annars väljer du ett befintligt Automation Kör som-konto. Det konto som du väljer länkad inte redan till en annan OMS-arbetsyta. Om det är, visas ett meddelande. Om kontot är redan länkad till en OMS-arbetsyta, Välj ett annat Automation kör som-konto eller skapa en.
    5. När du anger eller väljer du informationen som krävs, Välj **skapa**. Informationen har verifierats och Automation-konto och kör som-konton skapas. Du automatiskt tillbaka till den **OMS-arbetsytan** fönstret.  
6. När du anger eller väljer du informationen som krävs på den **OMS-arbetsytan** väljer **skapa**.  Informationen har verifierats och att arbetsytan har skapats. Om du vill spåra förloppet på menyn Välj **meddelanden**. Du kommer tillbaka till den **Lägg till lösning** fönstret.  
7. Under **Automation- och kontrollservern** inställningar, bekräfta att du vill installera de förvalda rekommenderade lösningarna. Om du ändrar något av standardalternativen, kan du installera lösningarna individuellt senare.  
8. Om du vill fortsätta med onboarding Automation och en OMS-arbetsyta, Välj **skapa**. Alla inställningar verifieras och sedan Azure försöker distribuera erbjudandet i din prenumeration. Den här processen kan ta flera sekunder. Om du vill följa upp förloppet i menyn, Välj **meddelanden**. 

När erbjudandet har publicerats eller så kan göra du följande:
* Börja skapa runbooks.
* Arbeta med hanteringslösningar som du har aktiverat.
* Distribuera en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) roll.
* Börjar arbeta med [logganalys](https://docs.microsoft.com/azure/log-analytics) att samla in data som genereras av resurser i molnet eller lokala miljön.   

## <a name="next-steps"></a>Nästa steg
* Kontrollera att det nya Automation-kontot kan autentiseras mot Azure-resurser. Mer information finns i [Test Azure Automation kör som-konto autentisering](automation-verify-runas-authentication.md).
* Lär dig hur du kommer igång med att skapa runbooks och relaterade faktorer innan du börjar redigera. Mer information finns i [runbook automatiseringstyper](automation-runbook-types.md).


