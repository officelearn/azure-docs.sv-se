---
title: SQL Server på Virtuella Windows-datorer i vanliga frågor och svar om Azure | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om att köra SQL Server på virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: d3d8908739d6dda76f4c3d44540c36b36115d6f5
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289409"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Vanliga frågor och svar om SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på Windows Azure Virtual Machines (VM)](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Avbildningar

1. **Vilka SQL Server Galleri avbildningar för virtuella datorer är tillgängliga?** 

   Azure upprätthåller avbildningar av virtuella datorer för alla stödda större versioner av SQL Server på alla utgåvor för både Windows och Linux. Mer information finns i den fullständiga listan över virtuella [Windows-avbildningar](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) och [virtuella Linux-avbildningar](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create).

1. **Uppdateras befintliga SQL Server Galleri avbildningar för virtuella datorer?**

   Varannan månad uppdateras SQL Server avbildningar i galleriet för virtuella datorer med de senaste Windows-och Linux-uppdateringarna. För Windows-avbildningar inkluderar detta alla uppdateringar som marker ATS som viktiga i Windows Update, inklusive viktiga SQL Server säkerhets uppdateringar och Service Pack. För Linux-avbildningar omfattar detta de senaste system uppdateringarna. SQL Server kumulativa uppdateringar hanteras på olika sätt för Linux och Windows. SQL Server kumulativa uppdateringar för Linux ingår också i uppdateringen. Men för tillfället uppdateras inte virtuella Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Kan SQL Server avbildningar av virtuella datorer ta bort från galleriet?**

   Ja. Azure upprätthåller bara en bild per huvud version och utgåva. Till exempel, när en ny SQL Server service pack lanseras, lägger Azure till en ny avbildning till galleriet för den service pack. SQL Server avbildningen för föregående service pack tas omedelbart bort från Azure Portal. Men det är fortfarande tillgängligt för etablering från PowerShell under de kommande tre månaderna. Föregående service packs avbildning är inte tillgänglig längre efter tre månader. Den här borttagnings principen gäller även om en SQL Server-version inte stöds när den når slutet av livs cykeln.


1. **Är det möjligt att distribuera en äldre avbildning av SQL Server som inte är synlig i Azure Portal?**

   Ja, med hjälp av PowerShell. Mer information om hur du distribuerar SQL Server virtuella datorer med hjälp av PowerShell finns i [så här etablerar du SQL Server virtuella datorer med Azure PowerShell](create-sql-vm-powershell.md).
   
1. **Är det möjligt att skapa en generaliserad Azure Marketplace SQL Server avbildning av mina SQL Server VM och använda den för att distribuera virtuella datorer?**

   Ja, men du måste [registrera varje SQL Server VM med SQL Server VM Resource Provider](sql-vm-resource-provider-register.md) för att kunna hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering. När du registrerar dig hos resurs leverantören måste du också ange licens typen för varje SQL Server VM.

1. **Hur gör jag för att generalisera SQL Server på den virtuella Azure-datorn och Använd den för att distribuera nya virtuella datorer?**

   Du kan distribuera en virtuell Windows Server-dator (utan SQL Server installerad) och använda [SQL Sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) -processen för att generalisera SQL Server på virtuell Azure-dator (Windows) med installations mediet för SQL Server. Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) kan hämta installationsmedia från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installations mediet från en Azure Marketplace SQL Server VM-avbildning som har önskad utgåva.

   Du kan också använda en av SQL Server-avbildningarna från Azure Marketplace för att generalisera SQL Server på virtuell Azure-dator. Observera att du måste ta bort följande register nyckel i käll avbildningen innan du skapar en egen avbildning. Om du inte gör det kan det leda till att bloating i installations programmet för SQL Server installationen och/eller SQL IaaS-tillägget är i felaktigt tillstånd.

   Sökväg till register nyckel:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server på virtuella Azure-datorer, inklusive de som distribueras från anpassade generaliserade avbildningar, bör [registreras med den virtuella SQL-resurs leverantören](./sql-vm-resource-provider-register.md?tabs=azure-cli%252cbash) för att uppfylla kraven för efterlevnad och för att använda valfria funktioner som automatiserad uppdatering och automatisk säkerhets kopiering. Med resurs leverantören kan du också [Ange licens typ](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) för varje SQL Server VM.

