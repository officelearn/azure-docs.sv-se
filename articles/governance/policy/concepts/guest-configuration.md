---
title: Förstå hur du granskar innehållet i en virtuell dator
description: Lär dig hur Azure Policy använder gäst-konfiguration för att granska inställningar i en Azure virtuell dator.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 74e36d944450e1ce2c61481b2cb7e345860212af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326888"
---
# <a name="understand-azure-policys-guest-configuration"></a>Förstå Azure Policy gäst-konfiguration

Förutom att granska och [Reparera](../how-to/remediate-resources.md) Azure-resurser kan Azure policy granska inställningarna i en virtuell dator. Verifieringen utförs av gäst-konfiguration-tillägget och klienten. Tillägget med klienten, verifierar inställningar, till exempel konfigurationen av operativsystemet, programkonfigurationen eller närvaro, miljöinställningar och mer.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Tillägget och klient

Granska inställningarna inuti en virtuell dator, en [tillägg för virtuell dator](../../../virtual-machines/extensions/overview.md) är aktiverad. Tillägget hämtar tillämpliga principtilldelning och motsvarande-konfigurationsdefinition.

### <a name="register-guest-configuration-resource-provider"></a>Registrera resursprovidern för gäst-konfiguration

Innan du kan använda gäst-konfigurationen, måste du registrera resursprovidern. Du kan registrera via portalen eller via PowerShell. Resurs leverantören registreras automatiskt om tilldelning av en princip för gäst konfiguration görs via portalen.

#### <a name="registration---portal"></a>Registrering – Portal

Registrera resursprovidern för gäst-konfiguration via Azure portal genom att följa dessa steg:

1. Starta Azure-portalen och klicka på **alla tjänster**. Sök efter och välj **prenumerationer**.

1. Hitta och klicka på den prenumeration som du vill aktivera gäst-konfiguration för.

1. I den vänstra menyn i den **prenumeration** klickar du på **resursprovidrar**.

1. Filtrera efter eller Bläddra tills du hittar **Microsoft.GuestConfiguration**, klicka sedan på **registrera** på samma rad.

#### <a name="registration---powershell"></a>Registrering – PowerShell

Registrera resursprovidern för gäst-konfiguration via PowerShell genom att köra följande kommando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Verifieringsverktyg för

Inuti den virtuella datorn använder klienten gäst Configuration lokala verktyg för att köra granskningen.

I följande tabell visas en lista över de lokala verktyg som används på varje operativsystem som stöds:

|Operativsystem|Verktyget-validering|Anteckningar|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby och Python installeras av tillägget gäst-konfiguration. |

### <a name="validation-frequency"></a>Validerings frekvens

