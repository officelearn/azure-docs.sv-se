---
title: Vanliga frågor och svar om Azure Monitor för virtuella datorer (GA) | Microsoft-dokument
description: Azure Monitor för virtuella datorer är en lösning i Azure som kombinerar hälso- och prestandaövervakning av operativsystemet Azure VM, samt automatiskt upptäcka programkomponenter och beroenden med andra resurser och mappar kommunikationen mellan Dem. Den här artikeln besvarar vanliga frågor om GA-versionen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283896"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor för virtuella datorer som är allmänt tillgängliga (GA) Vanliga frågor och svar
Vanliga frågor om allmän tillgänglighet täcker ändringar som gjordes under fjärde kvartalet 2019 och första kvartalet 2020 när vi förberedde oss för GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Uppdateringar för Azure Monitor för virtuella datorer
Vi släppte en ny version av Azure Monitor för virtuella datorer i januari 2020 inför vårt GA-meddelande. Kunder som aktiverar Azure Monitor för virtuella datorer får nu GA-versionen, men befintliga kunder som använder versionen av Azure Monitor för virtuella datorer från Q4 2019 och tidigare uppmanas att uppgradera. Den här vanliga frågor och svar ger vägledning för att utföra en uppgradering i stor skala om du har stora distributioner över flera arbetsytor.


Med den här uppgraderingen lagras Azure Monitor för virtuella datorers prestandadata i samma *InsightsMetrics-tabell* som [Azure Monitor för behållare](container-insights-overview.md), vilket gör det enklare för dig att fråga de två datauppsättningarna. Dessutom kan du lagra mer olika datauppsättningar som vi inte kunde lagra i tabellen som tidigare använts. 

Våra prestandavyer använder nu de data vi lagrar i tabellen *InsightsMetrics.*  Om du ännu inte har uppgraderat för att använda den senaste VMInsights-lösningen på arbetsytan visas inte längre informationen i diagrammen.  Du kan uppgradera från vår **Kom igång-sida** enligt beskrivningen nedan.


## <a name="what-is-changing"></a>Vad förändras?
Vi har släppt en ny lösning, med namnet VMInsights, som innehåller ytterligare funktioner för datainsamling tillsammans med en ny plats för lagring av dessa data i din Log Analytics-arbetsyta. 

Tidigare har vi aktiverat ServiceMap-lösningen på din arbetsyta och installationsprestandaräknare på din Log Analytics-arbetsyta för att skicka data till *tabellen Perf.* Den här nya lösningen skickar data till en tabell med namnet *InsightsMetrics* som också används av Azure Monitor för behållare. Med det här tabellschemat kan vi lagra ytterligare mått och tjänstdatauppsättningar som inte är kompatibla med *tabellformatet Perf.*

Vi har uppdaterat våra prestandadiagram för att använda de data vi lagrar i tabellen *InsightsMetrics.* Du kan uppgradera för att använda tabellen *InsightsMetrics* från vår **kom igång-sida** enligt beskrivningen nedan.


## <a name="how-do-i-upgrade"></a>Hur uppgraderar jag?
När en Log Analytics-arbetsyta uppgraderas till den senaste versionen av Azure Monitor till virtuella datorer uppgraderas beroendeagenten på var och en av de virtuella datorer som är anslutna till den arbetsytan. Varje virtuell dator som kräver uppgradering identifieras på fliken **Kom igång** i Azure Monitor för virtuella datorer i Azure-portalen. När du väljer att uppgradera en virtuell dator uppgraderas arbetsytan för den virtuella datorn tillsammans med andra virtuella datorer som är anslutna till arbetsytan. Du kan välja en enda virtuell dator eller flera virtuella datorer, resursgrupper eller prenumerationer. 

Använd följande kommando för att uppgradera en arbetsyta med PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Vad ska jag göra åt prestandaräknarna på min arbetsyta om jag installerar VMInsights-lösningen?

Den tidigare metoden för att aktivera Azure Monitor för virtuella datorer används prestandaräknare på arbetsytan. Den aktuella versionen lagrar dessa `InsightsMetrics`data i en tabell med namnet . Du kan välja att inaktivera dessa prestandaräknare i arbetsytan om du inte längre behöver använda dem. 

>[!NOTE]
>Om du har varningsregler som refererar till dessa räknare i `Perf` tabellen måste `InsightsMetrics` du uppdatera dem för att referera till nya data som lagras i tabellen. Se vår dokumentation till exempel loggfrågor som du kan använda som refererar till den här tabellen.
>

Om du bestämmer dig för att hålla prestandaräknarna aktiverade debiteras du `Perf` för de data somhttps://azure.microsoft.com/pricing/details/monitor/)förtärs och lagras i tabellen baserat på [Log Analytics-priser[( .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hur påverkar den här ändringen mina varningsregler?

Om du har skapat [loggaviseringar](../platform/alerts-unified-log.md) som frågar `Perf` efter de prestandaräknare för tabellinriktning som `InsightsMetrics` har aktiverats på arbetsytan bör du uppdatera dessa regler för att referera till tabellen i stället. Den här vägledningen gäller även `ServiceMapComputer_CL` `ServiceMapProcess_CL`för alla loggsökregler `VMComputer` med `VMProcess` hjälp av och eftersom dessa datauppsättningar flyttas till och tabeller.

Vi kommer att uppdatera den här faq och vår dokumentation för att inkludera exempel loggsökvarningsregler för de datauppsättningar vi samlar in.

## <a name="how-will-this-affect-my-bill"></a>Hur kommer detta att påverka min faktura?

