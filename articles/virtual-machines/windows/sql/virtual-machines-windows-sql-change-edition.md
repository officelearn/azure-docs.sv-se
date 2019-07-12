---
title: Hur du utför en uppgradering på plats av SQL Server edition på Azure VM | Microsoft Docs
description: Lär dig hur du ändrar utgåvan av SQL Server-VM i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607551"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Hur du utför en uppgradering på plats av SQL Server edition på Azure VM

Den här artikeln beskriver hur du ändrar utgåvan av SQL Server för en befintlig SQL Server på en Windows-dator i Azure. 

Utgåvan av SQL Server bestäms av produktnyckeln och har angetts med installationsprocessen. Versionen av avgör vad [funktioner](/sql/sql-server/editions-and-components-of-sql-server-2017) är tillgängliga i SQL Server-produkten. Du kan ändra SQL Server-versionen med installationsmediet och antingen nedgradering att minska kostnaderna eller uppgradera om du vill aktivera fler funktioner.

Om du har uppdaterat utgåvan av SQL Server med installationsmediet när du har registrerat hos SQL VM-resursprovidern bör sedan för att uppdatera Azure billing därefter du ange egenskapen SQL Server-versionen av SQL VM-resurs på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till din SQL Server-resursen för virtuella datorer. 
1. Under **inställningar**väljer **konfigurera** och välj sedan den önskade versionen av SQL Server under listrutan **Edition**. 

   ![Ändra edition metadata](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Granska varningen meddelar dig att måste du ändra SQL Server-versionen först egenskapen edition måste matcha versionen av SQL Server. 
1. Välj **tillämpa** verkställa ändringarna edition metadata. 


## <a name="prerequisites"></a>Förutsättningar

Om du vill göra en plats ändring av versionen av SQL Server, behöver du följande: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En Windows [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrerats hos den [SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).
- Konfigurera media med den önskade versionen av SQL Server. Kunder som har [software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan få sina installationsmediet från den [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har software assurance kan använda installationsmediet från en marketplace SQL Server-VM-avbildning som har sin önskade edition.


## <a name="upgrade-edition"></a>Uppgradera edition

  > [!WARNING]
  > - **Uppgradera versionen av SQL Server startar om tjänsten för SQL Server, samt alla associerade tjänster, till exempel Analysis Services och R Services.** 

Hämta SQL Server-installationsmedia för den önskade versionen av SQL Server om du vill uppgradera versionen av SQL Server, och gör sedan följande:

1. Kör Setup.exe från installationsmediet för SQL Server. 
1. Gå till **Underhåll** och välj den **versionsuppgradering** alternativet. 

   ![Uppgradera versionen av SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Välj **nästa** tills du når den **redo för uppgradering av utgåva** och välj sedan **uppgradera**. Konfigurationsfönstret kan låsa sig under ett par minuter när ändringen är ticka och sedan visas en **Slutför** sidan som bekräftar att din uppgradering av utgåva har slutförts. 

När SQL Server-utgåva har uppgraderats bör du ändra egenskapen ”Edition” för Sql-dator på Azure-portalen, enligt ovan. Detta kommer att uppdatera metadata och fakturering som är associerade med den här virtuella datorn.

## <a name="downgrade-edition"></a>Nedgradering edition

  > [!WARNING]
  > - **Nedgradera versionen av SQL Server kräver helt avinstallerar SQL Server, vilket kan innebära ytterligare nedtid**. 


Om du vill nedgradera versionen av SQL Server, behöver du helt avinstallera SQL Server och installera om den igen med önskat installationsmediet. 

Du kan nedgradera versionen av SQL Server genom att följa dessa steg:

1. Säkerhetskopiera alla databaser, inklusive systemdatabaserna. 
1. Flytta systemdatabaser (master, model och msdb) till en ny plats. 
1. Avinstallera SQL Server och alla associerade tjänster helt. 
1. Starta om den virtuella datorn. 
1. Installera SQL Server med mediet med den önskade versionen av SQL Server.
1. Installera senaste servicepack och kumulativa uppdateringar.  
1. Ersätt de nya systemdatabaser som skapades under installationen med systemdatabaser som du tidigare har flyttat till en annan plats. 

När SQL Server-utgåvan nedgraderas, bör du ändra egenskapen ”Edition” för SQL-dator i Azure-portalen, enligt ovan. Detta kommer att uppdatera metadata och fakturering som är associerade med den här virtuella datorn.

## <a name="remarks"></a>Kommentarer

 - Egenskapen edition för SQL Server-dator måste matcha utgåvan av SQL Server installerad på den virtuella datorn för alla virtuella datorer med SQL, inklusive både PAYG och BYOL licenstyper.
 - Om du släpper din SQL Server-VM-resurs ska du gå tillbaka till inställningen hårdkodade version för avbildningen.
  - Ändra versionen är en funktion i SQL VM-resursprovidern. Distribuera en marketplace-avbildning via Azure portal automatiskt registrerar en SQL Server VM med resursprovidern. Men kunder som lokal installation av SQL Server måste du manuellt [registrera sina SQL Server-VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- Lägga till en SQL Server VM till en tillgänglighetsuppsättning måste återskapa den virtuella datorn. Som sådana, alla virtuella datorer som har lagts till i en tillgänglighetsuppsättning set går tillbaka till standardutgåvan och versionen kommer att behöva ändras igen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


