---
title: Lär dig att granska innehållet i virtuella datorer
description: Lär dig hur Azure Policy använder gäst konfigurations agenten för att granska inställningar i virtuella datorer.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 205aa5a9292d0f70fed8247a8af1fe575ad3614e
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830504"
---
# <a name="understand-azure-policys-guest-configuration"></a>Förstå Azure Policy gäst-konfiguration

Utöver att granska och [Reparera](../how-to/remediate-resources.md) Azure-resurser kan Azure policy granska inställningarna i en dator. Verifieringen utförs av gästkonfigurationstillägget och klienten. Tillägget kontrollerar inställningar via klienten, till exempel:

- Operativ systemets konfiguration
- Programkonfiguration eller förekomst
- Miljöinställningar

För närvarande granskar Azure Policy-gästkonfigurationen endast inställningar i datorn. Den tillämpar inte konfigurationer.

## <a name="extension-and-client"></a>Tillägget och klient

Om du vill granska inställningarna i en dator är ett [tillägg för virtuell dator](../../../virtual-machines/extensions/overview.md) aktiverat. Tillägget hämtar tillämpliga principtilldelning och motsvarande-konfigurationsdefinition.

### <a name="limits-set-on-the-extension"></a>Begränsningar som angetts för tillägget

För att begränsa tillägget från att påverka program som körs på datorn får gäst konfigurationen inte överstiga mer än 5% processor belastning. Den här begränsningen finns för både inbyggda och anpassade definitioner.

## <a name="register-guest-configuration-resource-provider"></a>Registrera resursprovidern för gäst-konfiguration

Innan du kan använda gäst-konfigurationen, måste du registrera resursprovidern. Du kan registrera via portalen eller via PowerShell. Resurs leverantören registreras automatiskt om tilldelning av en princip för gäst konfiguration görs via portalen.

### <a name="registration---portal"></a>Registrering – Portal

Registrera resursprovidern för gäst-konfiguration via Azure portal genom att följa dessa steg:

1. Starta Azure-portalen och klicka på **alla tjänster**. Sök efter och välj **prenumerationer**.

1. Hitta och klicka på den prenumeration som du vill aktivera gäst-konfiguration för.

1. I den vänstra menyn i den **prenumeration** klickar du på **resursprovidrar**.

1. Filtrera efter eller Bläddra tills du hittar **Microsoft.GuestConfiguration**, klicka sedan på **registrera** på samma rad.

### <a name="registration---powershell"></a>Registrering – PowerShell

Registrera resursprovidern för gäst-konfiguration via PowerShell genom att köra följande kommando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Verifieringsverktyg för

I datorn använder gäst konfigurations klienten lokala verktyg för att köra granskningen.

I följande tabell visas en lista över de lokala verktyg som används på varje operativsystem som stöds:

|Operativsystem|Verktyget-validering|Anteckningar|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby och Python installeras av tillägget gäst-konfiguration. |

### <a name="validation-frequency"></a>Validerings frekvens

