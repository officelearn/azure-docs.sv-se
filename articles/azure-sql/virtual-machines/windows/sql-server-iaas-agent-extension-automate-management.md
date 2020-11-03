---
title: Vad är SQL Server IaaS agent extension?
description: Den här artikeln beskriver hur SQL Server IaaS agent-tillägget hjälper till att automatisera hanteringen av vissa administrations uppgifter för SQL Server på virtuella Azure-datorer. Detta inkluderar funktioner som automatisk säkerhets kopiering, automatiserad uppdatering, Azure Key Vault integrering, licensierings hantering, lagrings konfiguration och central hantering av alla SQL Server VM instanser.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 070058eae36bf4f8546cfcf4beb85ac5023e9c79
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286183"
---
# <a name="what-is-the-sql-server-iaas-agent-extension"></a>Vad är SQL Server IaaS agent extension?
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS agent Extension (SqlIaasExtension) körs på SQL Server på Azure-Virtual Machines (VM) för att automatisera hanterings-och administrations uppgifter. 

Den här artikeln innehåller en översikt över tillägget. Om du vill installera SQL Server IaaS-tillägget till SQL Server på virtuella Azure-datorer kan du läsa artiklarna för [Automatisk installation](sql-vm-resource-provider-automatic-registration.md), [enskilda virtuella datorer](sql-vm-resource-provider-register.md)eller [virtuella datorer i bulk](sql-vm-resource-provider-bulk-register.md). 

## <a name="overview"></a>Översikt

Tillägget SQL Server IaaS-agent ger ett antal fördelar för SQL Server på virtuella Azure-datorer: 

