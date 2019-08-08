---
title: System övervakning för säkerhet i Azure Australien
description: Vägledning om hur du konfigurerar system övervakning i de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779313"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>System övervakning för säkerhet i Azure Australien

Att ha robusta säkerhets strategier som omfattar övervakning i real tid och rutinmässiga säkerhets utvärderingar är avgörande för att du ska förbättra den dagliga drift säkerheten för dina IT-miljöer, inklusive molnet.

Cloud Security är en gemensam ansträngning mellan kunden och moln leverantören. Det finns fyra tjänster som Microsoft Azure tillhandahåller för att under lätta dessa krav med hänsyn till rekommendationerna i den [australiska cyberhot säkerhets centrets (ACSC) information säkerhet manuella kontroller](https://acsc.gov.au/infosec/ism/index.htm) (ISM). Mer specifikt är implementeringen av centraliserad händelse loggning, granskning av händelse logg och utvärdering och hantering av säkerhets risker. De Microsoft Azure tjänsterna är:

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC rekommenderar att du använder dessa tjänster för **skyddade** data. Genom att använda dessa tjänster kan du proaktivt övervaka och analysera dina IT-miljöer och fatta välgrundade beslut om var du bäst vill allokera resurser för att förbättra säkerheten. Var och en av dessa tjänster ingår i en kombinerad lösning för att ge dig bästa möjliga insikt, rekommendationer och skydd.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) tillhandahåller en enhetlig säkerhets hanterings konsol som du kan använda för att övervaka och förbättra säkerheten för Azure-resurser och dina värdbaserade data. Azure Security Center innehåller säkra poäng, ett resultat baserat på en analys av status för bästa praxis-konfigurationen från Azure Advisor och den övergripande kompatibiliteten för Azure Policy.

Azure Security Center ger Azure-kunder följande funktioner:

* Säkerhetsprincip, utvärdering och rekommendationer
* Insamling av och sökning i säkerhetshändelser
* Åtkomst-och program kontroller
* Avancerad hot identifiering
* Just-in-Time-Virtual Machines åtkomst kontroll
* Hybrid säkerhet

Resurs omfånget som övervakas av Azure Security Center kan expanderas för att inkludera stödda lokala resurser i en hybrid moln miljö. Detta inkluderar lokala resurser som övervakas för närvarande av en version av System Center Operations Manager som stöds.

På Security Center "standard"-nivån finns också molnbaserade säkerhets kontroller som krävs av [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm). Detta omfattar program vit listning och begränsning av administrativ behörighet via just-in-Time-åtkomst.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) är lösningen för centraliserad loggning för alla Azure-resurser och innehåller Log Analytics och Application Insights. Två viktiga data typer samlas in från Azure-resurser: loggar och mått. När du har samlat in i Azure Monitor kan loggnings information användas av en mängd olika verktyg och för olika ändamål.

![Azure Monitor-översikt](media/overview.png)

Azure Monitor innehåller även "Azure-aktivitets loggen". SActivity-loggen lagrar alla händelser på prenumerations nivå som har inträffat i Azure. Det gör att Azure-kunder kan se "vem, vad och när" bakom åtgärder som vidtas på sina Azure-resurser. Både resursbaserade loggningar som skickas till Azure Monitor och Azure aktivitets logg händelser kan analyseras med hjälp av det inbyggda Kusto-frågespråket. Dessa loggar kan sedan exporteras, användas för att skapa anpassade instrument paneler och vyer och konfigureras för att utlösa aviseringar och meddelanden.

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) analyser som stöds av Azure-resurser, systemgenererade loggfiler och aktuella resurspooler i din Azure-prenumeration. Den analys som anges i Azure Advisor genereras i real tid och baseras på Microsofts rekommenderade metod tips. Alla Azure-resurser som stöds och som läggs till i din miljö kommer att analyseras och lämpliga rekommendationer kommer att tillhandahållas. Azure Advisor rekommendationer kategoriseras i fyra kategorier för bästa praxis:

* Säkerhet
* Hög tillgänglighet
* Prestanda
* Kostnad

Säkerhets rekommendationer som skapats av Azure Advisor utgör en del av den övergripande säkerhets analys som tillhandahålls av Azure Security Center.

Informationen som samlas in av Azure Advisor ger administratörerna följande:

* Insikter om resurs konfiguration som inte uppfyller rekommenderad metod
* Vägledning om vilka åtgärder som vidtas för att utföra åtgärder
* Rangordning som anger vilka reparations åtgärder som ska vidtas som hög prioritet

### <a name="azure-policy"></a>Azure Policy

