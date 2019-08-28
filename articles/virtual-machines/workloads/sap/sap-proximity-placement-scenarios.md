---
title: Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program | Microsoft Docs
description: Beskriver distributions scenarier i SAP med placerings grupper för Azure närhet
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077993"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Närhetsplaceringsgrupper i Azure för optimal svarstid för nätverk med SAP-program
SAP-program som baseras på SAP NetWeaver-eller SAP S/4HANA-arkitekturen är känsliga för nätverks fördröjning mellan SAP-programnivån och SAP-databasnivå. Anledningen till att dessa arkitekturer är känsliga beror på att de flesta affärs logiken körs i program lagret. Som ett resultat av körningen av affärs logiken utfärdar SAP-programlagret frågor till databas nivån med en hög frekvens på tusentals och flera tusen tusen per sekund. I de flesta fall är arten av frågorna enkel. Och kan ofta köras på databas nivån på mindre än 500 mikrosekunder eller ännu mindre. Den tid det tar för nätverket att skicka en sådan fråga från program nivån till databas nivån och ta emot resultat mängden tillbaka från databas nivån har stor påverkan på den tid det tar att köra affärs processer. Den här känsligheten för nätverks fördröjning är anledningen till varför du måste ägna in SAP-distributions projekt för att uppnå optimal nätverks svars tid. I [SAP Obs #1100926 – vanliga frågor och svar: Nätverks prestanda](https://launchpad.support.sap.com/#/notes/1100926/E), SAP publicerade några rikt linjer i hur du klassificerar nätverks fördröjningen.

På den ena sidan, i många Azure-regioner, är antalet Data Center vuxna, även utlöst genom introduktionen av Tillgänglighetszoner. På den andra sidan har kunder, särskilt för avancerade SAP-system, använt mer specifika VM-SKU: er från M-seriens familj eller HANA stora instanser. Typer av virtuella Azure-datorer som inte finns i alla data Center i en angiven Azure-region. Som ett resultat av dessa två Tendencies, fick kunderna ärenden, där nätverks fördröjningen inte var i optimalt intervall och i vissa fall resulterade i optimala prestanda för sina SAP-system.

För att förhindra sådana problem erbjuder Azure en konstruktion som kallas [Azure närhets placerings grupp](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Den här nya funktionen har redan använts för att distribuera olika SAP-system. Se artikeln som refereras för begränsningar för närhets grupper. Den här artikeln beskriver de olika SAP-scenarier där Azure närhets placerings grupper kan användas eller ska användas.

## <a name="what-are-proximity-placement-groups"></a>Vad är placerings grupper för närhet 
En Azure närhets placerings grupp är en logisk konstruktion, som i definitions fasen är kopplad till en Azure-region och en Azure-resurs grupp. Under distributionen av virtuella datorer refereras en närhets placerings grupp till av:

- Den första distribuerade virtuella Azure-datorn som ska lösas i data centret. Den första virtuella datorn kan ses som en virtuell dator för virtuella datorer som distribueras i ett Data Center baserat på Azure Allocation-algoritmer som till exempel kombineras med användar definitioner för en bestämd Azure-tillgänglighets zon.
- Alla efterföljande virtuella datorer som har distribuerats refererar till närhets placerings gruppen för att placera alla efterföljande distribuerade virtuella Azure-datorer i samma data Center som den första virtuella datorn placerades i.

> [!NOTE]
> Om det inte finns någon värd maskin vara som är distribuerad och som kan köra en speciell VM-typ i samma data Center som den första virtuella datorn i, kommer distributionen av den efterfrågade VM-typen inte att lyckas och avslutas med ett fel meddelande. Detta kan vara fall där fler icke-vanliga virtuella datorer, t. ex. virtuella datorer med GPU: er eller HPC VM-typer, ska centreras, t. ex. en virtuell dator i M-serien som har distribuerats som första VM-typ

En enda [Azure-resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kan ha flera närhets placerings grupper tilldelade till sig själva. En närhets placerings grupp kan dock bara tilldelas en Azure-resurs grupp.

Genom att använda närhets placerings grupper bör du vara medveten om följande:

- När du driver de optimala prestanda för ditt SAP-system och begränsar dig till ett enda Azure-datacenter för det här systemet genom att använda närhets placerings grupper kan du kanske inte kombinera alla typer av virtuella dator familjer inom en sådan närhets grupp. Orsak är att viss värd maskin vara som behövs för att uteslutande köra en viss VM-typ kanske inte finns i data centret din "Anchor VM" för placerings gruppen har distribuerats
- I ett sådant SAP-Systems livs cykel kan du tvingas flytta systemet till ett annat data Center. En sådan flyttning kan framtvingas i fall där du beslutade att ditt utbyggnads nivå HANA-DBMS ska, till exempel, flytta från fyra noder till 16 noder. Men det finns inte tillräckligt med kapacitet längre för att få ytterligare 12 virtuella datorer av den typ som du redan har använt i samma data Center.
- På grund av inaktive ring av maskin vara kan Microsoft skapa kapaciteter för de VM-typer som du använde i ett annat data Center, i stället för samma data Center. En sådan förekomst kan innebära att du flyttar alla virtuella datorer för närhets placerings gruppen till ett annat data Center.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Azure närhets placerings grupper med SAP-system med enbart virtuella Azure-datorer
De flesta fall av distributioner av SAP-NetWeaver och S/4HANA-system i Azure använder inte [Hana-stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). För distributioner av sådana system är det viktigt att ge optimala prestanda mellan SAP-program skiktet och DBMS-nivån. För att göra detta definierar du en placerings grupp för Azure närhet för det här systemet. I de flesta kund distributioner väljer kunderna att bygga en enda Azure- [resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) för SAP-system. I sådana fall skulle det finnas en 1:1-relation mellan, till exempel produktions resurs grupper för ERP-systemet och dess närhets placerings grupp. I vissa andra distributions fall organiserade kunderna sina resurs grupper horisontellt och samlat in alla produktions system i en enda resurs grupp. I sådana fall skulle du ha en 1-till-många-relation mellan din resurs grupp för produktion av SAP-system och flera närhets placerings grupper för din produktion SAP ERP, SAP BW osv. Att sammanföra flera eller till och med alla SAP-produktions-eller icke-produktionssystem i en enda närhets placerings grupp bör undvikas. I undantag, där ett litet antal SAP-system eller ett SAP-system och vissa omgivande program krävs för nätverkskommunikation med låg latens, kan du överväga att flytta dessa system till en närhets placerings grupp. Orsaken till detta är att ju fler system som du grupperar i en närhets placerings grupp, desto högre sannolikhet:

- Att du behöver en VM-typ som inte kan köras i ett särskilt data Center som närhets placerings gruppen har fäst i.
- Resurser av vissa icke-vanliga virtuella datorer som t. ex. M-serien kan komma att bli uppfyllde längre när du frågar efter fler när du lägger till ytterligare program vara till en befintlig närhets placering över tid.

Den idealiska användningen som beskrivs ser ut så här:

![Närhets grupper för alla virtuella Azure-datorer](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

I det här fallet grupperas enskilda SAP-system i en resurs grupp var och en med en närhets placerings grupp. Det finns inget beroende av om du använder HANA Scale-Out eller DBMS-konfigurationer.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure närhets placerings grupper och HANA-stora instanser
I de fall där några av dina SAP-system förlitar sig på [Hana-stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) som program lager, kan stora förbättringar av nätverks fördröjningen mellan Hana: a stor instans enhet och virtuella Azure-datorer uppnås när du använder ett stort antal instanser av Hana som har varit distribuerat i [revision 4-rader eller](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)-stämplar. En av förbättringarna är att de HANA-stora instans enheterna, när de distribuerades, får en närhets placering som redan har distribuerats. Du kan använda den här närhets gruppen för att distribuera dina virtuella program lager datorer. Därför kommer de virtuella datorerna att distribueras i samma data Center som är värd för din HANA stor instans enhet.

För att kunna identifiera om din HANA-stora instans enhet distribueras i en revision 4-stämpel eller-rad, kontrollerar du artikeln [Azure Hana stor instanss-kontroll via Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). I översikten över attributen för en stor instans av HANA, kan du också ta reda på namnet på närhets placerings gruppen när den har skapats vid distributions tiden för din HANA stor instans enhet. Namnet som visas i översikten över attributen är namnet på närhets placerings gruppen, du bör använda för att distribuera dina virtuella program skikt till.

I motsats till SAP-system som bara använder Azure Virtual Machines kan du fatta beslutet om hur många [Azure-resurs grupper](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) som ska användas från dig till en viss nivå när du använder Hana-stora instanser. Alla instanser av HANA stor instans i en [Hana stor instans-klient](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) grupperas i en enda Azure-resurs grupp enligt beskrivningen [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). Om du inte vill att en distribution till olika klienter ska separera, till exempel produktion och icke-produktion eller vissa system, så kommer alla dina HANA stora instans enheter att distribueras i en HANA-stor instans-klient, som återigen har en 1:1-relation med Azure resurs grupp. Alla enskilda enheter har en separat närhets grupp definierad. 

Det innebär att grupperingen mellan Azures resurs grupper och närhets placerings grupper för en enskild klient ser ut så här:

![Närhets placerings grupper för alla virtuella Azure-datorer](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Kort exempel på distribution med Azure närhets placerings grupper
För att demonstrera hur du kan använda Azure närhets placerings grupper för att distribuera din virtuella dator, här visas en lista över PowerShell-kommandon som demonstrerar användningen för en första lite övning med Azure närhets placerings grupper

Det första steget efter att ha loggat in med din [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) är att kontrol lera om du är i rätt Azure-prenumeration som du vill använda för att distribuera med kommandot:

<pre><code>
Get-AzureRmContext
</code></pre>

Om du behöver byta till en annan prenumeration kan du göra det genom att köra det här kommandot:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Som ett tredje steg vill du skapa en ny Azure-resurs grupp med det här kommandot:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Du kan skapa den nya närhets gruppen nu med:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Nu kan du börja distribuera din första virtuella dator i den här närhets gruppen med ett kommando som:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Med kommandot ovan distribueras en Windows-baserad virtuell dator. När distributionen av den virtuella datorn är klar definieras Data Center omfånget för närhets placerings gruppen i Azure-regionen. Alla efterföljande VM-distributioner som refererar till närhets placerings gruppen som i det sista kommandot kommer att distribueras i samma Azure-datacenter så länge den virtuella dator typen kan finnas på maskin vara som placerats i data centret och/eller kapaciteten för den VM-typen tillgänglig.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinera tillgänglighets uppsättningar och Tillgänglighetszoner med närhets placerings grupper 
Med hjälp av Tillgänglighetszoner för distributioner av SAP-system är en av nack delarna att det inte går att kontrol lera att SAP-programlagret har distribuerats med tillgänglighets uppsättningar i den specifika zonen. Eftersom du vill att SAP-programlagret ska distribueras i samma zoner som DBMS-skiktet och referera till en tillgänglighets zon och en tillgänglighets uppsättning när du distribuerar en enskild virtuell dator inte stöds, tvingades du att distribuera program lagret genom att referera till en zon och förlorar därmed möjligheten att se till att de virtuella datorerna i program lagret är spridda över olika uppdaterings-och domän problem. Med hjälp av placerings grupper för närhet kan du lösa den här begränsningen. Ordningen på distributioner skulle se ut så här:

- Skapa en närhetsplaceringsgrupp
- Distribuera din "Anchor VM", vanligt vis DBMS-servern genom att referera till en viss Azure-tillgänglighets zon
- Skapa en tillgänglighets uppsättning som refererar till Azure närhets gruppen (se nedan)
- Distribuera program skiktets virtuella datorer genom att referera till tillgänglighets uppsättningen och närhets placerings gruppen

I stället för att distribuera den första virtuella datorn som visas ovan refererar du till en Azure tillgänglighets zon och närhets placerings gruppen när du distribuerar den virtuella datorn som:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

En lyckad distribution av den virtuella datorn som skulle vara värd för databas instansen av SAP-system i en Azure-tillgänglighets zon, är omfånget för närhets placeringen till ett av data centret som representerar den tillgänglighets zon som du har definierat .

Vi förutsätter att du distribuerar virtuella datorer för centrala tjänster på samma sätt som de virtuella DBMs-datorerna genom att referera till samma zon (er) som för de virtuella DBMS-datorerna och samma närhets placerings grupper. I nästa steg måste du skapa den tillgänglighets uppsättning (er) som du vill använda för program lagret i ditt SAP-system.
Placerings gruppen för närhet måste definieras och skapas. Kommandot för att skapa tillgänglighets uppsättningen kräver en ytterligare referens till närhets placerings gruppens ID (inte namnet). Du kan hämta ID för närhets placerings gruppen med:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

När du skapar tillgänglighets uppsättningen måste du överväga ytterligare parametrar när du använder Managed disks (standard om inget annat anges) och placerings grupper för närhet:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Vi rekommenderar att du använder tre fel domäner. Antalet fel domäner som stöds kan dock variera från region till region. I det här fallet var det högsta antalet möjliga fel domäner för de angivna regionerna två. För att distribuera virtuella datorer i program lager måste du lägga till en referens till namn på tillgänglighets uppsättningen och närhets placerings gruppens namn, så som visas här:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Slut resultatet för den här sekvensen är ett DBMS-skikt och centrala tjänster för ditt SAP-system som finns i en specifik tillgänglighets zon och ett SAP-program skikt som finns via tillgänglighets uppsättningar i samma Azure-datacenter som de virtuella DBMS-datorerna fick distribueras.

> [!NOTE]
> När du distribuerar en virtuell DBMS-dator till en zon och den andra DBMS-datorn i en annan zon för att skapa en hög tillgänglighets konfiguration måste du kräva olika närhets placerings grupper för var och en av zonerna. Samma si True för den tillgänglighets uppsättning som du kan använda

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Hämta ett befintligt system till placerings grupper för Azure närhet
Eftersom du redan har distribuerat SAP-system kanske du vill optimera nätverks fördröjningen för några av dina kritiska system och leta upp program lagret och DBMS-skiktet i samma data Center. I den offentliga för hands versions gruppen är det nödvändigt att ta bort de virtuella datorerna och en ny framställning av de virtuella datorerna för att kunna flytta till närhets placerings grupper. I det här steget av funktionerna är det inte tillräckligt tillräckligt för att stänga av de virtuella datorerna för att kunna tilldela de virtuella datorerna för avstängning till närhets placerings grupper.


## <a name="next-steps"></a>Nästa steg
Läs dokumentationen:

- [SAP-arbetsbelastning på Azure planering och distribution check lista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Förhandsgranskningsvyn Distribuera virtuella datorer till närhets placerings grupper med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Förhandsgranskningsvyn Distribuera virtuella datorer till närhets placerings grupper med hjälp av PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

