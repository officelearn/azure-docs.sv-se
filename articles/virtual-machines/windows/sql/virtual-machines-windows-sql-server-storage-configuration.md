---
title: Lagringskonfiguration för virtuella SQL Server-datorer | Microsoft-dokument
description: I det här avsnittet beskrivs hur Azure konfigurerar lagring för virtuella SQL Server-datorer under etablering (Resource Manager-distributionsmodell). Det förklarar också hur du kan konfigurera lagring för dina befintliga virtuella SQL Server-datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482143"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Lagringskonfiguration för SQL Server VM

När du konfigurerar en virtuell SQL Server-avbildning i Azure hjälper portalen till att automatisera lagringskonfigurationen. Detta inkluderar att koppla lagring till den virtuella datorn, göra lagringen tillgänglig för SQL Server och konfigurera den för att optimera för dina specifika prestandakrav.

I det här avsnittet beskrivs hur Azure konfigurerar lagring för virtuella SQL Server-datorer både under etablering och för befintliga virtuella datorer. Den här konfigurationen baseras på [prestandatips för](virtual-machines-windows-sql-performance.md) virtuella Azure-datorer som kör SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Krav

För att kunna använda de automatiska konfigurationsinställningarna för lagring kräver den virtuella datorn följande egenskaper:

