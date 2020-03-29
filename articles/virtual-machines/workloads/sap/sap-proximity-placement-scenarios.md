---
title: Azure närhetsplaceringsgrupper för SAP-program | Microsoft-dokument
description: Beskriver SAP-distributionsscenarier med Azure-närhetsplaceringsgrupper
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277600"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure-närhetsplaceringsgrupper för optimal nätverksfördröjning med SAP-program
SAP-program som baseras på SAP NetWeaver- eller SAP S/4HANA-arkitekturen är känsliga för nätverksfördröjning mellan SAP-programnivån och SAP-databasnivån. Den här känsligheten är resultatet av de flesta affärslogiken som körs i programlagret. Eftersom SAP-programlagret kör affärslogiken utfärdar det frågor till databasnivån med hög frekvens, med en hastighet av tusentals eller tiotusentals per sekund. I de flesta fall är arten av dessa frågor enkel. De kan ofta köras på databasnivån i 500 mikroseconds eller mindre.

Den tid som spenderas på nätverket för att skicka en sådan fråga från programnivån till databasnivån och ta emot resultatuppsättningen tillbaka har en stor inverkan på den tid det tar att köra affärsprocesser. Den här känsligheten för nätverksfördröjning är anledningen till att du måste uppnå optimal nätverksfördröjning i SAP-distributionsprojekt. Se [SAP Note #1100926 - Vanliga frågor och svar: Nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E) för riktlinjer för hur du klassificerar nätverksfördröjningen.

I många Azure-regioner har antalet datacenter ökat. Denna tillväxt har också utlösts av införandet av tillgänglighetszoner. Samtidigt använder kunder, särskilt för avancerade SAP-system, mer speciella VM-SKU:er i M-serien eller STORA HANA-instanser. Dessa Azure-typer av virtuella datorer är inte tillgängliga i alla datacenter i en viss Azure-region. På grund av dessa två tendenser har kunderna upplevt nätverksfördröjning som inte är i det optimala intervallet. I vissa fall resulterar den här svarstiden i suboptimala prestanda för deras SAP-system.

För att förhindra dessa problem erbjuder Azure [närhetsplaceringsgrupper](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Den här nya funktionen har redan använts för att distribuera olika SAP-system. Begränsningar för grupper för närhetsplacering finns i artikeln som refereras till i början av detta stycke. Den här artikeln beskriver SAP-scenarier där Azure proximity placement grupper kan eller bör användas.

## <a name="what-are-proximity-placement-groups"></a>Vad är närhetsplaceringsgrupper? 
En Azure-närhetsplaceringsgrupp är en logisk konstruktion. När en definieras är den bunden till en Azure-region och en Azure-resursgrupp. När virtuella datorer distribueras refereras en närhetsplaceringsgrupp av:

- Den första Azure VM som distribueras i datacentret. Du kan tänka dig den första virtuella datorn som en "scope VM" som distribueras i ett datacenter baserat på Azure-allokeringsalgoritmer som så småningom kombineras med användardefinitioner för en viss tillgänglighetszon.
- Alla efterföljande virtuella datorer som distribueras som refererar till proximity placement-gruppen, för att placera alla senare distribuerade virtuella Azure-datorer i samma datacenter som den första virtuella datorn.

> [!NOTE]
> Om det inte finns någon värdmaskinvara som kan köra en viss VM-typ i datacentret där den första virtuella datorn placerades, kommer distributionen av den begärda VM-typen inte att lyckas. Du får ett felmeddelande.

En enda [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kan ha flera närhetsplaceringsgrupper tilldelade till den. Men en närhetsplaceringsgrupp kan tilldelas endast en Azure-resursgrupp.

När du använder närhetsplaceringsgrupper bör du tänka på följande:

- När du strävar efter optimal prestanda för ditt SAP-system och begränsar dig till ett enda Azure-datacenter för systemet med hjälp av närhetsplaceringsgrupper, kanske du inte kan kombinera alla typer av vm-familjer inom placeringsgruppen. Dessa begränsningar beror på att värdmaskinvaran som behövs för att köra en viss VM-typ kanske inte finns i det datacenter som den "begränsade virtuella datorn" för placeringsgruppen har distribuerats.
- Under livscykeln för ett sådant SAP-system kan du tvingas flytta systemet till ett annat datacenter. Det här steget kan krävas om du bestämmer din utskalning HANA DBMS lager bör till exempel flytta från fyra noder till 16 noder, och det finns inte tillräckligt med kapacitet för att få ytterligare 12 virtuella datorer av den typ som du använde i datacentret.
- På grund av avveckling av maskinvara kan Microsoft bygga upp kapacitet för en vm-typ som du använde i ett annat datacenter, i stället för den som du ursprungligen använde. I det scenariot kan du behöva flytta alla närhetsplaceringsgruppens virtuella datorer till ett annat datacenter.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Närhetsplaceringsgrupper med SAP-system som endast använder virtuella Azure-datorer
De flesta SAP NetWeaver- och S/4HANA-systemdistributioner på Azure använder inte [STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). För distributioner som inte använder STORA HANA-instanser är det viktigt att ge optimal prestanda mellan SAP-programlagret och DBMS-nivån. För att göra det, definiera en Azure närhet placeringsgrupp bara för systemet.

I de flesta kunddistributioner skapar kunder en enda [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) för SAP-system. I så fall finns det en 1:1-relation mellan till exempel resursgruppen för produktions-affärssystem och dess närhetsplaceringsgrupp. I andra fall organiserar kunderna sina resursgrupper horisontellt och samlar in alla produktionssystem i en enda resursgrupp. I det här fallet skulle du ha en 1:1:1-relation mellan resursgruppen för produktions-SAP-system och flera närhetsplaceringsgrupper för din produktion SAP ERP, SAP BW och så vidare.

Undvik att bunta ihop flera SAP-produktionssystem eller icke-produktionssystem i en enda närhetsplaceringsgrupp. När ett litet antal SAP-system eller ett SAP-system och vissa omgivande program behöver ha nätverkskommunikation med låg latens, kan du överväga att flytta dessa system till en närhetsplaceringsgrupp. Du bör undvika paket med system eftersom ju fler system du grupperar i en närhetsplaceringsgrupp, desto större är chanserna:

- Att du behöver en vm-typ som inte kan köras i det specifika datadatacentret som proximity placement-gruppen begränsades till.
- Dessa resurser för icke-vanliga virtuella datorer, som virtuella datorer i M-serien, kan så småningom vara ouppfyllda när du behöver mer eftersom du lägger till programvara i en närhetsplaceringsgrupp över tid.

Så här ser den idealiska konfigurationen ut:

![Närhetsplaceringsgrupper med endast virtuella Azure-datorer](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

I det här fallet grupperas enskilda SAP-system i en resursgrupp vardera, med en närhetsplaceringsgrupp vardera. Det finns inget beroende av om du använder UTSKALNINGS- ELLER DBMS-skalningskonfigurationer.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Närhetsplaceringsgrupper och STORA HANA-instanser
Om vissa av dina SAP-system är beroende av [STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) för programlagret kan du uppleva betydande förbättringar i nätverksfördröjningen mellan HANA Large Instances-enheten och virtuella Azure-datorer när du använder HANA-enheter för stora instanser som distribueras i [Revision 4 rader eller stämplar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). En förbättring är att HANA Stora instanser enheter, när de distribueras, distribuera med en närhet placering grupp. Du kan använda den platsplaceringsgruppen för att distribuera virtuella datorer i programlager. Därför distribueras dessa virtuella datorer i samma datacenter som är värd för din HANA-enhet för stora instanser.

Om du vill ta reda på om din HANA-enhet för stora instanser distribueras i en revision 4-stämpel eller rad kontrollerar du artikeln [Azure HANA Large Instances control via Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). I attributöversikten för din HANA-enhet för stora instanser kan du också bestämma namnet på proximity placement-gruppen eftersom den skapades när din HANA-enhet för stora instanser distribuerades. Namnet som visas i attributöversikten är namnet på den närhetsplaceringsgrupp som du bör distribuera virtuella programlager virtuella datorer till.

Jämfört med SAP-system som bara använder virtuella Azure-datorer, när du använder HANA Stora instanser, har du mindre flexibilitet när du bestämmer hur många [Azure-resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) som ska användas. Alla HANA-enheter för stora instanser i en [HANA-klient för stora instanser grupperas](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) i en enda resursgrupp enligt beskrivningen [i den här artikeln](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). Såvida du inte distribuerar till olika klienter för att separera till exempel produktions- och icke-produktionssystem eller andra system, kommer alla DINA HANA-enheter för stora instanser att distribueras i en HANA-klient för stora instanser. Den här klienten har en 1:1-relation med en resursgrupp. Men en separat närhetsplaceringsgrupp kommer att definieras för var och en av de enskilda enheterna.

Därför kommer relationerna mellan Azure-resursgrupper och närhetsplaceringsgrupper för en enda klient att visas här:

![Närhetsplaceringsgrupper och STORA HANA-instanser](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exempel på distribution med närhetsplaceringsgrupper
Följande är några PowerShell-kommandon som du kan använda för att distribuera dina virtuella datorer med Azure-närhetsplaceringsgrupper.

Det första steget, när du har loggat in på [Azure Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)är att kontrollera om du är i Azure-prenumerationen som du vill använda för distributionen:

<pre><code>
Get-AzureRmContext
</code></pre>

Om du behöver byta till en annan prenumeration kan du göra det genom att köra det här kommandot:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Skapa en ny Azure-resursgrupp genom att köra det här kommandot:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Skapa den nya gruppen för närhetsplacering genom att köra det här kommandot:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Distribuera den första virtuella datorn i gruppen för närhetsplacering med hjälp av ett kommando som det här:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Kommandot föregående distribuerar en Windows-baserad virtuell dator. När den här VM-distributionen lyckas definieras datadataomfattningen för närhetsplaceringsgruppen inom Azure-regionen. Alla efterföljande VM-distributioner som refererar till proximity placement-gruppen, som visas i föregående kommando, kommer att distribueras i samma Azure-datacenter, så länge vm-typen kan vara värd för maskinvara som placeras i den datacentret och kapaciteten för den vm-typen är Tillgängliga.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinera tillgänglighetsuppsättningar och tillgänglighetszoner med närhetsplaceringsgrupper
En av nackdelarna med att använda tillgänglighetszoner för SAP-systemdistributioner är att du inte kan distribuera SAP-programlagret med hjälp av tillgänglighetsuppsättningar inom den specifika zonen. Du vill att SAP-programlagret ska distribueras i samma zoner som DBMS-lagret. Det går inte att referera till en tillgänglighetszon och en tillgänglighetsuppsättning när du distribuerar en enda virtuell dator. Tidigare var du tvungen att distribuera programlagret genom att referera till en zon. Du förlorade möjligheten att se till att virtuella programlager virtuella datorer var spridda över olika uppdaterings- och feldomäner.

Genom att använda närhetsplaceringsgrupper kan du kringgå den här begränsningen. Här är distributionssekvensen:

- Skapa en närhetsplaceringsgrupp.
- Distribuera din virtuella ankare, vanligtvis DBMS-servern, genom att referera till en tillgänglighetszon.
- Skapa en tillgänglighetsuppsättning som refererar till Azure-närhetsgruppen. (Se kommandot senare i den här artikeln.)
- Distribuera virtuella datorer i programlager genom att referera till tillgänglighetsuppsättningen och närhetsplaceringsgruppen.

I stället för att distribuera den första virtuella datorn enligt föregående avsnitt refererar du till en tillgänglighetszon och närhetsplaceringsgruppen när du distribuerar den virtuella datorn:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

En lyckad distribution av den här virtuella datorn skulle vara värd för databasinstansen för SAP-systemet i en tillgänglighetszon. Omfattningen av proximity placement-gruppen är fast i ett av de datacenter som representerar den tillgänglighetszon som du definierade.

Anta att du distribuerar virtuella centraltjänstdatorerna på samma sätt som DBMS-virtuella datorer, som refererar till samma zon eller zoner och samma närhetsplaceringsgrupper. I nästa steg måste du skapa de tillgänglighetsuppsättningar som du vill använda för programlagret i SAP-systemet.

Du måste definiera och skapa gruppen närhetsplacering. Kommandot för att skapa tillgänglighetsuppsättningen kräver ytterligare en referens till närhetsplaceringsgrupp-ID (inte namnet). Du kan hämta ID:t för gruppen närhetsplacering med det här kommandot:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

När du skapar tillgänglighetsuppsättningen måste du överväga ytterligare parametrar när du använder hanterade diskar (standard om inget annat anges) och närhetsplaceringsgrupper:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Helst bör du använda tre feldomäner. Men antalet feldomäner som stöds kan variera från region till region. I det här fallet är det maximala antalet feldomäner som är möjliga för de specifika regionerna två. Om du vill distribuera virtuella datorer i programlagret måste du lägga till en referens till namnet på tillgänglighetsuppsättningen och namnet på närhetsplaceringsgruppen, som du ser här:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Resultatet av den här distributionen är:
- Ett DBMS-lager och centrala tjänster för ditt SAP-system som finns i en viss tillgänglighetszon eller tillgänglighetszon.
- Ett SAP-programlager som finns via tillgänglighetsuppsättningar i samma Azure-datacenter som DBMS-datorn eller virtuella datorer.

> [!NOTE]
> Eftersom du distribuerar en DBMS-virtuell dator till en zon och den andra DBMS-virtuella datorn till en annan zon för att skapa en konfiguration med hög tillgänglighet, behöver du en annan närhetsplaceringsgrupp för var och en av zonerna. Detsamma gäller för alla tillgänglighetsuppsättningar som du använder.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Flytta ett befintligt system till närhetsplaceringsgrupper
Om du redan har distribuerat SAP-system kanske du vill optimera nätverksfördröjningen för vissa av dina kritiska system och hitta programlagret och DBMS-lagret i samma datacenter. Om du vill flytta de virtuella datorerna för en fullständig Azure-tillgänglighetsuppsättning till en befintlig närhetsplaceringsgrupp som redan är begränsad, måste du stänga av alla virtuella datorer i tillgänglighetsuppsättningen och tilldela tillgänglighetsuppsättningen till den befintliga närhetsplaceringsgruppen via Azure-portalen. PowerShell eller CLI. Om du vill flytta en virtuell dator som inte ingår i en tillgänglighetsuppsättning till en befintlig närhetsplaceringsgrupp behöver du bara stänga av den virtuella datorn och tilldela den till en befintlig närhetsplaceringsgrupp. 


## <a name="next-steps"></a>Nästa steg
Kolla in dokumentationen:

- [SAP-arbetsbelastningar på Azure: checklista för planering och distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Förhandsversion: Distribuera virtuella datorer till närhetsplaceringsgrupper med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Förhandsgranskning: Distribuera virtuella datorer till närhetsplaceringsgrupper med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