Faktureringen baseras fortfarande på data som förtärs och behålls på din Log Analytics-arbetsyta.

Prestandadata på maskinnivå som vi samlar in är desamma, har `Perf` en liknande storlek som de data vi lagrade i tabellen och kommer att kosta ungefär lika mycket.

## <a name="what-if-i-only-want-to-use-service-map"></a>Vad händer om jag bara vill använda Service Map?

Det är bra. Du kommer att se uppmaningar i Azure-portalen när du visar Azure Monitor för virtuella datorer om den kommande uppdateringen. När du har släppts får du en uppmaning om att uppdatera till den nya versionen. Om du föredrar att [Maps](vminsights-maps.md) bara använda maps-funktionen kan du välja att inte uppgradera och fortsätta att använda maps-funktionen i Azure Monitor för virtuella datorer och servicemappningslösningen som nås från arbetsytan eller instrumentpanelspanelen.

Om du väljer att manuellt aktivera prestandaräknarna på din arbetsyta kan du kanske se data i några av våra prestandadiagram som visas från Azure Monitor. När den nya lösningen släpps uppdaterar vi våra prestandadiagram `InsightsMetrics` för att fråga de data som lagras i tabellen. Om du vill se data från den tabellen i dessa diagram måste du uppgradera till den nya versionen av Azure Monitor för virtuella datorer.

Ändringarna för att `ServiceMapComputer_CL` flytta `ServiceMapProcess_CL` data från och påverkar både Service Map och Azure Monitor för virtuella datorer, så du måste fortfarande planera för den här uppdateringen.

Om du väljer att inte uppgradera till **VMInsights-lösningen** fortsätter vi att tillhandahålla äldre versioner `Perf` av våra prestandaarbetsböcker som refererar till data i tabellen.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Kommer datauppsättningarna för servicemappning också att lagras i InsightsMetrics?

Datauppsättningarna dupliceras inte om du använder båda lösningarna. Båda erbjudandena delar de datauppsättningar `VMComputer` som kommer att lagras `VMProcess` i (tidigare ServiceMapComputer_CL), `VMConnection`(tidigare ServiceMapProcess_CL) och `VMBoundPort` tabeller för att lagra kartdatauppsättningarna som vi samlar in.  

Tabellen `InsightsMetrics` lagrar VM-, process- och tjänstdatauppsättningar som vi samlar in och kommer endast att fyllas i om du använder Azure Monitor för virtuella datorer och VM Insights-lösningen. Service map-lösningen samlar inte in eller `InsightsMetrics` lagrar data i tabellen.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Kommer jag att debiteras dubbelt om jag har servicekartan och VMInsights-lösningarna på min arbetsyta?

Nej, de två lösningarna delar kartdatauppsättningarna som vi `VMComputer` lagrar `VMProcess` i (tidigare ServiceMapComputer_CL), `VMConnection`(tidigare ServiceMapProcess_CL) och `VMBoundPort`. Du debiteras inte dubbelt för dessa data om du har båda lösningarna på arbetsytan.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Om jag tar bort antingen Service Map eller VMInsights-lösningen kommer det att ta bort mina data?

Nej, de två lösningarna delar kartdatauppsättningarna som vi `VMComputer` lagrar `VMProcess` i (tidigare ServiceMapComputer_CL), `VMConnection`(tidigare ServiceMapProcess_CL) och `VMBoundPort`. Om du tar bort en av lösningarna märker dessa datauppsättningar att det fortfarande finns en lösning på plats som använder data och att den finns kvar på log analytics-arbetsytan. Du måste ta bort båda lösningarna från arbetsytan för att data ska kunna tas bort från den.

## <a name="health-feature-is-in-limited-public-preview"></a>Hälsofunktionen är i begränsad offentlig förhandsversion

Vi har fått mycket bra feedback från kunder om vår VM Health-funktionsuppsättning. Det finns ett stort intresse kring den här funktionen och spänningen över dess potential för att stödja övervakning arbetsflöden. Vi planerar att göra en rad ändringar för att lägga till funktioner och ta itu med den feedback vi har fått. 

För att minimera effekten av dessa ändringar för nya kunder har vi flyttat den här funktionen till en **begränsad offentlig förhandsversion.** Den här uppdateringen inträffade i oktober 2019.

Vi planerar att starta om den här hälsofunktionen 2020, efter att Azure Monitor för virtuella datorer finns i GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hur kommer befintliga kunder åt hälsofunktionen?

Befintliga kunder som använder hälsofunktionen kommer att fortsätta att ha åtkomst till den, men den kommer inte att erbjudas till nya kunder.  

Om du vill komma åt funktionen `feature.vmhealth=true` kan du lägga [https://portal.azure.com](https://portal.azure.com)till följande funktionsflagga i Azure Portal-URL:en . Exempel `https://portal.azure.com/?feature.vmhealth=true`.

Du kan också använda den här korta webbadressen, som ställer in funktionsflaggan automatiskt: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Som befintlig kund kan du fortsätta att använda hälsofunktionen på virtuella datorer som är anslutna till en befintlig arbetsytekonfiguration med hälsofunktionen.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Jag använder VM Health nu med en miljö och vill distribuera den till en ny

Om du är en befintlig kund som använder hälsofunktionen och vill använda den för vminsights@microsoft.com en ny utrullning, vänligen kontakta oss på begäran om instruktioner.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer läser du [Distribuera Azure Monitor för virtuella datorer](vminsights-enable-overview.md).
