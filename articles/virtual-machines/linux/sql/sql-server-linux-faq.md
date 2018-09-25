---
title: SQLServer på Linux Azure Virtual Machines vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om SQL Server körs på virtuella Linux Azure-datorer.
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
ms.openlocfilehash: e8297892c533f3b0126f925f81d3e9bc429828ef
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039963"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Vanliga frågor om SQL Server på Linux Azure Virtual Machines

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på Linux Azure Virtual Machines](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Den här artikeln fokuserar på frågor som är specifika för SQL Server på virtuella Linux-datorer. Om du kör SQL Server på virtuella Windows-datorer finns i den [Windows vanliga frågor och svar](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bilder

1. **Vilken SQL Server-galleriavbildningar av virtuella datorer är tillgängliga?**

   Azure underhåller avbildningar av virtuella datorer för alla stöds större versioner av SQL Server i alla utgåvor för både Linux och Windows. Mer information finns i den fullständiga listan med [Linux VM-avbildningar](sql-server-linux-virtual-machines-overview.md#create) och [Windows VM-avbildningar](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Befintliga SQL Server VM-galleriavbildningar uppdateras?**

   Varje två månaderna, SQL Server-avbildningar i galleriet för virtuella datorer uppdateras med den senaste Linux och Windows-uppdateringar. Detta omfattar de senaste uppdateringarna för system för Linux-avbildningar. Detta inkluderar alla uppdateringar som markeras som viktiga i Windows Update, inklusive viktiga uppdateringar för SQL Server-säkerhet och servicepack för Windows-avbildningar. Kumulativa uppdateringar för SQL Server hanteras annorlunda för Linux och Windows. För Linux ingår kumulativa uppdateringar för SQL Server även i uppdateringen. Men för närvarande uppdateras inte Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Vad relaterade SQL Server-paket installeras också?**

   SQL Server-paket som är installerade som standard på SQL Server Linux-datorer finns i [installerade paket](sql-server-linux-virtual-machines-overview.md#packages).

1. **Kan det få bort SQL Server-avbildningar från galleriet?**

   Ja. Azure underhåller en avbildning per större version och utgåva. Till exempel när ett nytt SQL Server servicepack släpps, lägger Azure till en ny avbildning i galleriet för detta servicepack. SQL Server-avbildning för tidigare servicepack tas omedelbart bort från Azure-portalen. Det är dock fortfarande tillgängligt för etablering från PowerShell för de kommande tre månaderna. Föregående bild av service pack är inte längre tillgänglig efter tre månader. Den här principen för borttagning av skulle gäller även om en SQL Server-version blir stöds inte när den når slutet av sin livscykel.

## <a name="creation"></a>Skapa

1. **Hur skapar jag en Linux Azure-dator med SQL Server?**

   Den enklaste lösningen är att skapa en Linux-dator med SQL Server. En självstudiekurs om registreringen i Azure och skapa en SQL-VM från portalen finns i [etablera en Linux SQL Server-dator i Azure-portalen](provision-sql-server-linux-virtual-machine.md). Du har också möjlighet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad version (utvecklare eller Express) eller genom att återanvända en licens för den lokala. Om du använda din egen licens måste du ha [License Mobility genom Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Varför kan jag lägga till en RHEL eller SLES SQL Server-dator med en Azure-prenumeration som har en utgiftsgräns?**

   RHEL- och SLES-datorer kräver en prenumeration med utan utgiftsgräns samt en verifierad betalningsmetod (vanligtvis ett kreditkort) som är associerade med prenumerationen. Om du etablerar en RHEL eller SLES VM utan att ta bort utgiftsgränsen ska inaktiveras din prenumeration och alla virtuella datorer/tjänster stoppas. Om du kör i det här tillståndet återaktivera prenumerationen [ta bort utgiftsgränsen](https://account.windowsazure.com/subscriptions). Dina återstående krediter återställs för den aktuella faktureringsperioden medan en tilläggsavgift för RHEL eller SLES VM-avbildning skickas kreditkortet om du väljer att starta och fortsätta att köra den.

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierad version av SQL Server på en Azure-dator?**

   Skapa först en Linux endast OS-dator. Kör sedan den [SQL Server-installationsstegen](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) för din Linux-distribution. Om du installerar någon av fritt licensierade versioner av SQL Server, måste du också ha en SQL Server-licens och [License Mobility genom Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Finns det Bring-Your-Own-License (BYOL) Linux virtuella datoravbildningar för SQL Server?**

   Det finns inga BYOL Linux-avbildningar för virtuella datorer för SQL Server för tillfället. Men kan du manuellt installera SQL Server på en virtuell dator endast Linux enligt beskrivningen i föregående frågor.

1. **Kan jag ändra en virtuell dator om du vill använda min egen SQL Server-licens om den har skapats från en användningsbaserad galleriavbildningar?**

   Nej. Du kan inte byta från betala per sekund licensing till att använda din egen licens. Du måste skapa en ny Linux-VM, installera SQL Server och migrera dina data. Se föregående fråga för mer information om hur du aktiverar din egen licens.

## <a name="administration"></a>Administration

1. **Kan jag hantera en Linux SQL Server-dator med SQL Server Management Studio (SSMS)?**

   Ja, men SSMS är för närvarande endast Windows-verktyget. Du måste ansluta via en fjärranslutning från en Windows-dator att använda SSMS med Linux SQL Server-datorer. Lokalt på Linux, den nya [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) verktyget kan utföra många administrativa uppgifter. Ett plattformsoberoende databas-hanteringsverktyg finns [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Kan jag ta bort SQL Server helt från en SQL-VM?**

   Ja, men fortsätter du att debiteras för din SQL-VM, enligt beskrivningen i [Pricing guidance för SQL Server Azure VM](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Om du behöver inte längre SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Du kan sedan ta bort den SQL Server-datorn.

## <a name="updating-and-patching"></a>Uppdatering och uppdatera

1. **Hur uppgraderar jag till en ny version/utgåva av SQL Server i en Azure-dator?**

   För närvarande finns ingen uppgradering på plats för SQL Server som körs i en Azure VM. Skapa en ny Azure-dator med den önskade versionen/utgåvan av SQL Server och migrera sedan databaserna till den nya servern med [standard för datamigrering](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Allmänt

1. **Stöds lösningar för hög tillgänglighet för SQL Server på Azure Virtual Machines?**

   Inte just nu. Always On Tillgänglighetsgrupper och redundanskluster båda kräver en klusterlösningen i Linux, till exempel Pacemaker. De Linux-distributionerna som stöds för SQL Server stöder inte sitt tillägg för hög tillgänglighet i molnet.

## <a name="resources"></a>Resurser

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server Linux VM](provision-sql-server-linux-virtual-machine.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Etablera en virtuell dator med SQL Server Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Vanliga frågor och svar (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)