---
title: Vanliga frågor och svar om SQL Server på virtuella Linux Azure-datorer | Microsoft-dokument
description: Den här artikeln innehåller svar på vanliga frågor om hur du kör SQL Server på virtuella Linux Azure-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1e729c608a2cad28c810f8d5236360c909a496b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70082034"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Vanliga frågor och svar för SQL Server på virtuella Linux Azure-datorer

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om hur du kör [SQL Server på Virtuella Linux Azure-datorer](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Den här artikeln fokuserar på frågor som är specifika för SQL Server på virtuella Linux-datorer. Om du kör SQL Server på Virtuella Datorer i Windows läser du [vanliga frågor och svar om Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Bilder

1. **Vilka sql server-galleriavbildningar för virtuella datorer är tillgängliga?**

   Azure underhåller virtuella datoravbildningar för alla större versioner av SQL Server som stöds på alla versioner för både Linux och Windows. Mer information finns i den fullständiga listan över [Linux VM-avbildningar](sql-server-linux-virtual-machines-overview.md#create) och [Windows VM-avbildningar](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Uppdateras befintliga virtuella sql server-galleriavbildningar?**

   Varannan månad uppdateras SQL Server-avbildningar i galleriet för virtuella datorer med de senaste Linux- och Windows-uppdateringarna. För Linux-avbildningar inkluderar detta de senaste systemuppdateringarna. För Windows-avbildningar innehåller detta alla uppdateringar som är markerade som viktiga i Windows Update, inklusive viktiga SQL Server-säkerhetsuppdateringar och Service Pack-enheter. Kumulativa SQL Server-uppdateringar hanteras på olika sätt för Linux och Windows. För Linux ingår även kumulativa SQL Server-uppdateringar i uppdateringen. Men för närvarande uppdateras inte Windows virtuella datorer med kumulativa uppdateringar för SQL Server eller Windows Server.

1. **Vilka relaterade SQL Server-paket är också installerade?**

   Information om hur du ser de SQL Server-paket som installeras som standard på virtuella SQL Server Linux-datorer finns [i Installerade paket](sql-server-linux-virtual-machines-overview.md#packages).

1. **Kan SQL Server virtuella datoravbildningar tas bort från galleriet?**

   Ja. Azure underhåller bara en avbildning per huvudversion och utgåva. När ett nytt SERVICE Pack från SQL Server släpps lägger Azure till en ny avbildning i galleriet för Service Pack.For example, when a new SQL Server Service Pack is released, Azure adds a new image to the gallery for that Service Pack. SQL Server-avbildningen för det tidigare Service Pack-paketet tas omedelbart bort från Azure-portalen. Den är dock fortfarande tillgänglig för etablering från PowerShell för de kommande tre månaderna. Efter tre månader är den tidigare Service Pack-avbildningen inte längre tillgänglig. Den här borttagningsprincipen skulle också gälla om en SQL Server-version inte stöds när den når slutet av livscykeln.

## <a name="creation"></a>Skapa

1. **Hur skapar jag en virtuell Linux Azure-dator med SQL Server?**

   Den enklaste lösningen är att skapa en Virtuell Linux-dator som innehåller SQL Server. En självstudiekurs om hur du registrerar dig för Azure och skapar en VIRTUELL SQL-dator från portalen finns [i Etablera en virtuell Linux SQL Server-dator i Azure-portalen](provision-sql-server-linux-virtual-machine.md). Du har också möjlighet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad utgåva (Utvecklare eller Express) eller genom att återanvända en lokal licens. Om du tar med din egen licens måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Varför kan jag inte etablera en virtuell RHEL- eller SLES SQL Server-dator med en Azure-prenumeration som har en utgiftsgräns?**

   Virtuella RHEL- och SLES-datorer kräver en prenumeration utan utgiftsgräns och en verifierad betalningsmetod (vanligtvis ett kreditkort) som är associerat med prenumerationen. Om du etablerar en virtuell RHEL- eller SLES-dator utan att ta bort utgiftsgränsen inaktiveras prenumerationen och alla virtuella datorer/tjänster stoppas. Om du stöter på det här tillståndet tar du bort utgiftsgränsen på [en ny aktivering](https://account.windowsazure.com/subscriptions). Dina återstående krediter kommer att återställas för den aktuella faktureringsperioden, men en RHEL eller SLES VM-bildtillägg går emot ditt kreditkort om du väljer att starta om och fortsätta köra det.

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Skapa först en virtuell linux-os-dator. Kör sedan [installationsstegen](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) för SQL Server för din Linux-distribution. Om du inte installerar en av de fritt licensierade utgåvorna av SQL Server måste du också ha en SQL Server-licens och [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Finns det Bring-Your-Own-License (BYOL) Linux virtuella maskin avbildningar för SQL Server?**

   För närvarande finns det inga BYOL Linux virtuella datoravbildningar för SQL Server. Du kan dock manuellt installera SQL Server på en virtuell Linux-dator som beskrivs i de tidigare frågorna.

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Nej. Du kan inte växla från pay-per-second-licensiering till att använda din egen licens. Du måste skapa en ny Virtuell Linux-dator, installera SQL Server och migrera dina data. Se föregående fråga för mer information om att ta med din egen licens.

## <a name="administration"></a>Administration

1. **Kan jag hantera en virtuell Linux SQL Server-dator med SQL Server Management Studio (SSMS)?**

   Ja, men SSMS är för närvarande ett Windows-verktyg. Du måste fjärransluta från en Windows-dator för att kunna använda SSMS med virtuella Linux SQL Server-datorer. Lokalt på Linux kan det nya [mssql-conf-verktyget](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) utföra många administrativa uppgifter. Ett verktyg för hantering av databaser över flera plattformar finns i [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Kan jag ta bort SQL Server helt från en virtuell SQL-dator?**

   Ja, men du kommer att fortsätta att debiteras för din VIRTUELLA SQL-dator enligt beskrivningen i [prisvägledning för virtuella SQL Server Azure-datorer](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Om du inte längre behöver SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Sedan kan du ta bort den virtuella SQL Server-datorn.

## <a name="updating-and-patching"></a>Uppdatering och korrigering

1. **Hur uppgraderar jag till en ny version/utgåva av SQL Server i en virtuell Azure-dator?**

   För närvarande finns det ingen uppgradering på plats för SQL Server som körs på en virtuell dator i Azure. Skapa en ny virtuell Azure-dator med önskad SQL Server-version/utgåva och migrera sedan databaserna till den nya servern med hjälp av [standarddatamigreringstekniker](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Allmänt

1. **Stöds lösningar för hög tillgänglighet för SQL Server på virtuella Azure-datorer?**

   Inte just nu. Alltid på tillgänglighetsgrupper och redundanskluster kräver båda en klusterlösning i Linux, till exempel Pacemaker. De Linuxdistributioner som stöds för SQL Server stöder inte tillägg för hög tillgänglighet i molnet.

## <a name="resources"></a>Resurser

**Virtuella Linux-datorer:**

* [Översikt över SQL Server på en virtuell Linux-dator](sql-server-linux-virtual-machines-overview.md)
* [Etablera en VIRTUELL SQL Server Linux-dator](provision-sql-server-linux-virtual-machine.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Virtuella datorer i Windows:**

* [Översikt över SQL Server på en Virtuell Windows-dator](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Etablera en VIRTUELL SQL Server Windows-dator](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Vanliga frågor och svar (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)