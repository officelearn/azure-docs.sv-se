---
title: Skydd av personuppgifter-dokument med Azure reporting verktyg | Microsoft Docs
description: "Använd Azure reporting services för att skydda sekretessen för personliga data i initiativ för att vara kompatibel med allmänna Data Protection förordning (BNPR)."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: 2c677a1dea001c6a414304fbaa683c3787b355b1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Skydd av personuppgifter-dokument med Azure reporting verktyg

Den här artikeln innehåller information om hur du använder Azure reporting services och tekniker för att skydda sekretessen för personliga data. Den här informationen kan användas för att uppfylla kraven i branschen eller statlig förordningar, till exempel allmänna Data Protection förordning (BNPR).
## <a name="scenario"></a>Scenario

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavet, Adriatiska havet, baltiska havet samt Förenta staterna. Om du vill att dessa åtgärder kan genererade den flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien

Företaget använder Microsoft Azure för bearbetning och lagring av företagets data. Detta inkluderar personligt identifierbar information, till exempel namn, adresser, telefonnummer och kreditkortsinformation av dess globala kundbas. Den innehåller också traditionella personal information, till exempel adresser, telefonnummer, skatt ID-nummer och annan information om företagets anställda på alla platser. Raden kryssning har också en stor databas med medlemmar av trafik och förmåner som innehåller personuppgifter för att spåra relationer med aktuella och tidigare kunder.

Företagets anställda åtkomst till nätverket från företagets fjärranslutna kontor och resa agenter finns runtom i världen har åtkomst till vissa företagsresurser.

## <a name="problem-statement"></a>Problembeskrivning

Företaget måste skydda kundernas och anställda och personliga data via en flera lager säkerhetsstrategi som använder Azure hanterings- och funktioner för att införa stränga kontroller vid åtkomst till och bearbetning av personliga data och måste kunna visa dess skyddsåtgärder för interna och externa granskare.

## <a name="company-goal"></a>Företagets mål

Det är ett mål för företaget att spåra alla åtkomst till och bearbetning av personliga data och se till att dokumentationen för tillräcklig integritetsskydd för personliga data finns på plats och fungerar som del av dess skydd på djupet säkerhetsstrategi.

## <a name="solutions"></a>Lösningar

Microsoft Azure tillhandahåller omfattande övervakning, loggning och diagnostik verktyg för att spåra och registrera aktiviteter och händelser som är associerade med åtkomst till och bearbeta personliga data, geografiska flödet av data och tredjeparts-åtkomst till personliga data. Eftersom säkerheten för personliga data i molnet är ett delat ansvar, tillhandahåller Microsoft kunder med också:

- Detaljerad information om dess egna bearbetning av kunders data

- Säkerhetsåtgärder hanteras av Microsoft

- Var och hur den skickar kunders data

- Information om Microsofts egna sekretess går igenom processen

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade, flera innehavare katalog och identity management-tjänsten. Tjänstens inloggning och granska rapporteringsfunktioner får du detaljerad inloggnings- och programinformation användning aktivitet som hjälper dig att övervaka och kontrollera åtkomst till kundernas och anställdas personliga data.

Det finns två typer av aktivitetsrapporter:

- Den [granskningsloggar aktivitetrapporter/](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) ger detaljerad information om aktiviteter eller uppgifter i systemet

- Den [inloggningar rapporten/aktivitetsloggen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) visar som har utfört varje aktivitet som anges i rapporten

Du kan spåra historiken för varje aktivitet och vem som utförde varje användning av två tillsammans. Båda typerna av rapporter kan anpassas och kan filtreras.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Hur kommer jag åt loggarna granskning och säkerhet?

Granskning och säkerhet loggarna kan nås från Active Directory-portalen på tre olika sätt: genom den **aktiviteten** avsnitt (väljer du antingen **granskningsloggar** eller **inloggningar**), eller från **användare och grupper** eller **företagsprogram** under **hantera** i Active Directory. Rapporter kan även nås via Azure Active Directory reporting API. 

1. Välj i Azure-portalen **Azure Active Directory.**

2. I den **aktiviteten** väljer **granskningsloggar.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Anpassa listan genom att klicka på **kolumner** i verktygsfältet.

4.  Markera ett objekt i listan att se alla tillgängliga information om den.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Azure Active Directory reporting även innehåller två typer av säkerhetsrapporter, **användare som har flaggats för risk** och **riskfyllda inloggningar**, som kan hjälpa dig att övervaka potentiella risker i Azure-miljön.

