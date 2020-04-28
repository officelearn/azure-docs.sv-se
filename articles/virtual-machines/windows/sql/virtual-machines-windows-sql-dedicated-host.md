---
title: SQL Server VM på en dedikerad Azure-värd
description: Läs mer om informationen om att köra en SQL Server VM på en dedikerad Azure-värd.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834356"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM på en dedikerad Azure-värd 

Den här artikeln beskriver information om hur du använder en SQL Server VM med en [dedikerad Azure-värd](/azure/virtual-machines/windows/dedicated-hosts). Ytterligare information om den dedikerade Azure-värden finns i blogg inlägget [Introduktion till Azure-dedikerad värd](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Översikt
Den [dedikerade Azure-värden](/azure/virtual-machines/windows/dedicated-hosts) är en tjänst som tillhandahåller fysiska servrar som kan vara värd för en eller flera virtuella datorer som är dedikerade till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i Microsofts Data Center, som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighets zon och fel domän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som passar bäst för dina behov.

## <a name="limitations"></a>Begränsningar

- Skalnings uppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- Följande VM-serien stöds: DSv3 och ESv3. 

## <a name="licensing"></a>Licensiering

Du kan välja mellan två olika licensierings alternativ när du lägger till SQL Server VM på en dedikerad Azure-värd. 

  - **SQL VM-licensiering**: Detta är det befintliga licensierings alternativet, där du betalar för varje SQL Server VM-licens individuellt. 
  - **Dedikerad värd licensiering**: den nya licensierings modellen som är tillgänglig för den dedikerade Azure-värden, där SQL Server licenser paketeras och betalas för på värdnivå. 


Alternativ på värdnivå för att använda befintliga SQL Server-licenser: 
  - SQL Server Enterprise Edition Azure Hybrid-förmån
    - Tillgängligt för kunder med SA eller prenumeration.
    - Licensiera alla tillgängliga fysiska kärnor och njut av obegränsad virtualisering (upp till max virtuella processorer som stöds av värden).
        - Mer information om hur du tillämpar Azure Hybrid-förmån på den dedikerade Azure-värden finns i [vanliga frågor och svar om Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server licenser som förvärv ATS före 1 oktober
      - SQL Server Enterprise Edition har licens alternativen värd och per virtuell dator. 
      - SQL Server Standard versionen har endast alternativet av-VM-licens tillgänglig. 
          - Mer information finns i [Microsofts produkt villkor](https://www.microsoft.com/licensing/product-licensing/products). 
  - Om inget SQL Server dedikerat alternativ för värd nivå är markerat kan SQL Server AHB väljas på nivån för enskilda virtuella datorer, precis som med virtuella datorer med flera innehavare.



## <a name="provisioning"></a>Etablering  
Att tillhandahålla en SQL Server VM till den dedikerade värden är inte något annat än någon annan virtuell Azure-dator. Du kan göra det med hjälp av [Azure PowerShell](../dedicated-hosts-powershell.md), [Azure Portal](../dedicated-hosts-portal.md)och [Azure CLI](../../linux/dedicated-hosts-cli.md).

Processen för att lägga till en befintlig SQL Server VM på den dedikerade värden kräver stillestånds tid, men påverkar inte data och kommer inte att ha någon data förlust. Alla databaser, inklusive system databaser, bör dock säkerhets kopie ras före flytten.

## <a name="virtualization"></a>Virtualisering 

En av fördelarna med en dedikerad värd är obegränsad virtualisering. Du kan till exempel ha licenser för 64 virtuella kärnor, men du kan konfigurera värden så att den har 128 virtuella kärnor, så du får dubbla virtuella kärnor men betalar bara för hälften av SQL Server licenser. 

Eftersom eftersom det är din värd, är du berättigad att ställa in virtualiseringen med 1:2-förhållandet. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: Hur fungerar Azure Hybrid-förmån för Windows Server/SQL Server-licenser på en särskild Azure-värd?**

A: kunderna kan använda värdet för sin befintliga Windows Server och SQL Server licenser med Software Assurance, eller kvalificera prenumerations licenser, för att betala en reducerad taxa på den Azure-dedikerade värd som använder Azure Hybrid-förmån. Kunder med Windows Server Data Center och SQL Server Enterprise Edition får obegränsad virtualisering (distribuera så många virtuella Windows Server-datorer som möjligt på värden som är beroende av den underliggande serverns fysiska kapacitet) när de licensierar hela värden och använder Azure Hybrid-förmån.  Alla Windows Server-och SQL Server-arbetsbelastningar i den dedikerade Azure-värden är också kvalificerade för utökade säkerhets uppdateringar för Windows Server och SQL Server 2008/R2 utan extra kostnad. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


