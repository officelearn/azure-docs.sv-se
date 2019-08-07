---
title: SQL Server på Virtuella Windows-datorer i vanliga frågor och svar om Azure | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om att köra SQL Server på virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 4b50b4acf6ea655c40821e7c49824af11aeeb9ab
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816298"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Vanliga frågor och svar om SQL Server som körs på virtuella Windows-datorer i Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på virtuella Windows-datorer i Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Den här artikeln fokuserar på problem som är specifika för SQL Server på virtuella Windows-datorer. Om du kör SQL Server på Linux virtuella datorer kan du läsa [vanliga frågor och svar om Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Avbildningar

1. **Vilka SQL Server Galleri avbildningar för virtuella datorer är tillgängliga?** 

   Azure upprätthåller avbildningar av virtuella datorer för alla stödda större versioner av SQL Server på alla utgåvor för både Windows och Linux. Mer information finns i den fullständiga listan över virtuella [Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) -avbildningar och [virtuella Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)-avbildningar.

1. **Uppdateras befintliga SQL Server Galleri avbildningar för virtuella datorer?**

   Varannan månad uppdateras SQL Server avbildningar i galleriet för virtuella datorer med de senaste Windows-och Linux-uppdateringarna. För Windows-avbildningar inkluderar detta alla uppdateringar som marker ATS som viktiga i Windows Update, inklusive viktiga SQL Server säkerhets uppdateringar och Service Pack. För Linux-avbildningar omfattar detta de senaste system uppdateringarna. SQL Server kumulativa uppdateringar hanteras på olika sätt för Linux och Windows. SQL Server kumulativa uppdateringar för Linux ingår också i uppdateringen. Men för tillfället uppdateras inte virtuella Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Kan SQL Server avbildningar av virtuella datorer ta bort från galleriet?**

   Ja. Azure upprätthåller bara en bild per huvud version och utgåva. Till exempel, när en ny SQL Server service pack lanseras, lägger Azure till en ny avbildning till galleriet för den service pack. SQL Server avbildningen för föregående service pack tas omedelbart bort från Azure Portal. Men det är fortfarande tillgängligt för etablering från PowerShell under de kommande tre månaderna. Föregående service packs avbildning är inte tillgänglig längre efter tre månader. Den här borttagnings principen gäller även om en SQL Server-version inte stöds när den når slutet av livs cykeln.


