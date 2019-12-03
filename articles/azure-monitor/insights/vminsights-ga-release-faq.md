---
title: Vanliga frågor och svar om Azure Monitor for VMs (GA) | Microsoft Docs
description: Azure Monitor for VMs är en lösning i Azure som kombinerar hälso-och prestanda övervakning av operativ systemet på den virtuella Azure-datorn samt identifierar program komponenter och beroenden automatiskt med andra resurser och mappar kommunikationen mellan objekten. I den här artikeln besvaras vanliga frågor om GA-versionen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 88634777897341f4bd4d8c12b5f9d3b6d9982758
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671540"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Monitor for VMs allmänt tillgängliga (GA)

Vi presenterade nyligen i [Azure Update](https://azure.microsoft.com/blog/) -bloggen några planerade ändringar som kommer att ske i oktober och november 2019. Mer information om de här ändringarna beskrivs i vanliga frågor och svar om allmän tillgänglighet.

## <a name="updates-for-azure-monitor-for-vms"></a>Uppdateringar för Azure Monitor for VMs

Vi lanserar en ny version av Azure Monitor for VMs i november. Kunder som aktiverar Azure Monitors för virtuella datorer efter den här versionen får den nya versionen automatiskt, men befintliga kunder som redan använder Azure Monitor for VMs uppmanas att uppgradera.  Dessa vanliga frågor och svar och vår dokumentation ger vägledning för att utföra en Mass uppgradering om du har stora distributioner över flera arbets ytor.

Med den här uppgraderingen lagras Azure Monitor for VMs prestanda data uppsättningar i samma `InsightsMetrics`s tabell som [Azure Monitor för behållare](container-insights-overview.md)och gör det enklare för dig att fråga de två data uppsättningarna. Du kan också lagra Mer Diverse data uppsättningar som vi inte kunde lagra i tabellen som vi tidigare använde.  Och våra prestandavyer kommer att uppdateras så att den nya tabellen används.

Vi flyttar till nya data typer för våra anslutnings data uppsättningar. Data som finns lagrade i `ServiceMapComputer_CL` och `ServiceMapProcess_CL`, som använder anpassade logg tabeller, flyttas till dedikerade data typer som heter `VMComputer` och `VMProcess`.  Genom att flytta till dedikerade data typer kan vi ge den här prioriteten för data inmatning och tabell schemat är standardiserat i alla kunder.

Vi inser att uppgraderingen innebär avbrott i arbetsflödet för befintliga kunder. Därför har vi valt att göra detta nu under den offentliga förhandsgranskningen i stället för senare när GA-versionen blir tillgänglig.

## <a name="what-will-change"></a>Vad kommer att ändras?

När du har slutfört onboarding-processen för Azure Monitor for VMs aktiverar du Tjänstkarta-lösningen på den arbets yta som du har valt för att lagra dina övervaknings data och konfigurerar sedan prestanda räknare för de data som vi samlar in från dina virtuella datorer. I kommande veckor kommer vi att lansera en ny lösning, med namnet **VMInsights**, som innehåller ytterligare funktioner för data insamling tillsammans med en ny plats för att lagra dessa data i Log Analytics.

Den nuvarande processen med att använda prestanda räknare i din arbets yta skickar data till prestanda tabellen i Log Analytics.  Den här nya lösningen skickar data till en tabell med namnet `InsightsMetrics` som också används av Azure Monitor för behållare. Det här tabell schemat gör att vi kan lagra ytterligare mått och tjänst data uppsättningar som inte är kompatibla med tabell formatet perf.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Vad ska jag göra med prestanda räknarna på min arbets yta om jag installerar VMInsights-lösningen?

Den aktuella metoden för att aktivera Azure Monitor for VMs använder prestanda räknare i din arbets yta. Den nya metoden lagrar dessa data i en ny tabell med namnet `InsightsMetrics`.

När vi uppdaterar vårt användar gränssnitt för att använda data i InsightsMetrics kommer vi att uppdatera vår dokumentation och kommunicera det via flera kanaler, inklusive att visa en banderoll i Azure Portal. Vid det här skedet kan du välja att inaktivera de här [prestanda räknarna](vminsights-enable-overview.md#performance-counters-enabled) i arbets ytan om du inte längre vill använda dem. 

>[!NOTE]
>Om du har varnings regler som refererar till dessa räknare i tabellen prestanda måste du uppdatera dem för att referera till de nya data i tabellen `InsightsMetrics`.  Se vår dokumentation för exempel på logg frågor som du kan använda för att referera till den här tabellen.
>

Om du bestämmer dig för att hålla prestanda räknarna aktiverade debiteras du för de data som matas in och bevaras i tabellen prestanda baserat på [Log Analytics priser [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hur påverkar den här ändringen mina varnings regler?

Om du har skapat [logg aviseringar](../platform/alerts-unified-log.md) som frågar `Perf`-tabellens mål prestanda räknare som har Aktiver ATS på arbets ytan, bör du uppdatera reglerna så att de refererar till `InsightsMetrics` tabellen i stället. Den här vägledningen gäller även för alla logg Sök regler som använder `ServiceMapComputer_CL` och `ServiceMapProcess_CL`, eftersom dessa data uppsättningar flyttas till `VMComputer` och `VMProcess` tabeller.

Vi kommer att uppdatera dessa vanliga frågor och svar och vår dokumentation för att inkludera exempel på loggs öknings varningar för de data uppsättningar som vi samlar in.

## <a name="will-there-be-any-changes-to-billing"></a>Kommer det att finnas några ändringar i faktureringen?

Faktureringen baseras fortfarande på inmatade data och behålls i Log Analytics-arbetsytan.

Prestanda data för dator nivå som vi samlar in är samma, är av samma storlek som de data som vi lagrade i tabellen prestanda och kostar ungefär samma belopp.

## <a name="what-if-i-only-want-to-use-service-map"></a>Vad händer om jag bara vill använda Tjänstkarta?

Det är bra.  Du kommer att se prompter i Azure Portal när du visar Azure Monitor for VMs om den kommande uppdateringen. När du har lanserat meddelandet får du en fråga om du vill uppdatera till den nya versionen. Om du bara vill använda [Maps](vminsights-maps.md) -funktionen kan du välja att inte uppgradera och fortsätta använda Maps-funktionen Azure Monitor for VMS och den tjänstkarta-lösning som nås från din arbets yta eller instrument panels panel.

Om du väljer att aktivera prestanda räknarna manuellt i arbets ytan kan du se data i några av våra prestanda diagram som visas från Azure Monitor. När den nya lösningen släpps uppdaterar vi våra prestanda diagram för att fråga data som lagras i `InsightsMetrics` tabellen. Om du vill se data från tabellen i dessa diagram måste du uppgradera till den nya versionen av Azure Monitor for VMs.

Ändringarna för att flytta data från `ServiceMapComputer_CL` och `ServiceMapProcess_CL` påverkar både Tjänstkarta och Azure Monitor for VMs, så du måste ändå planera för den här uppdateringen.

Om du väljer att inte uppgradera till **VMInsights** -lösningen kommer vi fortsätta att tillhandahålla äldre versioner av arbets böckerna för prestanda som refererar till data i `Perf`s tabellen.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Kommer Tjänstkarta data uppsättningar också att lagras i InsightsMetrics?

Data uppsättningarna kommer inte att dupliceras om du använder båda lösningarna. Båda erbjudandena delar de data uppsättningar som ska lagras i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection`och `VMBoundPort` tabeller för att lagra de kart data uppsättningar som vi samlar in.  

`InsightsMetrics` tabellen används för att lagra data uppsättningar för virtuella datorer, processer och tjänster som vi samlar in och kommer bara att fyllas i om du använder Azure Monitor for VMs.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Får jag dubbla avgifter om jag har Tjänstkarta-och VMInsights-lösningar på min arbets yta?

Nej, de två lösningarna delar mappnings data uppsättningar som vi lagrar i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection`och `VMBoundPort`.  Du debiteras inte för dessa data om du har båda lösningarna på arbets ytan.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Om jag tar bort Tjänstkarta eller VMInsights-lösningen tas mina data bort i Log Analytics?

Nej, de två lösningarna delar mappnings data uppsättningar som vi lagrar i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection`och `VMBoundPort`.  Om du tar bort en av lösningarna märker dessa data uppsättningar att det fortfarande finns en lösning på plats som använder dessa data och som finns kvar i Log Analytics.  Du måste ta bort båda lösningarna från din arbets yta för att data ska kunna tas bort från din Log Analytics-arbetsyta.

## <a name="when-will-this-update-be-released"></a>När frigörs den här uppdateringen?

Vi förväntar dig att lansera uppdateringen för Azure Monitor for VMs i mitten av november. Som vi får närmare lanserings datumet kommer vi att publicera uppdateringar här och presentera aviseringar i Azure Portal när du navigerar till Azure Monitor.

## <a name="health-feature-to-enter-limited-public-preview"></a>Hälso funktion för att ange begränsad offentlig för hands version

Vi har fått mycket bra feedback från kunder om vår funktions uppsättning för VM-hälsa.  Intresset för den här funktionen är stort och många ser fram emot möjligheten att övervaka arbetsflöden. Vi planerar att göra ett antal ändringar för att utöka funktionerna och integrera den feedback vi fått. För att minimera effekten av dessa ändringar för nya kunder flyttar vi den här funktionen till en begränsad offentlig för hands version.

Den här över gången börjar i tidigt oktober och bör slutföras i slutet av månaden.

Några av de områden vi kommer att fokusera på:

- Mer kontroll över hälso modellen och tröskelvärdet
- Redigering vid skalning av hälso modeller som gäller för virtuella datorers omfång
- Intern användning av aviserings plattformen för hantering av hälsobaserade aviserings regler
- Möjlighet att se hälso tillståndet i en större omfattning, till exempel en eller flera prenumerationer
- Minskad latens i hälso tillstånds över gångar och aviserings meddelanden

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hur kommer befintliga kunder att få åtkomst till hälso funktionen?

Befintliga kunder som använder hälso funktionen fortsätter att ha åtkomst till den, men den erbjuds inte till nya kunder.  

För att få åtkomst till funktionen kan du lägga till följande funktions flagga `feature.vmhealth=true` till portal-URL: en [https://portal.azure.com](https://portal.azure.com). Exempel `https://portal.azure.com/?feature.vmhealth=true`.

Du kan också använda den här korta URL: en, som ställer in funktions flaggan automatiskt: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Som en befintlig kund kan du fortsätta att använda hälso funktionen på virtuella datorer som är anslutna till en befintlig installation av arbets ytan med hälso funktionen.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>Jag använder VM-hälsa nu med en miljö och vill distribuera den till en ny miljö

Om du är en befintlig kund som använder hälso funktionen och vill använda den för en ny lansering kan du kontakta oss på vminsights@microsoft.com för att begära instruktioner.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer kan du läsa [distribuera Azure Monitor for VMS](vminsights-enable-overview.md).
