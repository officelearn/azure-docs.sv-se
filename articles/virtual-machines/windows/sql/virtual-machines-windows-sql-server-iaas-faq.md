---
title: "SQLServer på Windows Azure virtuella datorer och svar | Microsoft Docs"
description: "Den här artikeln innehåller svar på vanliga frågor och svar om att köra SQL Server på virtuella Azure-datorer."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/14/2017
ms.author: v-shysun
ms.openlocfilehash: 141dd1fe9e727f430b7c45dbb798f5471167c355
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-windows-azure-virtual-machines"></a>Vanliga frågor och svar för SQL Server på virtuella Windows Azure-datorer

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på virtuella Windows Azure-datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Den här artikeln fokuserar på specifika för SQL Server på virtuella Windows-datorer. Om du kör SQL Server på virtuella Linux-datorer finns i [Linux vanliga frågor och svar](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Bilder

1. **Vilken SQL Server-galleriavbildningar av virtuella datorer är tillgängliga?**

   Azure underhåller avbildningar av virtuella datorer för alla stöds större versioner av SQL Server på alla versioner för Windows- och Linux. Mer information finns i den fullständiga listan över [Windows VM-avbildningar](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) och [Linux VM-avbildningar](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Uppdateras befintliga galleriavbildningar för SQL Server-virtuella datorn?**

   Varje månad, SQL Server-avbildningar i galleriet för virtuella datorer uppdateras med den senaste Windows och Linux uppdateras. Detta inkluderar alla uppdateringar som markeras viktiga i Windows Update, inklusive viktiga uppdateringar för SQL Server-säkerhet och servicepack för Windows-avbildningar. Detta inkluderar de senaste uppdateringarna för system för Linux-avbildningar. SQL Server kumulativa uppdateringar hanteras annorlunda för Linux och Windows. För Linux ingår kumulativa uppdateringar för SQL Server även i uppdateringen. Men just nu, uppdateras inte virtuella Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Kan hämta SQL Server avbildningar av virtuella datorer bort från galleriet?**

   Ja. Azure har endast en bild per högre version och utgåva. Till exempel när ett nytt SQL Server servicepack släpps, Azure lägger till en ny avbildning i galleriet för detta servicepack. SQL Server-avbildning för tidigare servicepack tas omedelbart bort från Azure-portalen. Det är dock fortfarande tillgängligt för att etablera från PowerShell för de kommande tre månaderna. Avbildningen som tidigare service pack är inte längre tillgänglig efter tre månader. Ta bort principen gäller också om en SQL Server-version blir stöds inte när den når slutet av dess livscykel.

1. **Är det möjligt att ställa in konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. Du måste välja en av de angivna avbildningarna för virtuella galleriet bilder med SQL Server.

## <a name="creation"></a>Skapa

1. **Hur skapar jag en virtuell Azure-dator med SQL Server**

   Den enklaste lösningen är att skapa en virtuell dator med SQL Server. En självstudiekurs om registrerar dig för Azure och skapar en SQL-VM från portalen finns [etablera en virtuell dator med SQL Server i Azure portal](virtual-machines-windows-portal-sql-server-provision.md). Du kan välja en avbildning av virtuell dator som använder per minut SQL Server-licensiering eller du kan använda en avbildning som gör att du kan använda din egen SQL Server-licens. Du har också alternativet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad version (utvecklare eller Express) eller genom att återanvända en lokal licens. Om du tar din egen licens måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur kan jag migrera Mina lokala SQL Server-databas till molnet?**

   Först skapa en virtuell Azure-dator med en SQL Server-instans. Migrera lokala databaser till den instansen. Strategier för migrering av data, se [migrera en SQL Server-databas till SQL Server i en Azure VM](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierad version av SQL Server på en Azure VM?**

   Det finns två sätt att göra detta. Du kan etablera ett av de [avbildningar av virtuella datorer som har stöd för licenser](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Ett annat alternativ är att kopiera SQL Server-installationsmedia till en Windows Server-VM och installera SQL Server på den virtuella datorn. Licensiering skäl, måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Kan jag ändra en virtuell dator om du vill använda en egen SQL Server-licens om den har skapats från en betalning per användning galleriavbildningar?**

   Nej. Du kan inte växla från per minut licensiering med din egen licens. Skapa en ny virtuell Azure-dator med någon av de [avbildningar med BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), och sedan migrera dina databaser till den nya servern med hjälp av standard [tekniker för migrering av data](virtual-machines-windows-migrate-sql.md).

1. **Måste jag betala till SQL Server på en virtuell dator i Azure-licens om den används endast för vänteläge/redundans?**

   Om du har Software Assurance och använda licensera Mobility enligt beskrivningen i [virtuella vanliga frågor om licensiering](http://azure.microsoft.com/pricing/licensing-faq/) och du inte behöver betala för att licensiera en SQL-Server ingår som en passiv sekundär replik i en distribution med hög tillgänglighet. I annat fall måste du betala licensierar den.


## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner på standardinstansen?**

   Ja. SQL Server-installationsmedia finns i en mapp på den **C** enhet. Kör **Setup.exe** från den platsen för att lägga till nya SQL Server-instanser eller ändrar andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering bara köras mot standardinstansen.

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja. Men det finns vissa saker. Enligt informationen i det föregående svaret funktioner som är beroende av den [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) fungerar bara på standardinstansen. Om du avinstallerar standardinstansen tillägget fortsätter att söka efter den och kan ge fel i händelseloggen. Dessa fel är från följande två källor: **hantering av Microsoft SQL Server-inloggningsuppgifter** och **Microsoft SQL Server IaaS Agent**. Ett fel kan vara liknar följande:

      Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning till SQL Server. Servern hittades inte eller är inte tillgänglig.

   Om du bestämmer dig att avinstallera standardinstansen, avinstallera även den [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) samt.
   
   >[!NOTE]
   >En virtuell dator för SQL Server Azure faktureras enligt beskrivningen i [priser för SQL Server Azure Virtual Machines](virtual-machines-windows-sql-server-pricing-guidance.md). Om du tar bort SQL Server, fortsätter avgifterna för användning. Om du behöver inte längre SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Du kan ta bort den virtuella datorn för SQL Server.

## <a name="updating-and-patching"></a>Uppdatering och korrigering

1. **Hur uppgraderar till en ny version/utgåva av SQL Server i en Azure VM?**

   Det finns för närvarande ingen uppgradering på plats för SQL Server som körs i en Azure VM. Skapa en ny Azure virtuell dator med den önskade SQL Server-version/utgåvan och sedan migrera dina databaser till den nya servern med hjälp av standard [tekniker för migrering av data](virtual-machines-windows-migrate-sql.md).

1. **Hur uppdateringar och service packs tillämpas på en SQL Server-VM?**

   Virtuella datorer ger dig kontroll över värddatorn, inklusive när och hur du tillämpar uppdateringar. För operativsystemet, kan du manuellt koppla windows-uppdateringar eller så kan du aktivera en schemaläggningstjänsten kallas [automatisk uppdatering](virtual-machines-windows-sql-automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

## <a name="general"></a>Allmänt

1. **SQL Server Failover Cluster instanser (FCI) stöds på virtuella Azure-datorer?**

   Ja. Du kan [skapa ett redundanskluster i Windows på Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) och använda Storage Spaces Direct (S2D) för klusterlagring. Du kan också använda redundanskluster eller lagring tredjepartslösningar som beskrivs i [hög tillgänglighet och katastrofåterställning återställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

1. **Vad är skillnaden mellan SQL virtuella datorer och SQL Database-tjänsten?**

   Kör begreppsmässigt SQL Server på en virtuell Azure-dator är inte detsamma som kör SQL Server i en fjärransluten datacenter. Däremot [SQL-databas](../../../sql-database/sql-database-technical-overview.md) erbjuder databasen som en tjänst. Med SQL-databas har inte åtkomst till de datorer som värd för dina databaser. En fullständig jämförelse finns [Välj ett moln SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på Azure Virtual Machines (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Hur installerar verktyg för SQL-Data på min Azure VM?**

    Hämta och installera verktyg för SQL-Data från [Microsoft SQL Server Data Tools - Business Intelligence för Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Resurser

**Virtuella Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en virtuell dator med SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQLServer på en virtuell dator i Azure](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och Haveriberedskap för SQLServer på virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Programmet mönster och utvecklingsstrategier för SQLServer på virtuella Azure-datorer](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server-VM för Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
