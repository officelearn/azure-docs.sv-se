---
title: Översikt över SQL Server på Azure Linux Virtual Machines | Microsoft Docs
description: Läs mer om hur du kör fullständiga SQL Server-utgåvor på virtuella datorer med Azure Linux. Hämta direktlänkar till alla Linux SQL Server VM-avbildningar och tillhörande innehåll.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c97793dc28b4ef097d8baa34678aeb92bf123809
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778101"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Översikt över SQL Server i Azure Virtual Machines (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Med SQL Server på Azure Virtual Machines kan du använda fullständiga versioner av SQL Server i molnet utan att behöva hantera lokal maskinvara. Virtuella SQL Server-datorer kan även förenkla licensieringskostnaderna när du betalar per användning.

Virtuella Azure-datorer körs i många olika [geografiska regioner](https://azure.microsoft.com/regions/) runtom i världen. De också erbjuder även en mängd olika [datorstorlekar](../sizes.md). I bildgalleriet för virtuella datorer kan du skapa en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Det här innebär att virtuella datorer är ett bra alternativ för många olika SQL Server-arbetsbelastningar.

## <a id="create"></a> Komma igång med virtuella SQL-datorer

För att komma igång väljer du en avbildning av en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Följande avsnitt innehåller direktlänkar till Azure Portal för galleriavbildningarna för virtuella datorer med SQL Server.

> [!TIP]
> Mer information om prissättningen för SQL-avbildning finns i [prissättningssidan för virtuella Linux SQL Server-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Tillgängliga Windows SQL Server-VM-avbildningar finns i [Översikt över SQL Server i Azure Virtual Machines (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Installerade paket

När du konfigurerar SQL Server på Linux installerar du databasmotorns paket och sedan flera tillvalspaket beroende på kraven. Avbildningar av den virtuella Linux-datorn för SQL Server installerar de flesta paket automatiskt åt dig. Följande tabell visar vilka paket som installeras för varje distribution.

| Distribution | [Databasmotor](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Verktyg](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Fulltextsökning](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Tillägg för hög tillgänglighet](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nej](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nej](./media/sql-server-linux-virtual-machines-overview/no.png) | ![nej](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Relaterade produkter och tjänster

### <a name="linux-virtual-machines"></a>Virtuella Linux-datorer

* [Översikt över virtuella datorer](../overview.md)

### <a name="storage"></a>Storage

* [Introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Nätverk

* [Översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Skapa ett fullständigt domännamn i Azure-portalen](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux)
* [Jämförelse med Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Nästa steg

Kom igång med SQL Server på virtuella Linux-datorer i Azure:

* [Skapa en virtuell SQL Server-dator på Azure Portal](provision-sql-server-linux-virtual-machine.md)

Få svar på vanliga frågor om virtuella SQL-datorer på Linux:

* [Vanliga frågor och svar om SQL Server på virtuella Linux-datorer i Azure](sql-server-linux-faq.md)
