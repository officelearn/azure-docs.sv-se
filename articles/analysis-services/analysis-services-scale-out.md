---
title: Azure Analysis Services skala | Microsoft Docs
description: Replikera Azure Analysis Services-servrar med skalbarhet. Klient frågor kan sedan distribueras mellan flera fråge repliker i en skalbar frågenod.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1b40238dfc579e42d0389ae14fdea4b5692ede06
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572629"
---
# <a name="azure-analysis-services-scale-out"></a>Utskalning för Azure Analysis Services

Med utskalning kan klient frågor distribueras mellan flera *fråge repliker* i en *frågenod*, vilket minskar svars tiderna vid hög arbets belastning för frågor. Du kan också separera bearbetningen från Query-poolen och se till att klient frågor inte påverkas negativt av bearbetnings åtgärder. Skalbarhet kan konfigureras i Azure Portal eller med hjälp av Analysis Services REST API.

Skalbarhet är tillgängligt för servrar på standard pris nivån. Varje fråga replik debiteras enligt samma taxa som din server. Alla fråge repliker skapas i samma region som servern. Antalet repliker som du kan konfigurera begränsas av den region som servern finns i. Läs mer i [tillgänglighet per region](analysis-services-overview.md#availability-by-region). Skalbarhet ökar inte mängden tillgängligt minne för servern. Om du vill öka minnet måste du uppgradera planen. 

## <a name="why-scale-out"></a>Varför ska du skala ut?

I en typisk Server distribution fungerar en server som både bearbetnings Server och frågegrupp. Om antalet klient frågor mot modeller på servern överskrider QPU (Query Processing units) för din servers plan eller modell bearbetning sker samtidigt som arbets belastningen hög, kan prestanda minska. 

Med utskalning kan du skapa en frågenod med upp till sju ytterligare frågor om replik resurser (åtta totala, inklusive din *primära* Server). Du kan skala antalet repliker i Frågeredigeraren för att uppfylla QPU-krav vid kritiska tider och du kan när som helst separera en bearbetnings Server från Query-poolen. 

Oavsett hur många repliker av frågan som du har i en frågenod, distribueras inte bearbetnings arbets belastningar mellan frågans repliker. Den primära servern fungerar som bearbetnings Server. Fråga repliker har endast frågor mot de modell databaser som synkroniseras mellan den primära servern och varje replik i lagringspoolen. 

Vid utskalning kan det ta upp till fem minuter innan nya repliker av frågan läggs till i frågesyntaxen. När alla nya repliker av repliker är igång är nya klient anslutningar belastningsutjämnade över resurser i frågesyntaxen. Befintliga klient anslutningar ändras inte från den resurs som de för närvarande är anslutna till. Vid skalning i avslutas alla befintliga klient anslutningar till en resurs för en frågegrupp som tas bort från lagringspoolen. Klienterna kan återansluta till en återstående resurs för frågearkivet.

## <a name="how-it-works"></a>Hur det fungerar

När du konfigurerar utskalning första gången synkroniseras modell databaser på den primära servern *automatiskt* med nya repliker i en ny frågenod. Automatisk synkronisering sker bara en gång. Under den automatiska synkroniseringen kopieras den primära serverns datafiler (krypterade i vila i blob-lagring) till en annan plats, som också krypteras i vila i Blob Storage. Repliker i Frågeredigeraren har sedan *dehydratiseras* med data från den andra uppsättningen filer. 

Även om en automatisk synkronisering bara utförs när du skalar ut en server för första gången, kan du också utföra en manuell synkronisering. Synkronisering garanterar att data på repliker i frågesyntaxen matchar den primära servern. När du bearbetar (uppdaterar) modeller på den primära servern måste en synkronisering utföras *när* bearbetnings åtgärderna har slutförts. Den här synkroniseringen kopierar uppdaterade data från den primära serverns filer i Blob Storage till den andra uppsättningen filer. Repliker i Frågeredigeraren torkas sedan med uppdaterade data från den andra uppsättningen filer i Blob Storage. 

När du utför en efterföljande skalnings åtgärd, till exempel ökar antalet repliker i frågesyntaxen från två till fem, kommer de nya replikerna att vara torkade med data från den andra uppsättningen filer i Blob Storage. Det finns ingen synkronisering. Om du sedan utförde synkroniseringen efter en utskalning, skulle de nya replikerna i lagringspoolen bli dehydratiserade två gånger – en redundant hydrering. När du utför en efterföljande skalnings åtgärd är det viktigt att tänka på:

* Utför en synkronisering *före den skalbara åtgärden* för att undvika redundanta hydrering av de tillagda replikerna. Samtidig synkronisering och skalnings åtgärder som körs samtidigt är inte tillåtna.

* När du automatiserar både bearbetnings- *och* skalnings åtgärder är det viktigt att först bearbeta data på den primära servern, sedan utföra en synkronisering och sedan utföra en skalnings åtgärd. Den här sekvensen säkerställer minimal påverkan på QPU och minnes resurser.

* Synkronisering tillåts även när det inte finns några repliker i frågesyntaxen. Om du skalar bort från noll till en eller flera repliker med nya data från en bearbetnings åtgärd på den primära servern, ska du först utföra synkroniseringen utan repliker i frågesyntaxen och sedan skala ut. Om du synkroniserar innan du skalar ut undviker du redundanta hydrering av de nyligen tillagda replikerna.

* När du tar bort en modell databas från den primära servern tas den inte automatiskt bort från repliker i frågesyntaxen. Du måste utföra en synkroniseringsåtgärd med hjälp av PowerShell [-kommandot Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) som tar bort filen/erna för databasen från replikens delade Blob Storage-plats och sedan tar bort modell databasen på replikerna i Frågeredigeraren. För att avgöra om en modell databas finns på repliker i frågesyntaxen men inte på den primära servern, kontrollerar du att inställningen för att **bearbeta servern från fråga pool** är **Ja**. Använd sedan SSMS för att ansluta till den primära servern med hjälp av `:rw`-kvalificeraren för att se om databasen finns. Anslut sedan till repliker i lagringspoolen genom att ansluta utan kvalificeraren `:rw` för att se om samma databas också finns. Om databasen finns på repliker i frågesyntaxen, men inte på den primära servern, kör du en synkronisering.   

* När du byter namn på en databas på den primära servern finns det ett ytterligare steg som krävs för att säkerställa att databasen är korrekt synkroniserad med alla repliker. När du har bytt namn utför du en synkronisering genom att använda kommandot [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) och ange parametern `-Database` med det gamla databas namnet. Den här synkroniseringen tar bort databasen och filerna med det gamla namnet från alla repliker. Utför sedan en annan synkronisering som anger den `-Database` parametern med det nya databas namnet. Den andra synkroniseringen kopierar den nyligen namngivna databasen till den andra uppsättningen filer och dehydratiserar alla repliker. De här Synkroniseringarna kan inte utföras med kommandot Synchronize Model i portalen.

### <a name="separate-processing-from-query-pool"></a>Separat bearbetning från frågenod

För högsta prestanda för både bearbetnings-och fråge åtgärder kan du välja att separera bearbetnings servern från frågesyntaxen. När de är separerade tilldelas bara nya klient anslutningar till att fråga repliker i frågesyntaxen. Om bearbetnings åtgärder bara tar upp en kort tid kan du välja att bara separera bearbetnings servern från frågesyntaxen under den tid det tar att utföra bearbetnings-och synkroniseringsåtgärden, och sedan inkludera den i frågesyntaxen igen. När du delar upp bearbetnings servern från en frågenod, eller om du vill att det ska gå tillbaka till frågesyntaxen, kan det ta upp till fem minuter innan åtgärden har slutförts.

## <a name="monitor-qpu-usage"></a>Övervaka QPU-användning

Du kan kontrol lera om det är nödvändigt genom att övervaka servern i Azure Portal med hjälp av mått. Om din QPU regelbundet maxas ut, innebär det att antalet frågor mot dina modeller överskrider gränsen för QPU för ditt abonnemang. Längd måttet för programpoolstillstånd ökar också när antalet frågor i kön för trådpoolen överskrider tillgänglig QPU. 

Ett annat mått att titta på är genomsnittlig QPU av ServerResourceType. Med det här måttet jämförs genomsnitts QPU för den primära servern med den i frågesyntaxen. 

![Fråga för att skala ut mått](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Konfigurera QPU av ServerResourceType
1. Klicka på **Lägg till mått**i ett mått linje diagram. 
2. I **resurs**väljer du din server, sedan i **mått namn område**, väljer **Analysis Services standard mått**, sedan i **mått**väljer du **QPU**och väljer sedan **Gmsn**i **agg regering**. 
3. Klicka på **Använd delning**. 
4. I **värden**väljer du **ServerResourceType**.  

Läs [Övervaka servermått](analysis-services-monitor.md) för mer information.

## <a name="configure-scale-out"></a>Konfigurera utskalning

### <a name="in-azure-portal"></a>I Azure Portal

1. I portalen klickar du på **skala ut**. Använd skjutreglaget för att välja antalet replik servrar. Antalet repliker som du väljer är utöver den befintliga servern.  

2. I **separera bearbetnings servern från den aktuella poolen**väljer du Ja för att utesluta bearbetnings servern från fråga servrar. Klient [anslutningar](#connections) som använder standard anslutnings strängen (utan `:rw`) omdirigeras till repliker i frågesyntaxen. 

   ![Skjutreglage för skala ut](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicka på **Spara** för att etablera dina nya replik servrar. 

När du konfigurerar skalbarhet för en server första gången synkroniseras modeller på den primära servern automatiskt med repliker i frågesyntaxen. Automatisk synkronisering sker bara en gång, när du först konfigurerar att skala ut till en eller flera repliker. Efterföljande ändringar av antalet repliker på samma server *utlöser inte en annan automatisk synkronisering*. Den automatiska synkroniseringen utförs inte igen även om du ställer in servern på noll repliker och sedan på nytt skalar till ett valfritt antal repliker. 

## <a name="synchronize"></a>Klockan 

Synkroniseringsåtgärden måste utföras manuellt eller med hjälp av REST API.

### <a name="in-azure-portal"></a>I Azure Portal

I **översikt** > modell > **Synkronisera modell**.

![Skjutreglage för skala ut](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API

Använd **synkroniseringsåtgärden** .

#### <a name="synchronize-a-model"></a>Synkronisera en modell   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Hämta synkroniseringsstatus  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Retur status koder:


|Kod  |Beskrivning  |
|---------|---------|
|-1     |  Ogiltig       |
|0     | Replikera        |
|1     |  Återuppväcks       |
|2     |   Slutfört       |
|3     |   Misslyckad      |
|4     |    Slutför     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Installera eller uppdatera den senaste Azure PowerShell-modulen innan du](/powershell/azure/install-az-ps)använder PowerShell. 

Om du vill köra synkronisering använder du [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Använd [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)för att ange antalet repliker i frågan. Ange den valfria `-ReadonlyReplicaCount` parametern.

Om du vill separera bearbetnings servern från frågesyntaxen använder du [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den valfria `-DefaultConnectionMode` parametern som ska användas `Readonly`.

Läs mer i [använda ett huvud namn för tjänsten med modulen AZ. AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Anslutningar

På serverns översikts sida finns det två server namn. Om du ännu inte har konfigurerat skalbarhet för en server fungerar båda Server namnen som de ska. När du har konfigurerat skalbarhet för en server måste du ange rätt server namn beroende på Anslutnings typ. 

För slut användar klient anslutningar som Power BI Desktop, Excel och anpassade appar använder du **Server namn**. 

För SSMS, Visual Studio och anslutnings strängar i PowerShell, Azure Function-appar och AMO använder du **hanterings serverns namn**. Namnet på hanterings servern innehåller en särskild `:rw` (Läs-och skriv)-kvalificerare. Alla bearbetnings åtgärder sker på (den primära) hanterings servern.

![Server namn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Skala upp, skala ned eller skala ut

Du kan ändra pris nivån på en server med flera repliker. Samma pris nivå gäller för alla repliker. En skalnings åtgärd förflyttar först alla repliker på en gång och hämtar alla repliker på den nya pris nivån.

## <a name="troubleshoot"></a>Felsöka

**Problem:** Fel vid hämtning av användare **det går inte att hitta serverns\<namnet på server > instansen i anslutnings läget ReadOnly.**

**Lösning:** När du väljer **separera bearbetnings servern från alternativet för att ställa** in en fråga, omdirigeras klient anslutningar med hjälp av standard anslutnings strängen (utan `:rw`) till frågenoder för poolen. Om repliker i lagringspoolen inte är online på grund av att synkroniseringen ännu inte har slutförts, kan omdirigerade klient anslutningar inte fungera. För att förhindra misslyckade anslutningar måste det finnas minst två servrar i frågesyntaxen när en synkronisering utförs. Varje server synkroniseras individuellt medan andra är online. Om du väljer att inte använda bearbetnings servern i lagringspoolen under bearbetningen kan du välja att ta bort den från poolen för bearbetning och sedan lägga till den i poolen igen när bearbetningen har slutförts, men före synkroniseringen. Använd minnes-och QPU statistik för att övervaka synkroniseringsstatus.



## <a name="related-information"></a>Relaterad information

[Övervaka Server mått](analysis-services-monitor.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md) 
