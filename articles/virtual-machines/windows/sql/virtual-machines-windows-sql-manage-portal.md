---
title: Hantera SQL Server-datorer i Azure med Azure portal | Microsoft Docs
description: Lär dig hur du kommer åt den virtuella datorresursen SQL i Azure portal för en SQL Server-VM på Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e2a807bbd6baeb2f14a6d36f5d98a28d48725449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082721"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Hantera SQL Server-datorer i Azure med Azure portal

Det finns en ny åtkomstpunkt för att hantera din SQL Server-VM på Azure med den [Azure-portalen](https://portal.azure.com). 

Den **virtuella datorer med SQL** resursen är nu en oberoende management-tjänst som gör att du kan visa alla SQL Server-datorer samtidigt och ändrar inställningarna för SQL Server: 

![SQL VM-resurs](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Kommentarer

- Den **virtuella datorer med SQL** resursen är den rekommenderade metoden för att visa och hantera dina SQL Server-datorer. Men, för närvarande den **virtuella datorer med SQL** resursen stöder inte hantering av [supporten (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server-datorer. Hantera inställningar för EOS SQL Server-datorer med det inaktuella [fliken för SQL Server-konfiguration](#access-sql-server-configuration-tab) i stället. 
- Den **virtuella datorer med SQL** resurs är endast tillgänglig för SQL Server-datorer som har [registrerad hos resursprovidern SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider). 


## <a name="access-sql-virtual-machine-resource"></a>Åtkomst till SQL VM-resurs
För att komma åt SQL VM-resurs, gör du följande:

1. Öppna [Azure-portalen](https://portal.azure.com). 
1. Välj **alla tjänster**. 
1. Typ `SQL virtual machines` i sökrutan.
1. (Valfritt): Markera stjärnan bredvid **virtuella datorer med SQL** att lägga till det här alternativet på Favoriter-menyn. 
1. Välj **virtuella datorer med SQL**. 

   ![Hitta SQL VM virtuella datorer i alla tjänster](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Detta visar en lista över alla SQL Server-datorer i prenumerationen. Väljer du det alternativ som du vill hantera att starta den **virtuella datorer med SQL** resurs. Använd sökrutan om din SQL Server-VM inte är en gång. 

![Alla tillgängliga SQL-datorer](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Om du markerar din SQL Server-VM öppnas den **virtuella datorer med SQL** resurs: 


![SQL VM-resurs](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > Den **virtuella datorer med SQL** resursen är för dedikerad SQL Server-inställningar. Välj namnet på den virtuella datorn i den **VM** fält för att gå till inställningar som är specifika för den virtuella datorn, men inte exklusivt för SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Fliken för åtkomst till SQL Server-konfiguration
Konfigurationsfliken för SQL Server är inaktuell. Just nu, det är det enda sättet att hantera [supporten (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server-datorer och SQL Server-datorer som inte har [registrerad hos resursprovidern SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider).

För att komma åt konfigurationsfliken för inaktuella SQL server, måste du gå till den **virtuella datorer** resurs. Du gör detta genom att göra följande:

1. Öppna [Azure-portalen](https://portal.azure.com). 
1. Välj **alla tjänster**. 
1. Typ `virtual machines` i sökrutan.
1. (Valfritt): Markera stjärnan bredvid **virtuella datorer** att lägga till det här alternativet på Favoriter-menyn. 
1. Välj **virtuella datorer**. 

   ![Sök efter virtuella datorer](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Detta visar en lista över alla virtuella datorer i prenumerationen. Väljer du det alternativ som du vill hantera att starta den **VM** resurs. Använd sökrutan om din SQL Server-VM inte är en gång. 
1. Välj **konfiguration av SQL Server** i den **inställningar** fönstret för att hantera din SQL-Server. 

![Konfiguration av SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