[Azure policy](https://docs.microsoft.com/azure/governance/policy/overview) ger möjlighet att tillämpa regler som styr vilka typer av Azure-resurser och deras tillåtna konfiguration. Principen kan användas för att styra skapandet och konfigurationen av resurser, eller så kan den användas för att granska konfigurations inställningar i en miljö. Dessa gransknings resultat kan användas för att forma grunden för åtgärds aktiviteter. Azure Policy skiljer sig från rollbaserad åtkomst kontroll (RBAC); Azure Policy används för att begränsa resurser och deras konfiguration, används RBAC för att begränsa privilegie rad åtkomst till Azure-användare.

Oavsett om den här principen tillämpas eller om principen granskas, övervakas efterlevnaden kontinuerligt, och övergripande och datorspecifik kompatibilitetsinformation ges till administratörerna. Azure Policy information om kompatibilitet ges till Azure Security Center och utgör en del av de säkra poängen.

## <a name="key-design-considerations"></a>Viktiga design överväganden

När du implementerar en händelse logg strategi fokuserar ACSC-ISM på följande saker:

* Centraliserade loggnings funktioner
* Vissa händelser som ska loggas
* Händelse logg skydd
* Kvarhållning av händelse logg
* Granskning av händelse logg

I tillägg till insamling och hantering av loggar rekommenderar ISM även rutin sårbarhets bedömning av en organisations IT-miljö.

### <a name="centralised-logging"></a>Centraliserad loggning

Alla loggnings lösningar bör, där det är möjligt, konsolidera loggar som registrerats i ett enda data lager. Detta minskar inte bara drifts komplexiteten och förhindrar skapandet av flera datasilor, vilket gör att data som samlas in från flera källor kan analyseras tillsammans så att alla korrelerade händelser kan identifieras. Detta är viktigt för att upptäcka och hantera omfattningen av eventuella cyberhot säkerhets incidenter.

Detta krav uppfylls för alla Azure-kunder med Azure Monitor. Detta erbjudande ger inte bara en centraliserad loggnings lagrings plats i Azure för alla Azure-resurser, men du kan också strömma dina data till en Azure Event Hub. Azure Event Hubs tillhandahåller en fullständigt hanterad tjänst för data inmatning i real tid. När Azure Monitor data strömmas till en Azure Event Hub, kan data också enkelt anslutas till befintliga SIEM-databaser (Security information and Event Management) och ytterligare övervaknings verktyg från tredje part.

Microsoft erbjuder också sin egen Azure Native SIEM-lösning, Azure Sentinel. Azure Sentinel stöder en mängd olika data anslutningar och kan användas för att övervaka säkerhets händelser i hela företaget. Genom att kombinera data från [data kopplingar](https://docs.microsoft.com/azure/sentinel/connect-data-sources)som stöds, kan du använda Azure Sentinels inbyggda Machine Learning och Kusto-frågespråket och säkerhets administratörer har en enda lösning för varnings identifiering, Hot synlighet, proaktiv jakt, och hot svar. Sentinel tillhandahåller också en jakt-och Notebook-funktion som gör det möjligt för säkerhets administratörer att registrera alla steg som ingår i en säkerhets undersökning i en åter användnings bar Spelbok som kan delas i en organisation. Säkerhets administratörer kan till och med använda den inbyggda [användar analysen](https://docs.microsoft.com/azure/sentinel/user-analytics) för att undersöka åtgärder för en enda nominerad användare.

### <a name="logged-events-and-log-detail"></a>Loggade händelser och logg information

ISM innehåller en detaljerad lista över händelse logg typer som ska ingå i alla loggnings strategier. Alla fångade loggar måste innehålla tillräckligt med information för att kunna användas för att genomföra analyser och utredningar.

Loggarna som samlas in i Azure faller under någon av följande tre kategorier:

* **Kontroll-och hanterings loggar**: Dessa loggar innehåller information om Azure Resource Manager Skapa, uppdatera och ta bort åtgärder.

* **Data Plans loggar**: De innehåller händelser som skapats som en del av Azure-resursanvändningen. Innehåller källor som Windows-händelseloggar, inklusive system-, säkerhets-och program loggar.

* **Bearbetade händelser**: Dessa händelser innehåller information om händelser och aviseringar som har bearbetats automatiskt för kundens räkning av Azure. Ett exempel på en bearbetad händelse är en Azure Security Center avisering.

Övervakningen av virtuella Azure-datorer förbättras genom distributionen av den virtuella dator agenten för både Windows och Linux. Detta gör att den här markeringen ökar bredden på den insamlade loggnings informationen. Distributionen av den här agenten kan konfigureras att ske automatiskt via Azure Security Center.

Microsoft tillhandahåller detaljerad information om Azures resurs-särskilda loggar och deras [scheman](https://docs.microsoft.com/azure/security/fundamentals/log-audit).

### <a name="log-retention-and-protection"></a>Logg kvarhållning och skydd

 Händelse loggar måste lagras på ett säkert sätt för den begärda kvarhållningsperioden. ISM rekommenderar att loggarna bevaras i minst sju år. Azure erbjuder ett antal olika sätt för att säkerställa lång livs längd för dina insamlade loggar. Som standard lagras Azure logg händelser i 90 dagar. Loggdata som registreras av Azure Monitor kan flyttas och lagras på ett Azure Storage konto som krävs för långsiktig kvarhållning. Aktivitets loggar som lagras på ett Azure Storage konto kan behållas för ett angivet antal dagar, eller om det behövs.

Azure Storage konton som används för att lagra Azure logg händelser kan göras geo-redundanta och kan säkerhets kopie ras med Azure Backup. När de har samlats in av Azure Backup, kräver all borttagning av säkerhets kopior som innehåller loggar administratörs godkännande och säkerhets kopior som har marker ATS för borttagning fortfarande i 14 dagar med möjlighet att återställa. Azure Backup tillåter 9999 kopior av en skyddad instans, vilket ger över 27 års dagliga säkerhets kopieringar.

Rollbaserad Access Control ska användas för att kontrol lera åtkomsten till resurser som används för Azure-loggning. Azure Monitor, Azure Storage-konton och Azure-säkerhetskopieringar ska konfigureras med rollbaserade åtkomst kontroller för att säkerställa säkerheten för de data som finns i loggarna.

### <a name="log-auditing"></a>Logg granskning

Det sanna värdet för loggarna realiseras när de analyseras. Genom att använda både automatiserad och manuell analys och bekanta dig med tillgängliga verktyg kan du identifiera och hantera överträdelser av organisationens säkerhets policy och cyberhot säkerhets incidenter. Azure Monitor innehåller en omfattande uppsättning verktyg för att analysera insamlade loggar. Resultatet av den här analysen kan sedan delas mellan system, visualised eller spridas i flera format.

Loggdata som lagras i Azure Monitor sparas i en Log Analytics arbets yta. All analys börjar med en fråga. Azure Monitor frågor skrivs i Kusto-frågespråket. Frågor utgör grunden för alla utdata från Azure Monitor, från Azure-instrumentpaneler till varnings regler.

![Översikt över Azure logg frågor](media/queries-overview.png)

Granskning av loggar kan förbättras genom användning av övervaknings lösningar. Dessa är förpaketerade lösningar som innehåller vyer för samlings logik, frågor och data visualisering. Microsoft [tillhandahåller](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) ett antal övervaknings lösningar och ytterligare lösningar från produkt leverantörer finns på Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Sårbarhets bedömning och hantering

ISM-anteckningar om att rutinmässig sårbarhets bedömning och hantering är viktigt. Din IT-miljö utvecklas ständigt och det externa säkerhetshot förändras i oändlighet. Med Azure Security Center kan du utföra automatiserade sårbarhets bedömningar och få vägledning om hur du planerar och utför reparations aktiviteter.

Säkra poäng i Azure Security Center ger dig en lista över rekommendationer som, när de tillämpas, förbättrar säkerheten för din miljö. Listan sorteras efter effekten på det övergripande säkra resultatet från högsta till lägsta. Genom att sortera listan efter påverkan kan du fokusera på de mest prioriterade rekommendationerna som visar det bästa värdet för att förbättra säkerheten.

Azure Policy spelar också en viktig del i den pågående sårbarhets utvärderingen. De typer av principer som är tillgängliga i Azure Policy intervall från att tvinga fram resurs etiketter och-värden, för att begränsa de Azure-regioner där resurser kan skapas, för att blockera skapandet av specifika resurs typer helt. En uppsättning Azure-principer kan grupperas i initiativ. Initiativ används för att tillämpa relaterade Azure-principer som, när de tillämpas tillsammans som en grupp, utgör grunden för ett bestämt mål för säkerhet eller efterlevnad.

Azure Policy har ett bibliotek med inbyggda princip definitioner som ständigt växer. Azure Portal ger dig också möjlighet att redigera dina egna anpassade Azure Policys definitioner. När du har hittat en princip i det befintliga biblioteket eller skapat en ny kan du tilldela principen till Azure-resurser. De här tilldelningarna [](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) kan begränsas på olika nivåer i resurs hanterings hierarkin. Princip tilldelningen ärvs, vilket innebär att alla underordnade resurser inom ett område får samma princip tilldelning. Resurser kan också uteslutas från princip tilldelningen vid behov.

Alla distribuerade Azure-principer bidrar till en organisations säkra poäng. I en miljö med hög ekrar kan anpassade Azure Policys definitioner skapas och distribueras för att tillhandahålla gransknings information som är anpassad till vissa arbets belastningar.

## <a name="getting-started"></a>Komma igång

För att börja med Azure Security Center och utnyttja Azure Monitor, Advisor och policy, rekommenderar Microsoft följande inledande steg:

* Aktivera Azure Security Center
* Uppgradera till standard nivån
* Aktivera automatisk etablering av Microsoft Monitoring Agent till Azure Virtual Machines som stöds
* Granska, prioritise och minimera säkerhets rekommendationerna och aviseringarna på Security Center-instrumentpanelen

## <a name="next-steps"></a>Nästa steg

Läs [Azure policy och Azure-ritningar](azure-policy.md) för mer information om hur du implementerar styrning och kontroll över dina Azure-resurser i Australien för att säkerställa efterlevnad av principer.
