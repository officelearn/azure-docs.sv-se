---
title: Lagrings konfiguration för SQL Server virtuella datorer | Microsoft Docs
description: I det här avsnittet beskrivs hur Azure konfigurerar lagring för SQL Server virtuella datorer under etableringen (Resource Manager distributions modell). Det förklarar också hur du kan konfigurera lagring för befintliga SQL Server virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 5a8b6c9885099ed549f7f29d7f2096de5983d9d0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100385"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Lagrings konfiguration för SQL Server virtuella datorer

När du konfigurerar en SQL Server avbildning av virtuella datorer i Azure kan portalen automatisera lagrings konfigurationen. Detta innefattar att koppla lagring till den virtuella datorn, göra lagringen tillgänglig för SQL Server och konfigurera den för att optimera för dina särskilda prestanda krav.

I det här avsnittet beskrivs hur Azure konfigurerar lagring för dina SQL Server virtuella datorer både under etablering och för befintliga virtuella datorer. Den här konfigurationen baseras på [metod tips för prestanda](virtual-machines-windows-sql-performance.md) för virtuella Azure-datorer som kör SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda de automatiserade konfigurations inställningarna för lagring måste den virtuella datorn ha följande egenskaper:

* Etablerad med en [SQL Server galleri bild](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Använder [distributions modellen för Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Använder [Premium-SSD](../disks-types.md).

## <a name="new-vms"></a>Nya virtuella datorer

I följande avsnitt beskrivs hur du konfigurerar lagring för nya SQL Server virtuella datorer.

### <a name="azure-portal"></a>Azure Portal

När du konfigurerar en virtuell Azure-dator med hjälp av en SQL Server Galleri avbildning kan du välja att automatiskt konfigurera lagringen för den nya virtuella datorn. Du anger lagrings storlek, prestanda begränsningar och arbets belastnings typ. Följande skärm bild visar bladet lagrings konfiguration som används vid etablering av SQL VM.

![SQL Server VM lagrings konfiguration under etableringen](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Baserat på dina val utför Azure följande konfigurations åtgärder för lagring när du har skapat den virtuella datorn:

* Skapar och kopplar Premium-SSD till den virtuella datorn.
* Konfigurerar data diskarna så att de kan nås SQL Server.
* Konfigurerar data diskarna i en lagringspool baserat på de angivna kraven för storlek och prestanda (IOPS och data flöde).
* Kopplar lagringspoolen till en ny enhet på den virtuella datorn.
* Optimerar den här nya enheten baserat på din angivna arbets belastnings typ (data lager, transaktionell bearbetning eller allmänt).

Mer information om hur Azure konfigurerar lagrings inställningar finns i [avsnittet lagrings konfiguration](#storage-configuration). En fullständig genom gång av hur du skapar en SQL Server VM i Azure Portal finns i vägledningen för [etablering](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Resurs hanterings mallar

Om du använder följande Resource Manager-mallar bifogas två Premium-datadiskar som standard, utan konfiguration av lagringspool. Du kan dock anpassa dessa mallar om du vill ändra antalet Premium-datadiskar som är anslutna till den virtuella datorn.

* [Skapa en virtuell dator med automatisk säkerhets kopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Skapa en virtuell dator med automatisk uppdatering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Skapa en virtuell dator med AKV-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer kan du ändra vissa lagrings inställningar i Azure Portal. Öppna din [resurs för virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)och välj **Översikt**. På sidan SQL Server översikt visas den aktuella lagrings användningen för den virtuella datorn. Alla enheter som finns på den virtuella datorn visas i det här diagrammet. För varje enhet visas lagrings utrymmet i fyra delar:

* SQL-data
* SQL-logg
* Annat (icke-SQL-lagring)
* Tillgängligt

Om du vill ändra lagrings inställningarna väljer du **Konfigurera** under **Inställningar**. 

![Konfigurera lagring för befintliga SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

De konfigurations alternativ som visas varierar beroende på om du har använt den här funktionen tidigare. När du använder för första gången kan du ange dina lagrings krav för en ny enhet. Om du tidigare använde den här funktionen för att skapa en enhet kan du välja att utöka enhetens lagring.

### <a name="use-for-the-first-time"></a>Använd för första gången

Om det är första gången du använder den här funktionen kan du ange lagrings storlek och prestanda begränsningar för en ny enhet. Den här upplevelsen liknar vad du kan se vid etablerings tiden. Den största skillnaden är att du inte har behörighet att ange arbets belastnings typ. Denna begränsning förhindrar att befintliga SQL Server konfigurationer avbryts på den virtuella datorn.

Azure skapar en ny enhet utifrån dina specifikationer. I det här scenariot utför Azure följande uppgifter för lagrings konfiguration:

* Skapar och kopplar data diskar för Premium Storage till den virtuella datorn.
* Konfigurerar data diskarna så att de kan nås SQL Server.
* Konfigurerar data diskarna i en lagringspool baserat på de angivna kraven för storlek och prestanda (IOPS och data flöde).
* Kopplar lagringspoolen till en ny enhet på den virtuella datorn.

Mer information om hur Azure konfigurerar lagrings inställningar finns i [avsnittet lagrings konfiguration](#storage-configuration).

### <a name="add-a-new-drive"></a>Lägg till en ny enhet

Om du redan har konfigurerat lagring på SQL Server VM kan du utöka lagringen med två nya alternativ. Det första alternativet är att lägga till en ny enhet, vilket kan öka prestanda nivån för den virtuella datorn.

När du har lagt till enheten måste du dock utföra en extra manuell konfiguration för att uppnå prestanda ökningen.

### <a name="extend-the-drive"></a>Utöka enheten

Det andra alternativet för att utöka lagringen är att utöka den befintliga enheten. Det här alternativet ökar det tillgängliga lagrings utrymmet för enheten, men det ökar inte prestandan. Med lagringspooler kan du inte ändra antalet kolumner efter det att lagringspoolen har skapats. Antalet kolumner bestämmer antalet parallella skrivningar som kan vara stripe över data diskarna. Därför kan inga tillagda data diskar öka prestandan. De kan bara ge mer lagrings utrymme för de data som skrivs. Den här begränsningen innebär också att när du utökar enheten bestämmer antalet kolumner det minsta antalet data diskar som du kan lägga till. Så om du skapar en lagringspool med fyra data diskar är antalet kolumner också fyra. Varje gång du utökar lagringen måste du lägga till minst fyra data diskar.

![Utöka en enhet för en virtuell SQL-dator](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Storage-konfiguration

Det här avsnittet innehåller en referens för de lagrings konfigurations ändringar som Azure utför automatiskt vid etablering eller konfiguration av SQL VM i Azure Portal.

* Om du har valt färre än två TBs-lagring för den virtuella datorn skapar inte Azure en lagringspool.
* Om du har valt minst två TBs för lagring för den virtuella datorn konfigurerar Azure en lagringspool. Nästa avsnitt av det här avsnittet innehåller information om konfigurationen av lagringspoolen.
* Automatisk lagrings konfiguration använder alltid [Premium SSD](../disks-types.md) P30-datadiskar. Det finns därför en 1:1-mappning mellan det valda antalet terabyte och antalet data diskar som är anslutna till den virtuella datorn.

Information om priser finns på sidan för [lagrings priser](https://azure.microsoft.com/pricing/details/storage) på fliken **disklagring** .

### <a name="creation-of-the-storage-pool"></a>Skapandet av lagringspoolen

Azure använder följande inställningar för att skapa lagringspoolen på SQL Server virtuella datorer.

| Inställning | Value |
| --- | --- |
| Rand storlek |256 KB (data lager hantering); 64 KB (transaktion) |
| Diskstorlekar |1 TB varje |
| Cache |Läsa |
| Fördelnings storlek |storlek för 64 KB NTFS-allokeringsenhet |
| Snabb fil initiering |Aktiverad |
| Låsa sidor i minnet |Aktiverad |
| Återställning |Enkel återställning (ingen återhämtning) |
| Antal kolumner |Antal data diskar<sup>1</sup> |
| TempDB-plats |Lagrat på data diskar<sup>2</sup> |

<sup>1</sup> när lagringspoolen har skapats kan du inte ändra antalet kolumner i lagringspoolen.

<sup>2</sup> den här inställningen gäller endast den första enhet som du skapar med hjälp av lagrings konfigurations funktionen.

## <a name="workload-optimization-settings"></a>Inställningar för arbets belastnings optimering

I följande tabell beskrivs de tre tillgängliga alternativen för arbets belastnings typer och deras motsvarande optimeringar:

| Typ av arbetsbelastning | Beskrivning | Optimeringar |
| --- | --- | --- |
| **Allmänt** |Standardinställning som stöder de flesta arbets belastningar |Inga |
| **Transaktionell bearbetning** |Optimerar lagringen för traditionella databas OLTP-arbetsbelastningar |Spårnings flagga 1117<br/>Spårnings flagga 1118 |
| **Datalagring** |Optimerar lagringen för analys-och rapporterings arbets belastningar |Spårnings flagga 610<br/>Spårnings flagga 1117 |

> [!NOTE]
> Du kan bara ange arbets belastnings typen när du etablerar en virtuell SQL-dator genom att välja den i steget lagrings konfiguration.

## <a name="next-steps"></a>Nästa steg

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på Azure-Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
