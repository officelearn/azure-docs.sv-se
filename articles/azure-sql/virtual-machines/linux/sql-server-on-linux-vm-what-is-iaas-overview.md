---
title: Översikt över SQL Server på Azure Virtual Machines för Linux | Microsoft Docs
description: Lär dig mer om hur du kör fullständiga SQL Server-versioner på Azure Virtual Machines för Linux. Hämta direktlänkar till alla Linux SQL Server VM-avbildningar och tillhörande innehåll.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 721f30f5b17b078f3a3905204f6be56db25adead
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669485"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Översikt över SQL Server i Azure Virtual Machines (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server på Azure Virtual Machines kan du använda fullständiga versioner av SQL Server i molnet utan att behöva hantera lokal maskin vara. Virtuella SQL Server-datorer kan även förenkla licensieringskostnaderna när du betalar per användning.

Virtuella Azure-datorer körs i många olika [geografiska regioner](https://azure.microsoft.com/regions/) runtom i världen. De också erbjuder även en mängd olika [datorstorlekar](../../../virtual-machines/windows/sizes.md). I bildgalleriet för virtuella datorer kan du skapa en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Det här innebär att virtuella datorer är ett bra alternativ för många olika SQL Server-arbetsbelastningar. 

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a>Kom igång med SQL Server virtuella datorer

För att komma igång väljer du en avbildning av en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Följande avsnitt innehåller direktlänkar till Azure Portal för galleriavbildningarna för virtuella datorer med SQL Server.

> [!TIP]
> Mer information om hur du förstår priser för SQL Server avbildningar finns [på sidan med priser för virtuella Linux-datorer som kör SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> För att se tillgängliga SQL Server avbildningar av virtuella datorer för Windows, se [Översikt över SQL Server på Azure Virtual Machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Installerade paket

När du konfigurerar SQL Server på Linux installerar du databas motor paketet och sedan flera valfria paket beroende på dina behov. Avbildningar av den virtuella Linux-datorn för SQL Server installerar de flesta paket automatiskt åt dig. Följande tabell visar vilka paket som installeras för varje distribution.

| Distribution | [Databasmotor](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Verktyg](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Fulltextsökning](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Tillägg för hög tillgänglighet](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![nej](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| SLES | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![nej](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![nej](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| Ubuntu | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Relaterade produkter och tjänster

### <a name="linux-virtual-machines"></a>Virtuella Linux-datorer

* [Översikt över Azure Virtual Machines](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Storage

* [Introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Nätverk

* [Översikt över Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../../virtual-network/public-ip-addresses.md)
* [Skapa ett fullständigt domännamn i Azure-portalen](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server på Linux dokumentation](https://docs.microsoft.com/sql/linux)
* [Jämförelse med Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Nästa steg

Kom igång med SQL Server på Linux virtuella datorer:

* [Skapa en virtuell SQL Server-dator på Azure Portal](sql-vm-create-portal-quickstart.md)

Få svar på vanliga frågor om SQL Server virtuella datorer i Linux:

* [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](frequently-asked-questions-faq.md)