Mer information om tjänsten reporting finns [Azure Active Directory reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Besök [Azure Active Directory aktivitetsrapporter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) för mer information om rapporterna som är tillgängliga i Azure Active Directory. Den här platsen innehåller mer information om hur du använder [granskningsloggar aktivitetsrapporter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) och [inloggningsaktivitet rapporter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) i portalen. Det innehåller även information om [användare som har flaggats för risk](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) och [riskfyllda inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins) säkerhetsrapporter.

Besök den [Azure Active Directory audit API-referens](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) webbplats för mer information om hur du ansluter till Azure Directory reporting programmässigt.

### <a name="log-analytics"></a>Log Analytics

[Logga Analytics](https://azure.microsoft.com/services/log-analytics/) kan [samla in data från Azure-Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) att korrelera med andra data och tillhandahålla ytterligare analys. Azure övervakaren samlar in och analyserar övervakningsdata för Azure-miljön. 

Analysverktyg i logganalys som loggen sökningar, vyer och lösningar arbeta mot alla insamlade data, vilket ger dig centraliserad analys av hela miljön. Logganalys kan sammanställa och analysera händelseloggarna för Windows, IIS-loggar och systemloggar som kan hjälpa dig att upptäcka potentiella överträdelser av personliga data som kan uttsätta personuppgifter till obehöriga användare.

#### <a name="how-do-i-use-log-analytics"></a>Hur använder Log Analytics?

Du kan använda logganalys via OMS-portalen eller Azure-portalen från en webbläsare. Log Analytics tillhandahåller en frågesyntax för att snabbt hämta och konsolidera data i databasen. Du kan skapa och spara loggen sökningar för att analysera data i portalen direkt.

Om du vill skapa en logganalys-arbetsytan i Azure-portalen, gör du följande:

1. Välj **logganalys** från listan över tjänster på Marketplace.

2. Välj **skapa,** sedan ange namnet på din OMS-arbetsyta din prenumeration, resursgrupp, plats och välj prisnivån.

3. Klicka på **OK** att visa en lista över dina arbetsytor.

4. Välj en arbetsyta om du vill visa information.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Besök den [logganalys dokumentationen](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) lära dig mer om tjänsten.

Besök den [komma igång med en logganalys-arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) kursen hjälper dig att skapa en arbetsyta för utvärdering och lära dig grunderna för hur du använder tjänsten.

Finns följande webbsidor mer detaljerad information om hur du ansluter för att använda logganalys med loggar som beskrivs ovan:

[Windows-händelseloggar datakällor i logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[IIS-loggar i logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Syslog-datakällor i logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure-Monitor/Azure-aktivitetsloggen 

[Azure-Monitor](https://azure.microsoft.com/services/monitor/) innehåller basnivån infrastruktur mått och loggar för de flesta tjänster i Microsoft Azure.
Övervakning hjälper dig att få djupa insikter om din Azure-program. Azure övervakaren är beroende av tillägget Azure diagnostics (Windows eller Linux) att samla in de flesta program nivån mått och loggar. [Azure-aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) är en av de resurser som du kan visa med Azure-Monitor. Den spårar varje API-anrop och innehåller en mängd information om aktiviteter som sker i [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Du kan söka aktivitetsloggen (tidigare kallade drift- eller granskningsloggar) för information om resurs som visas av Azure-infrastrukturen. 

Även om en stor del av informationen i aktivitetsloggen gäller prestanda och tjänstens hälsa, finns det även information som rör skydd av data. Använder aktivitetsloggen, kan du bestämma den ”vad som, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på resurserna i din Azure-prenumeration.

Till exempel innehåller en post när en administratör tar bort en nätverkssäkerhetsgrupp som kan påverka skyddet av personliga data. Aktiviteten loggposter som lagras i Azure-Monitor i 90 dagar.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Hur använder data som samlas in av Azure-Monitor?

Det finns ett antal sätt att använda data i aktivitetsloggen och andra Azure-Monitor-resurser.

- Du kan strömma data till andra platser i verkliga rad.

- Du kan lagra data under längre tid än standard, med hjälp av en [Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-introduction) och ange en bevarandeprincip.

- Kan visual data i grafik och diagram, med hjälp av den [Azure-portalen](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/), eller tredje parts visualiseringsverktyg.

- Du kan fråga efter data med hjälp av Azure övervakaren REST API, CLI-kommandona [PowerShell](https://docs.microsoft.com/powershell/) cmdlets eller .NET SDK.

Om du vill komma igång med Azure-Monitor, Välj **alla tjänster** i Azure-portalen.

1. Rulla ned till **övervakaren** i den **övervaka och hantera** avsnitt.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Övervakaren öppnas i den **aktivitetsloggen** vyn.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Du kan skapa och spara frågor för vanliga filter och fästa de viktigaste frågorna till en instrumentpanel i portalen så att du alltid vet om det har inträffat några händelser som uppfyller dina kriterier.

1. Du kan filtrera vyn av resursgrupp, timespan och händelsekategori.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. Du kan sedan fästa frågor till portalens instrumentpanel genom att klicka på den **PIN-kod** knappen. På så sätt kan du skapa en enda källa för användningsdata om dina tjänster. Frågans namn och antalet resultat visas på instrumentpanelen.

Du kan också använda övervakaren för att visa måtten för alla Azure-resurser, Konfigurera diagnostikinställningar för- och aviseringar och söka i loggen. Mer information om hur du använder Azure-Monitor och aktivitetsloggen finns [Kom igång med Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Azure Diagnostics 

Diagnostik-funktionen i Azure aktiverar insamlingen av data från flera källor. Windows händelseloggar, bland annat säkerhetsloggen, kan vara särskilt användbart i spårning och dokumentera skydd av personuppgifter. Säkerhetsloggen spårar misslyckade och lyckade inloggningshändelser, samt behörighetsändringar, identifiera mönster som anger vissa typer av attacker, ändringar av säkerhetsrelaterade principer, medlemskap i säkerhetsgrupper och mycket mer.

Till exempel aviserar händelse-ID 4695 försök unprotection av granskningsbara skyddade data. Detta gäller till Data Protection API (DPAPI), som hjälper dig för att skydda data, till exempel privata nycklar, lagrade autentiseringsuppgifter och annan konfidentiell information.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Hur aktiverar tillägget diagnostik för virtuella Windows-datorer?

Du kan använda PowerShell för att aktivera tillägget diagnostik för en Windows-VM för att samla in loggdata. Steg för att göra det beror på vilken distributionsmodell du använder (Resource Manager eller klassisk). Om du vill aktivera tillägget diagnostik på en befintlig virtuell dator som har skapats via Resource Manager-distributionsmodellen, kan du använda den [Set-AzureRMVMDiagnosticsExtension PowerShell-cmdleten](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* är sökvägen till filen som innehåller konfiguration för diagnostik i XML. Detaljerade instruktioner om hur du aktiverar Azure-diagnostik på en virtuell dator, se [Använd PowerShell för att aktivera Azure-diagnostik i en virtuell dator som kör Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

Azure diagnostics-tillägget kan överföra insamlade data till ett Azure storage-konto eller skicka det till tjänster som Application Insights. Du kan sedan använda data för granskning.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Hur jag för att lagra och visa diagnostikdata?

Det är viktigt att komma ihåg att diagnostikdata inte lagras permanent om du överför den till Microsoft Azure storage-emulatorn eller till Azure-lagring. Följ dessa steg för att lagra och visa diagnostiska data i Azure Storage:

1. Ange ett lagringskonto i ServiceConfiguration.cscfg-filen. Azure Diagnostics kan använda Blob-tjänsten eller tabelltjänsten, beroende på vilken typ av data. Windows-händelseloggar lagras i tabellformat.

2. Överföra data. Du kan begära för att överföra diagnostiska data via konfigurationsfilen. SDK 2.4 och tidigare, kan du också göra begäran programmässigt.

3. Visa data med hjälp av [Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), [Server Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) i Visual Studio eller [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) i Azure Management Studio.

Mer information om hur du utför var och en av de här stegen finns [Store och visa diagnostiska data i Azure Storage.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Azure Storage-analys 

Storage Analytics loggar detaljerad information om lyckade och misslyckade förfrågningar till en storage-tjänst. Den här informationen kan användas för att övervaka enskilda begäranden som kan bidra till att dokumentera åtkomst till personliga data som lagras i tjänsten. Storage Analytics loggning är inte aktiverad som standard för ditt lagringskonto. Du kan aktivera den i Azure-portalen.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Konfigurera övervakning för ett lagringskonto?

Konfigurera övervakning för ett lagringskonto genom att göra följande:

1. Välj **lagringskonton** i Azure portal, välj sedan namnet på det konto som du vill övervaka.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. I den **övervakning** väljer **diagnostik.**

3.  Välj den **typen** mått data du vill övervaka för varje tjänst (Blob, tabell-fil). Om du vill instruera Azure Storage att spara diagnostik loggar för Läs-, Skriv- och delete-begäranden för blob-, tabell- och queue-tjänster, Välj **Blob-loggar, tabellen loggar** och **kö loggar.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Med skjutreglaget längst ned i **kvarhållning** princip i dagar (värdet 1 – 365). Standardvärdet är sju dagar.

5. Välj **spara** tillämpa konfigurationsinställningarna.

Lagring loggning loggposter innehålla följande information om varje enskild begäran:

- Tidsinformation som starttid, svarstid för slutpunkt till slutpunkt och svarstid för servern.

- Ange information om Lagringsåtgärden som åtgärden, nyckeln för lagringsobjektet klienten har åtkomst till, lyckade eller misslyckade och HTTP-statuskoden returneras till klienten.

- Information om autentisering, till exempel en typ av autentisering används av klienten.

- Concurrency information, till exempel ETag-värde och tidsstämpel för senaste ändring.

- Storleken på begäran och svar-meddelanden.

Mer detaljerad information om hur du aktiverar loggning för Storage Analytics finns [övervaka ett lagringskonto i Azure-portalen.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Azure Security Center 

[Azure Security Center](https://azure.microsoft.com/services/security-center/) säkerhetsstatusen för resurserna i Azure för att förhindra att identifiera hot och ger rekommendationer för att svara. Det finns flera sätt att dokumentet din säkerhetsåtgärder som skyddar personliga data.

Övervakning av säkerhetshälsa hjälper dig att säkerställa att dina säkerhetsprinciper. Säkerhetsövervakning är en proaktiv strategi där resurserna för att identifiera datorer som inte uppfyller organisationens normer och bästa praxis. Du kan övervaka säkerhetsläget för följande resurser:

- Beräkningstimmar (virtuella datorer och molntjänster)

- Networking (virtuella nätverk)

- Lagrings- och (server och databas granskning och hotidentifiering identifiering, TDE, lagringskryptering)

- Program (potentiella säkerhetsproblem)

Säkerhetsproblem i någon av dessa kategorier kan utgöra ett hot mot säkerheten för de personliga data.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Hur visar säkerhetsläget för min Azure-resurser?

Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. Du kan visa alla eventuella säkerhetsproblem identifieras i den **förebyggande** på instrumentpanelen.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. I den **förebyggande** väljer den **Compute** panelen. Du ser här en **översikt,** tillsammans med den **virtuella datorer** lista över alla virtuella datorer och deras säkerhetsstatus och **molntjänster** lista över webb- och arbetsroller roller som övervakas via Security Center.

2. På den **översikt** fliken, andra en rekommendation om du vill visa mer information.

3. På den **virtuella datorer** väljer du en virtuell dator för att visa ytterligare information.

När datainsamling har aktiverats i Azure Security Center, Microsoft Monitoring Agent etableras automatiskt på alla befintliga och nya kompatibla virtuella datorer som distribueras. Data som samlas in från den här agenten lagras i antingen en befintlig [logganalys](https://azure.microsoft.com/services/log-analytics/) arbetsytan som är associerade med din prenumeration eller en ny arbetsyta.

[Hot Intelligence rapporter](https://docs.microsoft.com/azure/security-center/security-center-threat-report) tillhandahålls av Security Center. Dessa ger användbar information för att urskilja angriparens identitet, mål, aktuell och historisk attack kampanjer och medvetande, verktyg och procedurer som används. Information om minskning och reparation ingår också.

Huvudsyftet med rapporterna hot är att hjälpa dig att reagera effektivt omedelbart hot och vidta åtgärder för efteråt för att undvika problemet. Informationen i rapporterna kan också vara användbart när du dokumentera dina incidenter för rapporterings- och granskningsändamål.

Hot Intelligence rapporterna visas i. PDF-format, som nås via en länk i den **rapporter** i den **misstänkta processen körs** bladet för varje säkerhetsvarning i Azure Security Center.

Mer information om hur du visar och använder Threat Intelligence rapporten finns [Azure Security Center hot Intelligence-rapporten.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Nästa steg:

[Komma igång med Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Vad är Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Översikt över övervakning i Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Introduktion till Azure-aktivitetsloggen (video)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