1. **Kan jag använda min egen virtuella hård disk för att distribuera ett SQL Server VM?**

   Ja, men du måste [registrera varje SQL Server VM med SQL Server VM Resource Provider](sql-vm-resource-provider-register.md) för att kunna hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering.

1. **Är det möjligt att konfigurera konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. För Galleri avbildningar för virtuella datorer som innehåller SQL Server måste du välja en av de angivna avbildningarna antingen via Azure Portal eller via [PowerShell](create-sql-vm-powershell.md). Du kan dock distribuera en virtuell Windows-dator och själv installera SQL Server till den. Du måste sedan [registrera SQL Server VM med SQL Server VM Resource Provider](sql-vm-resource-provider-register.md) för att kunna hantera dina SQL Server VM i Azure Portal, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering. 


## <a name="creation"></a>Skapa

1. **Hur gör jag för att skapar du en virtuell Azure-dator med SQL Server?**

   Det enklaste sättet är att skapa en virtuell dator som innehåller SQL Server. En själv studie kurs om hur du registrerar dig för Azure och skapar en SQL Server VM från portalen finns i [etablera en SQL Server virtuell dator i Azure Portal](create-sql-vm-portal.md). Du kan välja en avbildning av en virtuell dator som använder SQL Server licens för betala per sekund, eller så kan du använda en avbildning som gör att du kan använda en egen SQL Server-licens. Du kan också välja att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad utgåva (utvecklare eller Express) eller genom att återanvända en lokal licens. Se till att [Registrera din SQL Server VM med SQL Server VM Resource Provider](sql-vm-resource-provider-register.md) för att hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering. Om du använder din egen licens måste du ha [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur kan jag migrera min lokala SQL Server-databas till molnet?**

   Skapa först en virtuell Azure-dator med en SQL Server instans. Migrera sedan dina lokala databaser till den instansen. Information om strategier för datamigrering finns i [Migrera en SQL Server databas till SQL Server i en virtuell Azure-dator](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Det finns tre sätt att göra detta på. Om du är en Enterprise-avtal (EA)-kund kan du etablera en av de [virtuella dator avbildningar som har stöd för licenser](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), som även kallas för att ta din egen licens (BYOL). Om du har [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)kan du aktivera [Azure Hybrid-förmån](licensing-model-azure-hybrid-benefit-ahb-change.md) på en befintlig PAYG-avbildning (betala per användning). Ett annat alternativ är att kopiera SQL Server-installationsmedia till en virtuell Windows Server-dator och installera SQL Server på den virtuella datorn. Se till att registrera SQL Server VM med [resurs leverantören](sql-vm-resource-provider-register.md) för funktioner som till exempel portal hantering, automatisk säkerhets kopiering och automatisk uppdatering. 

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Ja. Du kan enkelt byta en PAYG (betala per användning)-Galleri avbildningen (BYOL) genom att aktivera [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Mer information finns i [så här ändrar du licensierings modellen för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). Den här funktionen är för närvarande endast tillgänglig för offentliga och Azure Government moln kunder.

1. **Kräver byte av licensieringsmodeller någon avbrottstid för SQL Server?**

   Nej. Att [ändra licensierings modellen](licensing-model-azure-hybrid-benefit-ahb-change.md) kräver ingen stillestånds tid för SQL Server eftersom ändringen börjar gälla omedelbart och inte kräver omstart av den virtuella datorn. Men för att registrera SQL Server VM med SQL Server VM Resource Provider, är [SQL IaaS-tillägget](sql-server-iaas-agent-extension-automate-management.md) en förutsättning och installation av SQL IaaS-tillägget i _full_ läge startar om SQL Server tjänsten. Om SQL IaaS-tillägget behöver installeras kan du antingen installera det i _Lightweight_ -läge för begränsade funktioner eller installera det i _fullständigt_ läge under en underhålls period. SQL IaaS-tillägget som installeras i _Lightweight_ -läge kan uppgraderas till _fullständigt_ läge när som helst, men kräver en omstart av SQL Servers tjänsten. 
   
1. **Är det möjligt att växla licensierings modeller på en SQL Server VM som distribueras med den klassiska modellen?**

   Nej. Ändring av licensierings modeller stöds inte på en klassisk virtuell dator. Du kan migrera den virtuella datorn till Azure Resource Manager-modellen och registrera dig hos SQL Server VM-resursprovidern. När den virtuella datorn har registrerats hos SQL Server VM-resursprovidern blir ändringar av licensieringsmodellen tillgängliga på den virtuella datorn.

1. **Kan jag använda Azure Portal för att hantera flera instanser på samma virtuella dator?**

   Nej. Portalhantering är en funktion som tillhandahålls av SQL Server VM-resursprovidern, som förlitar sig på SQL Server IaaS-agenttillägget. Samma begränsningar gäller för resursprovidern som för tillägget. Portalen kan antingen bara hantera en standardinstans, eller en namngiven instans, så länge den har konfigurerats korrekt. Mer information om dessa begränsningar finns i [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md). 

1. **Kan prenumerationer hos molnlösningsleverantörer aktivera Azure Hybrid-förmånen?**

   Ja, Azure Hybrid-förmånen är tillgänglig för prenumerationer hos molnlösningsleverantörer. CSP-kunder bör först distribuera en avbildning med betala per användning och sedan [ändra licensierings modellen](licensing-model-azure-hybrid-benefit-ahb-change.md) till egen licens.
   
 
1. **Måste jag betala för att licensiera SQL Server på en virtuell Azure-dator om den endast används för vänteläge/redundans?**

   Om du vill ha en kostnads fri passiv licens för en sekundär tillgänglighets grupp i vänte läge eller en instans av redundanskluster måste du uppfylla alla följande kriterier som beskrivs i [produkt licens villkoren](https://www.microsoft.com/licensing/product-licensing/products):

   1. Du har [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) genom [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Den passiva SQL Server-instansen hanterar inte SQL Server-data till klienter eller kör aktiva SQL Server-arbetsbelastningar. Den används endast för att synkronisera med den primära servern och på annat sätt upprätthålla den passiva databasen i ett varmt vänteläge. Om den betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server arbets belastningar eller utför något annat arbete än vad som anges i produkt villkoren, måste det vara en betald licensierad SQL Server-instans. Följande aktivitet tillåts på den sekundära instansen: konsekvenskontroll av databas eller CheckDB, fullständiga säkerhetskopior, säkerhetskopior av transaktionslogg och övervakning av resursanvändningsdata. Du kan också köra den primära och motsvarande haveriberedskapsinstansen samtidigt under korta perioder av haveriberedskap var 90:e dag. 
   1. Den aktiva SQL Server-licensen omfattas av Software Assurance och tillåter **en** passiv sekundär SQL Server instans, med upp till samma beräknings mängd som den licensierade aktiva servern. 
   1. Den sekundära SQL Server VM använder en [katastrof återställnings](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) licens i Azure Portal.
   
1. **Vad betraktas som en passiv instans?**

   Den passiva SQL Server-instansen hanterar inte SQL Server-data till klienter eller kör aktiva SQL Server-arbetsbelastningar. Den används endast för att synkronisera med den primära servern och på annat sätt upprätthålla den passiva databasen i ett varmt vänteläge. Om den betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server-arbetsbelastningar, eller utför något annat arbete än vad som anges i produktvillkoren, måste det vara en betald licensierad SQL Server-instans. Följande aktivitet tillåts på den sekundära instansen: konsekvenskontroll av databas eller CheckDB, fullständiga säkerhetskopior, säkerhetskopior av transaktionslogg och övervakning av resursanvändningsdata. Du kan också köra den primära och motsvarande haveriberedskapsinstansen samtidigt under korta perioder av haveriberedskap var 90:e dag.
   

1. **Vilka scenarier kan utnyttja haveriberedskapsförmånen (DR)?**

   [Licens guiden](https://aka.ms/sql2019licenseguide) innehåller scenarier där Disaster Recovery-förmånen kan användas. Mer information hittar du i produktvillkoren och du kan även prata med dina licenskontakter eller kontoansvarig.

1. **Vilka prenumerationer har stöd för haveriberedskapsförmånen (DR)?**

   Omfattande program som erbjuder Software Assurance-likvärdiga prenumerationsrättigheter som en fast förmån har stöd för DR-förmånen. Det här omfattar, men är inte begränsat till, öppning svärdet (OV), Open Value-prenumerationen (OVS), Enterprise-avtal (EA), Enterprise-avtal-prenumeration (EAS) och server-och moln registrering (SCE). Mer information hittar du i [produkt villkoren](https://www.microsoft.com/licensing/product-licensing/products) och pratar med licens kontakterna eller konto chefen. 

   
 ## <a name="resource-provider"></a>Resursprovider

1. **Kommer den virtuella datorn att registreras med den nya SQL Server VM resurs leverantören att få ytterligare kostnader?**

   Nej. SQL Server VM Resource Provider möjliggör bara ytterligare hanterbarhet för SQL Server på virtuella Azure-datorer utan extra kostnad. 

1. **Är SQL Server VM Resource Provider tillgänglig för alla kunder?**
 
   Ja, så länge som SQL Server VM distribuerades i det offentliga molnet med Resource Manager-modellen och inte den klassiska modellen. Alla andra kunder kan registrera sig för den nya SQL Server VM resurs leverantören. Men endast kunder med [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) -förmånen kan använda sin egen licens genom att aktivera [Azure Hybrid-förmån (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) på en SQL Server VM. 

1. **Vad händer med resurs leverantörs resursen ( _Microsoft. SqlVirtualMachine_ ) om den virtuella dator resursen flyttas eller tas bort?** 

   När resursen Microsoft. Compute/VirtualMachine släpps eller flyttas, meddelas den associerade Microsoft. SqlVirtualMachine-resursen för att asynkront replikera åtgärden.

1. **Vad händer med den virtuella datorn om resurs leverantörs resursen ( _Microsoft. SqlVirtualMachine_ ) tas bort?**

    Resursen Microsoft. Compute/VirtualMachine påverkas inte när resursen Microsoft. SqlVirtualMachine släpps. Licensierings ändringarna kommer dock att återställas till den ursprungliga avbildnings källan. 

1. **Är det möjligt att registrera distribuerade SQL Server virtuella datorer med SQL Server VM Resource Provider?**

    Ja. Om du har distribuerat SQL Server från ditt eget medium och installerat SQL IaaS-tillägget kan du registrera dina SQL Server VM med resurs leverantören för att få de hanterings förmåner som ges av tillägget för SQL-IaaS.    


## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner för standard instansen?**

   Ja. Installations mediet för SQL Server finns i en mapp på **C** -enheten. Kör **Setup.exe** från den platsen för att lägga till nya SQL Server instanser eller ändra andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel automatisk säkerhets kopiering, automatisk uppdatering och Azure Key Vault-integrering, endast fungerar mot standard instansen eller en namngiven instans som kon figurer ATS korrekt (se fråga 3). Kunder som använder [Software Assurance via Azure Hybrid-förmån](licensing-model-azure-hybrid-benefit-ahb-change.md) eller licens modellen **betala per** användning kan installera flera instanser av SQL Server på den virtuella datorn utan att det medför extra licens kostnader. Ytterligare SQL Server instanser kan begränsa system resurserna om de inte är korrekt konfigurerade. 

1. **Vad är det maximala antalet instanser på en virtuell dator?**
   SQL Server 2012 till SQL Server 2019 kan stödja [50-instanser](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) på en fristående server. Detta är samma gräns oavsett i Azure lokalt. Se [metod tips](performance-guidelines-best-practices.md#multiple-instances) för att lära dig hur du förbereder din miljö bättre. 

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja, men det finns vissa överväganden. Först kan SQL Server-kopplad fakturering fortsätta att inträffa beroende på licens modellen för den virtuella datorn. För det andra, enligt vad som anges i föregående svar, finns det funktioner som förlitar sig på [SQL Server IaaS agent-tillägg](sql-server-iaas-agent-extension-automate-management.md). Om du avinstallerar standard instansen utan att ta bort IaaS-tillägget, fortsätter tillägget att söka efter standard instansen och kan generera händelse logg fel. Felen är från följande två källor: **Microsoft SQL Server hantering av autentiseringsuppgifter** och **Microsoft SQL Server IaaS-agenten**. Ett av felen kan vara något av liknar följande:

      Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig.

   Om du väljer att avinstallera standard instansen avinstallerar du även [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md) . 

1. **Kan jag använda en namngiven instans av SQL Server med IaaS-tillägget?**
   
   Ja, om den namngivna instansen är den enda instansen på SQL Server, och om den ursprungliga standard instansen [avinstallerades korrekt](sql-server-iaas-agent-extension-automate-management.md#named-instance-support). Om det inte finns någon standard instans och det finns flera namngivna instanser på en enda SQL Server VM, kommer SQL Server IaaS agent-tillägget inte att installeras.  

1. **Kan jag ta bort SQL Server och tillhör ande licens fakturering från en SQL Server VM?**

   Ja, men du måste vidta ytterligare åtgärder för att undvika att debiteras SQL Server-instansen enligt beskrivningen i [pris vägledningen](pricing-guidance.md). Om du vill ta bort SQL Server-instansen fullständigt kan du migrera till en annan virtuell Azure-dator utan att SQL Server förinstallerat på den virtuella datorn och ta bort den aktuella SQL Server VM. Följ dessa steg om du vill behålla den virtuella datorn men stoppa SQL Server fakturering: 

   1. Säkerhetskopiera alla dina data, inklusive system databaser, om det behövs. 
   1. Avinstallera SQL Server fullständigt, inklusive SQL IaaS-tillägget (om det finns).
   1. Installera den kostnads fria [SQL Express-versionen](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Registrera dig för SQL VM-resurspoolen i [Lightweight-läge](sql-vm-resource-provider-register.md).
   1. valfritt Inaktivera tjänsten Express SQL Server genom att inaktivera tjänsten starta. 

1. **Kan jag använda Azure Portal för att hantera flera instanser på samma virtuella dator?**

   Nej. Portal hantering tillhandahålls av SQL VM Resource Provider, som förlitar sig på SQL Server IaaS agent-tillägg. Samma begränsningar gäller för resurs leverantören som tillägget. Portalen kan antingen bara hantera en standard instans eller en namngiven instans så länge den är korrekt konfigurerad. Mer information finns i [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Uppdatering och uppdatering

1. **Hur gör jag för att ändra till en annan version/utgåva av SQL Server på en virtuell Azure-dator?**

   Kunder kan ändra sin version/utgåva av SQL Server med hjälp av installationsmediet som innehåller den önskade versionen eller utgåvan av SQL Server. När utgåvan har ändrats använder du Azure-portalen för att ändra versionsegenskapen för den virtuella datorn så att faktureringen för den virtuella datorn återspeglas korrekt. Mer information finns i avsnittet [om ändrings versioner av en SQL Server VM](change-sql-server-edition.md). Det finns inte någon faktureringsskillnad för olika versioner av SQL Server, så ingen ytterligare åtgärd krävs när versionen av SQL Server har ändrats.

1. **Var kan jag få installationsmedia för att ändra utgåva eller version av SQL Server?**

   Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta installationsmedia från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installations mediet från en Azure Marketplace SQL Server VM-avbildning som har önskad utgåva.
   
1. **Hur tillämpas uppdateringar och servicepack på en SQL Server VM?**

   Virtuella datorer ger dig kontroll över värddatorn, inklusive när och hur du tillämpar uppdateringar. Du kan manuellt installera Windows-uppdateringar för operativsystemet eller aktivera en schemaläggningstjänst som kallas [automatisk uppdatering](automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

1. **Kan jag uppgradera min SQL Server 2008/2008 R2-instans när den har registrerats med SQL Server VM Resource Provider?**

   Om operativ systemet är Windows Server 2008 R2 eller senare, ja. Du kan använda installations medier för att uppgradera versionen och versionen av SQL Server och sedan kan du uppgradera ditt [SQL IaaS-tillägg](sql-server-iaas-agent-extension-automate-management.md#management-modes)) från _ingen agent_ till _full_. På så sätt får du till gång till alla fördelar med SQL IaaS-tillägget, till exempel portal hantering, automatiserade säkerhets kopieringar och automatiserad uppdatering. Om operativ system versionen är Windows Server 2008 stöds endast noagent-läge. 

1. **Hur kan jag få kostnadsfria utökade säkerhetsuppdateringar för mina SQL Server 2008- och SQL Server 2008 R2-instanser vars support har nått slutet?**

   Du kan få [kostnads fria utökade säkerhets uppdateringar](sql-server-2008-extend-end-of-support.md) genom att flytta din SQL Server som-är till en virtuell Azure-dator. Mer information finns i avsnittet om [alternativ när supporten upphör](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Allmänt

1. **Stöds SQL Server FCI (failover Cluster instances) på virtuella Azure-datorer?**

   Ja. Du kan installera en instans av ett redundanskluster med hjälp av antingen [Premium fil resurser (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) eller [Storage Spaces Direct (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) för underlag rings systemet. Premium-filresurser ger IOPS och data flödes kapacitet som uppfyller behoven hos många arbets belastningar. För i/o-intensiva arbets belastningar kan du överväga att använda lagrings dirigering baserat på hanterade Premium eller Ultra disks. Du kan också använda klustring eller lagrings lösningar från tredje part enligt beskrivningen i [hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > För närvarande stöds inte det _fullständiga_ [SQL Server IaaS Agent Extension](sql-server-iaas-agent-extension-automate-management.md) för SQL Server FCI på Azure. Vi rekommenderar att du avinstallerar det _fullständiga_ tillägget från virtuella datorer som ingår i FCI och installerar tillägget i _Lightweight_ -läge i stället. Det här tillägget har stöd för funktioner, till exempel automatisk säkerhets kopiering och uppdatering och vissa Portal funktioner för SQL Server. Dessa funktioner fungerar inte för SQL Server virtuella datorer när den _fullständiga_ agenten har avinstallerats.

1. **Vad är skillnaden mellan SQL Server virtuella datorer och tjänsten SQL Database?**

   Att köra SQL Server på en virtuell Azure-dator är inte detsamma som att köra SQL Server i ett fjärranslutet Data Center. [Azure SQL Database](../../database/sql-database-paas-overview.md) erbjuder däremot databas som en tjänst. Med SQL Database har du inte till gång till de datorer som är värdar för dina databaser. En fullständig jämförelse finns i [Välj ett moln SQL Server alternativ: Azure SQL (PaaS) Database eller SQL Server på virtuella Azure-datorer (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Hur gör jag för att installera SQL Data Tools på min virtuella Azure-dator?**

    Hämta och installera SQL Data Tools från [Microsoft SQL Server data verktyg – Business Intelligence för Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Stöds distribuerade transaktioner med MSDTC på SQL Server virtuella datorer?**
   
    Ja. Lokal DTC stöds för SQL Server 2016 SP2 och senare. Program måste dock testas när du använder Always on-tillgänglighetsgrupper, eftersom transaktioner under flygningen inte går att redundansväxla och måste göras om. Klustrad DTC är tillgänglig från och med Windows Server 2019. 

## <a name="resources"></a>Resurser

**Virtuella Windows-datorer** :

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Etablera SQL Server på en virtuell Windows-dator](create-sql-vm-portal.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](migrate-to-vm-from-sql-server.md)
* [Hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Metod tips för prestanda för SQL Server på Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Program mönster och utvecklings strategier för SQL Server på Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuella Linux-datorer** :

* [Översikt över SQL Server på en virtuell Linux-dator](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Etablera SQL Server på en virtuell Linux-dator](../linux/sql-vm-create-portal-quickstart.md)
* [Vanliga frågor och svar (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server på Linux dokumentation](/sql/linux/sql-server-linux-overview)