1. **Är det möjligt att distribuera en äldre avbildning av SQL Server som inte är synlig i Azure Portal?**

   Ja, med hjälp av PowerShell. Mer information om hur du distribuerar SQL Server virtuella datorer med hjälp av PowerShell finns i [så här etablerar du SQL Server virtuella datorer med Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Kan jag skapa en VHD-avbildning från en SQL Server VM?**

   Ja, men det finns några överväganden. Om du distribuerar den här virtuella hård disken till en ny virtuell dator i Azure får du inte avsnittet SQL Server konfiguration i portalen. Du måste sedan hantera SQL Server konfigurations alternativ via PowerShell. Du debiteras också för enligt priset för den virtuella SQL-dator som din avbildning ursprungligen var baserad på. Detta gäller även om du tar bort SQL Server från den virtuella hård disken innan du distribuerar. 

1. **Är det möjligt att konfigurera konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. För Galleri avbildningar för virtuella datorer som innehåller SQL Server måste du välja en av de angivna avbildningarna antingen via Azure Portal eller via [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Flikar

1. **Hur gör jag för att skapar du en virtuell Azure-dator med SQL Server?**

   Den enklaste lösningen är att skapa en virtuell dator som innehåller SQL Server. En själv studie kurs om hur du registrerar dig för Azure och skapar en virtuell SQL-dator från portalen finns i [etablera en SQL Server virtuell dator i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Du kan välja en avbildning av en virtuell dator som använder SQL Server licens för betala per sekund, eller så kan du använda en avbildning som gör att du kan använda en egen SQL Server-licens. Du kan också välja att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad utgåva (utvecklare eller Express) eller genom att återanvända en lokal licens. Om du använder din egen licens måste du ha [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur kan jag migrera min lokala SQL Server-databas till molnet?**

   Skapa först en virtuell Azure-dator med en SQL Server instans. Migrera sedan dina lokala databaser till den instansen. Information om strategier för datamigrering finns i [Migrera en SQL Server databas till SQL Server i en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenser

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Det finns två sätt att göra detta på. Du kan etablera en av de [avbildningar av virtuella datorer som har stöd för licenser](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), som även kallas bring-your-own-license (BYOL). Ett annat alternativ är att kopiera SQL Server-installationsmedia till en virtuell Windows Server-dator och installera SQL Server på den virtuella datorn. Men om du installerar SQL Server manuellt finns det ingen portalintegrering, och SQL Server IaaS Agent-tillägget stöds inte. Därmed fungerar inte funktioner som automatisk säkerhetskopiering och automatisk uppdatering i det här scenariot. Därför rekommenderar vi att du använder en av BYOL-galleriavbildningarna. Om du vill använda BYOL eller dina egna SQL Server Media på en virtuell Azure-dator måste du [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Måste jag betala för att licensiera SQL Server på en virtuell Azure-dator om den endast används för vänteläge/redundans?**

   Om du har Software Assurance och använder Licensmobilitet som det beskrivs i [vanliga frågor och svar om licensiering för virtuella datorer](https://azure.microsoft.com/pricing/licensing-faq/)behöver du inte betala till licens en SQL Server som deltar som en passiv sekundär replik i en ha-distribution. I annat fall behöver du betala för att licensiera den.

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Ja. Du kan enkelt flytta mellan de två licensierings modellerna, om du ursprungligen började med en Galleri avbildning enligt principen betala per användning. Du kan dock inte växla din licens till betala per användning om du ursprungligen började med en BYOL-avbildning. Mer information finns i [så här ändrar du licensierings modellen för en SQL Server VM](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Den här funktionen är för närvarande endast tillgänglig för kunder med offentliga moln.

1. **Bör jag använda BYOL-avbildningar eller SQL VM RP för att skapa den nya virtuella SQL-datorn?**

   BYOL-avbildningar (Bring-your-own-license) är endast tillgängliga för EA-kunder. Andra kunder som har Software Assurance bör använda SQL VM Resource Provider för att skapa en virtuell SQL-dator med [Azure Hybrid-förmån (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Kräver byte av licensieringsmodeller någon avbrottstid för SQL Server?**

   Nej. Att [ändra licensierings modellen](virtual-machines-windows-sql-ahb.md) kräver ingen stillestånds tid för SQL Server eftersom ändringen börjar gälla omedelbart och inte kräver omstart av den virtuella datorn. Om du vill registrera SQL Server VM med den virtuella SQL-IaaS är tillägget för [SQL-tillägget](virtual-machines-windows-sql-server-agent-extension.md) en förutsättning och installationen av SQL IaaS-tillägget startar om SQL Servers tjänsten. Om SQL IaaS-tillägget måste installeras bör det därför ske medan underhåll pågår. 

1. **Kan CSP-prenumerationer aktivera Azure Hybrid-förmån?**

   Ja, Azure Hybrid-förmån är tillgängligt för CSP-prenumerationer. CSP-kunder bör först distribuera en avbildning med betala per användning och sedan [ändra licensierings modellen](virtual-machines-windows-sql-ahb.md) till egen licens.  

1. **Blir det några ytterligare kostnader om jag registrerar min virtuella dator med den nya resursprovidern för virtuell SQL-dator?**

   Nej. Resursprovidern för virtuell SQL-dator ger bara ytterligare hanterbarhet för SQL Server på virtuella Azure-datorer utan extra kostnad. 

1. **Är resursprovidern för virtuell SQL-dator tillgänglig för alla kunder?**
 
   Ja. Alla kunder kan registrera med den nya resursprovidern för virtuell SQL-dator. Men endast kunder med Software Assurance-förmånen kan aktivera [Azure Hybrid-förmån (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (eller BYOL) på en SQL Server VM. 

1. **Vad händer med _Microsoft. SqlVirtualMachine_ -resursen om den virtuella dator resursen flyttas eller tas bort?** 

   När resursen Microsoft. Compute/VirtualMachine släpps eller flyttas, meddelas den associerade Microsoft. SqlVirtualMachine-resursen för att asynkront replikera åtgärden.

1. **Vad händer med den virtuella datorn om _Microsoft. SqlVirtualMachine_ -resursen släpps?**

    Resursen Microsoft. Compute/VirtualMachine påverkas inte när resursen Microsoft. SqlVirtualMachine släpps. Licensierings ändringarna kommer dock att återställas till den ursprungliga avbildnings källan. 

1. **Är det möjligt att registrera egendistribuerade virtuella SQL-datorer med resursprovidern för virtuell SQL-dator?**

    Ja. Om du har distribuerat SQL Server från ditt eget medium och installerat SQL IaaS-tillägget kan du registrera dina SQL Server VM med resurs leverantören för att få de hanterings förmåner som ges av tillägget för SQL-IaaS. Du kan dock inte konvertera en egendistribuerad virtuell SQL-dator till betala per användning.

1. **Är det möjligt att växla licensierings modell på en SQL Server VM distribuerad med hjälp av den klassiska modellen?**

   Nej. Det finns inte stöd för att ändra licensierings modell på en klassisk virtuell dator. Du kan migrera den virtuella datorn till Resource Manager-modellen (ARM) och registrera dig hos resurs leverantören för SQL-VM. När den virtuella datorn har registrerats med resurs leverantören för SQL-VM kommer licensierings modell ändringar att vara tillgängliga på den virtuella datorn. 
   

## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner på standardinstansen?**

   Ja. Installations mediet för SQL Server finns i en mapp på **C** -enheten. Kör **Setup. exe** från den platsen för att lägga till nya SQL Server instanser eller ändra andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel automatisk säkerhets kopiering, automatisk uppdatering och Azure Key Vault-integrering, endast fungerar mot standard instansen eller en namngiven instans som kon figurer ATS korrekt (se fråga 3). 

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja, men det finns vissa överväganden. Som det anges i föregående svar finns det funktioner som förlitar sig på [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md).  Om du avinstallerar standard instansen utan att ta bort IaaS-tillägget, fortsätter tillägget att söka efter det och kan generera händelse logg fel. Dessa fel kommer från följande två källor: **Microsoft SQL Server hantering av autentiseringsuppgifter** och **Microsoft SQL Server IaaS**-agenten. Ett av felen kan vara något av liknar följande:

      Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig.

   Om du väljer att avinstallera standard instansen avinstallerar du även [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md) .

1. **Kan jag använda en namngiven instans av SQL Server med IaaS-tillägget**?
   
   Ja, om den namngivna instansen är den enda instansen på SQL Server, och om den ursprungliga standard instansen avinstallerades [korrekt](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). Om det inte finns någon standard instans och det finns flera namngivna instanser på en enda SQL Server VM, kommer IaaS-tillägget inte att installeras. 

1. **Kan jag ta bort SQL Server helt från en virtuell SQL-dator?**

   Ja, men du kommer att fortsätta att debiteras för din virtuella SQL-dator enligt beskrivningen i [pris vägledningen för SQL Server virtuella Azure-datorer](virtual-machines-windows-sql-server-pricing-guidance.md). Om du inte längre behöver SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Sedan kan du ta bort den virtuella SQL Server-datorn.
   
## <a name="updating-and-patching"></a>Uppdatering och uppdatering

1. **Hur gör jag för att ändra till en annan version/utgåva av SQL Server på en virtuell Azure-dator?**

   Kunder kan ändra sin version/utgåva av SQL Server med hjälp av installationsmediet som innehåller den önskade versionen eller utgåvan av SQL Server. När utgåvan har ändrats använder du Azure-portalen för att ändra versionsegenskapen för den virtuella datorn så att faktureringen för den virtuella datorn återspeglas korrekt. Mer information finns i avsnittet [om ändrings versioner av en SQL Server VM](virtual-machines-windows-sql-change-edition.md). 


1. **Hur tillämpas uppdateringar och service packs på en SQL Server VM?**

   Virtuella datorer ger dig kontroll över värddatorn, inklusive när och hur du tillämpar uppdateringar. För operativ systemet kan du manuellt tillämpa Windows-uppdateringar, eller så kan du aktivera en schemaläggnings tjänst som kallas [automatiserad uppdatering](virtual-machines-windows-sql-automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

## <a name="general"></a>Allmänt

1. **Stöds SQL Server FCI (failover Cluster instances) på virtuella Azure-datorer?**

   Ja. Du kan [skapa ett Windows-redundanskluster på Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) och använda LAGRINGSDIRIGERING (S2D) för kluster lagringen. Du kan också använda klustring eller lagrings lösningar från tredje part enligt beskrivningen i [hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > För närvarande stöds inte [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md) för SQL Server FCI på Azure. Vi rekommenderar att du avinstallerar tillägget från virtuella datorer som ingår i FCI. Det här tillägget har stöd för funktioner, till exempel automatisk säkerhets kopiering och uppdatering och vissa Portal funktioner för SQL. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har avinstallerats.

1. **Vad är skillnaden mellan virtuella SQL-datorer och tjänsten SQL Database?**

   Att köra SQL Server på en virtuell Azure-dator är inte detsamma som att köra SQL Server i ett fjärranslutet Data Center. [SQL Database](../../../sql-database/sql-database-technical-overview.md) erbjuder däremot databas som en tjänst. Med SQL Database har du inte till gång till de datorer som är värdar för dina databaser. En fullständig jämförelse finns i [Välj ett moln SQL Server alternativ: Azure SQL-databas (PaaS) eller SQL Server på virtuella Azure-datorer](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)(IaaS).

1. **Hur gör jag för att installera SQL Data Tools på min virtuella Azure-dator?**

    Hämta och installera SQL Data Tools från [Microsoft SQL Server data verktyg – Business Intelligence för Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Stöds distribuerade transaktioner med MSDTC på SQL Server virtuella datorer?**
   
    Ja. Lokal DTC stöds för SQL Server 2016 SP2 och senare. Program måste dock testas när du använder Always on-tillgänglighetsgrupper, eftersom transaktioner under flygningen inte går att redundansväxla och måste göras om. Klustrad DTC är tillgänglig från och med Windows Server 2019. 

## <a name="resources"></a>Resurser

**Virtuella Windows-datorer**:

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en SQL Server virtuell Windows-dator](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Program mönster och utvecklings strategier för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQL Server på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en virtuell SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
