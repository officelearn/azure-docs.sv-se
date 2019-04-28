---
title: Lagringskonfiguration för SQL Server VM | Microsoft Docs
description: Det här avsnittet beskrivs hur Azure konfigurerar lagring för SQL Server-datorer under etableringen (Resource Manager-distributionsmodellen). Den förklarar hur du konfigurerar lagring för dina befintliga SQL Server-datorer.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: da850b8ff9174fa310c5247cd7e99af69db28a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477454"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Lagringskonfiguration för SQL Server-datorer

När du konfigurerar en SQL Server VM-avbildning i Azure portalen som hjälper dig för att automatisera din konfiguration för lagring. Detta inkluderar ansluter lagringsenheter till den virtuella datorn, vilket gör att lagring tillgänglig för SQL Server och konfigurera den för att optimera för dina specifika prestandakrav.

Det här avsnittet förklarar hur Azure konfigurerar lagring för dina SQL Server-datorer både under etableringen och för befintliga virtuella datorer. Den här konfigurationen baseras på den [prestandametodtips](virtual-machines-windows-sql-performance.md) för virtuella Azure-datorer som kör SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda inställningarna för automatisk lagring, kräver den virtuella datorn följande egenskaper:

* Etablerade med en [SQL Server-galleriet bilden](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Använder den [Resource Manager-distributionsmodellen](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Använder [premium SSD](../disks-types.md).

## <a name="new-vms"></a>Nya virtuella datorer

I följande avsnitt beskrivs hur du konfigurerar lagring för nya SQL Server-datorer.

### <a name="azure-portal"></a>Azure Portal

När du etablerar en Azure-dator med hjälp av en SQL Server-avbildning i galleriet, kan du välja att automatiskt konfigurera lagring för den nya virtuella datorn. Du kan ange lagringsstorlek, prestandabegränsningar och typer av arbetsbelastningar. Följande skärmbild visar bladet Storage används under SQL VM etablering.

![Lagringskonfiguration för SQL Server VM under etableringen](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Azure utför baserat på dina val, följande konfigurationsuppgifterna för lagring när du har skapat den virtuella datorn:

* Skapar och bifogar premium SSD: er till den virtuella datorn.
* Konfigurerar datadiskar för att vara tillgänglig för SQL Server.
* Konfigurerar datadiskar i en lagringspool baserat på de angivna storlek och prestanda (IOPS och dataflöde).
* Kopplar lagringspoolen till en ny enhet på den virtuella datorn.
* Optimerar den här nya enheten baserat på ditt angivna Arbetsbelastningstyp (datalagring, överföringsprocesser eller Allmänt).

Mer information om hur konfigurerar Azure storage-inställningar finns i den [Storage konfigurationsavsnittet](#storage-configuration). En genomgång av hur du skapar en SQL Server VM i Azure Portal, se [självstudiekursen om etablering](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Hantera resursmallar

Om du använder följande Resource Manager-mallar, kopplas två diskar för premium-data som standard med ingen konfigurering av lagringspool. Du kan anpassa mallarna för att ändra antalet premiumdiskar för data som är kopplade till den virtuella datorn.

* [Skapa virtuell dator med automatisk säkerhetskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Skapa virtuell dator med inställningen automatisk uppdatering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Skapa virtuell dator med AKV-integreringen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Befintliga virtuella datorer

För befintliga SQL Server-datorer, kan du ändra vissa Lagringsinställningar i Azure-portalen. Välj den virtuella datorn, gå till området Inställningar och välj sedan SQL Server-konfiguration. Konfiguration av SQL Server-bladet visar aktuella lagringsanvändningen för den virtuella datorn. Alla enheter som finns på den virtuella datorn visas i det här diagrammet. För varje enhet visar lagringsutrymmet i fyra delar:

* SQL-data
* SQL-logg
* Andra (icke-SQL-lagring)
* Tillgängligt

![Konfigurera lagring för befintliga SQLServer-dator](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Om du vill konfigurera lagring för att lägga till en ny enhet eller utöka en befintlig enhet, klickar du på länken Redigera ovanför diagrammet.

De konfigurationsalternativ som du ser varierar beroende på om du har använt den här funktionen innan. När du använder för första gången måste ange du din lagringskraven för en ny enhet. Om du tidigare har använt den här funktionen för att skapa en enhet kan du utöka lagring för den enheten.

### <a name="use-for-the-first-time"></a>Använd för första gången

Om det är första gången du använder den här funktionen kan du ange lagringsgränserna för storlek och prestanda för en ny enhet. Detta liknar vad som skulle visas på etableringstid. Den största skillnaden är att du inte har behörighet att ange vilken Arbetsbelastningstyp. Den här begränsningen förhindrar att störa eventuella befintliga SQL Server-konfigurationer på den virtuella datorn.

![Konfigurera SQL Server Storage skjutreglage](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure skapar en ny enhet baserat på dina specifikationer. Azure utför följande konfigurationsuppgifterna för lagring i det här scenariot:

* Skapar och bifogar premium-lagringsdiskar data till den virtuella datorn.
* Konfigurerar datadiskar för att vara tillgänglig för SQL Server.
* Konfigurerar datadiskar i en lagringspool baserat på de angivna storlek och prestanda (IOPS och dataflöde).
* Kopplar lagringspoolen till en ny enhet på den virtuella datorn.

Mer information om hur konfigurerar Azure storage-inställningar finns i den [Storage konfigurationsavsnittet](#storage-configuration).

### <a name="add-a-new-drive"></a>Lägg till en ny enhet

Om du redan har konfigurerat lagring på SQL Server-dator, öppnar Expandera lagring två nya alternativ. Det första alternativet är att lägga till en ny enhet, vilket kan öka prestandanivå för den virtuella datorn.

![Lägg till en ny enhet till en SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

När du lägger till enheten måste du utföra några extra manuell konfiguration för att uppnå ökade prestanda.

### <a name="extend-the-drive"></a>Utöka enheten

Ett annat alternativ för att expandera lagring är att utöka den befintliga enheten. Det här alternativet ökar tillgängligt lagringsutrymme för enheten, men det ökar inte prestanda. Du kan inte ändra antalet kolumner när lagringspoolen har skapats med lagringspooler. Antalet kolumner som anger antalet parallella skrivningar, som kan vara stripe över datadiskar. Därför kan inte eventuella datadiskar som har lagts till öka prestanda. Användaren kan bara ange mer lagringsutrymme för data som skrivs. Den här begränsningen innebär också att antalet kolumner när du utökar enheten, anger det minsta antalet datadiskar som du kan lägga till. Så om du skapar en lagringspool med fyra datadiskar, är antalet kolumner också fyra. Vill du utöka lagring, måste du lägga till minst fyra datadiskar.

![Utöka en enhet för en SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Storage-konfiguration

Det här avsnittet innehåller en referens för att konfigurationsändringarna för lagring som Azure utför automatiskt när SQL VM-etableringstillstånd eller konfigureras i Azure Portal.

* Om du har valt färre än två TB lagring för den virtuella datorn, Azure inte att skapa en lagringspool.
* Om du har valt minst två TB lagring för den virtuella datorn, konfigurerar en lagringspool i Azure. Nästa avsnitt i det här avsnittet innehåller information om konfigurationen för lagringspooler.
* Konfigurationen för automatisk lagring alltid använder [premium SSD](../disks-types.md) P30 datadiskar. Det är därför en 1:1-mappning mellan det valda antalet terabyte och antalet diskar som är anslutna till den virtuella datorn.

Information om priser finns i den [priser för Storage](https://azure.microsoft.com/pricing/details/storage) sidan på den **disklagring** fliken.

### <a name="creation-of-the-storage-pool"></a>Skapandet av lagringspoolen

Azure använder du följande inställningar för att skapa lagringspoolen på SQL Server-datorer.

| Inställning | Värde |
| --- | --- |
| Stripe-storlek |256 KB (datalager;) 64 KB (för transaktioner) |
| Diskstorlekar |1 TB |
| Cache |Läsa |
| Allokeringsstorlek |Storlek på 64 KB NTFS-allokeringsenhet |
| Initieringen av omedelbar fil |Enabled |
| Låsa sidor i minnet |Enabled |
| Återställning |Enkel återställning (ingen återhämtning) |
| Antal kolumner |Antal datadiskar<sup>1</sup> |
| TempDB-plats |Lagras på datadiskar<sup>2</sup> |

<sup>1</sup> när lagringspoolen har skapats kan du kan inte ändra antalet kolumner i lagringspoolen.

<sup>2</sup> den här inställningen gäller bara den första enheten som du skapar med storage-konfigurationen.

## <a name="workload-optimization-settings"></a>Inställningar för optimering av arbetsbelastning

I följande tabell beskrivs de tre arbetsbelastning typ tillgängliga alternativen och deras motsvarande optimeringar:

| Typ av arbetsbelastning | Beskrivning | Optimeringar |
| --- | --- | --- |
| **Allmänt** |Standardinställningen som har stöd för de flesta arbetsbelastningar |Ingen |
| **Transaktionsbearbetning** |Optimerar lagringen för traditionella OLTP databasarbetsbelastningar |Spårningsflaggan 1117<br/>Spårningsflaggan 1118 |
| **Datalagring** |Optimerar lagringen för analys- och rapporteringsarbetsbelastningar |Spårningsflaggan 610<br/>Spårningsflaggan 1117 |

> [!NOTE]
> Du kan bara ange typen av arbetsbelastningar när du etablerar en virtuell SQL-dator genom att välja den i lagring konfigurationssteg.

## <a name="next-steps"></a>Nästa steg

Andra ämnen som rör som kör SQL Server i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
