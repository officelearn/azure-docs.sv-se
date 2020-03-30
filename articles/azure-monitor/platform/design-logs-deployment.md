---
title: Utforma distributionen av Azure Monitor Logs | Microsoft-dokument
description: I den här artikeln beskrivs överväganden och rekommendationer för kunder som förbereder distribution av en arbetsyta i Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: e493b07814821496f941a4b81402ba0b49acbede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248832"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Utforma distributionen av Dina Azure Monitor Logs

Azure Monitor lagrar [loggdata](data-platform-logs.md) i en Log Analytics-arbetsyta, som är en Azure-resurs och en behållare där data samlas in, aggregeras och fungerar som en administrativ gräns. Även om du kan distribuera en eller flera arbetsytor i din Azure-prenumeration, finns det flera saker du bör förstå för att säkerställa att din första distribution följer våra riktlinjer för att ge dig en kostnadseffektiv, hanterbar och skalbar distribution som uppfyller dina organisationers behov.

Data på en arbetsyta är ordnade i tabeller, som var och en lagrar olika typer av data och har sin egen unika uppsättning egenskaper baserat på den resurs som genererar data. De flesta datakällor skriver till sina egna tabeller på en Log Analytics-arbetsyta.

![Exempel på arbetsytedatamodell](./media/design-logs-deployment/logs-data-model-01.png)

En log analytics-arbetsyta innehåller:

* En geografisk plats för datalagring.
* Dataisolering genom att ge olika användare åtkomsträttigheter efter en av våra rekommenderade designstrategier.
* Utrymme för konfiguration av inställningar som [prisnivå,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier) [kvarhållning](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)och [datatak](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume).

Den här artikeln innehåller en detaljerad översikt över design- och migreringsöverväganden, åtkomstkontrollöversikt och en förståelse för de designimplementeringar som vi rekommenderar för din IT-organisation.



## <a name="important-considerations-for-an-access-control-strategy"></a>Viktiga överväganden för en strategi för tillträdeskontroll

Identifiera antalet arbetsytor du behöver påverkas av ett eller flera av följande krav:

* Du är ett globalt företag och du behöver loggdata som lagras i specifika regioner av datasuveränitet eller efterlevnadsskäl.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du hanterar flera avdelningar eller affärsgrupper och du vill att var och en ska se sina egna data, men inte data från andra. Det finns heller inget affärskrav för en konsoliderad över avdelnings- eller affärsgruppsvy.

IT-organisationer idag modelleras efter antingen en centraliserad, decentraliserad eller en mellanhybrid av båda strukturerna. Därför har följande distributionsmodeller för arbetsytor ofta använts för att mappa till någon av dessa organisationsstrukturer:

* **Centraliserad:** Alla loggar lagras på en central arbetsyta och administreras av ett enda team, med Azure Monitor som ger differentierad åtkomst per team. I det här fallet är det enkelt att hantera, söka över resurser och korskorrelera loggar. Arbetsytan kan växa avsevärt beroende på mängden data som samlas in från flera resurser i prenumerationen, med ytterligare administrativa omkostnader för att upprätthålla åtkomstkontrollen för olika användare. Denna modell kallas "hubb och eker".
* **Decentraliserad:** Varje team har sin egen arbetsyta skapad i en resursgrupp som de äger och hanterar, och loggdata är segregerade per resurs. I det här fallet kan arbetsytan hållas säker och åtkomstkontroll är förenligt med resursåtkomst, men det är svårt att korskorrelera loggar. Användare som behöver en bred bild av många resurser kan inte analysera data på ett meningsfullt sätt.
* **Hybrid**: Efterlevnadskrav för säkerhetsgranskning komplicerar det här scenariot ytterligare eftersom många organisationer implementerar båda distributionsmodellerna parallellt. Detta resulterar ofta i en komplex, dyr och svårbehållen konfiguration med luckor i loggar täckning.

När du använder Log Analytics-agenterna för att samla in data måste du förstå följande för att planera distributionen av din agent:

* Om du vill samla in data från Windows-agenter kan du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](../../azure-monitor/platform/agent-windows.md), även när den rapporterar till en hanteringsgrupp för System Center Operations Manager. Windows-agenten kan rapportera upp till fyra arbetsytor.
* Linux-agenten stöder inte flera mål och kan bara rapportera till en enda arbetsyta.

Om du använder System Center Operations Manager 2012 R2 eller senare:

