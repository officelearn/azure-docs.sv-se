---
title: I stället förändring av SQL Server edition
description: Lär dig hur du ändrar versionen av den virtuella SQL Server-datorn i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605457"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Ändring på plats av SQL Server-utgåvan på Azure VM

I den här artikeln beskrivs hur du ändrar versionen av SQL Server på en virtuell Windows-dator i Azure. 

Utgåvan av SQL Server bestäms av produktnyckeln och anges under installationsprocessen med installationsmediet. Utgåvan avgör vilka [funktioner](/sql/sql-server/editions-and-components-of-sql-server-2017) som är tillgängliga i SQL Server-produkten. Du kan ändra SQL Server-utgåvan med installationsmediet och antingen nedgradera för att minska kostnaderna eller uppgradera för att aktivera fler funktioner.

När utgåvan av SQL Server har ändrats internt till SQL Server VM måste du sedan uppdatera egenskapen för SQL Server i Azure-portalen för fakturering. 

## <a name="prerequisites"></a>Krav

Om du vill göra en ändring på plats av utgåvan av SQL Server behöver du följande: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [VIRTUELL SQL Server-dator i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) som är registrerad hos SQL [VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).
- Installationsmedia med **önskad utgåva** av SQL Server. Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta sina installationsmedia från [Volymlicensieringscentret](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installationsmediet från en Virtuell Azure Marketplace `C:\SQLServerFull`SQL Server-avbildning som har önskad utgåva (vanligtvis finns i). 


## <a name="upgrade-an-edition"></a>Uppgradera en utgåva

> [!WARNING]
> Om du uppgraderar utgåvan av SQL Server startas tjänsten för SQL Server om, tillsammans med alla associerade tjänster, till exempel Analysis Services och R Services. 

Om du vill uppgradera utgåvan av SQL Server hämtar du installationsmediet för SQL Server för önskad utgåva av SQL Server och gör sedan följande:

1. Öppna Setup.exe från INSTALLATIONSmediet för SQL Server. 
1. Gå till **Underhåll** och välj alternativet **Edition Upgrade.** 

   ![Val för uppgradering av versionen av SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Välj **Nästa** tills du når sidan **Klar att uppgradera utgåvan** och välj sedan **Uppgradera**. Inställningsfönstret kan sluta svara i några minuter medan ändringen börjar gälla. En **fullständig** sida bekräftar att uppgraderingen av utgåvan är klar. 

När SQL Server-utgåvan har uppgraderats ändrar du egenskapen edition för den virtuella SQL Server-datorn i Azure-portalen. Detta uppdaterar metadata och fakturering som är associerade med den här virtuella datorn.

## <a name="downgrade-an-edition"></a>Nedgradera en utgåva

Om du vill nedgradera utgåvan av SQL Server måste du avinstallera SQL Server helt och installera om den igen med önskat installationsmedia för utgåvan. 

> [!WARNING]
> Om du avinstallerar SQL Server kan det uppstå ytterligare driftstopp. 

Du kan nedgradera utgåvan av SQL Server genom att följa följande:

1. Säkerhetskopiera alla databaser, inklusive systemdatabaserna. 
1. Flytta systemdatabaser (huvud,modell och msdb) till en ny plats. 
1. Avinstallera SQL Server helt och alla associerade tjänster. 
1. Starta om den virtuella datorn. 
1. Installera SQL Server med hjälp av media med önskad utgåva av SQL Server.
1. Installera de senaste Service Pack-versionerna och kumulativa uppdateringar.  
1. Ersätt de nya systemdatabaser som skapades under installationen med de systemdatabaser som du tidigare flyttade till en annan plats. 

När SQL Server-utgåvan har nedgraderats ändrar du egenskapen edition för den virtuella SQL Server-datorn i Azure-portalen. Detta uppdaterar metadata och fakturering som är associerade med den här virtuella datorn.

## <a name="change-edition-in-portal"></a>Ändra utgåva i portalen 

När du har ändrat versionen av SQL Server med installationsmediet och registrerat din VIRTUELLA SQL Server-dator med [SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md)kan du sedan använda Azure-portalen för att ändra egenskapen Edition för SQL Server VM för faktureringsändamål. Det gör du på följande sätt: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till resursen för virtuell SQL Server. 
1. Under **Inställningar**väljer du **Konfigurera**. Välj sedan önskad utgåva av SQL Server i listrutan under **Edition**. 

   ![Ändra metadata för upplagan](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Granska varningen som säger att du måste ändra SQL Server-utgåvan först och att egenskapen edition måste matcha SQL Server-utgåvan. 
1. Välj **Använd** om du vill använda ändringar av utgåvormetadata. 


## <a name="remarks"></a>Anmärkningar

- Egenskapen edition för SQL Server VM måste matcha den utgåva av SQL Server-instansen som är installerad för alla virtuella SQL Server-datorer, inklusive både användningsbaserad betalning och med-din-egen-licenstyper av licenser.
- Om du släpper din SQL Server VM-resurs går du tillbaka till inställningen för hårdkodad utgåva av avbildningen.
- Möjligheten att ändra utgåvan är en funktion i SQL VM-resursprovidern. Distribuera en Azure Marketplace-avbildning via Azure-portalen registrerar automatiskt en VIRTUELL SQL Server med resursleverantören. Kunder som själv installerar SQL Server måste dock [registrera sin virtuella SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md)manuellt.
- Om du lägger till en virtuell SQL Server-dator i en tillgänglighetsuppsättning måste den virtuella datorn skapas om. Alla virtuella datorer som läggs till i en tillgänglighetsuppsättning går tillbaka till standardutgåvan och utgåvan måste ändras igen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


