---
title: Lär dig att granska innehållet i virtuella datorer
description: Lär dig hur Azure Policy använder agenten Gästkonfiguration för att granska inställningar i virtuella datorer.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 1721c0f1ca7c084d636278aabc96f8dac3293038
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759090"
---
# <a name="understand-azure-policys-guest-configuration"></a>Förstå Azure-principens gästkonfiguration

Förutom att granska och [åtgärda](../how-to/remediate-resources.md) Azure-resurser kan Azure Policy granska inställningar inuti en dator. Verifieringen utförs av gästkonfigurationstillägget och klienten. Tillägget kontrollerar inställningar via klienten, till exempel:

- Konfigurationen av operativsystemet
- Programkonfiguration eller förekomst
- Miljöinställningar

För närvarande granskar de flesta Azure Policy Guest Configuration-principer endast inställningarna inuti datorn. De tillämpar inte konfigurationer. Undantaget är en inbyggd princip [som refereras nedan](#applying-configurations-using-guest-configuration).

## <a name="resource-provider"></a>Resursprovider

Innan du kan använda Gästkonfiguration måste du registrera resursleverantören. Resursleverantören registreras automatiskt om tilldelning av en princip för gästkonfiguration görs via portalen. Du kan registrera dig manuellt via [portalen,](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)eller [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="extension-and-client"></a>Tillägg och klient

Om du vill granska inställningarna i en dator aktiveras ett [tillägg för den virtuella datorn.](../../../virtual-machines/extensions/overview.md) Tillägget hämtar tillämplig principtilldelning och motsvarande konfigurationsdefinition.

> [!Important]
> Tillägget Gästkonfiguration krävs för att utföra granskningar i virtuella Azure-datorer.
> Om du vill distribuera tillägget i stor skala tilldelar du följande principdefinitioner:
>   - Distribuera förutsättningar för att aktivera princip för gästkonfiguration på virtuella datorer i Windows.
>   - Distribuera förutsättningar för att aktivera princip för gästkonfiguration på virtuella Linux-datorer.

### <a name="limits-set-on-the-extension"></a>Gränser för förlängningen

För att begränsa tillägget från att påverka program som körs inuti datorn, är gästkonfigurationen inte tillåtet att överskrida mer än 5% av CPU. Den här begränsningen finns för både inbyggda och anpassade definitioner.

### <a name="validation-tools"></a>Valideringsverktyg

Inuti datorn använder gästkonfigurationsklienten lokala verktyg för att köra granskningen.

I följande tabell visas en lista över de lokala verktyg som används i varje operativsystem som stöds:

|Operativsystem|Valideringsverktyg|Anteckningar|
|-|-|-|
|Windows|[Windows PowerShell önskad tillståndskonfiguration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Kock Inspec](https://www.chef.io/inspec/)| Om Ruby och Python inte finns på datorn installeras de av tillägget Gästkonfiguration. |

### <a name="validation-frequency"></a>Valideringsfrekvens

Gästkonfigurationsklienten söker efter nytt innehåll var femte minut. När en gästtilldelning har tagits emot kontrolleras inställningarna för den konfigurationen på nytt med ett 15-minutersintervall.
Resultaten skickas till resursleverantören gästkonfiguration när granskningen är klar. När en [principutvärderingsutlösare](../how-to/get-compliance-data.md#evaluation-triggers) inträffar skrivs datorns tillstånd till resursleverantören gästkonfiguration. Den här uppdateringen gör att Azure-principen utvärderar Azure Resource Manager-egenskaperna. En Azure-principutvärdering på begäran hämtar det senaste värdet från resursleverantören gästkonfiguration. Det utlöser dock inte en ny granskning av konfigurationen i datorn.

## <a name="supported-client-types"></a>Klienttyper som stöds

Principer för gästkonfiguration ingår i nya versioner. Äldre versioner av operativsystem som är tillgängliga på Azure-marknadsplatsen är undantagna om gästkonfigurationsagenten inte är kompatibel. I följande tabell visas en lista över operativsystem som stöds på Azure-avbildningar:

|Utgivare|Namn|Versioner|
|-|-|-|
|Canonical|Ubuntu Server|14.04 och senare|
|Credativ (credativ)|Debian|8 och senare|
|Microsoft|Windows Server|2012 och senare|
|Microsoft|Windows-klient|Windows 10|
|OpenLogic|CentOS|7.3 och senare|
|Red Hat|Red Hat Enterprise Linux|7.4 och senare|
|Suse|SLES|12 SP3 och senare|

### <a name="unsupported-client-types"></a>Klienttyper som inte stöds

Windows Server Nano Server stöds inte i någon version.

## <a name="guest-configuration-extension-network-requirements"></a>Nätverkskrav för gästkonfigurationstillägg

För att kommunicera med resursleverantören gästkonfiguration i Azure kräver datorer utgående åtkomst till Azure-datacenter i port **443**. Om ett nätverk i Azure inte tillåter utgående trafik konfigurerar du undantag med regler [för nätverkssäkerhetsgrupp.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
[Servicemärket](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" kan användas för att referera till tjänsten Gästkonfiguration.

## <a name="azure-managed-identity-requirements"></a>Azure-kraven för hanterad identitet

**DeployIfNotExists-principerna** som lägger till tillägget till virtuella datorer aktiverar också en systemtilldelad hanterad identitet, om det inte finns någon.

> [!WARNING]
> Undvik att aktivera användartilldelade hanterad identitet till virtuella datorer i omfång för principer som aktiverar systemtilldelade hanterade identitet. Den tilldelade användarens identitet ersätts och kan datorn inte svara.

## <a name="guest-configuration-definition-requirements"></a>Definitionskrav för gästkonfiguration

Varje granskning som körs av gästkonfiguration kräver två principdefinitioner, en **DeployIfNotExists-definition** och en **AuditIfNotExists-definition.** 

**Principdefinitionen DeployIfNotExists** validerar och korrigerar följande objekt:

- Verifiera att datorn har tilldelats en konfiguration som ska utvärderas. Om det för närvarande inte finns någon tilldelning hämtar du tilldelningen och förbereder maskinen genom att:
  - Autentisera till datorn med hjälp av en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installera den senaste versionen av **tillägget Microsoft.GuestConfiguration**
  - Installera [valideringsverktyg](#validation-tools) och beroenden om det behövs

Om **distributionomexistenstilldelningen** inte är kompatibel kan en [reparationsuppgift](../how-to/remediate-resources.md#create-a-remediation-task) användas.

När **distributionomnotnotexisternas** tilldelning är kompatibel avgör **AuditIfNotExists-principtilldelningen** om gästtilldelningen är kompatibel eller icke-kompatibel. Valideringsverktyget ger resultaten till gästkonfigurationsklienten. Klienten vidarebefordrar resultatet till gästtillägget, vilket gör dem tillgängliga via resursleverantören Gästkonfiguration.

Azure Policy använder egenskapen Compliance Status **för att** rapportera efterlevnad i noden **Efterlevnad.** Mer information finns i [hämta efterlevnadsdata](../how-to/get-compliance-data.md).

> [!NOTE]
> Principen **DeployIfNotExists** krävs för att **auditifnotexister-principen** ska returnera resultat. Utan **DeployIfNotExists**visar **auditifnotexisterna-principen** "0 av 0" resurser som status.

Alla inbyggda principer för gästkonfiguration ingår i ett initiativ för att gruppera definitionerna för användning i tilldelningar. Det inbyggda initiativet _ \[\]Preview: Audit Password security inuti Linux- och Windows-datorer_ innehåller 18 principer. Det finns sex **DeployIfNotExists** och **AuditIfNotExists** par för Windows och tre par för Linux. [Principdefinitionslogiken](definition-structure.md#policy-rule) verifierar att endast måloperativsystemet utvärderas.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Granska inställningar för operativsystemet efter branschens baslinjer

Ett initiativ i Azure Policy ger möjlighet att granska operativsysteminställningar efter en "baslinje". Definition, _ \[förhandsversion:\]Granska virtuella windows-datorer som inte matchar Azure-säkerhetsbaslinjeinställningar_ innehåller en uppsättning regler som baseras på Active Directory-grupprincip.

De flesta inställningarna är tillgängliga som parametrar. Med parametrar kan du anpassa det som granskas. Anpassa principen till dina krav eller mappa principen till information från tredje part, till exempel branschregleringsstandarder.

Vissa parametrar stöder ett heltalsvärdeintervall. Inställningen Maximal lösenordsålder kan till exempel granska den effektiva grupprincipinställningen. En "1,70" intervall skulle bekräfta att användarna är skyldiga att ändra sina lösenord minst var 70 dagar, men inte mindre än en dag.

Om du tilldelar principen med hjälp av en Distributionsmall för Azure Resource Manager använder du en parameterfil för att hantera undantag. Checka in filerna till ett versionskontrollsystem som Git. Kommentarer om filändringar ger bevis för varför en tilldelning är ett undantag från det förväntade värdet.

#### <a name="applying-configurations-using-guest-configuration"></a>Använda konfigurationer med gästkonfiguration

Den senaste funktionen i Azure Policy konfigurerar inställningar inuti datorer. Definitionen _Konfigurera tidszonen på Windows-datorer_ gör ändringar på datorn genom att konfigurera tidszonen.

När du tilldelar definitioner som börjar med _Konfigurera_måste du också tilldela _definitionsut distribuera förutsättningar för att aktivera gästkonfigurationsprincip på Virtuella datorer i Windows_. Du kan kombinera dessa definitioner i ett initiativ om du vill.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Tilldela principer till datorer utanför Azure

De granskningsprinciper som är tillgängliga för gästkonfiguration omfattar resurstypen **Microsoft.HybridCompute/machines.** Alla datorer som finns upptagna till [Azure Arc för servrar](../../../azure-arc/servers/overview.md) som omfattas av principtilldelningen inkluderas automatiskt.

### <a name="multiple-assignments"></a>Flera tilldelningar

Gästkonfigurationsprinciper stöder för närvarande endast tilldelning av samma gästtilldelning en gång per dator, även om principtilldelningen använder olika parametrar.

## <a name="client-log-files"></a>Loggfiler för klient

Tillägget Gästkonfiguration skriver loggfiler till följande platser:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Var `<version>` refererar till det aktuella versionsnumret.

### <a name="collecting-logs-remotely"></a>Samla in loggar på distans

Det första steget i felsökningen av gästkonfigurationskonfigurationer `Test-GuestConfigurationPackage` eller -moduler bör vara att använda cmdleten så här skapar du [en anpassad granskningsprincip för gästkonfiguration för Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Om det inte lyckas kan insamling av klientloggar hjälpa till att diagnostisera problem.

#### <a name="windows"></a>Windows

Samla in information från loggfiler med hjälp av [Azure VM Run Command](../../../virtual-machines/windows/run-command.md), följande exempel PowerShell-skript kan vara till hjälp.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Samla in information från loggfiler med hjälp av [Azure VM Run Command](../../../virtual-machines/linux/run-command.md), följande exempel Bash skript kan vara till hjälp.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Exempel på gästkonfiguration

Inbyggda principexempel för gästkonfiguration finns på följande platser:

- [Inbyggda principdefinitioner - Gästkonfiguration](../samples/built-in-policies.md#guest-configuration)
- [Inbyggda initiativ - Gästkonfiguration](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy-exempel GitHub-repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du visar information om varje inställning från [efterlevnadsvyn gästkonfiguration](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
