---
title: Hantera virtuella SQL Server-datorer i Azure med hjälp av Azure-portalen | Microsoft-dokument
description: Lär dig hur du kommer åt sql-resursen för virtuella datorer i Azure-portalen för en VIRTUELL SQL Server-dator som finns på Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243216"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Hantera virtuella SQL Server-datorer i Azure med hjälp av Azure-portalen

I [Azure-portalen](https://portal.azure.com)är **RESURSEN VIRTUELLA SQL-datorer** en oberoende hanteringstjänst. Du kan använda den för att visa alla dina virtuella SQL Server-datorer samtidigt och ändra inställningar som är dedikerade till SQL Server: 

![Resurs för virtuella SQL-datorer](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Anmärkningar

- Vi rekommenderar att du använder **resursen virtuella SQL-datorer** för att visa och hantera virtuella SQL Server-datorer i Azure. Men för närvarande stöder **resursen virtuella SQL-datorer** inte hanteringen av virtuella SQL [Server-virtuella](virtual-machines-windows-sql-server-2008-eos-extend-support.md) datorer. Om du vill hantera inställningar för virtuella SQL Server-datorer med slutsupporter använder du i stället den inaktuella [SQL Server-konfigurationsfliken.](#access-the-sql-server-configuration-tab) 
- **Resursen virtuella SQL-datorer** är endast tillgänglig för virtuella SQL Server-datorer som har [registrerats hos SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Få tillgång till resursen virtuella SQL-datorer
Så här öppnar du resursen **virtuella SQL-datorer:**

1. Öppna [Azure-portalen](https://portal.azure.com). 
1. Välj **Alla tjänster**. 
1. Ange **virtuella SQL-datorer** i sökrutan.
1. (Valfritt): Välj stjärnan bredvid **virtuella SQL-datorer** för att lägga till det här alternativet **på** favoritmenyn. 
1. Välj **virtuella SQL-datorer**. 

   ![Hitta virtuella SQL Server-datorer i alla tjänster](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Portalen listar alla virtuella SQL Server-datorer som är tillgängliga i prenumerationen. Välj den som du vill hantera för att öppna **resursen virtuella SQL-datorer.** Använd sökrutan om den virtuella datorn för SQL Server inte visas. 

   ![Alla tillgängliga virtuella SQL Server-datorer](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Om du väljer den virtuella virtuella datorn för SQL Server öppnas **resursen för virtuella SQL-datorer:** 


   ![Resurs för virtuella SQL-datorer](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **Resursen virtuella SQL-datorer** är avsedd för dedikerade SQL Server-inställningar. Välj namnet på den virtuella datorn i rutan **Virtuell dator** om du vill öppna inställningar som är specifika för den virtuella datorn, men inte exklusivt för SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Öppna konfigurationsfliken SQL Server
Konfigurationsfliken **SQL Server** har inaktuell. För närvarande är det den enda metoden för att hantera virtuella SQL Server-virtuella datorer och virtuella SQL [Server-datorer](virtual-machines-windows-sql-server-2008-eos-extend-support.md) som inte har [registrerats hos SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).

Om du vill komma åt den inaktuella **SQL Server-konfigurationsfliken** går du till resursen **virtuella datorer.** Använd följande steg:

1. Öppna [Azure-portalen](https://portal.azure.com). 
1. Välj **Alla tjänster**. 
1. Ange **virtuella datorer** i sökrutan.
1. (Valfritt): Välj stjärnan **bredvid Virtuella datorer** för att lägga till det här alternativet på favoritmenyn. **Favorites** 
1. Välj **Virtuella datorer**. 

   ![Sök efter virtuella datorer](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Portalen listar alla virtuella datorer i prenumerationen. Välj den som du vill hantera för att öppna resursen **virtuella datorer.** Använd sökrutan om den virtuella datorn för SQL Server inte visas. 
1. Välj **SQL Server-konfiguration** i fönstret **Inställningar** för att hantera din virtuella SQL Server-dator. 

   ![SQL Server-konfiguration](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