- **Funktions förmåner** : tillägget låser upp ett antal funktioner för automatiserings funktioner, till exempel portal hantering, licens flexibilitet, automatisk säkerhets kopiering, automatiserad uppdatering och mer. Mer information finns i [funktions förmånerna](#feature-benefits) längre fram i den här artikeln. 

- **Kompatibilitet** : tillägget ger en förenklad metod för att uppfylla kravet på att meddela Microsoft att Azure Hybrid-förmån har Aktiver ATS som anges i produkt villkoren. Den här processen är en negation som behöver hantera licens registrerings formulär för varje resurs.  

- **Kostnads fri** : tillägget i alla tre hanterbarhets lägen är helt kostnads fritt. Det finns ingen ytterligare kostnad kopplad till resurs leverantören eller med ändring av hanterings lägen. 

- **Förenklad licens hantering** : tillägget fören klar SQL Server licens hantering, och gör att du snabbt kan identifiera SQL Server virtuella datorer med Azure Hybrid-förmån aktiverat med hjälp av [Azure Portal](manage-sql-vm-portal.md), Azure CLI eller PowerShell: 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---


> [!IMPORTANT]
> SQL IaaS agent-tillägget samlar in data i Express syfte att ge kunderna valfria förmåner när de använder SQL Server i Azure Virtual Machines. Microsoft kommer inte att använda dessa data för licens granskningar utan kundens medgivande. Se [SQL Server sekretess tillägg](/sql/sql-server/sql-server-privacy#non-personal-data) för mer information.


## <a name="feature-benefits"></a>Funktions förmåner 

Tillägget SQL Server IaaS agent låser upp ett antal funktions förmåner för att hantera din SQL Server VM. 

Följande tabell innehåller information om dessa fördelar: 


| Funktion | Beskrivning |
| --- | --- |
| **Portalhantering** | Låser upp [hantering i portalen](manage-sql-vm-portal.md), så att du kan visa alla dina SQL Server virtuella datorer på en plats, och så att du kan aktivera och inaktivera SQL-funktioner direkt från portalen. 
| **Automatisk säkerhets kopiering** |Automatisera schemaläggning av säkerhets kopieringar för alla databaser antingen för standard instansen eller en [korrekt installerad](frequently-asked-questions-faq.md#administration) namngiven instans av SQL Server på den virtuella datorn. Mer information finns i [Automatisk säkerhets kopiering för SQL Server i Azure Virtual Machines (Resource Manager)](automated-backup-sql-2014.md). |
| **Automatiserad uppdatering** |Konfigurerar en underhålls period då viktiga Windows-och SQL Server säkerhets uppdateringar till den virtuella datorn kan ske, så att du kan undvika uppdateringar under hög belastnings tider för din arbets belastning. Mer information finns i [automatiserad uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](automated-patching.md). |
| **Azure Key Vault-integrering** |Gör att du kan installera och konfigurera Azure Key Vault automatiskt på din SQL Server VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure-Virtual Machines (Resource Manager)](azure-key-vault-integration-configure.md). |
| **Flexibel licensiering** | Spara pengar genom [sömlös över gång](licensing-model-azure-hybrid-benefit-ahb-change.md) från den tillkommande licensen (även kallat Azure Hybrid-förmån) till licens modellen betala per användning och tillbaka igen. | 
| **Flexibel version/utgåva** | Om du väljer att ändra [versionen](change-sql-server-version.md) [eller versionen](change-sql-server-edition.md) av SQL Server kan du uppdatera metadata i Azure Portal utan att behöva distribuera om hela SQL Server VM.  | 


## <a name="management-modes"></a>Hanterings lägen

SQL IaaS-tillägget har tre hanterings lägen:

- **Lightweight** mode kräver inte omstart av SQL Server, men stöder bara ändring av licens typ och utgåva av SQL Server. Använd det här alternativet för SQL Server virtuella datorer med flera instanser, eller som deltar i en instans av en redundanskluster (FCI). Det här hanterings läget behåller binärfilerna för SQL IaaS-agenttjänsten på datorn, men installerar inte agenten. Läge för förenklad hantering är standard hanterings läget när du använder funktionen för [automatisk registrering](sql-vm-resource-provider-automatic-registration.md) , eller när en hanterings typ inte anges vid manuell registrering. Det går inte att påverka minne eller CPU när du använder Lightweight-läge och det finns ingen associerad kostnad. Vi rekommenderar att du först registrerar SQL Server VM i Lightweight-läge och sedan uppgraderar till fullständigt läge under en schemalagd underhålls period.

- **Fullständigt** läge ger alla funktioner, men kräver omstart av SQL Server-och system administratörs behörighet. Använd den för att hantera en SQL Server VM med en enda instans. I fullständigt läge installeras två Windows-tjänster som har minimal påverkan på minne och CPU – dessa kan övervakas via aktivitets hanteraren. Det finns ingen kostnad för att använda läget fullständig hanterbarhet. 

- **Noagent** -läge är dedikerat till SQL Server 2008 och SQL Server 2008 R2 installerat på Windows Server 2008. Det påverkar inte minne eller CPU när du använder noagent-läge. Det finns ingen kostnad för att använda noagent Managed mode. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten genom att använda Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Installation

Tillägget SQL Server IaaS Agent installeras när du registrerar dina SQL Server virtuella datorer med resurs leverantören för SQL-VM. När du registrerar med resurs leverantören skapas den **virtuella SQL-datorns** _resurs_ i din prenumeration, som är en _separat_ resurs från den virtuella dator resursen. När du avregistrerar SQL Server VM från resurs leverantören tas den **virtuella SQL-datorn** bort _, men den faktiska virtuella datorn tas_ inte bort.

Genom att distribuera en SQL Server VM Azure Marketplace-avbildning via Azure Portal registreras SQL Server VM automatiskt med resurs leverantören. Men om du väljer att själv installera SQL Server på en virtuell Azure-dator eller etablera en virtuell Azure-dator från en anpassad virtuell hård disk måste du registrera din SQL Server VM med providern för SQL-VM-IaaS för att installera SQL Agent-tillägget. 

Om du vill installera tillägget registrerar du SQL Server VM med resurs leverantören:
- [Automatiskt för alla aktuella och framtida virtuella datorer i en prenumeration](sql-vm-resource-provider-automatic-registration.md)
- [För en enskild virtuell dator](sql-vm-resource-provider-register.md)
- [För flera virtuella datorer i grupp](sql-vm-resource-provider-bulk-register.md)

### <a name="named-instance-support"></a>Stöd för namngivna instanser

SQL Server IaaS-tillägget fungerar med en namngiven instans av SQL Server om det är den enda SQL Server-instans som är tillgänglig på den virtuella datorn. Tillägget kan inte installeras på virtuella datorer som har flera SQL Server-instanser. 

Om du vill använda en namngiven instans av SQL Server distribuerar du en virtuell Azure-dator, installerar en enda namngiven SQL Server-instans till den och registrerar den sedan med [providern för SQL VM-resursen](sql-vm-resource-provider-register.md) för att installera tillägget.

Du kan också använda en namngiven instans med en Azure Marketplace SQL Server-avbildning genom att följa dessa steg: 

   1. Distribuera en SQL Server VM från Azure Marketplace. 
   1. [Avregistrera](sql-vm-resource-provider-register.md#unregister-from-rp) SQL Server VM från resurs leverantören för SQL-VM. 
   1. Avinstallera SQL Server helt i SQL Server VM.
   1. Installera SQL Server med en namngiven instans i SQL Server VM. 
   1. Installera SQL IaaS agent-tillägget genom [att registrera SQL Server VM med providern för SQL VM-resursen](sql-vm-resource-provider-register.md#register-with-rp). 

## <a name="verify-status-of-extension"></a>Verifiera tilläggets status

Använd Azure Portal eller Azure PowerShell för att kontrol lera status för tillägget. 


### <a name="azure-portal"></a>Azure Portal

Kontrol lera att tillägget är installerat i Azure Portal. 

Välj **alla inställningar** i fönstret virtuell dator och välj sedan **tillägg**. Du bör se **SqlIaasExtension** -tillägget som visas.

![Status för SQL Server IaaS agent extension i Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Du kan också använda cmdleten **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Föregående kommando bekräftar att agenten är installerad och innehåller allmän statusinformation. Du kan hämta detaljerad statusinformation om automatisk säkerhets kopiering och korrigering med hjälp av följande kommandon:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Begränsningar

SQL IaaS agent-tillägget stöder bara: 

- SQL Server virtuella datorer som distribueras via Azure Resource Manager. SQL Server virtuella datorer som distribueras via den klassiska modellen stöds inte. 
- SQL Server virtuella datorer som distribueras till det offentliga molnet eller Azure Government molnet. Distributioner till andra privata eller offentliga moln stöds inte. 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

**Bör jag registrera mina SQL Server VM som tillhandahålls från en SQL Server-avbildning på Azure Marketplace?**

Nej. Microsoft registrerar automatiskt virtuella datorer som tillhandahålls från SQL Server avbildningar på Azure Marketplace. Registrering med SQL VM Resource Provider krävs bara om den virtuella datorn *inte* har tillhandahållits från SQL Server avbildningar på Azure Marketplace och SQL Server var själv installerad.

**Är resursprovidern för virtuell SQL-dator tillgänglig för alla kunder?** 

Ja. Kunderna bör registrera sina SQL Server virtuella datorer med resurs leverantören för SQL-VM om de inte använde en SQL Server avbildning från Azure Marketplace och i stället själv installerade SQL Server, eller om de har gjort sin anpassade virtuella hård disk. Virtuella datorer som ägs av alla typer av prenumerationer (direkt, Enterprise-avtal och moln lösnings leverantör) kan registreras med providern för SQL VM-resurs.

**Vad är standard hanterings läget vid registrering med den virtuella SQL-resurs leverantören?**

Standard hanterings läget när du registrerar med resurs leverantören för SQL-VM är *Lightweight*. Om egenskapen SQL Server hantering inte har angetts när du registrerar dig hos providern för SQL VM-resurs, anges läget som Lightweight och tjänsten SQL Server kommer inte att starta om. Vi rekommenderar att du registrerar med resurs leverantören för SQL-VM: en i Lightweight-läge först och sedan uppgraderar till fullständig under en underhålls period. På samma sätt är standard hantering också Lightweight när du använder [funktionen för automatisk registrering](sql-vm-resource-provider-automatic-registration.md).

**Vilka är kraven för att registreras med resurs leverantören för den virtuella SQL-datorn?**

Det finns inga krav på att registrera med den SQL VM-adressresurs som inte har SQL Server installerat på den virtuella datorn. Observera att om SQL IaaS agent-tillägget installeras i full läge startas SQL Server-tjänsten om, så att du kan göra det under en underhålls period rekommenderas.

**Kommer att registreras med den virtuella SQL-adressresursen installera en agent på den virtuella datorn?**

Ja, om du registrerar med resurs leverantören för SQL-VM i fullständig hanterbarhets läge installeras en agent på den virtuella datorn. Det går inte att registrera i Lightweight-eller noagent-läge. 

Registrering med den virtuella SQL VM-providern i Lightweight-läge kopierar endast *binärfilerna* för SQL IaaS-agenttjänsten till den virtuella datorn. agenten installeras inte. Dessa binärfiler används sedan för att installera agenten när hanterings läget har uppgraderats till full.


**Kommer registreringen med den virtuella SQL-adressresursen att starta om SQL Server på den virtuella datorn?**

Det beror på vilket läge som anges under registreringen. Om läget för enkel eller utan agent anges, kommer SQL Server-tjänsten inte att starta om. Men om du anger hanterings läget som full kommer SQL Server-tjänsten att starta om. Funktionen för automatisk registrering registrerar SQL Server virtuella datorer i Lightweight-läge, om inte Windows Server-versionen är 2008, vilket innebär att SQL Server VM registreras i noagent-läge. 

**Vad är skillnaden mellan lägena för enkel och noagent-hantering vid registrering med den virtuella SQL-adressresursen?** 

Hanterings läget för noagent är det enda tillgängliga hanterings läget för SQL Server 2008 och SQL Server 2008 R2 på Windows Server 2008. För alla senare versioner av Windows Server är de två tillgängliga lägena för hanterbarhet lätta och fullständiga. 

I noagent-läge krävs att SQL Server versions-och versions egenskaper anges av kunden. I läget för lätta från den virtuella datorn hittar du versionen och versionen av SQL Server-instansen.

**Kan jag registrera med den virtuella SQL-adressresursen utan att ange licens typen SQL Server?**

Nej. Den SQL Server licens typen är inte en valfri egenskap när du registrerar med providern för SQL VM-resursen. Du måste ange SQL Server licens typ som betala per användning eller Azure Hybrid-förmån när du registrerar med resurs leverantören för SQL-VM i alla hanterbarhets lägen (noagent, Lightweight och full). Om du har någon av de kostnads fria versionerna av SQL Server installerade, till exempel Developer eller Evaluation Edition, måste du registrera dig med betala per användning-licens. Azure Hybrid-förmån är endast tillgängligt för betalda versioner av SQL Server som Enterprise-och standard-versioner.

**Kan jag uppgradera SQL Server IaaS-tillägget från noagent-läge till fullständigt läge?**

Nej. Att uppgradera hanterbarhets läget till full eller Lightweight är inte tillgängligt för noagent-läge. Detta är en teknisk begränsning i Windows Server 2008. Du måste först uppgradera operativ systemet till Windows Server 2008 R2 eller senare och sedan kan du uppgradera till fullständigt hanterings läge. 

**Kan jag uppgradera SQL Server IaaS-tillägget från förenklat läge till fullständigt läge?**

Ja. Uppgradering av hanterbarhets läget från Lightweight till full stöds via Azure PowerShell eller Azure Portal. Då aktive ras en omstart av SQL Servers tjänsten.

**Kan jag nedgradera SQL Server IaaS-tillägget från fullständigt läge till noagent eller läget för förenklad hantering?**

Nej. Det finns inte stöd för att nedgradera SQL Server IaaS-tilläggets hanterbarhets läge. Läget för hanterbarhets kan inte nedgraderas från fullständigt läge till läget för enkel eller utan agent, och det kan inte nedgraderas från läget Lightweight till noagent. 

Om du vill ändra hanterbarhets läget från fullständig hanterbarhet [avregistrerar](sql-vm-resource-provider-register.md#unregister-from-rp) du SQL Server VM från providern för SQL VM _-resurs genom_ att släppa den virtuella SQL-datorns resurs och omregistrera SQL Server VM med den virtuella SQL-adressresursen i ett annat hanterings läge.

**Kan jag registrera med resurs leverantören för SQL-VM från Azure Portal?**

Nej. Registrering med providern för SQL VM-resursen är inte tillgänglig i Azure Portal. Registrering med SQL VM Resource providern stöds bara med Azure CLI eller Azure PowerShell. 

**Kan jag registrera en virtuell dator med resurs leverantören för SQL-VM innan SQL Server installeras?**

Nej. En virtuell dator måste ha minst en SQL Server (databas motor) för att kunna registreras hos resurs leverantören för SQL-VM. Om det inte finns någon SQL Server instans på den virtuella datorn, kommer den nya Microsoft. SqlVirtualMachine-resursen att vara i ett felaktigt tillstånd.

**Kan jag registrera en virtuell dator med resurs leverantören för SQL-VM om det finns flera SQL Server instanser?**

Ja. Resurs leverantören för den virtuella SQL-datorn registrerar bara en SQL Server instans (databas motor). Resurs leverantören för SQL-VM registrerar standard SQL Server-instansen om flera instanser används. Om det inte finns någon standard instans, stöds bara registrering i Lightweight-läge. Om du vill uppgradera från läget för förenklad till fullständig hantering måste du antingen använda standard SQL Server-instansen eller den virtuella datorn får bara ha en namngiven SQL Server-instans.

**Kan jag registrera en instans av SQL Server-redundanskluster med den virtuella SQL-adressresursen?**

Ja. SQL Server instanser av kluster för växling vid fel på en virtuell Azure-dator kan registreras med resurs leverantören för SQL-VM i lättviktigt läge. SQL Server kan dock inte uppgraderas till läget fullständig hanterbarhet.

**Kan jag registrera min virtuella dator med resurs leverantören för SQL-VM om en Always on-tillgänglighetsgruppen är konfigurerad?**

Ja. Det finns inga begränsningar för att registrera en SQL Server-instans på en virtuell Azure-dator med resurs leverantören för SQL-VM om du deltar i en konfiguration med Always on-tillgänglighetsgrupper.

**Vad kostar det att registrera med den virtuella SQL VM-adressresursen eller genom att uppgradera till fullständigt hanterbarhets läge?**

Inga. Det finns ingen avgift kopplad till registrering med den virtuella SQL VM-adressresursen eller med något av de tre hanterbarhets lägena. Att hantera SQL Server VM med Resource providern är helt kostnads fritt. 

**Vilken prestanda påverkas av att använda olika hanterbarhets lägen?**

Det påverkas inte när du använder *Noagent* -och *Lightweight* hanterbarhets läge. Det finns minimal påverkan när du använder läget *fullständig* hantering från två tjänster som är installerade på operativ systemet. Dessa kan övervakas via aktivitets hanteraren och visas i den inbyggda konsolen för Windows-tjänster. 

De två tjänst namnen är:
- `SqlIaaSExtensionQuery` (Visnings namn- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Visnings namn- `Microsoft SQL Server IaaS Agent` )

**Hur gör jag för att ta bort tillägget?**

Ta bort tillägget genom att [avregistrera](sql-vm-resource-provider-register.md#unregister-from-rp) SQL Server VM från resurs leverantören för SQL-VM. 

## <a name="next-steps"></a>Nästa steg

Om du vill installera SQL Server IaaS-tillägget till SQL Server på virtuella Azure-datorer kan du läsa artiklarna för [Automatisk installation](sql-vm-resource-provider-automatic-registration.md), [enskilda virtuella datorer](sql-vm-resource-provider-register.md)eller [virtuella datorer i bulk](sql-vm-resource-provider-bulk-register.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server för azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md).
