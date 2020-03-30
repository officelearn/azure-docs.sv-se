---
title: Virtuell SQL Server på en Azure-dedikerad värd
description: Lär dig mer om information om hur du kör en VIRTUELL SQL Server på en Azure Dedicated Host.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834356"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Virtuell SQL Server på en Azure-dedikerad värd 

I den här artikeln beskrivs detaljerna för att använda en virtuell SQL Server-dator med en [Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts). Ytterligare information om den azure dedikerade värden finns i blogginlägget [Introduktion av Azure Dedikerad värd](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Översikt
[Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) är en tjänst som tillhandahåller fysiska servrar – som kan vara värd för en eller flera virtuella datorer – som är dedikerad till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i Microsofts datacenter, som tillhandahålls som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighetszon och feldomän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som bäst uppfyller dina behov.

## <a name="limitations"></a>Begränsningar

- Skalningsuppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- Följande VM-serier stöds: DSv3 och ESv3. 

## <a name="licensing"></a>Licensiering

Du kan välja mellan två olika licensieringsalternativ när du lägger till din VIRTUELLA SQL Server-dator i en Azure Dedicated Host. 

  - **SQL VM-licensiering:** Det här är det befintliga licensieringsalternativet, där du betalar för varje SQL Server VM-licens individuellt. 
  - **Dedikerad värdlicensiering:** Den nya licensieringsmodellen som är tillgänglig för Azure Dedicated Host, där SQL Server-licenser paketeras och betalas på värdnivå. 


Alternativ på värdnivå för att använda befintliga SQL Server-licenser: 
  - Azure Hybrid-fördel för SQL Server Enterprise Edition
    - Tillgänglig för kunder med SA eller prenumeration.
    - Licensiera alla tillgängliga fysiska kärnor och njut av obegränsad virtualisering (upp till max vCPUs som stöds av värden).
        - Mer information om hur du tillämpar Azure Hybrid-förmånen på Azure Dedikerad värd finns i [vanliga frågor och svar om Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server-licenser som förvärvats före den 1 oktober
      - SQL Server Enterprise Edition har både värdnivå och by-VM-licensalternativ. 
      - SQL Server Standard Edition har endast licensalternativ för för virtuell dator tillgängligt. 
          - Mer information finns i [Microsofts produktvillkor](https://www.microsoft.com/licensing/product-licensing/products). 
  - Om inget SQL Server-dedikerat värdnivåalternativ är markerat kan SQL Server AHB väljas på nivån för enskilda virtuella datorer, precis som med virtuella datorer med flera innehavare.



## <a name="provisioning"></a>Etablering  
Att etablera en virtuell SQL Server-dator till den dedikerade värden skiljer sig inte från någon annan virtuell Azure-dator. Du kan göra det med [Azure PowerShell,](../dedicated-hosts-powershell.md) [Azure-portalen](../dedicated-hosts-portal.md)och [Azure CLI](../../linux/dedicated-hosts-cli.md).

Processen att lägga till en befintlig VIRTUELL SQL Server till den dedikerade värden kräver driftstopp, men påverkar inte data och kommer inte att ha dataförlust. Alla databaser, inklusive systemdatabaser, bör dock säkerhetskopieras före flytten.

## <a name="virtualization"></a>Virtualisering 

En av fördelarna med en dedikerad värd är obegränsad virtualisering. Du kan till exempel ha licenser för 64 vCore, men du kan konfigurera värden så att den har 128 virtuella kärnor, så du får dubbla virtuella kärnor men bara betalar för hälften av SQL Server-licenserna. 

Eftersom det är din värd, är du berättigad att ställa in virtualisering med en 1:2 förhållande. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: Hur fungerar Azure Hybrid Benefit för Windows Server/SQL Server-licenser på Azure Dedicated Host?**

S: Kunder kan använda värdet av sina befintliga Windows Server- och SQL Server-licenser med Software Assurance, eller kvalificerande prenumerationslicenser, för att betala ett reducerat pris på Azure Dedicated Host med Azure Hybrid Benefit. Windows Server Datacenter- och SQL Server Enterprise Edition-kunder får obegränsad virtualisering (distribuera så många virtuella Windows Server-datorer som möjligt på värden som omfattas av den underliggande serverns fysiska kapacitet) när de licensierar hela värden och använda Azure Hybrid Benefit.  Alla Windows Server- och SQL Server-arbetsbelastningar i Azure Dedicated Host är också berättigade till utökade säkerhetsuppdateringar för Windows Server och SQL Server 2008/R2 utan extra kostnad. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


