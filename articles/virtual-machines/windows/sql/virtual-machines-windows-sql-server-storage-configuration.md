---
title: "Konfiguration för lagring för virtuella SQL Server-datorer | Microsoft Docs"
description: "Det här avsnittet beskrivs hur Azure konfigurerar lagring för virtuella SQL Server-datorer under etableringen (Resource Manager-modellen). Här beskrivs också hur du konfigurerar lagring för din befintliga SQL Server-datorer."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 7d076b970481b68d9c352d54f3452b8e222f5c64
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfiguration för lagring för virtuella SQL Server-datorer
När du konfigurerar en avbildning av virtuell dator för SQL Server i Azure hjälper portalen till att automatisera konfigurationen för lagring. Detta inkluderar ansluter lagringsenheter till den virtuella datorn, vilket gör att lagring tillgänglig för SQL Server och konfigurera den att optimera för dina specifika krav.

Det här avsnittet beskrivs hur Azure konfigurerar lagring för din SQL Server-datorer både under etableringen och för befintliga virtuella datorer. Den här konfigurationen baseras på den [prestandarelaterade metodtips](virtual-machines-windows-sql-performance.md) för Azure virtuella datorer som kör SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Krav
Om du vill använda inställningarna för automatisk lagring, kräver den virtuella datorn följande egenskaper:

* Etablerade med en [bild av SQL Server-galleriet](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Använder den [Resource Manager-distributionsmodellen](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Använder [Premiumlagring](../premium-storage.md).

## <a name="new-vms"></a>Nya virtuella datorer
I följande avsnitt beskrivs hur du konfigurerar lagring för nya virtuella datorer för SQL Server.

### <a name="azure-portal"></a>Azure Portal
Etablera en virtuell Azure-dator med en SQL Server-galleriet bild kan du konfigurera lagring för den nya virtuella datorn automatiskt. Du kan ange lagringsstorlek, prestandabegränsningarna och Arbetsbelastningstyp. Följande skärmbild visar bladet Storage konfiguration används under SQL VM etablering.

![Konfigurera SQL Server VM lagring under etableringen.](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Azure utför följande konfigurationsuppgifterna för lagring när du har skapat den virtuella datorn baserat på dina val:

* Skapar och bifogar premium lagringsdiskar data till den virtuella datorn.
* Konfigurerar datadiskar för att komma åt SQL-servern.
* Konfigurerar datadiskar i lagringspoolen baserat på de angivna storlek och prestanda (IOPS och genomströmning) krav.
* Associerar lagringspoolen med en ny enhet på den virtuella datorn.
* Optimerar nya enheten baserat på angivna belastningstyp (datalagring, överföringsprocesser eller Allmänt).

Mer information om hur Azure konfigurerar inställningar för lagring finns i [lagring konfigurationsavsnittet](#storage-configuration). En fullständig genomgång av hur du skapar en SQL Server-VM i Azure Portal finns [självstudiekursen om etablering](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Resursen hantera mallar
Om du använder följande Resource Manager-mallar är kopplade två diskar för premium-data som standard med ingen konfigurationen för lagringspooler. Du kan dock anpassa mallarna för att ändra antalet premiumdiskar för data som är kopplade till den virtuella datorn.

* [Skapa virtuell dator med automatisk säkerhetskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Skapa virtuell dator med inställningen automatisk uppdatering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Skapa virtuell dator med AKV-integreringen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Befintliga virtuella datorer
För befintliga SQL Server virtuella datorer kan ändra du vissa Lagringsinställningar i Azure-portalen. Välj den virtuella datorn, gå till området Inställningar och välj SQL Server-konfigurationsfilen. Konfiguration av SQL Server-bladet visar den nuvarande lagringsanvändningen av den virtuella datorn. Alla enheter som finns på den virtuella datorn visas i det här diagrammet. För varje enhet visar lagringsutrymmet i fyra avsnitt:

* SQL-data
* SQL-logg
* Andra (icke-SQL-lagring)
* Tillgänglig

![Konfigurera lagring för befintliga SQLServer-dator](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Klicka på Redigera om du vill konfigurera lagring för att lägga till en ny enhet eller utöka en befintlig enhet ovanför diagrammet.

De konfigurationsalternativ som du ser varierar beroende på om du har använt funktionen innan. När du använder för första gången måste ange du din lagringskraven för en ny enhet. Om du tidigare har använt funktionen för att skapa en enhet kan du utöka lagring för den här enheten.

### <a name="use-for-the-first-time"></a>Använd för första gången
Om det är första gången du använder den här funktionen kan du ange lagringsgränser för storlek och prestanda för en ny enhet. Det här upplevelsen liknar vad som visas vid etablering tid. Den största skillnaden är att du inte får ange vilken Arbetsbelastningstyp. Den här begränsningen kan störa eventuella befintliga SQL Server-konfigurationer på den virtuella datorn.

![Konfigurera SQL Server Storage skjutreglage](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure skapar en ny enhet baserat på dina specifikationer. I det här scenariot utför Azure storage följande konfigurationsåtgärder:

* Skapar och bifogar premium lagringsdiskar data till den virtuella datorn.
* Konfigurerar datadiskar för att komma åt SQL-servern.
* Konfigurerar datadiskar i lagringspoolen baserat på de angivna storlek och prestanda (IOPS och genomströmning) krav.
* Associerar lagringspoolen med en ny enhet på den virtuella datorn.

Mer information om hur Azure konfigurerar inställningar för lagring finns i [lagring konfigurationsavsnittet](#storage-configuration).

### <a name="add-a-new-drive"></a>Lägg till en ny enhet
Om du redan har konfigurerat lagring på SQL Server-VM öppnar expanderande lagring två nya alternativ. Det första alternativet är att lägga till en ny enhet som kan öka prestanda på den virtuella datorn.

![Lägg till en ny enhet till en SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

När du lägger till enheten måste du utföra vissa extra manuell konfiguration för att uppnå prestandaökning.

### <a name="extend-the-drive"></a>Utöka enheten
Ett annat alternativ för att expandera lagring är att utöka den befintliga enheten. Det här alternativet ökar tillgängligt lagringsutrymme för enheten, men det ökar inte prestanda. Du kan inte ändra antalet kolumner när lagringspoolen har skapats med lagringspooler. Antalet kolumner som anger antalet parallella skrivningar, som kan vara stripe över datadiskar. Därför kan inte några datadiskar som lagts till öka prestanda. De kan bara ange mer lagringsutrymme för data som skrivs. Den här begränsningen innebär också att antalet kolumner när du utökar enheten anger det minsta antalet datadiskar som du kan lägga till. Om du skapar en lagringspool med fyra datadiskar är antalet kolumner därför också fyra. När du utökar lagringen, måste du lägga till minst fyra datadiskar.

![Utöka en enhet för en SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Storage-konfiguration
Det här avsnittet innehåller en referens för att konfigurationsändringarna för lagring som Azure utför automatiskt under SQL VM etablering eller konfigurationen för Azure-portalen.

* Om du har valt färre än två TBs lagring för den virtuella datorn, Azure inte att skapa en lagringspool.
* Om du har valt minst två TBs lagring för den virtuella datorn, konfigurerar en lagringspool i Azure. Nästa avsnitt i det här avsnittet innehåller information om konfigurationen för lagringspooler.
* Automatisk alltid lagringskonfiguration använder [premiumlagring](../premium-storage.md) P30 datadiskar. Det är därför en 1:1-mappning mellan din valda antalet terabyte och antalet datadiskar som är kopplade till den virtuella datorn.

Information om priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/storage) sida på den **disklagring** fliken.

### <a name="creation-of-the-storage-pool"></a>Skapandet av lagringspoolen
Azure har följande inställningar för att skapa lagringspoolen på SQL Server-datorer.

| Inställning | Värde |
| --- | --- |
| Stripe-storlek |256 KB (datalagring;) 64 KB (transaktionell) |
| Diskstorlekar |1 TB |
| Cache |Läsa |
| Allokeringsstorlek |Storlek på 64 KB NTFS allokeringsenhet |
| Snabbmeddelanden filen initiering |Enabled |
| Låsa sidor i minnet |Enabled |
| Återställning |Enkel återställning (ingen återhämtning) |
| Antal kolumner |Antalet datadiskar<sup>1</sup> |
| TempDB-plats |Lagras på datadiskar<sup>2</sup> |

<sup>1</sup> när lagringspoolen har skapats kan du inte ändra antalet kolumner i lagringspoolen.

<sup>2</sup> den här inställningen gäller bara den första enheten som du skapar med konfigurationen för lagring.

## <a name="workload-optimization-settings"></a>Inställningar för optimering av arbetsbelastning
I följande tabell beskrivs tre arbetsbelastning typen alternativen och deras motsvarande optimeringar:

| Arbetsbelastningstyp | Beskrivning | Optimeringar |
| --- | --- | --- |
| **Allmänt** |Standardinställning som stöder de flesta arbetsbelastningar |Ingen |
| **Transaktionell bearbetning** |Optimerar lagringen för traditionella OLTP-arbetsbelastningar |Spårningsflagga 1117<br/>Spårningsflagga 1118 |
| **Datalagring** |Optimerar lagringen för analys- och rapporteringsarbetsbelastningar |Spårningsflagga 610<br/>Spårningsflagga 1117 |

> [!NOTE]
> Du kan bara ange vilken arbetsbelastning när du etablerar en virtuell SQL-dator genom att markera den i konfigurationssteget för lagring.
>
>

## <a name="next-steps"></a>Nästa steg
Andra avsnitt relaterade till SQL Server som körs i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