Klienten för gäst konfiguration söker efter nytt innehåll var 5: e minut. När en gäst tilldelning tas emot kontrol leras inställningarna på 15-minuters intervall. Resultat skickas till resurs leverantören för gäst konfigurationen så snart granskningen är klar. När en [utlösare](../how-to/get-compliance-data.md#evaluation-triggers) för princip utvärdering inträffar skrivs datorns tillstånd till resurs leverantören för gäst konfiguration. Den här uppdateringen gör att Azure Policy utvärdera Azure Resource Manager egenskaper. En utvärdering på begäran Azure Policy hämtar det senaste värdet från resurs leverantören för gäst konfigurationen. Den utlöser dock inte en ny granskning av konfigurationen på datorn.

## <a name="supported-client-types"></a>Stöds klienttyper

I följande tabell visas en lista över operativsystem som stöds på Azure-avbildningar:

|Utgivare|Namn|Versioner|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Data Center, 2012 R2 Data Center, 2016 Data Center, 2019 Data Center|
|Microsoft|Windows-klient|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Gäst konfiguration kan granska noder som kör ett operativ system som stöds. Om du vill granska virtuella datorer som använder en anpassad avbildning måste du duplicera **DeployIfNotExists** -definitionen och ändra **IF** -avsnittet så att det innehåller dina avbildnings egenskaper.

### <a name="unsupported-client-types"></a>Klientappar typer

Windows Server Nano Server stöds inte i någon version.

## <a name="guest-configuration-extension-network-requirements"></a>Nätverks krav för gäst konfigurations tillägg

För att kunna kommunicera med resurs leverantören för gäst konfiguration i Azure måste datorer ha utgående åtkomst till Azure-datacenter på port **443**. Om du använder ett privat virtuellt nätverk i Azure som inte tillåter utgående trafik, konfigurerar du undantag med regler för [nätverks säkerhets grupper](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .
[Service tag-](../../../virtual-network/service-tags-overview.md) GuestAndHybridManagement kan användas för att referera till gäst konfigurations tjänsten.

## <a name="guest-configuration-definition-requirements"></a>Definition av gäst konfigurationskrav

Varje konfiguration för gransknings körning av gäst kräver två princip definitioner, en **DeployIfNotExists** -definition och en **AuditIfNotExists** -definition. **DeployIfNotExists** -definitionen används för att förbereda datorn med gäst konfigurations agenten och andra komponenter som stöder [verifierings verktygen](#validation-tools).

Den **DeployIfNotExists** principdefinitionen kontrollerar och korrigerar följande objekt:

- Verifiera att datorn har tilldelats en konfiguration som ska utvärderas. Om ingen tilldelning för närvarande finns kan du hämta tilldelningen och förbereda datorn genom att:
  - Autentisera till datorn med en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installera den senaste versionen av den **Microsoft.GuestConfiguration** tillägg
  - Installera [verifieringsverktyg](#validation-tools) och beroenden, om det behövs

Om tilldelningen **DeployIfNotExists** är icke-kompatibel kan en [reparations uppgift](../how-to/remediate-resources.md#create-a-remediation-task) användas.

När **DeployIfNotExists** -tilldelningen är kompatibel använder **AuditIfNotExists** -princip tilldelningen de lokala verifierings verktygen för att avgöra om konfigurations tilldelningen är kompatibel eller inte kompatibel. Verktyget verifiering ger resultatet till gäst-konfiguration-klienten. Klienten vidarebefordrar resultaten till gäst-tillägg, vilket gör dem tillgängliga via resursprovidern gäst-konfiguration.

Azure Policy använder resursen gäst konfigurationstjänst **complianceStatus** egenskapen att rapportera kompatibilitet i den **efterlevnad** noden. Mer information finns i [komma kompatibilitetsdata](../how-to/get-compliance-data.md).

> [!NOTE]
> **DeployIfNotExists** -principen krävs för att **AuditIfNotExists** -principen ska returnera resultat. Utan **DeployIfNotExists**visar **AuditIfNotExists** -principen "0 av 0" resurser som status.

Alla inbyggda principer för gästen konfiguration ingår i ett initiativ till gruppen definitioner för modulen tilldelningar. Det inbyggda initiativet med namnet _\[för hands version\]: granska säkerhets inställningar för lösen ord i Linux-och Windows-datorer_ innehåller 18 principer. Det finns sex **DeployIfNotExists** och **AuditIfNotExists** par för Windows och tre par för Linux. [Princip definitions](definition-structure.md#policy-rule) logiken verifierar att endast mål operativ systemet utvärderas.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Granska operativ system inställningar efter bransch bas linjer

Ett av de initiativ som är tillgängliga i Azure Policy ger möjlighet att granska operativ systemets inställningar i virtuella datorer efter "bas linje" från Microsoft. Definitionen\[för _hands version\]: granska virtuella Windows-datorer som inte matchar inställningarna för Azures säkerhets bas linje_ innehåller en fullständig uppsättning gransknings regler baserade på inställningarna från Active Directory Grupprincip.

De flesta av inställningarna är tillgängliga som parametrar. Med den här funktionen kan du anpassa vad som granskas för att justera principen med organisationens krav eller mappa principen till information från tredje part, till exempel bransch regelverks standarder.

Vissa parametrar har stöd för ett heltals värde intervall. Till exempel kan den högsta ålders parametern för lösen ord anges med en intervall operator för att ge flexibilitet till dator ägare. Du kan granska att den effektiva grupprincips inställningen som kräver att användarna ändrar sina lösen ord inte ska vara mer än 70 dagar, men inte vara mindre än en dag. Som det beskrivs i info-bubblan för parametern, för att göra den här affärs principen till ett effektivt gransknings värde, ställer du in värdet på "1, 70".

Om du tilldelar principen med hjälp av en mall för Azure Resource Manager distribution kan du använda en parameter fil för att hantera inställningarna från käll kontroll. Genom att använda ett verktyg som Git för att hantera ändringar i gransknings principer med kommentarer i varje inchecknings dokument, bevis för varför en tilldelning ska vara ett undantag till det förväntade värdet.

#### <a name="applying-configurations-using-guest-configuration"></a>Använda konfigurationer med gäst konfiguration

Den senaste funktionen i Azure Policy konfigurerar inställningar i datorer. Definitionen _Konfigurera tids zonen på Windows-datorer_ gör ändringar i datorn genom att konfigurera tids zonen.

När du tilldelar definitioner som börjar med _Konfigurera_måste du också tilldela _krav för definitions distribution för att aktivera principen för gäst konfiguration på virtuella Windows-datorer_. Du kan kombinera dessa definitioner i ett initiativ om du väljer.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Tilldela principer till datorer utanför Azure

De gransknings principer som är tillgängliga för gäst konfiguration är resurs typen **Microsoft. HybridCompute/Machines** . Alla datorer som har publicerats till [Azure-bågen för servrar](../../../azure-arc/servers/overview.md) som omfattas av princip tilldelningen ingår automatiskt.

### <a name="multiple-assignments"></a>Flera tilldelningar

Principer för gäst konfiguration stöder för närvarande bara tilldelning av samma gäst tilldelning en gång per dator, även om princip tilldelningen använder olika parametrar.

## <a name="built-in-resource-modules"></a>Inbyggda resurs moduler

När du installerar gäst konfigurations tillägget ingår PowerShell-modulen "GuestConfiguration" i den senaste versionen av DSC-resurs-moduler. Den här modulen kan laddas ned från PowerShell-galleriet med hjälp av länken "manuell hämtning" från sidan för modulen [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). Fil formatet '. nupkg ' kan byta namn till '. zip ' för att expandera och granska.

## <a name="client-log-files"></a>Loggfiler för klienter

Gäst konfigurations tillägget skriver loggfiler till följande platser:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Där `<version>` refererar till det aktuella versions numret.

### <a name="collecting-logs-remotely"></a>Samla in loggar via fjärr anslutning

Det första steget i Felsöka konfigurationer av gäst konfiguration eller moduler bör vara att använda `Test-GuestConfigurationPackage`-cmdleten enligt stegen i [testa ett gäst konfigurations paket](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Om det inte lyckas kan insamling av klient loggar hjälpa till att diagnostisera problem.

#### <a name="windows"></a>Windows

Om du vill använda funktionen för körning av virtuella Azure-datorer för att avbilda information från loggfiler i Windows-datorer kan du använda PowerShell-skriptet i följande exempel. Mer information finns i [köra PowerShell-skript i din virtuella Windows-dator med kommandot kör](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Om du vill använda funktionen för körning av virtuella Azure-datorer för att avbilda information från loggfiler på Linux-datorer kan du vara användbar i följande exempel bash-skript. Mer information finns i [Kör Shell-skript i din virtuella Linux-dator med kommandot kör](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Exempel på gäst konfiguration

Exempel på gäst konfiguration av principer finns på följande platser:

- [Exempel index – gäst konfiguration](../samples/index.md#guest-configuration)
- [Azure Policy exempel GitHub lagrings platsen](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
