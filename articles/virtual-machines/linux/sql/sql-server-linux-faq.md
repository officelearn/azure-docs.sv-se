---
title: SQLServer på virtuella datorer i Linux Azure vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor och svar om att köra SQL Server på Azure virtuella Linux-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 56a0629249cdb5f0f098d2b7b6d36b3fbb215009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Vanliga frågor och svar för SQL Server på Linux Azure Virtual Machines

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på Linux Azure Virtual Machines](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Den här artikeln fokuserar på specifika för SQL Server på virtuella Linux-datorer. Om du kör SQL Server på virtuella Windows-datorer finns i [vanliga frågor om Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bilder

1. **Vilken SQL Server-galleriavbildningar av virtuella datorer är tillgängliga?**

   Azure underhåller avbildningar av virtuella datorer för alla stöds större versioner av SQL Server på alla versioner för både Linux och Windows. Mer information finns i den fullständiga listan över [Linux VM-avbildningar](sql-server-linux-virtual-machines-overview.md#create) och [Windows VM-avbildningar](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Uppdateras befintliga galleriavbildningar för SQL Server-virtuella datorn?**

   Varje månad, SQL Server-avbildningar i galleriet för virtuella datorer uppdateras med senaste Linux och Windows-uppdateringar. Detta inkluderar de senaste uppdateringarna för system för Linux-avbildningar. Detta inkluderar alla uppdateringar som markeras viktiga i Windows Update, inklusive viktiga uppdateringar för SQL Server-säkerhet och servicepack för Windows-avbildningar. SQL Server kumulativa uppdateringar hanteras annorlunda för Linux och Windows. För Linux ingår kumulativa uppdateringar för SQL Server även i uppdateringen. Men just nu, uppdateras inte virtuella Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Vad relaterad SQL Server-paket installeras också?**

   SQL Server-paket som installeras som standard på SQL Server Linux virtuella datorer finns i [installerade paket](sql-server-linux-virtual-machines-overview.md#packages).

1. **Kan hämta SQL Server avbildningar av virtuella datorer bort från galleriet?**

   Ja. Azure har endast en bild per högre version och utgåva. Till exempel när ett nytt SQL Server servicepack släpps, Azure lägger till en ny avbildning i galleriet för detta servicepack. SQL Server-avbildning för tidigare servicepack tas omedelbart bort från Azure-portalen. Det är dock fortfarande tillgängligt för att etablera från PowerShell för de kommande tre månaderna. Avbildningen som tidigare service pack är inte längre tillgänglig efter tre månader. Ta bort principen gäller också om en SQL Server-version blir stöds inte när den når slutet av dess livscykel.

## <a name="creation"></a>Skapa

1. **Hur skapar jag en virtuell Linux-Azure-dator med SQL Server**

   Den enklaste lösningen är att skapa en virtuell Linux-dator med SQL Server. En självstudiekurs om registrerar dig för Azure och skapar en SQL-VM från portalen finns [etablera en virtuell dator i Linux SQL Server i Azure portal](provision-sql-server-linux-virtual-machine.md). Du har också alternativet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad version (utvecklare eller Express) eller genom att återanvända en lokal licens. Om du tar din egen licens måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Varför kan jag etablera en RHEL eller SLES SQL Server-VM med en Azure-prenumeration som har en utgiftsgräns?**

   RHEL och SLES virtuella datorer kräver en prenumeration med ingen utgiftsgräns och verifierade betalningsmetod (vanligtvis ett kreditkort) som är associerade med prenumerationen. Om du etablerar en RHEL eller SLES VM utan att ta bort utgiftsgränsen ska inaktiveras din prenumeration och alla virtuella datorer och tjänster stoppas. Om du kör i det här tillståndet kan aktivera prenumerationen igen [ta bort utgiftsgränsen](https://account.windowsazure.com/subscriptions). Kommer att återställas din återstående kredit för den aktuella faktureringsperioden men en RHEL eller SLES VM avbildningen tillägg går mot ditt kreditkort om du vill starta om och fortsätta att köra den.

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierad version av SQL Server på en Azure VM?**

   Först skapa en virtuell Linux OS-only-dator. Kör sedan den [SQL Server-installationssteg](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) för Linux-distribution. Om du installerar en fritt licensierad version av SQL Server, du måste ha en licens för SQL Server och [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Finns det Bring-Your-äger-licens (BYOL) Linux virtuella avbildningar för SQL Server?**

   Det finns inga BYOL Linux virtuella bilder för SQL Server för tillfället. Du kan dock manuellt installera SQL Server på en virtuell dator endast Linux enligt beskrivningen i föregående frågor.

1. **Kan jag ändra en virtuell dator om du vill använda en egen SQL Server-licens om den har skapats från en betalning per användning galleriavbildningar?**

   Nej. Du kan inte växla från per sekund licensiering med din egen licens. Du måste skapa en ny Linux VM, installera SQL Server och migrera dina data. Se föregående fråga mer information om hur du aktiverar din egen licens.

## <a name="administration"></a>Administration

1. **Kan jag hantera en Linux SQL Server-dator med SQL Server Management Studio (SSMS)?**

   Ja, men SSMS är för närvarande endast för Windows-verktyget. Du måste ansluta via en fjärranslutning från en Windows-dator för att använda SSMS med Linux SQL Server-datorer. Lokalt på Linux, för den nya [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) verktyget kan utföra många administrativa uppgifter. Om du vill förhandsgranska plattformsoberoende databashanteringsverktyg finns [SQL Server Operations Studio (förhandsgranskning)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

1. **Kan jag ta bort SQL Server helt från en SQL-VM?**

   Ja, men du kommer att fortsätta debiteras för din SQL-VM enligt beskrivningen i [priser för SQL Server Azure Virtual Machines](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Om du behöver inte längre SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Du kan ta bort den virtuella datorn för SQL Server.

## <a name="updating-and-patching"></a>Uppdatering och korrigering

1. **Hur uppgraderar till en ny version/utgåva av SQL Server i en Azure VM?**

   Det finns för närvarande ingen uppgradering på plats för SQL Server som körs i en Azure VM. Skapa en ny Azure virtuell dator med den önskade SQL Server-version/utgåvan och sedan migrera dina databaser till en ny server med hjälp av [standarddata migrering tekniker](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Allmänt

1. **Stöds lösningar för hög tillgänglighet för SQL Server på virtuella Azure-datorer?**

   Inte just nu. Always On-Tillgänglighetsgrupper och redundanskluster båda kräver klusterlösningen i Linux, till exempel Pacemaker. De Linux-distributioner som stöds för SQL Server stöder inte sitt tillägg för hög tillgänglighet i molnet.

## <a name="resources"></a>Resurser

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server-VM för Linux](provision-sql-server-linux-virtual-machine.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Virtuella Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Etablera en virtuell dator med SQL Server Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Vanliga frågor och svar (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)