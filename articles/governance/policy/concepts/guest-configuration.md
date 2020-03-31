---
title: Lär dig att granska innehållet i virtuella datorer
description: Lär dig hur Azure Policy använder agenten Gästkonfiguration för att granska inställningar i virtuella datorer.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: cc2ba11f75da5f993b99c90e5d0cc1030003203e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257264"
---
# <a name="understand-azure-policys-guest-configuration"></a>Förstå Azure-principens gästkonfiguration

Förutom att granska och [åtgärda](../how-to/remediate-resources.md) Azure-resurser kan Azure Policy granska inställningar inuti en dator. Verifieringen utförs av gästkonfigurationstillägget och klienten. Tillägget kontrollerar inställningar via klienten, till exempel:

- Konfigurationen av operativsystemet
- Programkonfiguration eller förekomst
- Miljöinställningar

För närvarande granskar Azure Policy-gästkonfigurationen endast inställningar i datorn. Den tillämpar inte konfigurationer.

## <a name="extension-and-client"></a>Tillägg och klient

Om du vill granska inställningarna i en dator aktiveras ett [tillägg för den virtuella datorn.](../../../virtual-machines/extensions/overview.md) Tillägget hämtar tillämplig principtilldelning och motsvarande konfigurationsdefinition.

### <a name="limits-set-on-the-extension"></a>Gränser för förlängningen

För att begränsa tillägget från att påverka program som körs inuti datorn, är gästkonfigurationen inte tillåtet att överskrida mer än 5% av CPU-användningen. Den här begränsningen finns för både inbyggda och anpassade definitioner.

## <a name="register-guest-configuration-resource-provider"></a>Registrera resursleverantör för gästkonfiguration

Innan du kan använda Gästkonfiguration måste du registrera resursleverantören. Du kan registrera dig via portalen eller via PowerShell. Resursleverantören registreras automatiskt om tilldelning av en princip för gästkonfiguration görs via portalen.

### <a name="registration---portal"></a>Registrering - Portal

Så här registrerar du resursleverantören för gästkonfiguration via Azure-portalen:

1. Starta Azure-portalen och klicka på **Alla tjänster**. Sök efter och välj **Prenumerationer**.

1. Sök och klicka på den prenumeration som du vill aktivera Gästkonfiguration för.

1. Klicka på **Resursleverantörer**på menyn prenumerationsmeny. **Subscription**

1. Filtrera efter eller rulla tills du hittar **Microsoft.GuestConfiguration**och klicka sedan på **Registrera** på samma rad.

### <a name="registration---powershell"></a>Registrering - PowerShell

Om du vill registrera resursleverantören för gästkonfiguration via PowerShell kör du följande kommando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Valideringsverktyg

Inuti datorn använder gästkonfigurationsklienten lokala verktyg för att köra granskningen.

I följande tabell visas en lista över de lokala verktyg som används i varje operativsystem som stöds:

|Operativsystem|Valideringsverktyg|Anteckningar|
|-|-|-|
|Windows|[Windows PowerShell önskad tillståndskonfiguration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Inspec](https://www.chef.io/inspec/)| Ruby och Python installeras av tillägget Gästkonfiguration. |

### <a name="validation-frequency"></a>Valideringsfrekvens

Gästkonfigurationsklienten söker efter nytt innehåll var femte minut. När en gästuppgift har tagits emot kontrolleras inställningarna på ett 15-minutersintervall. Resultaten skickas till resursleverantören gästkonfiguration så snart granskningen är klar. När en [principutvärderingsutlösare](../how-to/get-compliance-data.md#evaluation-triggers) inträffar skrivs datorns tillstånd till resursleverantören gästkonfiguration. Den här uppdateringen gör att Azure-principen utvärderar Azure Resource Manager-egenskaperna. En Azure-principutvärdering på begäran hämtar det senaste värdet från resursleverantören gästkonfiguration. Det utlöser dock inte en ny granskning av konfigurationen i datorn.

## <a name="supported-client-types"></a>Klienttyper som stöds

I följande tabell visas en lista över operativsystem som stöds på Azure-avbildningar:

|Utgivare|Namn|Versioner|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ (credativ)|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows-klient|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6|
|Suse|SLES|12 SP3 (PÅ ANDRA)|

> [!IMPORTANT]
> Gästkonfiguration kan granska noder som kör ett operativsystem som stöds. Om du vill granska virtuella datorer som använder en anpassad avbildning måste du duplicera definitionen **DeployIfNotExists** och ändra avsnittet **Om** så att de innehåller dina bildegenskaper.

### <a name="unsupported-client-types"></a>Klienttyper som inte stöds

Windows Server Nano Server stöds inte i någon version.

## <a name="guest-configuration-extension-network-requirements"></a>Nätverkskrav för gästkonfigurationstillägg

För att kommunicera med resursleverantören gästkonfiguration i Azure kräver datorer utgående åtkomst till Azure-datacenter i port **443**. Om du använder ett privat virtuellt nätverk i Azure som inte tillåter utgående trafik konfigurerar du undantag med [regler för nätverkssäkerhetsgrupp.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
[Servicemärket](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" kan användas för att referera till tjänsten Gästkonfiguration.

## <a name="guest-configuration-definition-requirements"></a>Definitionskrav för gästkonfiguration

Varje granskning som körs av gästkonfiguration kräver två principdefinitioner, en **DeployIfNotExists-definition** och en **AuditIfNotExists-definition.** **DeployIfNotExists-definitionen** används för att förbereda datorn med gästkonfigurationsagenten och andra komponenter för att stödja [valideringsverktygen](#validation-tools).

**Principdefinitionen DeployIfNotExists** validerar och korrigerar följande objekt:

- Verifiera att datorn har tilldelats en konfiguration som ska utvärderas. Om det för närvarande inte finns någon tilldelning hämtar du tilldelningen och förbereder maskinen genom att:
  - Autentisera till datorn med hjälp av en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installera den senaste versionen av **tillägget Microsoft.GuestConfiguration**
  - Installera [valideringsverktyg](#validation-tools) och beroenden om det behövs

Om **distributionomexistenstilldelningen** inte är kompatibel kan en [reparationsuppgift](../how-to/remediate-resources.md#create-a-remediation-task) användas.

När **distributionomnotnotexisterna-tilldelningen** är kompatibel använder **AuditIfNotExists** principtilldelning de lokala valideringsverktygen för att avgöra om konfigurationstilldelningen är kompatibel eller icke-kompatibel. Valideringsverktyget ger resultaten till gästkonfigurationsklienten. Klienten vidarebefordrar resultatet till gästtillägget, vilket gör dem tillgängliga via resursleverantören Gästkonfiguration.

Azure Policy använder egenskapen Compliance Status **för att** rapportera efterlevnad i noden **Efterlevnad.** Mer information finns i [hämta efterlevnadsdata](../how-to/get-compliance-data.md).

> [!NOTE]
> Principen **DeployIfNotExists** krävs för att **auditifnotexister-principen** ska returnera resultat. Utan **DeployIfNotExists**visar **auditifnotexisterna-principen** "0 av 0" resurser som status.

Alla inbyggda principer för gästkonfiguration ingår i ett initiativ för att gruppera definitionerna för användning i tilldelningar. Det inbyggda initiativet _ \[\]Preview: Audit Password security settings inside Linux and Windows machines_ innehåller 18 principer. Det finns sex **DeployIfNotExists** och **AuditIfNotExists** par för Windows och tre par för Linux. [Principdefinitionslogiken](definition-structure.md#policy-rule) verifierar att endast måloperativsystemet utvärderas.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Granska inställningar för operativsystemet efter branschens baslinjer

Ett av de initiativ som är tillgängliga i Azure Policy ger möjlighet att granska operativsysteminställningar i virtuella datorer efter en "baslinje" från Microsoft. Definition, _ \[förhandsversion:\]Granska virtuella Windows-datorer som inte matchar Azure-säkerhetsbaslinjeinställningar_ innehåller en fullständig uppsättning granskningsregler baserat på inställningar från Active Directory-grupprincipen.

De flesta inställningarna är tillgängliga som parametrar. Med den här funktionen kan du anpassa vad som granskas för att anpassa principen till dina organisationskrav eller för att mappa principen till information från tredje part, till exempel branschregleringsstandarder.

Vissa parametrar stöder ett heltalsvärdeintervall. Parametern Maximum Password Age kan till exempel ställas in med hjälp av en intervalloperator för att ge flexibilitet till maskinägare. Du kan granska att den effektiva grupprincipinställningen som kräver att användarna ändrar sina lösenord inte bör vara mer än 70 dagar, men bör inte vara mindre än en dag. Som beskrivs i informationsbubblan för parametern anger du värdet till "1,70" för att göra den här affärsprincipen till det effektiva granskningsvärdet.

Om du tilldelar principen med hjälp av en Distributionsmall för Azure Resource Manager kan du använda en parameterfil för att hantera dessa inställningar från källkontrollen. Använda ett verktyg som Git för att hantera ändringar i granskningsprinciper med kommentarer vid varje incheckningsdokument bevis på varför en tilldelning bör vara ett undantag från det förväntade värdet.

#### <a name="applying-configurations-using-guest-configuration"></a>Använda konfigurationer med gästkonfiguration

Den senaste funktionen i Azure Policy konfigurerar inställningar inuti datorer. Definitionen _Konfigurera tidszonen på Windows-datorer_ gör ändringar på datorn genom att konfigurera tidszonen.

När du tilldelar definitioner som börjar med _Konfigurera_måste du också tilldela _definitionsut distribuera förutsättningar för att aktivera gästkonfigurationsprincip på Virtuella datorer i Windows_. Du kan kombinera dessa definitioner i ett initiativ om du vill.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Tilldela principer till datorer utanför Azure

De granskningsprinciper som är tillgängliga för gästkonfiguration omfattar resurstypen **Microsoft.HybridCompute/machines.** Alla datorer som finns upptagna till [Azure Arc för servrar](../../../azure-arc/servers/overview.md) som omfattas av principtilldelningen inkluderas automatiskt.

### <a name="multiple-assignments"></a>Flera tilldelningar

Gästkonfigurationsprinciper stöder för närvarande endast tilldelning av samma gästtilldelning en gång per dator, även om principtilldelningen använder olika parametrar.

## <a name="built-in-resource-modules"></a>Inbyggda resursmoduler

När du installerar tillägget Gästkonfiguration ingår PowerShell-modulen "GuestConfiguration" i den senaste versionen av DSC-resursmoduler. Denna modul kan laddas ner från PowerShell Gallery med hjälp av "Manuell nedladdning" länken från modulsidan [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). Filformatet ".nupkg" kan döpas om till '.zip' för att packa upp och granska.

## <a name="client-log-files"></a>Loggfiler för klient

Tillägget Gästkonfiguration skriver loggfiler till följande platser:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Var `<version>` refererar till det aktuella versionsnumret.

### <a name="collecting-logs-remotely"></a>Samla in loggar på distans

Det första steget i felsökningen av gästkonfigurationskonfigurationer `Test-GuestConfigurationPackage` eller -moduler bör vara att använda cmdleten så här skapar du [en anpassad granskningsprincip för gästkonfiguration för Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Om det inte lyckas kan insamling av klientloggar hjälpa till att diagnostisera problem.

#### <a name="windows"></a>Windows

Om du vill använda azure VM Run Command-funktionen för att samla in information från loggfiler i Windows-datorer kan följande exempel PowerShell-skript vara till hjälp. Mer information finns [i Köra PowerShell-skript i windows VM med kommandot Kör](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Om du vill använda azure VM Run Command-funktionen för att samla in information från loggfiler i Linux-datorer kan följande exempel Bash-skript vara till hjälp. Mer information finns [i Kör skalskript i din Virtuella Linux-dator med Kommandot Kör](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Exempel på gästkonfiguration

Källa för de inbyggda initiativen för principgästenskonfiguration finns på följande platser:

- [Inbyggda principdefinitioner - Gästkonfiguration](../samples/built-in-policies.md#guest-configuration)
- [Inbyggda initiativ - Gästkonfiguration](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy-exempel GitHub-repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