* Etablerat med en [SQL Server-galleriavbildning](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Använder [resurshanterarens distributionsmodell](../../../azure-resource-manager/management/deployment-models.md).
* Använder [premium-SSD:er](../disks-types.md).

## <a name="new-vms"></a>Nya virtuella datorer

I följande avsnitt beskrivs hur du konfigurerar lagring för nya virtuella SQL Server-datorer.

### <a name="azure-portal"></a>Azure Portal

När du etablerar en Virtuell Azure-dator med hjälp av en SQL Server-galleriavbildning väljer du **Ändra konfiguration** på fliken **SQL Server-inställningar** för att öppna sidan Prestandaoptimerad lagringskonfiguration. Du kan antingen lämna värdena som standard eller ändra vilken typ av diskkonfiguration som bäst passar dina behov baserat på din arbetsbelastning. 

![Konfiguration av VM-lagring i SQL Server under etablering](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Välj vilken typ av arbetsbelastning du distribuerar SQL Server för under **Lagringsoptimering**. Med alternativet **Allmän** optimering har du som standard en datadisk med 5000 max IOPS, och du kommer att använda samma enhet för dina data, transaktionslogg och TempDB-lagring. Om du väljer **antingen Transactional processing** (OLTP) eller **Dataasponsering** skapas en separat disk för data, en separat disk för transaktionsloggen och lokal SSD för TempDB används. Det finns inga lagringsskillnader mellan **transaktionsbearbetning** och **datalagring,** men det ändrar din [stripe-konfiguration och spårningsflaggor](#workload-optimization-settings). Om du väljer premiumlagring ställs cachelagringen in i Cachelagringen till *ReadOnly* för dataenheten och Ingen för loggenheten enligt bästa praxis för SQL Server VM.Choosing premium storage sets the caching to ReadOnly for the data drive, and *None* for the log drive as per [SQL Server VM performance best practices](virtual-machines-windows-sql-performance.md). 

![Konfiguration av VM-lagring i SQL Server under etablering](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Diskkonfigurationen är helt anpassningsbar så att du kan konfigurera lagringstopologin, disktypen och IOPs som du behöver för din SQL Server VM-arbetsbelastning. Du har också möjlighet att använda UltraSSD (förhandsversion) som ett alternativ för **disktypen** om din virtuella SQL Server-datorer finns i en av de regioner som stöds (Östra USA 2, Sydostasien och Norra Europa) och du har aktiverat [ultradiskar för din prenumeration](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Dessutom har du möjlighet att ställa in cachelagring för diskarna. Virtuella Azure-datorer har en cachelagringsteknik på flera nivåer som kallas [Blob-cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) när den används med [Premium-diskar](/azure/virtual-machines/windows/disks-types#premium-ssd). Blob Cache använder en kombination av virtuell dator RAM och lokala SSD för cachelagring. 

Diskcachelagring för Premium SSD kan vara *ReadOnly,* *ReadWrite* eller *None*. 

- *ReadOnly-cachelagring* är mycket fördelaktigt för SQL Server-datafiler som lagras på Premium Storage. *ReadOnly* caching ger låg läsfördröjning, hög läsning IOPS och dataflöde som, läser utförs från cache, som finns inom VM-minne och lokala SSD. Dessa läsningar är mycket snabbare än läsningar från datadisken, som kommer från Azure-blob-lagringen. Premiumlagring räknar inte de läsningar som visas från cachen mot diskenSops och dataflöde. Därför kan din tillämpliga uppnå högre total IOPS och dataflöde. 
- *Ingen* cachekonfiguration bör användas för diskarna som är värd för SQL Server Log-filen eftersom loggfilen skrivs sekventiellt och inte drar nytta av *ReadOnly-cachelagring.* 
- *ReadWrite-cachelagring* bör inte användas för att vara värd för SQL Server-filer eftersom SQL Server inte stöder datakonsekvens med *ReadWrite-cachen.* Skriver avfallskapacitet för ReadOnly-blob-cachen och svarstider ökar något om skrivningar går igenom *ReadOnly* *ReadOnly-blob-cachelager.* 


   > [!TIP]
   > Se till att lagringskonfigurationen matchar de begränsningar som den valda vm-storleken medför. Om du väljer lagringsparametrar som överskrider `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`prestandataket för vm-storleken uppstår fel: . Antingen minska IOPs genom att ändra disktypen, eller öka prestanda cap begränsning genom att öka den virtuella datorns storlek. 


Baserat på dina val utför Azure följande lagringskonfigurationsuppgifter när du har skapat den virtuella datorn:

* Skapar och kopplar premium-SSD:er till den virtuella datorn.
* Konfigurerar datadiskarna så att de är tillgängliga för SQL Server.
* Konfigurerar datadiskarna i en lagringspool baserat på de angivna kraven för storlek och prestanda (IOPS och dataflöde).
* Associerar lagringspoolen med en ny enhet på den virtuella datorn.
* Optimerar den nya enheten baserat på din angivna arbetsbelastningstyp (datalagring, transaktionsbearbetning eller allmänt).

Mer information om hur Azure konfigurerar lagringsinställningar finns i [avsnittet Lagringskonfiguration](#storage-configuration). En fullständig genomgång av hur du skapar en virtuell SQL Server-dator i Azure-portalen finns [i etableringshandledningen](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Resurshantera mallar

Om du använder följande Resource Manager-mallar är två premiumdatadiskar kopplade som standard utan konfiguration av lagringspoolen. Du kan dock anpassa dessa mallar för att ändra antalet premiumdatadiskar som är kopplade till den virtuella datorn.

* [Skapa virtuell dator med automatisk säkerhetskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Skapa virtuell dator med automatisk korrigering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Skapa virtuell dator med AKV-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Snabbstartsmall

Du kan använda följande snabbstartsmall för att distribuera en VIRTUELL SQL Server med hjälp av lagringsoptimering. 

* [Skapa virtuell dator med lagringsoptimering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Skapa VM med UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga virtuella SQL Server-datorer kan du ändra vissa lagringsinställningar i Azure-portalen. Öppna [resursen virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)och välj **Översikt**. Sidan ÖVERSIKT över SQL Server visar den aktuella lagringsanvändningen för den virtuella datorn. Alla enheter som finns på den virtuella datorn visas i det här diagrammet. För varje enhet visas lagringsutrymmet i fyra avsnitt:

* SQL-data
* SQL-logg
* Övrigt (icke-SQL-lagring)
* Tillgängligt

Om du vill ändra lagringsinställningarna väljer du **Konfigurera** under **Inställningar**. 

![Konfigurera lagring för befintlig VIRTUELL SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Du kan ändra diskinställningarna för de enheter som konfigurerades under processen för att skapa SQL Server VM. Om du väljer **Utöka enheten** öppnas sidan för enhetsändring, så att du kan ändra disktypen och lägga till ytterligare diskar. 

![Konfigurera lagring för befintlig VIRTUELL SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Storage-konfiguration

Det här avsnittet innehåller en referens för de lagringskonfigurationsändringar som Azure automatiskt utför under SQL VM-etablering eller konfiguration i Azure-portalen.

* Azure konfigurerar en lagringspool från lagring som valts från den virtuella datorn. I nästa avsnitt i det här avsnittet finns information om konfiguration av lagringspooler.
* Automatisk lagringskonfiguration använder alltid [premium SSD](../disks-types.md) P30-datadiskar. Följaktligen finns det en 1:1-mappning mellan det valda antalet Terabyte och antalet datadiskar som är kopplade till den virtuella datorn.

Prisinformation finns på prissidan [För lagring](https://azure.microsoft.com/pricing/details/storage) på fliken **Disklagring.**

### <a name="creation-of-the-storage-pool"></a>Skapande av lagringspoolen

Azure använder följande inställningar för att skapa lagringspoolen på virtuella SQL Server-datorer.

| Inställning | Värde |
| --- | --- |
| Randstorlek |256 KB (Datalagring); 64 KB (transaktions) |
| Diskstorlekar |1 TB vardera |
| Cache |Läsa |
| Allokeringsstorlek |64 KB NTFS allokeringsenhetsstorlek |
| Återställning | Enkel återhämtning (ingen återhämtning) |
| Antal kolumner |Antal datadiskar upp till 8<sup>1</sup> |


<sup>1</sup> När lagringspoolen har skapats kan du inte ändra antalet kolumner i lagringspoolen.


## <a name="workload-optimization-settings"></a>Inställningar för arbetsbelastningsoptimering

I följande tabell beskrivs de tre tillgängliga alternativen för arbetsbelastningstyp och motsvarande optimeringar:

| Typ av arbetsbelastning | Beskrivning | Optimeringar |
| --- | --- | --- |
| **Allmänt** |Standardinställning som stöder de flesta arbetsbelastningar |Ingen |
| **Transaktionsbearbetning** |Optimerar lagringen för traditionella OLTP-arbetsbelastningar för databasen |Spåra flagga 1117<br/>Spåra flagga 1118 |
| **Datalagring** |Optimerar lagringen för analytiska arbetsbelastningar och rapporteringsarbetsbelastningar |Spåra flagga 610<br/>Spåra flagga 1117 |

> [!NOTE]
> Du kan bara ange arbetsbelastningstypen när du etablerar en virtuell SQL-dator genom att välja den i lagringskonfigurationssteget.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).
