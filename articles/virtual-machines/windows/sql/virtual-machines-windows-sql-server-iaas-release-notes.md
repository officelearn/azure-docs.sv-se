---
title: SQLServer på Azure VM viktig information | Microsoft Docs
description: Lär dig mer om nya funktioner och förbättringar av SQL Server på en Azure VM
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 52036d8f5d25fc4a4f2d1b602428e9cba4762b7f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993147"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server på Azure-dator viktig information

Azure kan du distribuera en virtuell dator med en bild av SQL Server som är inbyggda i. Den här artikeln innehåller nya funktioner och förbättringar som du kan förvänta dig i den senaste versionen av SQL Server distribueras på virtuella Azure-datorer. 


## <a name="november-2018"></a>November 2018
- **Ny SQL-resursprovider**: det finns en ny resource provider för SQL-datorer som kan ge en bättre hantering av den virtuella datorn. Mer information om hur du registrerar din virtuella dator finns i [registrera befintliga SQL-VM med ny resursprovider](virtual-machines-windows-sql-ahb.md#register-existing-sql-vm-with-new-resource-provider).
- **Växla licensieringsmodell**: du kan växla mellan modellen betala per användning och bring-your-own-licens för din SQL-VM med Azure CLI eller PowerShell. Mer information finns i [ändra så att licensieringsmodellen för en SQL-VM](virtual-machines-windows-sql-ahb.md)



## <a name="additional-resources"></a>Ytterligare resurser

**Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en virtuell dator med SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQLServer på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och katastrofåterställning för SQLServer på Azure virtuella datorer](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Programmönster och utvecklingsstrategier för SQLServer på Azure virtuella datorer](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