* Varje Hanteringsgrupp för Operations Manager kan [bara anslutas till en arbetsyta](../platform/om-agents.md). 
* Linux-datorer som rapporterar till en hanteringsgrupp måste konfigureras för att rapportera direkt till en Log Analytics-arbetsyta. Om dina Linux-datorer redan rapporterar direkt till en arbetsyta och du vill övervaka dem med Operations Manager följer du dessa steg för att [rapportera till en Hanteringsgrupp för Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Du kan installera Log Analytics Windows-agenten på Windows-datorn och låta den rapportera till både Operations Manager integrerat med en arbetsyta och en annan arbetsyta.

## <a name="access-control-overview"></a>Översikt över åtkomstkontroll

Med rollbaserad åtkomstkontroll (RBAC) kan du bara bevilja användare och grupper den mängd åtkomst de behöver för att arbeta med övervakningsdata på en arbetsyta. På så sätt kan du anpassa dig till it-organisationens operativsystem med hjälp av en enda arbetsyta för att lagra insamlade data som är aktiverade på alla dina resurser. Du beviljar till exempel åtkomst till ditt team som ansvarar för infrastrukturtjänster som finns på virtuella Azure-datorer (VMs), och som ett resultat har de endast åtkomst till loggar som genereras av de virtuella datorerna. Detta följer vår nya resurskontextloggmodell. Grunden för den här modellen är för varje loggpost som skickas ut av en Azure-resurs, den associeras automatiskt med den här resursen. Loggar vidarebefordras till en central arbetsyta som respekterar omfång och RBAC baserat på resurserna.

De data som en användare har åtkomst till bestäms av en kombination av faktorer som visas i följande tabell. Var och en beskrivs i avsnitten nedan.

| Faktor | Beskrivning |
|:---|:---|
| [Åtkomstläge](#access-mode) | Metod som användaren använder för att komma åt arbetsytan.  Definierar omfattningen av tillgängliga data och det åtkomstkontrollläge som används. |
| [Läget Åtkomstkontroll](#access-control-mode) | Ange på arbetsytan som definierar om behörigheter ska användas på arbetsytan eller resursnivå. |
| [Behörigheter](manage-access.md) | Behörigheter som tillämpas på enskilda användare eller grupper av användare för arbetsytan eller resursen. Definierar vilka data användaren ska ha åtkomst till. |
| [RBAC på tabellnivå](manage-access.md#table-level-rbac) | Valfria detaljerade behörigheter som gäller för alla användare oavsett åtkomstläge eller åtkomstkontrollläge. Definierar vilka datatyper en användare kan komma åt. |

## <a name="access-mode"></a>Åtkomstläge

*Åtkomstläget* refererar till hur en användare kommer åt en Log Analytics-arbetsyta och definierar omfattningen av de data som de kan komma åt. 

Användare har två alternativ för att komma åt data:

* **Arbetsyta-kontext:** Du kan visa alla loggar på arbetsytan som du har behörighet till. Frågor i det här läget begränsas till alla data i alla tabeller på arbetsytan. Det här är det åtkomstläge som används när loggar används med arbetsytan som scope, till exempel när du väljer **Loggar** från **Azure Monitor-menyn** i Azure-portalen.

    ![Log Analytics-kontext från arbetsytan](./media/design-logs-deployment/query-from-workspace.png)

* **Resurskontext:** När du öppnar arbetsytan för en viss resurs, resursgrupp eller prenumeration, till exempel när du väljer **Loggar** från en resursmeny i Azure-portalen, kan du visa loggar för endast resurser i alla tabeller som du har åtkomst till. Frågor i det här läget begränsas till endast data som är associerade med den resursen. Det här läget möjliggör också detaljerad RBAC.

    ![Log Analytics-kontext från resurs](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Loggar är endast tillgängliga för resurskontextfrågor om de är korrekt associerade med den relevanta resursen. För närvarande har följande resurser begränsningar:
    > - Datorer utanför Azure
    > - Service Fabric
    > - Application Insights
    >
    > Du kan testa om loggarna är korrekt associerade med deras resurs genom att köra en fråga och granska de poster du är intresserad av. Om rätt resurs-ID finns i egenskapen [_ResourceId](log-standard-properties.md#_resourceid) är data tillgängliga för resurscentrerade frågor.

Azure Monitor bestämmer automatiskt rätt läge beroende på vilken kontext du utför loggsökningen från. Scopet visas alltid i avsnittet längst upp till vänster i Log Analytics.

### <a name="comparing-access-modes"></a>Jämföra åtkomstlägen

I följande tabell sammanfattas åtkomstlägena:

| | Arbetsyta-sammanhang | Resurskontext |
|:---|:---|:---|
| Vem är varje modell avsedd för? | Central administration. Administratörer som behöver konfigurera datainsamling och användare som behöver åtkomst till en mängd olika resurser. Krävs också för närvarande för användare som behöver komma åt loggar för resurser utanför Azure. | Programteam. Administratörer av Azure-resurser som övervakas. |
| Vad behöver en användare för att visa loggar? | Behörigheter till arbetsytan. Visa **behörigheter för arbetsyta** i Hantera åtkomst med hjälp av [arbetsytebehörigheter](manage-access.md#manage-access-using-workspace-permissions). | Läs åtkomst till resursen. Visa **resursbehörigheter** i [Hantera åtkomst med Azure-behörigheter](manage-access.md#manage-access-using-azure-permissions). Behörigheter kan ärvas (till exempel från den resursgrupp som innehåller) eller tilldelas direkt till resursen. Behörighet till loggarna för resursen tilldelas automatiskt. |
| Vad är omfattningen av behörigheter? | Arbetsytan. Användare med åtkomst till arbetsytan kan fråga alla loggar på arbetsytan från tabeller som de har behörighet till. Se [Kontroll över tabellåtkomst](manage-access.md#table-level-rbac) | Azure-resurs. Användaren kan fråga loggar för specifika resurser, resursgrupper eller prenumerationer som de har åtkomst till från valfri arbetsyta men kan inte fråga loggar för andra resurser. |
| Hur kan användaråtkomstloggar? | <ul><li>Starta **loggar** från **Azure Monitor-menyn.**</li></ul> <ul><li>Starta **loggar** från **Log Analytics-arbetsytor**.</li></ul> <ul><li>Från Azure [Monitor-arbetsböcker](../visualizations.md#workbooks).</li></ul> | <ul><li>Starta **loggar** från menyn för Azure-resursen</li></ul> <ul><li>Starta **loggar** från **Azure Monitor-menyn.**</li></ul> <ul><li>Starta **loggar** från **Log Analytics-arbetsytor**.</li></ul> <ul><li>Från Azure [Monitor-arbetsböcker](../visualizations.md#workbooks).</li></ul> |

## <a name="access-control-mode"></a>Läget Åtkomstkontroll

*Åtkomstkontrollläget* är en inställning på varje arbetsyta som definierar hur behörigheter bestäms för arbetsytan.

* **Kräv arbetsytebehörigheter**: Det här kontrollläget tillåter inte detaljerad RBAC. För att en användare ska komma åt arbetsytan måste de ha behörighet till arbetsytan eller till specifika tabeller.

    Om en användare kommer åt arbetsytan enligt arbetsytekontextläget har han eller hon åtkomst till alla data i alla tabeller som de har beviljats åtkomst till. Om en användare kommer åt arbetsytan enligt resurskontextläget har han eller hon endast åtkomst till data för resursen i alla tabeller som de har beviljats åtkomst till.

    Det här är standardinställningen för alla arbetsytor som skapats före mars 2019.

* **Använd resurs- eller arbetsytabehörigheter**: Det här kontrollläget tillåter detaljerad RBAC. Användare kan beviljas åtkomst till endast data som är `read` associerade med resurser som de kan visa genom att tilldela Azure-behörighet. 

    När en användare öppnar arbetsytan i arbetsytekontextläge gäller arbetsytebehörigheter. När en användare kommer åt arbetsytan i resurskontextläge verifieras endast resursbehörigheter och arbetsytebehörigheter ignoreras. Aktivera RBAC för en användare genom att ta bort dem från arbetsytebehörigheter och låta deras resursbehörigheter identifieras.

    Det här är standardinställningen för alla arbetsytor som skapats efter mars 2019.

    > [!NOTE]
    > Om en användare bara har resursbehörigheter till arbetsytan kan han eller hon bara komma åt arbetsytan med hjälp av resurskontextläge förutsatt att åtkomstläget för arbetsytan är inställt på **Använd resurs- eller arbetsytabehörigheter**.

Mer information om hur du ändrar åtkomstkontrollläget i portalen, med PowerShell eller använder en Resource Manager-mall finns i [Konfigurera åtkomstkontrollläge](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Volymgräns för intag

Azure Monitor är en storskalig datatjänst som betjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Tröskelvärdet för standardtmatningshastighet är inställt på **6 GB/min** per arbetsyta. Detta är ett ungefärligt värde eftersom den faktiska storleken kan variera mellan datatyper beroende på logglängden och dess kompressionsförhållande. Den här gränsen gäller inte för data som skickas från agenter eller [datainsamlar-API](data-collector-api.md).

Om du skickar data med en högre hastighet till en enda arbetsyta tas vissa data bort och en händelse skickas till *tabellen Operation* på arbetsytan var sjätte timme medan tröskelvärdet fortsätter att överskridas. Om din intagsvolym fortsätter att överskrida hastighetsgränsen eller om du förväntar dig att nå den någon gång snart kan du begära en ökning av arbetsytan genom att öppna en supportbegäran.
 
Om du vill meddelas om en sådan händelse på arbetsytan skapar du en [loggaviseringsregel](alerts-log.md) med följande fråga med varningslogikbas på antalet resultatgaller än noll.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Rekommendationer

![Exempel på resurskontextdesign](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Det här scenariot täcker en enda arbetsytedesign i din IT-organisationers prenumeration som inte begränsas av datasuveränitet eller regelefterlevnad, eller behöver mappa till de regioner som dina resurser distribueras inom. Det ger dina organisationer säkerhet och IT-administratör team möjlighet att utnyttja den förbättrade integrationen med Azure åtkomsthantering och säkrare åtkomstkontroll.

Alla resurser, övervakningslösningar och insikter som Application Insights och Azure Monitor för virtuella datorer, stöd för infrastruktur och program som underhålls av de olika teamen är konfigurerade för att vidarebefordra sina insamlade loggdata till IT-organisationer centraliserad delad arbetsyta. Användare i varje grupp får åtkomst till loggar för resurser som de har fått åtkomst till.

När du har distribuerat din arbetsyta arkitektur, kan du genomdriva detta på Azure-resurser med [Azure Policy](../../governance/policy/overview.md). Det är ett sätt att definiera principer och säkerställa efterlevnad av dina Azure-resurser så att de skickar alla sina resursloggar till en viss arbetsyta. Med azure-virtuella datorer eller skalningsuppsättningar för virtuella datorer kan du till exempel använda befintliga principer som utvärderar efterlevnad och rapportresultat för arbetsytor, eller anpassa för att åtgärda om de inte är kompatibla.  

## <a name="workspace-consolidation-migration-strategy"></a>Migreringsstrategi för konsolidering av arbetsytor

För kunder som redan har distribuerat flera arbetsytor och är intresserade av att konsolidera till resurskontextåtkomstmodellen rekommenderar vi att du använder en inkrementell metod för att migrera till den rekommenderade åtkomstmodellen och du försöker inte uppnå detta snabbt eller aggressivt. Om du följer en stegvis metod för att planera, migrera, validera och dra sig tillbaka efter en rimlig tidslinje kommer du att undvika oplanerade incidenter eller oväntade effekter på molnåtgärderna. Om du inte har en datalagringsprincip av efterlevnads- eller affärsskäl måste du bedöma lämplig tid för att lagra data på arbetsytan som du migrerar från under processen. När du konfigurerar om resurser för att rapportera till den delade arbetsytan kan du fortfarande analysera data på den ursprungliga arbetsytan efter behov. När migreringen är klar ska du inte ta bort den om du styrs för att lagra data på den ursprungliga arbetsytan före slutet av kvarhållningsperioden.

När du planerar migreringen till den här modellen bör du tänka på följande:

* Förstå vilka branschregler och interna policyer för lagring av data som du måste följa.
* Se till att dina programteam kan arbeta inom den befintliga resurskontextfunktionen.
* Identifiera åtkomsten som beviljas till resurser för dina programteam och testa i en utvecklingsmiljö innan du implementerar i produktion.
* Konfigurera arbetsytan så att **använda resurs- eller arbetsytabehörigheter**.
* Ta bort programteams behörighet att läsa och fråga arbetsytan.
* Aktivera och konfigurera alla övervakningslösningar, insikter som Azure Monitor för behållare och/eller Azure Monitor för virtuella datorer, dina Automation-konton och hanteringslösningar som uppdateringshantering, start-/stopp-virtuella datorer osv., som har distribuerats på den ursprungliga arbetsytan.

## <a name="next-steps"></a>Nästa steg

Om du vill implementera de säkerhetsbehörigheter och kontroller som rekommenderas i den här guiden granskar du [hantera åtkomsten till loggar .](manage-access.md)
