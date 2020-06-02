---
title: Översikt över SQL Server på Azure Linux Virtual Machines | Microsoft Docs
description: Läs mer om hur du kör fullständiga SQL Server-utgåvor på virtuella datorer med Azure Linux. Hämta direktlänkar till alla Linux SQL Server VM-avbildningar och tillhörande innehåll.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 61b8982868bf14a7b5a5441049cb7fa21cdd9d6d
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266040"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Översikt över SQL Server i Azure Virtual Machines (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Med SQL Server på Azure Virtual Machines kan du använda fullständiga versioner av SQL Server i molnet utan att behöva hantera lokal maskinvara. Virtuella SQL Server-datorer kan även förenkla licensieringskostnaderna när du betalar per användning.

Virtuella Azure-datorer körs i många olika [geografiska regioner](https://azure.microsoft.com/regions/) runtom i världen. De också erbjuder även en mängd olika [datorstorlekar](../../../virtual-machines/windows/sizes.md). I bildgalleriet för virtuella datorer kan du skapa en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Det här innebär att virtuella datorer är ett bra alternativ för många olika SQL Server-arbetsbelastningar. 

## <a name="get-started-with-sql-vms"></a><a id="create"></a>Kom igång med virtuella SQL-datorer

För att komma igång väljer du en avbildning av en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Följande avsnitt innehåller direktlänkar till Azure Portal för galleriavbildningarna för virtuella datorer med SQL Server.

> [!TIP]
> Mer information om prissättningen för SQL-avbildning finns i [prissättningssidan för virtuella Linux SQL Server-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Tillgängliga Windows SQL Server-VM-avbildningar finns i [Översikt över SQL Server i Azure Virtual Machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Installerade paket

När du konfigurerar SQL Server på Linux installerar du databasmotorns paket och sedan flera tillvalspaket beroende på kraven. Avbildningar av den virtuella Linux-datorn för SQL Server installerar de flesta paket automatiskt åt dig. Följande tabell visar vilka paket som installeras för varje distribution.

| Distribution | [Databasmotor](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Verktyg](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Full texts ökning](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Tillägg för hög tillgänglighet](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![nej](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| SLES | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![nej](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![nej](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| Ubuntu | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![ja](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Relaterade produkter och tjänster

### <a name="linux-virtual-machines"></a>Virtuella Linux-datorer

* [Översikt över virtuella datorer](../../../virtual-machines/linux/overview.md)

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

Kom igång med SQL Server på virtuella Linux-datorer i Azure:

* [Skapa en virtuell SQL Server-dator på Azure Portal](sql-vm-create-portal-quickstart.md)

Få svar på vanliga frågor om virtuella SQL-datorer på Linux:

* [Vanliga frågor och svar om SQL Server på virtuella Linux-datorer i Azure](frequently-asked-questions-faq.md)