Klienten för gäst konfiguration söker efter nytt innehåll var 5: e minut. När en gäst tilldelning tas emot kontrol leras inställningarna på 15-minuters intervall. Resultat skickas till resurs leverantören för gäst konfigurationen så snart granskningen är klar. När en utlösare för princip [utvärdering](../how-to/get-compliance-data.md#evaluation-triggers) inträffar skrivs datorns tillstånd till resurs leverantören för gäst konfiguration. Detta gör att Azure Policy utvärdera Azure Resource Manager egenskaper. En utvärdering på begäran Azure Policy hämtar det senaste värdet från resurs leverantören för gäst konfigurationen. Den utlöser dock inte en ny granskning av konfigurationen i den virtuella datorn.

### <a name="supported-client-types"></a>Stöds klienttyper

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

### <a name="guest-configuration-extension-network-requirements"></a>Nätverks krav för gäst konfigurations tillägg

För att kunna kommunicera med resurs leverantören för gäst konfiguration i Azure måste virtuella datorer ha utgående åtkomst till Azure-datacenter på port **443**. Om du använder ett privat virtuellt nätverk i Azure och inte tillåter utgående trafik måste undantag konfigureras med regler för [nätverks säkerhets grupper](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . För tillfället finns det ingen service tag för Azure Policy gäst konfiguration.

I IP-adress listor kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Du behöver bara tillåta utgående åtkomst till IP-adresserna i de regioner där de virtuella datorerna distribueras.

> [!NOTE]
> XML-filen för Azure datacenter-IP-adress innehåller de IP-adressintervall som används i Microsoft Azure Data Center. Filen innehåller beräknings-, SQL-och lagrings intervall. En uppdaterad fil publiceras varje vecka. Filen visar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka. Det är en bra idé att ladda ned den nya XML-filen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. Azure ExpressRoute-användare bör Observera att den här filen används för att uppdatera Border Gateway Protocol-annonsering (BGP) för Azure-utrymmet under den första veckan i varje månad.

## <a name="guest-configuration-definition-requirements"></a>Definition av gäst konfigurationskrav

Varje konfiguration för gransknings körning av gäst kräver två princip definitioner, en **DeployIfNotExists** -definition och en **gransknings** definition. **DeployIfNotExists** -definitionen används för att förbereda den virtuella datorn med gäst konfigurations agenten och andra komponenter som stöder [verifierings verktygen](#validation-tools).

Den **DeployIfNotExists** principdefinitionen kontrollerar och korrigerar följande objekt:

- Verifiera den virtuella datorn har tilldelats en konfiguration som ska utvärderas. Om ingen tilldelning finns för närvarande kan hämta tilldelningen och förbereda den virtuella datorn med:
  - Autentisering till den virtuella datorn med en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installera den senaste versionen av den **Microsoft.GuestConfiguration** tillägg
  - Installera [verifieringsverktyg](#validation-tools) och beroenden, om det behövs

Om tilldelningen **DeployIfNotExists** är icke-kompatibel kan en [reparations uppgift](../how-to/remediate-resources.md#create-a-remediation-task) användas.

När **DeployIfNotExists** -tilldelningen är kompatibel använder **gransknings** princip tilldelningen de lokala verifierings verktygen för att avgöra om konfigurations tilldelningen är kompatibel eller inte kompatibel.
Verktyget verifiering ger resultatet till gäst-konfiguration-klienten. Klienten vidarebefordrar resultaten till gäst-tillägg, vilket gör dem tillgängliga via resursprovidern gäst-konfiguration.

Azure Policy använder resursen gäst konfigurationstjänst **complianceStatus** egenskapen att rapportera kompatibilitet i den **efterlevnad** noden. Mer information finns i [komma kompatibilitetsdata](../how-to/getting-compliance-data.md).

> [!NOTE]
> **DeployIfNotExists** -principen krävs för att **gransknings** principen ska returnera resultat.
> Utan **DeployIfNotExists**visar gransknings  principen "0 av 0" resurser som status.

Alla inbyggda principer för gästen konfiguration ingår i ett initiativ till gruppen definitioner för modulen tilldelningar. Det inbyggda initiativet med namnet *[för hands version]: Granska säkerhets inställningar för lösen ord i virtuella Linux-* och Windows-datorer innehåller 18 principer. Det finns sex **DeployIfNotExists** -  och gransknings par för Windows och tre par för Linux. I båda fallen logiken i definitionen verifierar endast målet operativsystemet ska utvärderas baserat på den [principregeln](definition-structure.md#policy-rule) definition.

## <a name="multiple-assignments"></a>Flera tilldelningar

Principer för gäst konfiguration stöder för närvarande bara tilldelning av samma gäst tilldelning en gång per virtuell dator, även om princip tilldelningen använder olika parametrar.

## <a name="client-log-files"></a>Loggfiler för klienter

Gäst konfigurations tillägget skriver loggfiler till följande platser:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Där `<version>` refererar till det aktuella versions numret.

## <a name="guest-configuration-samples"></a>Exempel på gäst konfiguration

Exempel på gäst konfiguration av principer finns på följande platser:

- [Exempel index – gäst konfiguration](../samples/index.md#guest-configuration)
- [Azure policy samples GitHub lagrings platsen](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/index.md).
