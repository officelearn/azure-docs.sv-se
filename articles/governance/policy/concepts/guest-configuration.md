---
title: Förstå hur du granskar innehållet på en dator
description: Lär dig hur Azure Policy använder gäst konfiguration för att granska inställningar i en Azure-dator.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/20/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 733b7fd8ef5b302df754fc7299b420739baff153
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172476"
---
# <a name="understand-azure-policys-guest-configuration"></a>Förstå Azure Policy gäst-konfiguration

Utöver att granska och [Reparera](../how-to/remediate-resources.md) Azure-resurser kan Azure policy granska inställningarna i en dator. Verifieringen utförs av gäst-konfiguration-tillägget och klienten. Tillägget, via klienten, validerar inställningar som:

- Operativ systemets konfiguration
- Program konfiguration eller närvaro
- Miljöinställningar

För närvarande granskar Azure Policy-gäst konfigurationen endast inställningar i datorn. Konfigurationen används inte.

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
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby och Python installeras av tillägget gäst-konfiguration. |

### <a name="validation-frequency"></a>Validerings frekvens

Klienten för gäst konfiguration söker efter nytt innehåll var 5: e minut. När en gäst tilldelning tas emot kontrol leras inställningarna på 15-minuters intervall. Resultat skickas till resurs leverantören för gäst konfigurationen så snart granskningen är klar. När en utlösare för princip [utvärdering](../how-to/get-compliance-data.md#evaluation-triggers) inträffar skrivs datorns tillstånd till resurs leverantören för gäst konfiguration. Den här uppdateringen gör att Azure Policy utvärdera Azure Resource Manager egenskaper. En utvärdering på begäran Azure Policy hämtar det senaste värdet från resurs leverantören för gäst konfigurationen. Den utlöser dock inte en ny granskning av konfigurationen på datorn.

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

För att kunna kommunicera med resurs leverantören för gäst konfiguration i Azure måste datorer ha utgående åtkomst till Azure-datacenter på port **443**. Om du använder ett privat virtuellt nätverk i Azure som inte tillåter utgående trafik, konfigurerar du undantag med regler för [nätverks säkerhets grupper](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Det finns för närvarande ingen service tag för Azure Policy gäst konfiguration.

I IP-adress listor kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Du behöver bara tillåta utgående åtkomst till IP-adresserna i de regioner där de virtuella datorerna distribueras.

> [!NOTE]
> XML-filen för Azure datacenter-IP-adress innehåller de IP-adressintervall som används i Microsoft Azure Data Center. Filen innehåller beräknings-, SQL-och lagrings intervall. En uppdaterad fil publiceras varje vecka. Filen visar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka. Det är en bra idé att ladda ned den nya XML-filen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. Azure ExpressRoute-användare bör Observera att den här filen används för att uppdatera Border Gateway Protocol-annonsering (BGP) för Azure-utrymmet under den första veckan i varje månad.

## <a name="guest-configuration-definition-requirements"></a>Definition av gäst konfigurationskrav

Varje konfiguration för gransknings körning av gäst kräver två princip definitioner, en **DeployIfNotExists** -definition och en **AuditIfNotExists** -definition. **DeployIfNotExists** -definitionen används för att förbereda datorn med gäst konfigurations agenten och andra komponenter som stöder [verifierings verktygen](#validation-tools).

Den **DeployIfNotExists** principdefinitionen kontrollerar och korrigerar följande objekt:

- Verifiera att datorn har tilldelats en konfiguration som ska utvärderas. Om ingen tilldelning för närvarande finns kan du hämta tilldelningen och förbereda datorn genom att:
  - Autentisera till datorn med en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installera den senaste versionen av den **Microsoft.GuestConfiguration** tillägg
  - Installera [verifieringsverktyg](#validation-tools) och beroenden, om det behövs

Om tilldelningen **DeployIfNotExists** är icke-kompatibel kan en [reparations uppgift](../how-to/remediate-resources.md#create-a-remediation-task) användas.

När **DeployIfNotExists** -tilldelningen är kompatibel använder **AuditIfNotExists** -princip tilldelningen de lokala verifierings verktygen för att avgöra om konfigurations tilldelningen är kompatibel eller inte kompatibel. Verktyget verifiering ger resultatet till gäst-konfiguration-klienten. Klienten vidarebefordrar resultaten till gäst-tillägg, vilket gör dem tillgängliga via resursprovidern gäst-konfiguration.

Azure Policy använder resursen gäst konfigurationstjänst **complianceStatus** egenskapen att rapportera kompatibilitet i den **efterlevnad** noden. Mer information finns i [komma kompatibilitetsdata](../how-to/getting-compliance-data.md).

> [!NOTE]
> **DeployIfNotExists** -principen krävs för att **AuditIfNotExists** -principen ska returnera resultat. Utan **DeployIfNotExists**visar **AuditIfNotExists** -principen "0 av 0" resurser som status.

Alla inbyggda principer för gästen konfiguration ingår i ett initiativ till gruppen definitioner för modulen tilldelningar. Det inbyggda initiativet med namnet *[för hands version]: Granska lösen ords säkerhets inställningar i Linux-och* Windows-datorer innehåller 18 principer. Det finns sex **DeployIfNotExists** och **AuditIfNotExists** par för Windows och tre par för Linux. [Princip definitions](definition-structure.md#policy-rule) logiken verifierar att endast mål operativ systemet utvärderas.

### <a name="multiple-assignments"></a>Flera tilldelningar

Principer för gäst konfiguration stöder för närvarande bara tilldelning av samma gäst tilldelning en gång per dator, även om princip tilldelningen använder olika parametrar.

## <a name="built-in-resource-modules"></a>Inbyggda resurs moduler

När du installerar gäst konfigurations tillägget ingår PowerShell-modulen "GuestConfiguration" i den senaste versionen av DSC-resurs-moduler. Den här modulen kan laddas ned från PowerShell-galleriet med hjälp av länken "manuell hämtning" från sidan för modulen [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/).
Fil formatet '. nupkg ' kan byta namn till '. zip ' för att expandera och granska.

## <a name="client-log-files"></a>Loggfiler för klienter

Gäst konfigurations tillägget skriver loggfiler till följande platser:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Där `<version>` refererar till det aktuella versions numret.

### <a name="collecting-logs-remotely"></a>Samla in loggar via fjärr anslutning

Det första steget i Felsöka konfigurationer av gäst konfiguration eller moduler bör vara att använda `Test-GuestConfigurationPackage` cmdleten genom att följa stegen i [testa ett gäst konfigurations paket](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
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
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/index.md).