---
title: Hantera SQL Server virtuella datorer i Azure med hjälp av Azure Portal | Microsoft Docs
description: Lär dig hur du får åtkomst till resursen för virtuella SQL-datorer i Azure Portal för en SQL Server VM som finns på Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79243216"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Hantera SQL Server virtuella datorer i Azure med hjälp av Azure Portal

I [Azure Portal](https://portal.azure.com)är resursen för **virtuella SQL-datorer** en oberoende hanterings tjänst. Du kan använda den för att visa alla dina SQL Server virtuella datorer samtidigt och ändra inställningarna som är reserverade för SQL Server: 

![Resurs för virtuella SQL-datorer](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Anmärkningar

- Vi rekommenderar att du använder resursen **SQL Virtual Machines** för att visa och hantera dina SQL Server virtuella datorer i Azure. Men för närvarande stöder inte resursen **SQL Virtual Machines** hanteringen av [support](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server virtuella datorer. Om du vill hantera inställningarna för support SQL Server virtuella datorer använder du fliken inaktuell [SQL Server konfiguration](#access-the-sql-server-configuration-tab) i stället. 
- Resursen **SQL Virtual Machines** är bara tillgänglig för SQL Server virtuella datorer som har [registrerats med PROVIDERN för SQL VM-resurs](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Åtkomst till resursen för virtuella SQL-datorer
Gör så här för att få åtkomst till resursen för **virtuella SQL-datorer** :

1. Öppna [Azure Portal](https://portal.azure.com). 
1. Välj **alla tjänster**. 
1. Ange **virtuella SQL-datorer** i sökrutan.
1. (Valfritt): Välj stjärnan bredvid **virtuella datorer i SQL** för att lägga till det här alternativet i **Favoriter** -menyn. 
1. Välj **virtuella SQL-datorer**. 

   ![Hitta SQL Server virtuella datorer i alla tjänster](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Portalen visar alla SQL Server virtuella datorer som är tillgängliga i prenumerationen. Välj den som du vill hantera för att öppna resursen för **virtuella SQL-datorer** . Använd sökrutan om SQL Server VM inte visas. 

   ![Alla tillgängliga SQL Server virtuella datorer](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   När du väljer SQL Server VM öppnas resursen **SQL Virtual Machines** : 


   ![Resurs för virtuella SQL-datorer](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> Resursen **SQL Virtual Machines** är avsedd för dedikerade SQL Server inställningar. Välj namnet på den virtuella datorn i rutan **virtuell dator** för att öppna inställningar som är unika för den virtuella datorn, men inte exklusiv för att SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Öppna fliken SQL Server konfiguration
Fliken **SQL Server konfiguration** är inaktuell. För närvarande är det den enda metoden för att hantera [support](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server virtuella datorer och SQL Server virtuella datorer som inte har [registrerats med resurs leverantören för SQL-VM](virtual-machines-windows-sql-register-with-resource-provider.md).

Öppna den föråldrade **SQL Server fliken Konfiguration** genom att gå till resursen **virtuella datorer** . Använd följande steg:

1. Öppna [Azure Portal](https://portal.azure.com). 
1. Välj **alla tjänster**. 
1. Ange **virtuella datorer** i sökrutan.
1. (Valfritt): Välj stjärnan bredvid **virtuella datorer** för att lägga till det här alternativet i **Favoriter** -menyn. 
1. Välj **Virtuella datorer**. 

   ![Sök efter virtuella datorer](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Portalen visar alla virtuella datorer i prenumerationen. Välj den som du vill hantera för att öppna resursen för **virtuella datorer** . Använd sökrutan om SQL Server VM inte visas. 
1. Välj **SQL Server konfiguration** i fönstret **Inställningar** för att hantera SQL Server VM. 

   ![SQL Server-konfiguration](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


