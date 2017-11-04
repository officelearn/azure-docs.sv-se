---
title: "SQLServer på virtuella datorer i Linux Azure vanliga frågor och svar | Microsoft Docs"
description: "Den här artikeln innehåller svar på vanliga frågor och svar om att köra SQL Server på Azure virtuella Linux-datorer."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Vanliga frågor och svar för SQL Server på Linux Azure Virtual Machines

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på Linux Azure Virtual Machines](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Det här avsnittet fokuserar på specifika för SQL Server på virtuella Linux-datorer. Om du kör SQL Server på virtuella Windows-datorer finns i [vanliga frågor om Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. **Hur skapar jag en virtuell Linux-Azure-dator med SQL Server**

   Den enklaste lösningen är att skapa en virtuell Linux-dator med SQL Server. En självstudiekurs om registrerar dig för Azure och skapar en SQL-VM från portalen finns [etablera en virtuell dator i Linux SQL Server i Azure portal](provision-sql-server-linux-virtual-machine.md). Du har också alternativet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad version (utvecklare eller Express) eller genom att återanvända en lokal licens. Om du tar din egen licens måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Hur uppgraderar till en ny version/utgåva av SQL Server i en Azure VM?**

   Det finns för närvarande ingen uppgradering på plats för SQL Server som körs i en Azure VM. Skapa en ny Azure virtuell dator med den önskade SQL Server-version/utgåvan och sedan migrera dina databaser till en ny server med hjälp av [standarddata migrering tekniker](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

1. **Hur kan jag installera min licensierad version av SQL Server på en Azure VM?**

   Först skapa en virtuell Linux OS-only-dator. Kör sedan den [SQL Server-installationssteg](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) för Linux-distribution. Om du installerar en fritt licensierad version av SQL Server, du måste ha en licens för SQL Server och [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Finns det Bring-Your-äger-licens (BYOL) Linux virtuella avbildningar för SQL Server?**

   Det finns inga BYOL Linux virtuella bilder för SQL Server för tillfället. Du kan dock manuellt installera SQL Server på en virtuell dator endast Linux enligt beskrivningen i föregående frågor.

1. **Kan jag ändra en virtuell dator om du vill använda en egen SQL Server-licens om den har skapats från en betalning per användning galleriavbildningar?**

   Nej. Du kan inte växla från per minut licensiering med din egen licens. Du måste skapa en ny Linux VM, installera SQL Server och migrera dina data. Se föregående fråga mer information om hur du aktiverar din egen licens.

1. **Vad relaterad SQL Server-paket installeras också?**

   SQL Server-paket som installeras som standard på SQL Server Linux virtuella datorer finns i [installerade paket](sql-server-linux-virtual-machines-overview.md#packages).

1. **Stöds lösningar för hög tillgänglighet för SQL Server på virtuella Azure-datorer?**

   Inte just nu. Always On-Tillgänglighetsgrupper och redundanskluster båda kräver klusterlösningen i Linux, till exempel Pacemaker. De Linux-distributioner som stöds för SQL Server stöder inte sitt tillägg för hög tillgänglighet i molnet.

1. **Varför kan jag etablera en RHEL eller SLES SQL Server-VM med en Azure-prenumeration som har en utgiftsgräns?**

   RHEL och SLES virtuella datorer kräver en prenumeration med ingen utgiftsgräns och verifierade betalningsmetod (vanligtvis ett kreditkort) som är associerade med prenumerationen. Om du etablerar en RHEL eller SLES VM utan att ta bort utgiftsgränsen ska inaktiveras din prenumeration och alla virtuella datorer och tjänster stoppas. Om du kör i det här tillståndet kan aktivera prenumerationen igen [ta bort utgiftsgränsen](https://account.windowsazure.com/subscriptions). Kommer att återställas din återstående kredit för den aktuella faktureringsperioden men en RHEL eller SLES VM avbildningen tillägg går mot ditt kreditkort om du vill starta om och fortsätta att köra den.

## <a name="resources"></a>Resurser

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server-VM för Linux](provision-sql-server-linux-virtual-machine.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Virtuella Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Etablera en virtuell dator med SQL Server Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Vanliga frågor och svar (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)