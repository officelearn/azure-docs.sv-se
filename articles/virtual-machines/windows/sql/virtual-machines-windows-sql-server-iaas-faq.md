---
title: Vanliga frågor och svar om SQL Server på virtuella Datorer i Windows | Microsoft-dokument
description: Den här artikeln innehåller svar på vanliga frågor om hur du kör SQL Server på virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249742"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Vanliga frågor och svar om SQL Server som körs på virtuella Windows-datorer i Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om hur du kör [SQL Server på Virtuella Windows-datorer i Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Den här artikeln fokuserar på problem som är specifika för SQL Server på virtuella datorer i Windows. Om du kör SQL Server på Virtuella Linux-datorer läser du [vanliga frågor och svar](../../linux/sql/sql-server-linux-faq.md)om Linux .

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Bilder

1. **Vilka sql server-galleriavbildningar för virtuella datorer är tillgängliga?** 

   Azure underhåller virtuella datoravbildningar för alla större versioner av SQL Server som stöds på alla versioner för både Windows och Linux. Mer information finns i den fullständiga listan över [Windows VM-avbildningar](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) och [Linux VM-avbildningar](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Uppdateras befintliga virtuella sql server-galleriavbildningar?**

   Varannan månad uppdateras SQL Server-avbildningar i galleriet för virtuella datorer med de senaste Windows- och Linux-uppdateringarna. För Windows-avbildningar innehåller detta alla uppdateringar som är markerade som viktiga i Windows Update, inklusive viktiga SQL Server-säkerhetsuppdateringar och Service Pack-enheter. För Linux-avbildningar inkluderar detta de senaste systemuppdateringarna. Kumulativa SQL Server-uppdateringar hanteras på olika sätt för Linux och Windows. För Linux ingår även kumulativa SQL Server-uppdateringar i uppdateringen. Men för närvarande uppdateras inte Windows virtuella datorer med kumulativa uppdateringar för SQL Server eller Windows Server.

1. **Kan SQL Server virtuella datoravbildningar tas bort från galleriet?**

   Ja. Azure underhåller bara en avbildning per huvudversion och utgåva. När ett nytt SERVICE Pack från SQL Server släpps lägger Azure till en ny avbildning i galleriet för Service Pack.For example, when a new SQL Server Service Pack is released, Azure adds a new image to the gallery for that Service Pack. SQL Server-avbildningen för det tidigare Service Pack-paketet tas omedelbart bort från Azure-portalen. Den är dock fortfarande tillgänglig för etablering från PowerShell för de kommande tre månaderna. Efter tre månader är den tidigare Service Pack-avbildningen inte längre tillgänglig. Den här borttagningsprincipen skulle också gälla om en SQL Server-version inte stöds när den når slutet av livscykeln.


1. **Är det möjligt att distribuera en äldre avbildning av SQL Server som inte syns i Azure-portalen?**

   Ja, genom att använda PowerShell. Mer information om hur du distribuerar virtuella SQL Server-datorer med PowerShell finns i Så här etablerar du [virtuella SQL Server-datorer med Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Kan jag skapa en generaliserad Azure SQL Server Marketplace-avbildning av min virtuella SQL Server-dator och använda den för att distribuera virtuella datorer?**

   Ja, men du måste sedan [registrera varje VIRTUELL SQL Server med SQL Server VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) för att hantera din VIRTUELLA SQL Server-dator i portalen, samt använda funktioner som automatisk korrigering och automatiska säkerhetskopieringar. När du registrerar dig hos resursprovidern måste du också ange licenstypen för varje VIRTUELL SQL Server. 

1. **Kan jag använda min egen virtuella hårddisk för att distribuera en VIRTUELL SQL Server?**

   Ja, men du måste sedan [registrera varje VIRTUELL SQL Server med SQL Server VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) för att hantera din VIRTUELLA SQL Server-dator i portalen, samt använda funktioner som automatisk korrigering och automatiska säkerhetskopieringar.

1. **Är det möjligt att ställa in konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. För galleriavbildningar för virtuella datorer som innehåller SQL Server måste du välja en av de angivna avbildningarna antingen via Azure-portalen eller via [PowerShell](virtual-machines-windows-ps-sql-create.md). Du kan dock distribuera en Virtuell Windows och självinstallera SQL Server till den. Du måste sedan [registrera din VIRTUELLA SQL Server-dator med SQL Server VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) för att hantera din VIRTUELLA SQL Server-dator i portalen, samt använda funktioner som automatisk korrigering och automatiska säkerhetskopieringar. 


## <a name="creation"></a>Skapa

1. **Hur skapar jag en virtuell Azure-dator med SQL Server?**

   Den enklaste metoden är att skapa en virtuell dator som innehåller SQL Server. En självstudiekurs om hur du registrerar dig för Azure och skapar en virtuell SQL Server-dator från portalen finns [i Etablera en virtuell SQL Server-dator i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md). Du kan välja en avbildning för virtuella datorer som använder SQL Server-licensiering per sekund, eller så kan du använda en avbildning som gör att du kan ta med din egen SQL Server-licens. Du har också möjlighet att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad utgåva (Utvecklare eller Express) eller genom att återanvända en lokal licens. Var noga med att [registrera din VIRTUELLA SQL Server-dator med SQL Server VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) för att hantera din VIRTUELLA SQL Server-dator i portalen, samt använda funktioner som automatisk korrigering och automatiska säkerhetskopieringar. Om du tar med din egen licens måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur migrerar jag min lokala SQL Server-databas till molnet?**

   Skapa först en virtuell Azure-dator med en SQL Server-instans. Migrera sedan lokala databaser till den instansen. Strategier för datamigrering finns i [Migrera en SQL Server-databas till SQL Server i en Virtuell Azure.](virtual-machines-windows-migrate-sql.md)

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Det finns tre sätt att göra detta. Om du är en EA-kund (Enterprise Agreement) kan du tillhandahålla en av de [virtuella datoravbildningar som stöder licenser](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), som också kallas bring-your-own-license (BYOL). Om du har [programvarusäkring](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)kan du aktivera [Azure Hybrid-förmånen](virtual-machines-windows-sql-ahb.md) på en befintlig PAY-as-you-go -avbildning.If you have software assurance , you can enable the Azure Hybrid Benefit on an existing pay-as-you-go (PAYG) image. Du kan också kopiera SQL Server-installationsmediet till en virtuell Windows Server-dator och sedan installera SQL Server på den virtuella datorn. Var noga med att registrera din VIRTUELLA SQL Server-dator med [resursleverantören](virtual-machines-windows-sql-register-with-resource-provider.md) för funktioner som portalhantering, automatisk säkerhetskopiering och automatisk korrigering. 

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Ja. Du kan enkelt växla en PAY-as-you-go -bild (PAYG) för att ge din egen licens (BYOL) genom att aktivera [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Mer information finns i [Så här ändrar du licensieringsmodellen för en VIRTUELL SQL Server](virtual-machines-windows-sql-ahb.md). För närvarande är den här funktionen endast tillgänglig för public cloud-kunder.

1. **Kräver byte av licensieringsmodeller någon avbrottstid för SQL Server?**

   Nej. [Om du ändrar licensieringsmodellen](virtual-machines-windows-sql-ahb.md) krävs inga driftstopp för SQL Server eftersom ändringen träder i kraft omedelbart och inte kräver en omstart av den virtuella datorn. För att registrera din VIRTUELLA SQL Server-dator med SQL Server VM-resursprovidern är [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md) en förutsättning och om du installerar SQL IaaS-tillägget i _fullständigt_ läge startas SQL Server-tjänsten om. Som sådan, om SQL IaaS-tillägget behöver installeras, antingen installera den i _lättläge_ för begränsad funktionalitet, eller installera den i _fullt_ läge under ett underhållsfönster. SQL IaaS-tillägget som installeras i _lättviktsläge_ kan uppgraderas till _fullt_ läge när som helst, men kräver en omstart av SQL Server-tjänsten. 
   
1. **Är det möjligt att byta licensieringsmodell på en VIRTUELL SQL Server som distribueras med klassisk modell?**

   Nej. Ändra licensieringsmodell stöds inte på en klassisk virtuell dator. Du kan migrera den virtuella datorn till Azure Resource Manager-modellen och registrera dig hos SQL Server VM-resursprovidern. När den virtuella datorn har registrerats hos SQL Server VM-resursprovidern blir licensmodelländringar tillgängliga på den virtuella datorn.

1. **Kan jag använda Azure-portalen för att hantera flera instanser på samma virtuella dator?**

   Nej. Portalhantering är en funktion som tillhandahålls av SQL Server VM-resursprovidern, som är beroende av tillägget SQL Server IaaS Agent. Därför gäller samma begränsningar för resursprovidern som för tillägget. Portalen kan antingen bara hantera en standardinstans eller en namngiven instans, så länge den har konfigurerats korrekt. Mer information om dessa begränsningar finns i [SQL Server IaaS-agenttillägg](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Kan CSP-prenumerationer aktivera Azure Hybrid Benefit?**

   Ja, Azure Hybrid-förmånen är tillgänglig för CSP-prenumerationer. CSP-kunder bör först distribuera en pay-as-you-go-avbildning och sedan [ändra licensieringsmodellen](virtual-machines-windows-sql-ahb.md) för att ta med dig din egen licens.
   
 
1. **Måste jag betala för att licensiera SQL Server på en virtuell Azure-dator om den endast används för vänteläge/redundans?**

   Om du vill ha en kostnadsfri passiv licens för en sekundär tillgänglighetsgrupp för vänteläge eller redundanskluster måste du uppfylla alla följande kriterier som beskrivs i [produktlicensvillkoren:](https://www.microsoft.com/licensing/product-licensing/products)

   1. Du har [licensmobilitet](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) via [Software Assurance.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. Den passiva SQL Server-instansen betjänar inte SQL Server-data till klienter eller kör aktiva SQL Server-arbetsbelastningar. Det används endast för att synkronisera med den primära servern och på annat sätt underhålla den passiva databasen i ett varmt vänteläge. Om data betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server-arbetsbelastningar, eller utför något annat arbete än vad som anges i produkttermerna, måste det vara en betald licensierad SQL Server-instans. Följande aktivitet tillåts i den sekundära instansen: databaskonsekvenskontroller eller CheckDB, fullständiga säkerhetskopior, säkerhetskopiering av transaktionsloggar och övervakning av resursanvändningsdata. Du kan också köra den primära och motsvarande katastrofåterställningsinstansen samtidigt under korta perioder av haveriberedskapstestning var 90:e dag. 
   1. Den aktiva SQL Server-licensen omfattas av Software Assurance och möjliggör **en** passiv sekundär SQL Server-instans, med upp till samma mängd beräkning som den licensierade aktiva servern, endast. 
   1. Den sekundära VIRTUELLA SQL Server använder licensen [för haveriberedskap](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) i Azure-portalen.
   
1. **Vad anses vara en passiv instans?**

   Den passiva SQL Server-instansen betjänar inte SQL Server-data till klienter eller kör aktiva SQL Server-arbetsbelastningar. Det används endast för att synkronisera med den primära servern och på annat sätt underhålla den passiva databasen i ett varmt vänteläge. Om data betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server-arbetsbelastningar, eller utför något annat arbete än vad som anges i produkttermerna, måste det vara en betald licensierad SQL Server-instans. Följande aktivitet tillåts i den sekundära instansen: databaskonsekvenskontroller eller CheckDB, fullständiga säkerhetskopior, säkerhetskopiering av transaktionsloggar och övervakning av resursanvändningsdata. Du kan också köra den primära och motsvarande katastrofåterställningsinstansen samtidigt under korta perioder av haveriberedskapstestning var 90:e dag.
   

1. **Vilka scenarier kan använda distaster Recovery (DR) fördel?**

   [Licensieringsguiden](https://aka.ms/sql2019licenseguide) innehåller scenarier där ersättningen för katastrofåterställning kan användas. Mer information finns i dina produktvillkor och prata med dina licenskontakter eller kontohanterare.

1. **Vilka prenumerationer stöder dr-förmånen (Disaster Recovery)?**

   Omfattande program som erbjuder Software Assurance motsvarande teckningsrätter som en fast förmån stöder DR-förmånen. Detta inkluderar. men är inte begränsat till, Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Subscription Agreement (EAS) och Server and Cloud Enrollment (SCE). Mer information finns i [produktvillkoren](https://www.microsoft.com/licensing/product-licensing/products) och prata med dina licenskontakter eller acocunt manager. 

   
 ## <a name="resource-provider"></a>Resursprovider

1. **Kommer registreringen av min virtuella dator med den nya SQL Server VM-resursleverantören att medföra ytterligare kostnader?**

   Nej. SQL Server VM-resursprovidern aktiverar bara ytterligare hanterbarhet för SQL Server på Azure VM utan extra avgifter. 

1. **Är SQL Server VM-resursprovidern tillgänglig för alla kunder?**
 
   Ja, så länge SOM SQL Server VM har distribuerats på det offentliga molnet med hjälp av Resource Manager-modellen och inte den klassiska modellen. Alla andra kunder kan registrera sig hos den nya SQL Server VM-resursprovidern. Endast kunder med [Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) Assurance-förmånen kan dock använda sin egen licens genom att aktivera Azure Hybrid Benefit (AHB) på en VIRTUELL SQL Server.However, only customers with the Software Assurance benefit can use their own license by activating the [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) on a SQL Server VM. 

1. **Vad händer med resursprovidern (_Microsoft.SqlVirtualMachine_) resurs om vm-resursen flyttas eller tas bort?** 

   När Microsoft.Compute/VirtualMachine-resursen tas bort eller flyttas meddelas den associerade Microsoft.SqlVirtualMachine-resursen att asynkront replikera åtgärden.

1. **Vad händer med den virtuella datorn om resursleverantören (_Microsoft.SqlVirtualMachine_) resursen tas bort?**

    Microsoft.Compute/VirtualMachine-resursen påverkas inte när Microsoft.SqlVirtualMachine-resursen har tagits bort. Licensändringarna kommer dock som standard tillbaka till den ursprungliga bildkällan. 

1. **Är det möjligt att registrera självutnyttjade SQL Server-virtuella datorer med SQL Server VM-resursprovidern?**

    Ja. Om du distribuerade SQL Server från dina egna media och installerade SQL IaaS-tillägget kan du registrera din VIRTUELLA SQL Server-dator med resursleverantören för att få de hanterbarhetsfördelar som tillhandahålls av SQL IaaS-tillägget. Du kan dock inte konvertera en självdiutförd VIRTUELL SQL Server till betalning per start.


   


## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner för standardinstansen?**

   Ja. SQL Server-installationsmediet finns i **C** en mapp på C-enheten. Kör **Setup.exe** från den platsen om du vill lägga till nya SQL Server-instanser eller ändra andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel Automatisk säkerhetskopiering, Automatisk korrigering och Azure Key Vault-integrering, endast fungerar mot standardinstansen eller en namngiven instans som har konfigurerats korrekt (se fråga 3). 

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja, men det finns vissa överväganden. För det första kan SQL Server-associerad fakturering fortsätta att ske beroende på licensmodellen för den virtuella datorn. För det andra, som anges i föregående svar, det finns funktioner som förlitar sig på [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md). Om du avinstallerar standardinstansen utan att även ta bort IaaS-tillägget fortsätter tillägget att leta efter standardinstansen och kan generera händelseloggfel. Dessa fel kommer från följande två källor: **Microsoft SQL Server-autentiseringsuppgifter och** Microsoft SQL Server **IaaS Agent**. Ett av felen kan vara något av liknar följande:

      Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig.

   Om du bestämmer dig för att avinstallera standardinstansen avinstallerar du även [SQL Server IaaS-agenttillägget.](virtual-machines-windows-sql-server-agent-extension.md) 

1. **Kan jag använda en namngiven instans av SQL Server med IaaS-tillägget?**
   
   Ja, om den namngivna instansen är den enda instansen på SQL Server och om den ursprungliga standardinstansen [avinstallerades korrekt](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Om det inte finns någon standardinstans och det finns flera namngivna instanser på en enda VIRTUELL SQL Server- -kan inte SQL Server IaaS-agenttillägget installeras. 

1. **Kan jag ta bort SQL Server helt från en VIRTUELL SQL Server?**

   Ja, men du kommer att fortsätta att debiteras för din VIRTUELLA SQL Server enligt beskrivningen i [prisvägledning för virtuella SQL Server Azure-datorer](virtual-machines-windows-sql-server-pricing-guidance.md). Om du inte längre behöver SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Sedan kan du ta bort den virtuella SQL Server-datorn.
   
## <a name="updating-and-patching"></a>Uppdatering och korrigering

1. **Hur gör jag för att ändra till en annan version/utgåva av SQL Server på en virtuell Azure-dator?**

   Kunder kan ändra sin version/utgåva av SQL Server med hjälp av installationsmediet som innehåller den önskade versionen eller utgåvan av SQL Server. När utgåvan har ändrats använder du Azure-portalen för att ändra versionsegenskapen för den virtuella datorn så att faktureringen för den virtuella datorn återspeglas korrekt. Mer information finns i [ändra utgåva av en VIRTUELL SQL Server](virtual-machines-windows-sql-change-edition.md). Det finns ingen faktureringsskillnad för olika versioner av SQL Server, så när versionen av SQL Server har ändrats behövs ingen ytterligare åtgärd.

1. **Var kan jag få installationsmediet att ändra utgåvan eller versionen av SQL Server?**

   Kunder som har [programvarusäkring](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta sina installationsmedia från [Volymlicensieringscentret](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har programvarusäkring kan använda installationsmediet från en Marketplace SQL Server VM-avbildning som har önskad utgåva.
   
1. **Hur tillämpas uppdateringar och Service Pack på en virtuell SQL Server-dator?**

   Virtuella datorer ger dig kontroll över värddatorn, inklusive när och hur du tillämpar uppdateringar. För operativsystemet kan du manuellt installera Windows-uppdateringar eller aktivera en schemaläggningstjänst som kallas [Automatisk korrigering](virtual-machines-windows-sql-automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

1. **Kan jag uppgradera min SQL Server 2008 / 2008 R2-instans efter att ha registrerat den hos SQL Server VM-resursprovidern?**

   Ja. Du kan använda alla installationsmedia för att uppgradera versionen och utgåvan av SQL Server, och sedan kan du uppgradera ditt [SQL IaaS-tilläggsläge](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) från _ingen agent_ till _full_. Om du gör det får du tillgång till alla fördelar med SQL IaaS-tillägget, till exempel portalhanterbarhet, automatiska säkerhetskopieringar och automatisk korrigering. 

1. **Hur kan jag få kostnadsfria utökade säkerhetsuppdateringar för mina support-INSTANSER AV SQL Server 2008 och SQL Server 2008 R2?**

   Du kan få [kostnadsfria utökade säkerhetsuppdateringar](virtual-machines-windows-sql-server-2008-eos-extend-support.md) genom att flytta din SQL Server som den är till en virtuell Azure SQL-dator. Mer information finns i [slutet av supportalternativen](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Allmänt

1. **Stöds FCI-klusterinstanser (SQL Server redundanskluster) på virtuella Azure-datorer?**

   Ja. Du kan installera en redundansklusterin med antingen [PFS (Premium File Shares)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) eller [storage spaces direct (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) för lagringsundersystemet. Premium-filresurser tillhandahåller IOPS- och dataflödeskapacitet som uppfyller behoven hos många arbetsbelastningar. För IO-intensiva arbetsbelastningar bör du överväga att använda lagringsutrymmen direkt baserat på manged premium eller ultra-diskar. Du kan också använda kluster- eller lagringslösningar från tredje part enligt beskrivningen i [Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > För närvarande stöds inte _det fullständiga_ SQL [Server IaaS-agenttillägget](virtual-machines-windows-sql-server-agent-extension.md) för SQL Server FCI på Azure. Vi rekommenderar att du avinstallerar _hela_ tillägget från virtuella datorer som deltar i FCI och installerar tillägget i _lättviktsläge_ i stället. Det här tillägget stöder funktioner, till exempel Automatisk säkerhetskopiering och korrigering och vissa portalfunktioner för SQL Server. Dessa funktioner fungerar inte för virtuella SQL Server-datorer när _den fullständiga_ agenten har avinstallerats.

1. **Vad är skillnaden mellan virtuella SQL Server-datorer och SQL Database-tjänsten?**

   Begreppsmässigt är det inte så annorlunda att köra SQL Server på en virtuell Azure-dator än att köra SQL Server i ett fjärrdatacenter. [SQL Database](../../../sql-database/sql-database-technical-overview.md) erbjuder däremot databas som en tjänst. Med SQL Database har du inte åtkomst till de datorer som är värdar för dina databaser. En fullständig jämförelse finns i [Välj ett SQL Server-alternativ i molnet: Azure SQL (PaaS) Databas eller SQL Server på virtuella Azure-datorer (IaaS).](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

1. **Hur installerar jag SQL Data-verktyg på min Virtuella Azure-dator?**

    Ladda ned och installera SQL Data-verktygen från [Microsoft SQL Server Data Tools - Business Intelligence för Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Stöds distribuerade transaktioner med MSDTC på virtuella SQL Server-datorer?**
   
    Ja. Lokal DTC stöds för SQL Server 2016 SP2 och senare. Program måste dock testas när du använder alltid på tillgänglighetsgrupper, eftersom transaktioner under flygning under en redundans misslyckas och måste göras om. Klustrade DTC är tillgänglig från och med Windows Server 2019. 

## <a name="resources"></a>Resurser

**Virtuella datorer i Windows:**

* [Översikt över SQL Server på en Virtuell Windows.](virtual-machines-windows-sql-server-iaas-overview.md)
* [Etablera en VIRTUELL SQL Server Windows-dator](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Programmönster och utvecklingsstrategier för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer:**

* [Översikt över SQL Server på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en VIRTUELL SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
