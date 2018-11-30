---
title: SQLServer på Windows-datorer i Azure vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om SQL Server körs på virtuella Azure-datorer.
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
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: edfd2e9e03aefa4833c8472a43d4857f08b95780
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495481"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Vanliga frågor om SQL Server som körs på Windows-datorer i Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på Windows-datorer i Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Den här artikeln fokuserar på frågor som är specifika för SQL Server på virtuella Windows-datorer. Om du kör SQL Server på virtuella Linux-datorer finns i den [Linux vanliga frågor och svar](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bilder

1. **Vilken SQL Server-galleriavbildningar av virtuella datorer är tillgängliga?**

   Azure underhåller avbildningar av virtuella datorer för alla stöds större versioner av SQL Server i alla utgåvor för både Windows och Linux. Mer information finns i den fullständiga listan med [Windows VM-avbildningar](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) och [Linux VM-avbildningar](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Befintliga SQL Server VM-galleriavbildningar uppdateras?**

   Varje två månaderna, SQL Server-avbildningar i galleriet för virtuella datorer uppdateras med den senaste Windows och Linux uppdateras. Detta inkluderar alla uppdateringar som markeras som viktiga i Windows Update, inklusive viktiga uppdateringar för SQL Server-säkerhet och servicepack för Windows-avbildningar. Detta omfattar de senaste uppdateringarna för system för Linux-avbildningar. Kumulativa uppdateringar för SQL Server hanteras annorlunda för Linux och Windows. För Linux ingår kumulativa uppdateringar för SQL Server även i uppdateringen. Men för närvarande uppdateras inte Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Kan det få bort SQL Server-avbildningar från galleriet?**

   Ja. Azure underhåller en avbildning per större version och utgåva. Till exempel när ett nytt SQL Server servicepack släpps, lägger Azure till en ny avbildning i galleriet för detta servicepack. SQL Server-avbildning för tidigare servicepack tas omedelbart bort från Azure-portalen. Det är dock fortfarande tillgängligt för etablering från PowerShell för de kommande tre månaderna. Föregående bild av service pack är inte längre tillgänglig efter tre månader. Den här principen för borttagning av skulle gäller även om en SQL Server-version blir stöds inte när den når slutet av sin livscykel.

1. **Kan jag skapa en VHD-avbildning från en SQL Server VM?**

   Ja, men det finns några saker. Om du distribuerar den här virtuella Hårddisken till en ny virtuell dator i Azure kan göra du inte ge konfiguration av SQL Server-avsnittet i portalen. Du måste sedan hantera SQL Server-konfigurationsalternativ via PowerShell. Dessutom debiteras du för frekvensen för SQL-VM din avbildning ursprungligen baserades på. Detta gäller även om du tar bort SQL Server från den virtuella Hårddisken innan du distribuerar. 

1. **Är det möjligt att ställa in konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. För galleriavbildningar för virtuella datorer med SQL Server, måste du välja något av de angivna avbildningarna.

## <a name="creation"></a>Skapa

1. **Hur skapar jag en Azure virtuell dator med SQL Server?**

   Den enklaste lösningen är att skapa en virtuell dator med SQL Server. En självstudiekurs om registreringen i Azure och skapa en SQL-VM från portalen finns i [etablera en virtuell dator med SQL Server i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md). Du kan välja en avbildning av virtuell dator som använder betala per sekund SQL Server-licensiering eller du kan använda en avbildning som gör att du kan använda din egen SQL Server-licens. Du har också möjlighet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad version (utvecklare eller Express) eller genom att återanvända en licens för den lokala. Om du använda din egen licens måste du ha [License Mobility genom Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur kan jag migrera min lokala SQL Server-databas till molnet?**

   Skapa först en Azure virtuell dator med en SQL Server-instans. Sedan migrera dina lokala databaser till den instansen. Migreringsstrategier för data, se [migrera en SQL Server-databas till SQL Server i en Azure VM](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierad version av SQL Server på en Azure-dator?**

   Det finns två sätt att göra detta. Du kan etablera en av de [avbildningar av virtuella datorer som har stöd för licenser](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), som kallas också bring-your-own-license (BYOL). Ett annat alternativ är att kopiera SQL Server-installationsmediet till en Windows Server-dator och installera SQL Server på den virtuella datorn. Men om du installerar SQL Server manuellt, det finns inga portalintegrering och SQL Server IaaS Agent-tillägget stöds inte, så funktioner som automatisk säkerhetskopiering och automatisk uppdatering fungerar inte i det här scenariot. Därför bör du använda en av galleriavbildningar BYOL. Om du vill använda BYOL eller din egen SQL Server-media på en Azure virtuell dator, måste du ha [License Mobility genom Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).


1. **Måste jag betala till SQL Server på en Azure VM-licens om den används endast för vänteläge/redundans?**

   Om du har Software Assurance och använda License Mobility enligt beskrivningen i Virtual Machine vanliga frågor om licensiering,] (https://azure.microsoft.com/pricing/licensing-faq/) och du inte behöver betala för att licensiera en SQL-Server som deltar som en passiv sekundär replik i en distribution med hög tillgänglighet. I annat fall måste betala att licensiera den.

1. **Kan jag ändra en virtuell dator om du vill använda min egen SQL Server-licens om den har skapats från en användningsbaserad galleriavbildningar?**

   Ja. Du kan flytta enkelt flytta mellan två licensieringsmodeller finns, oavsett den avbildning som ursprungligen har distribuerats. Mer information finns i [ändra så att licensieringsmodellen för en SQL-VM](virtual-machines-windows-sql-ahb.md).

1. **Bör jag använda BYOL-avbildningar eller SQL VM RP för att skapa den nya SQL VM?**

   Bring-your-own-license (BYOL)-avbildningar är bara tillgängliga för EA-kunder. Andra kunder som har Software Assurance ska använda SQL VM-resursprovidern för att skapa en SQL-VM med [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Byta licensieringsmodellerna kräver inget driftstopp för SQL Server?**

   Nej. [Ändra så att licensieringsmodellen](virtual-machines-windows-sql-ahb.md) kräver inte någon avbrottstid för SQL Server när ändringen är omedelbart verksam och inte kräver en omstart av den virtuella datorn. 

1. **CSP-prenumerationer kan aktivera Azure Hybrid-förmånen?**

   Ja. [Ändra så att licensieringsmodellen](virtual-machines-windows-sql-ahb.md) är tillgänglig för CSP-prenumerationer. 

1. **Medför ytterligare kostnader när du registrerar den virtuella datorn med den nya providern för SQL VM-resurs?**

   Nej. Resursprovidern SQL VM kan bara ytterligare hanterbarhet för SQL Server på virtuella Azure-datorer med inga ytterligare avgifter. 

1. **Finns SQL VM-resursprovidern för alla kunder?**
 
   Ja. Alla kunder kan registrera med den nya SQL-VM-resursprovidern. Dock bara kunder med Software Assurance-förmån kan aktivera den [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (eller BYOL) på en SQL Server VM. 

1. **Vad händer med _* Microsoft.SqlVirtualMachine_* resursen om den Virtuella datorresursen flyttats eller tagits bort?** 

   När Microsoft.Compute/VirtualMachine resursen tas bort eller flyttas, och sedan den associerade resursen Microsoft.SqlVirtualMachine meddelas att asynkront replikeras igen.

1. **Vad händer med den virtuella datorn om _* Microsoft.SqlVirtualMachine_* resursen tas bort?**

   Resursen Microsoft.Compute/VirtualMachine påverkas inte när Microsoft.SqlVirtualMachine resursen tas bort. Som standard men licensiering ändringarna tillbaka till den ursprungliga källan för avbildningen. 

1. **Är det möjligt att registrera lokal distribuerade SQL Server-datorer med SQL VM-resursprovidern?**

   Ja. Om du har distribuerat SQL Server från din egen media, kan du registrera din SQL-VM med resursprovidern att hämta de hanterbarhet fördelarna med SQL IaaS-tillägget. Du kan dock inte att konvertera till PAYG-en lokal distribuerade SQL-VM. 

## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner på standardinstans?**

   Ja. Media för installation av SQL Server finns i en mapp på den **C** enhet. Kör **Setup.exe** från den platsen för att lägga till nya SQL Server-instanser eller för att ändra andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering, bara köras mot standardinstansen.

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja, men det finns några överväganden. Enligt informationen i föregående svar, funktioner som förlitar sig på den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md) fungerar bara på standardinstansen. Om du avinstallerar standardinstansen tillägget fortsätter att söka efter den och generera felen i händelseloggen. Felen är från följande två källor: **Microsoft SQL Server-Autentiseringshantering** och **Microsoft SQL Server IaaS Agent**. Felen kan vara något av liknar följande:

      Ett nätverksrelaterat eller instans-specifika fel uppstod när upprättar en anslutning till SQL Server. Servern hittades inte eller var inte tillgänglig.

   Om du bestämmer dig att avinstallera standardinstansen, avinstallera även den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md) samt.

1. **Kan jag ta bort SQL Server helt från en SQL-VM?**

   Ja, men fortsätter du att debiteras för din SQL-VM, enligt beskrivningen i [Pricing guidance för SQL Server Azure VM](virtual-machines-windows-sql-server-pricing-guidance.md). Om du behöver inte längre SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Du kan sedan ta bort den SQL Server-datorn.
   
## <a name="updating-and-patching"></a>Uppdatering och uppdatera

1. **Hur uppgraderar jag till en ny version/utgåva av SQL Server i en Azure-dator?**

   För närvarande finns ingen uppgradering på plats för SQL Server som körs i en Azure VM. Skapa en ny Azure-dator med den önskade versionen/utgåvan av SQL Server och migrera sedan databaserna till den nya servern med hjälp av standard [för datamigrering](virtual-machines-windows-migrate-sql.md).

1. **Hur uppdateringar och service packs tillämpas på en SQL Server VM?**

   Virtuella datorer ger dig kontroll över värddatorn, även när och hur du tillämpar uppdateringar. För operativsystemet, kan du manuellt installera windows-uppdateringar, eller du kan aktivera en schemaläggningstjänst som kallas [automatisk uppdatering](virtual-machines-windows-sql-automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

## <a name="general"></a>Allmänt

1. **SQL Server Failover Cluster instanser (FCI) stöds på Azure Virtual Machines?**

   Ja. Du kan [skapa ett Windows-redundanskluster i Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) och använda Lagringsdirigering (S2D) för klusterlagring. Du kan också använda klustring eller lagring tredjepartslösningar som beskrivs i [hög tillgänglighet och katastrofåterställning återställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > För närvarande den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md) stöds inte för FCI för SQL Server på Azure. Vi rekommenderar att du avinstallerar tillägget från virtuella datorer som deltar i Redundansklusterinstanser. Det här tillägget har stöd för funktioner, till exempel automatisk säkerhetskopiering och uppdatering och vissa portalen funktioner för SQL. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har avinstallerats.

1. **Vad är skillnaden mellan virtuella datorer med SQL och SQL Database-tjänsten?**

   Den övergripande som kör SQL Server på virtuella Azure-datorer är inte som skiljer sig från som kör SQL Server i ett fjärranslutet datacenter. Däremot [SQL Database](../../../sql-database/sql-database-technical-overview.md) erbjuder databas som en tjänst. Med SQL Database kan har du inte åtkomst till de datorer som värd för dina databaser. En fullständig jämförelse i [Välj ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på Azure Virtual Machines (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Hur installerar jag verktyg för SQL-Data på min Azure-VM**

    Ladda ned och installera verktyg för SQL-Data från [Microsoft SQL Server Data Tools – Business Intelligence för Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Resurser

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
