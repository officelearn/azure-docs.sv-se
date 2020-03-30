---
title: Felsöka utvärdering och beroendevisualisering i Azure Migrate
description: Få hjälp med felsökningsutvärdering och beroendevisualisering i Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127666"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Felsöka utvärdering/beroendevisualisering

Den här artikeln hjälper dig att felsöka problem med utvärdering och beroendevisualisering med [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Frågor om bedömningsberedskap

Åtgärda problem med bedömningsberedskap enligt följande:

**Problem** | **Snabbkorrigering**
--- | ---
Starttyp som inte stöds | Azure stöder inte virtuella datorer med efi-starttyp. Vi rekommenderar att du konverterar starttypen till BIOS innan du kör en migrering. <br/><br/>Du kan använda Azure Migrera servermigrering för att hantera migreringen av sådana virtuella datorer. Den konverterar starttypen för den virtuella datorn till BIOS under migreringen.
Villkorslöst windows-operativsystem som stöds | Operativsystemet har passerat supportdatumet och behöver ett CSA (Custom Support Agreement) för [support i Azure](https://aka.ms/WSosstatement). Överväg att uppgradera innan du migrerar till Azure.
Windows-operativsystem som inte stöds | Azure stöder endast [valda Windows OS-versioner](https://aka.ms/WSosstatement). Överväg att uppgradera datorn innan du migrerar till Azure.
Villkorligt godkände Linux OS | Azure stöder endast [valda Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md). Överväg att uppgradera datorn innan du migrerar till Azure.
Ogenomtämjda Linux OS | Datorn kan starta i Azure, men Azure tillhandahåller inget operativsystemstöd. Överväg att uppgradera till en [godkänd Linux-version](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure.
Okänt operativsystem | Operativsystemet för den virtuella datorn angavs som "Övrigt" i vCenter Server. Det här beteendet blockerar Azure Migrate från att verifiera Azure-beredskapen för den virtuella datorn. Kontrollera att operativsystemet [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
Bitversion som inte stöds | Virtuella datorer med 32-bitars operativsystem kan starta i Azure, men vi rekommenderar att du uppgraderar till 64-bitars innan du migrerar till Azure.
Kräver en Microsoft Visual Studio-prenumeration | Datorn kör ett Windows-klientoperativsystem, som endast stöds genom en Visual Studio-prenumeration.
Det gick inte att hitta den virtuella datorn för den nödvändiga lagringsprestandan | Lagringsprestanda (in-/utdataåtgärder per sekund [IOPS] och dataflöde) som krävs för datorn överskrider Azure VM-stöd. Minska lagringskraven för maskinen före migreringen.
Det gick inte att hitta den virtuella datorn för den nödvändiga nätverksprestandan | Nätverksprestanda (in/ut) som krävs för datorn överskrider Azure VM-stöd. Minska nätverkskraven för maskinen.
Det gick inte att hitta den virtuella datorn på den angivna platsen | Använd en annan målplats före migreringen.
En eller flera olämpliga diskar | En eller flera diskar som är anslutna till den virtuella datorn uppfyller inte Azure-kraven. A<br/><br/> Azure Migrate: Server Assessment stöder för närvarande inte Ultra SSD-diskar och bedömer diskarna baserat på diskgränserna för premiumhanterade diskar (32 TB).<br/><br/> För varje disk som är ansluten till den virtuella datorn kontrollerar du att diskens storlek är < 64 TB (stöds av Ultra SSD-diskar).<br/><br/> Om den inte är det minskar du diskstorleken innan du migrerar till Azure, eller använder flera diskar i Azure och [tar dem samman](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) för att få högre lagringsgränser. Kontrollera att prestandan (IOPS och dataflöde) som behövs av varje disk stöds av [Azure-hanterade virtuella datordiskar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits).
Ett eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort från datorn före migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar från datorn före migreringen.
Diskstorleken överskrider gränsen | Azure Migrate: Server Assessment stöder för närvarande inte Ultra SSD-diskar och bedömer diskarna baserat på premiumdiskgränser (32 TB).<br/><br/> Azure stöder dock diskar med upp till 64 TB -storlek (stöds av Ultra SSD-diskar). Förminska diskar till mindre än 64 TB före migreringen, eller använd flera diskar i Azure och [dra ihop dem](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) för att få högre lagringsgränser.
Disken är inte tillgänglig på den angivna platsen | Kontrollera att disken är på din målplats innan du migrerar.
Disken är inte tillgänglig för den angivna redundansen | Disken ska använda den redundanslagringstyp som definieras i bedömningsinställningarna (LRS som standard).
Det gick inte att fastställa diskens lämplighet på grund av ett internt fel | Prova att skapa en ny bedömning för gruppen.
Den virtuella datorn med nödvändiga kärnor och minne hittades inte | Azure kunde inte hitta en lämplig VM-typ. Minska minnet och antalet kärnor på den lokala datorn innan du migrerar.
Det gick inte att avgöra den virtuella datorns lämplighet på grund av ett internt fel | Prova att skapa en ny bedömning för gruppen.
Det gick inte att avgöra lämpligheten för en eller flera diskar på grund av ett internt fel | Prova att skapa en ny bedömning för gruppen.
Det gick inte att avgöra lämpligheten för ett eller flera nätverkskort på grund av ett internt fel | Prova att skapa en ny bedömning för gruppen.

## <a name="linux-vms-are-conditionally-ready"></a>Virtuella Linux-datorer är "villkorligt klara"

Serverutvärdering markerar virtuella Linux-datorer som "villkorligt redo" på grund av ett känt mellanrum i Serverutvärdering.

- Gapet hindrar den från att identifiera den mindre versionen av Linux OS som är installerat på de lokala virtuella datorerna.
- För RHEL 6.10 identifierar serverutvärdering för närvarande endast RHEL 6 som OS-version.
-  Eftersom Azure endast stöder specifika versioner av Linux markeras virtuella Linux-datorer för närvarande som villkorligt klara i Serverutvärdering.
- Du kan avgöra om Linux OS som körs på den lokala virtuella datorn är godkänt i Azure genom att granska [Azure Linux-stöd](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  När du har verifierat den godkända distributionen kan du ignorera den här varningen.

## <a name="azure-skus-bigger-than-on-premises"></a>Azure SKU:er som är större än lokala

Azure Migrate Server Assessment kan rekommendera Azure VM SKU:er med fler kärnor och minne än aktuell lokal allokering baserat på typen av utvärdering:


- Vm SKU-rekommendationen beror på bedömningsegenskaperna.
- Detta påverkas av vilken typ av bedömning du utför i Serverbedömning: *Prestandabaserad*eller *Som lokal*.
- För prestandabaserade utvärderingar tar Server Assessment hänsyn till användningsdata för lokala virtuella datorer (CPU, minne, disk och nätverksanvändning) för att bestämma rätt mål-VM SKU för lokala virtuella datorer. Det ger också en komfortfaktor vid fastställandet av effektivt utnyttjande.
- För lokal storlek beaktas inte prestandadata och mål-SKU rekommenderas baserat på lokal allokering.

Om du vill visa hur detta kan påverka rekommendationerna ska vi ta ett exempel:

Vi har en lokal virtuell dator med fyra kärnor och åtta GB minne, med 50% CPU-användning och 50% minnesanvändning, och en specificerad komfortfaktor på 1,3.

-  Om utvärderingen är **Som lokalt**rekommenderas en Azure VM SKU med fyra kärnor och 8 GB minne.
- Om bedömningen är prestandabaserad, baserad på effektiv CPU- och minnesanvändning (50 % av 4 kärnor * 1,3 = 2,6 kärnor och 50 % av 8 GB minne * 1,3 = 5,3 GB minne), är den billigaste VM SKU med fyra kärnor (närmaste kärnantal) och åtta GB minne (närmaste stöds minnesstorlek) rekommenderas.
- [Läs mer](concepts-assessment-calculation.md#types-of-assessments) om bedömningsstorlek.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure disk SKU:er som är större än lokala

Azure Migrate Server Assessment kan rekommendera en större disk baserat på typen av bedömning.
- Diskstorleken i Serverutvärdering beror på två bedömningsegenskaper: storlekskriterier och lagringstyp.
- Om storlekskriterierna är **prestandabaserade**och lagringstypen är inställd **på Automatisk,** beaktas IOPS- och dataflödesvärdena för disken när måldisktypen (standard-hårddisk, standard-SSD eller Premium) identifierars. En disk SKU från disktypen rekommenderas sedan och rekommendationen tar hänsyn till storlekskraven för den lokala disken.
- Om storlekskriterierna är **prestandabaserade**och lagringstypen är **Premium**rekommenderas en premiumdisk SKU i Azure baserat på IOPS-, dataflödes- och storlekskraven för den lokala disken. Samma logik används för att utföra diskstorlek när storlekskriterierna är **Som lokalt** och lagringstypen är **Standard HDD,** **Standard SSD**eller **Premium**.

Om du till exempel har en lokal disk med 32 GB minne, men den aggregerade läsa och skriva IOPS för disken är 800 IOPS, rekommenderar Server Assessment en premiumdisk (på grund av de högre IOPS-kraven) och rekommenderar sedan en disk SKU som kan stödja krävs IOPS och storlek. Den bästa matchningen i det här exemplet är P15 (256 GB, 1100 IOPS). Även om storleken som krävs av den lokala disken var 32 GB, rekommenderar Server Assessment en större disk på grund av det höga IOPS-kravet på den lokala disken.

## <a name="utilized-corememory-percentage-missing"></a>Använd kärn-/minnesprocent saknas

Serverutvärderingsrapporter "PercentageOfCoresUtilizedMissing" eller "PercentageOfMemoryUtilizedMissing" när Azure Migrate-enheten inte kan samla in prestandadata för relevanta lokala virtuella datorer.

- Detta kan inträffa om de virtuella datorerna är avstängda under utvärderingstiden. Installationen kan inte samla in prestandadata för en virtuell dator när den är avstängd.
- Om bara minnesräknare saknas och du försöker bedöma virtuella hyper-virtuella datorer kontrollerar du om du har aktiverat dynamiskt minne på dessa virtuella datorer. Det finns ett känt problem endast för virtuella hyper-virtuella datorer, där en Azure Migrate-enhet inte kan samla in minnesanvändningsdata för virtuella datorer som inte har dynamiskt minne aktiverat.
- Om någon av prestandaräknarna saknas faller Azure Migrate Server Assessment tillbaka till de allokerade kärnorna och minnet och rekommenderar en motsvarande vm-storlek.
- Om alla prestandaräknare saknas, se till att kraven för portåtkomst för bedömning är uppfyllda. Läs mer om portåtkomstkraven för [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) och [fysisk](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) serverutvärdering.

## <a name="is-the-operating-system-license-included"></a>Ingår operativsystemets licens?

Azure Migrate Server Assessment tar för närvarande hänsyn till licenskostnaden för operativsystemet endast för Windows-datorer. Licenskostnader för Linux-datorer beaktas för närvarande inte.

## <a name="how-does-performance-based-sizing-work"></a>Hur fungerar prestationsbaserad storlek?

Server Assessment samlar kontinuerligt in prestandadata för lokala datorer och använder dem för att rekommendera VM-SKU:n och disk-SKU:n i Azure. [Läs om hur](concepts-assessment-calculation.md#calculate-sizing-performance-based) prestandabaserade data samlas in.


## <a name="dependency-visualization-in-azure-government"></a>Beroendevisualisering i Azure Government

Azure Migrate är beroende av servicemappning för funktionen för beroendevisualisering. Eftersom tjänstöversikten för närvarande inte är tillgänglig i Azure Government är den här funktionen inte tillgänglig i Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Beroenden visas inte efter agentinstallation

När du har installerat beroendevisualiseringsagenter på lokala virtuella datorer tar Azure Migrate vanligtvis 15-30 minuter att visa beroenden i portalen. Om du har väntat i mer än 30 minuter kontrollerar du att Microsoft Monitoring Agent (MMA) kan ansluta till Log Analytics-arbetsytan.

För virtuella Windows-datorer:
1. Starta MMA på Kontrollpanelen.
2. I **Microsoft Monitoring Agent-egenskaperna** > **Azure Log Analytics (OMS)** kontrollerar du att **statusen** för arbetsytan är grön.
3. Om statusen inte är grön kan du prova att ta bort arbetsytan och lägga till den igen i MMA.

    ![MMA-status](./media/troubleshoot-assessment/mma-properties.png)

För virtuella Linux-datorer kontrollerar du att installationskommandona för MMA och beroendeagenten lyckades.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

- **MMS-agent:** Granska operativsystemen [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)och [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) som stöds.
- **Beroendeagent:** de [windows- och Linux-operativsystem](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) som stöds.

## <a name="visualize-dependencies-for--hour"></a>Visualisera beroenden för > timme

Även om Azure Migrate låter dig gå tillbaka till ett visst datum under den senaste månaden, är den maximala varaktigheten för vilken du kan visualisera beroenden en timme.

Du kan till exempel använda tidslängdsfunktionen i beroendekartan för att visa beroenden för igår, men du kan bara visa dem under en timme.

Du kan dock använda Azure Monitor-loggar för att [fråga beroendedata](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre varaktighet.

## <a name="visualized-dependencies-for--10-machines"></a>Visualiserade beroenden för > 10 maskiner

I Azure Migrate Server Assessment kan du [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) med upp till 10 virtuella datorer. För större grupper rekommenderar vi att du delar upp de virtuella datorerna i mindre grupper för att visualisera beroenden.

## <a name="machines-show-install-agent"></a>Maskiner visar "Installera agent"

När du migrerar datorer med beroendevisualisering aktiverad till Azure kan datorer visa åtgärden "Installera agent" i stället för "Visa beroenden" på grund av följande beteende:


- Efter migreringen till Azure inaktiveras lokala datorer och motsvarande virtuella datorer snurras upp i Azure. Dessa maskiner skaffar en annan MAC-adress.
- Datorer kan också ha en annan IP-adress, baserat på om du har behållit den lokala IP-adressen eller inte.
- Om både MAC- och IP-adresser skiljer sig från lokala, associerar Azure Migrate inte de lokala datorerna med några tjänstmappberoendedata. I det här fallet visas alternativet att installera agenten i stället för att visa beroenden.
- Efter en testmigrering till Azure förblir lokala datorer aktiverade som förväntat. Motsvarande datorer som snurrats upp i Azure hämtar olika MAC-adresser och kan hämta olika IP-adresser. Om du inte blockerar utgående Azure Monitor-loggtrafik från dessa datorer associerar Azure Migrate inte de lokala datorerna med några tjänstmappberoendedata och visar därför alternativet att installera agenter i stället för att visa beroenden.


## <a name="capture-network-traffic"></a>Fånga nätverkstrafik

Samla in nätverkstrafikloggar enligt följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Tryck på F12 för att starta utvecklarverktyg. Om det behövs avmarkerar du inställningen **Rensa poster i navigering.**
3. Välj fliken **Nätverk** och börja samla in nätverkstrafik:
   - I Chrome väljer du **Bevara logg**. Inspelningen ska starta automatiskt. En röd cirkel indikerar att trafiken fångas. Om den röda cirkeln inte visas markerar du den svarta cirkeln för att starta.
   - I Microsoft Edge och Internet Explorer ska inspelningen startas automatiskt. Om den inte gör det väljer du den gröna uppspelningsknappen.
4. Försök återskapa felet.
5. När du har stött på felet under inspelningen stoppar du inspelningen och sparar en kopia av den inspelade aktiviteten:
   - Högerklicka och välj Spara **som har med innehåll i**Chrome . Den här åtgärden komprimerar och exporterar loggarna som en .har-fil.
   - I Microsoft Edge eller Internet Explorer väljer du alternativet **Exportera infångad trafik.** Den här åtgärden komprimerar och exporterar loggen.
6. Välj fliken **Konsol** för att kontrollera om det finns varningar eller fel. Så här sparar du konsolloggen:
   - Högerklicka var som helst i konsolloggen i Chrome. Välj **Spara som**, om du vill exportera och zip loggen.
   - Högerklicka på felen i Microsoft Edge eller Internet Explorer och välj **Kopiera alla**.
7. Stäng utvecklarverktyg.

## <a name="next-steps"></a>Nästa steg

[Skapa](how-to-create-assessment.md) eller [anpassa](how-to-modify-assessment.md) en utvärdering.
