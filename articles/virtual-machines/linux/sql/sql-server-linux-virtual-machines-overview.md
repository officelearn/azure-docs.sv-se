---
title: Översikt över SQL Server på Azure Linux Virtual Machines | Microsoft Docs
description: Läs mer om hur du kör fullständiga SQL Server-utgåvor på virtuella datorer med Azure Linux. Hämta direktlänkar till alla Linux SQL Server VM-avbildningar och tillhörande innehåll.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 03/22/2018
ms.author: jroth
ms.openlocfilehash: e752ad844a6efe572564e7081ebac87193e9c2a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Översikt över SQL Server i Azure Virtual Machines (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Det här ämnet beskriver dina alternativ när du kör en SQL Server på virtuella Azure Linux-datorer, tillsammans med [länkar till portalavbildningar](#create).

> [!NOTE]
> Om du redan är bekant med SQL Server och bara vill se hur man distribuerar en virtuell SQL Server Linux-dator i Windows, hittar du mer information i [Etablera en virtuell Linux SQL Server-dator i Azure](provision-sql-server-linux-virtual-machine.md). Eller om du vill skapa en virtuell Windows-dator med SQL Server finns information i [Etablera en virtuell Windows SQL Server-dator i Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Om du är en databasadministratör eller en utvecklare, kan Azure VM:ar ge dig ett sätt att flytta dina lokala SQL Server-arbetsbelastningar och program till molnet.

## <a name="scenarios"></a>Scenarier

Det finns flera skäl att välja att ha sin data i Azure. Om du utvecklar eller migrerar programmet till Azure kan du förbättra prestanda genom att även placera ursprungsdata i Azure. Du får automatiskt tillgång till flera datacenter för en global närvaro och haveriberedskap. Din data är också mycket säker och beständig.

SQL Server som kör på Azure VM:ar är ett alternativ för att lagra din relationsdata i Azure. Du kan även välja att använda Azure SQL Database-tjänsten. Mer information om att välja mellan SQL Server på virtuella datorer och Azure SQL Database finns i [Välj ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på virtuella Azure-datorer (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a id="create"></a>Skapa en ny virtuell SQL-dator

Du hittar stegvisa anvisningar för hur du skapar en ny virtuell SQL-dator i självstudien [Etablera en virtuell Linux SQL Server-dator i Azure](provision-sql-server-linux-virtual-machine.md).

Följande tabell innehåller en matris med de senaste SQL Server-avbildningarna i galleriet för virtuella datorer. Klicka på en länk om du vill skapa en ny virtuell dator med SQL med den version, den utgåva och det operativsystem du angett.

> [!TIP]
> Om du vill förstå prissättningen för den virtuella datorn och SQL för dessa avbildningar kan du se [prissidan för virtuella Linux SQL Server-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

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

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar och använder SQL Server på Linux finns i [Översikt över SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
