---
title: Lär dig att granska innehållet i en virtuell dator
description: Lär dig hur Azure Policy använder gäst-konfiguration för att granska inställningar i en Azure virtuell dator.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 19f55c7d383d64e6c400e22e624b713f6c42dc58
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649296"
---
# <a name="understand-azure-policys-guest-configuration"></a>Förstå Azure Policy gäst-konfiguration

Förutom granskning och [åtgärda](../how-to/remediate-resources.md) Azure-resurser, Azure Policy kan granska inställningarna i en virtuell dator. Verifieringen utförs av gäst-konfiguration-tillägget och klienten. Tillägget med klienten, verifierar inställningar, till exempel konfigurationen av operativsystemet, programkonfigurationen eller närvaro, miljöinställningar och mer.

> [!IMPORTANT]
> För närvarande endast **inbyggda** principer stöds med gästen konfiguration.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Tillägget och klient

Granska inställningarna inuti en virtuell dator, en [tillägg för virtuell dator](../../../virtual-machines/extensions/overview.md) är aktiverad. Tillägget hämtar tillämpliga principtilldelning och motsvarande-konfigurationsdefinition.

### <a name="register-guest-configuration-resource-provider"></a>Registrera resursprovidern för gäst-konfiguration

Innan du kan använda gäst-konfigurationen, måste du registrera resursprovidern. Du kan registrera via portalen eller via PowerShell. Resursprovidern registreras automatiskt om tilldelningen av en gäst Konfigurationsprincip görs via portalen.

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

### <a name="validation-frequency"></a>Frekvens för verifiering

Gäst-konfigurationen kontrollerar klienten för nytt innehåll var femte minut. När en gäst-tilldelning tas emot, kontrolleras inställningarna med 15 minuters intervall. Resultaten skickas till resursprovidern gäst konfiguration när granskningen är klar. När en princip [utvärdering utlösaren](../how-to/get-compliance-data.md#evaluation-triggers) inträffar, tillståndet för datorn skrivs till resursprovidern gäst-konfiguration. Den här händelsen gör Azure Policy att utvärdera Azure Resource Manager-egenskaper. En på begäran principutvärdering hämtar det senaste värdet från resursprovidern gäst-konfiguration. Dock utlöser inte den en ny granskning av konfigurationen på den virtuella datorn.

### <a name="supported-client-types"></a>Stöds klienttyper

I följande tabell visas en lista över operativsystem som stöds på Azure-avbildningar:

|Utgivare|Namn|Versioner|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Gäst-konfigurationen stöds inte för närvarande på anpassade avbildningar.

### <a name="unsupported-client-types"></a>Klientappar typer

I följande tabell visas operativsystem som inte stöds:

|Operativsystem|Anteckningar|
|-|-|
|Windows-klient | Klientoperativsystem (till exempel Windows 7 och Windows 10) stöds inte.
|Windows Server 2016 Nano Server | Stöds ej.|

## <a name="guest-configuration-definition-requirements"></a>Definition av gäst konfigurationskrav

Varje audit köras av gäst-konfigurationen kräver två principdefinitioner, en **DeployIfNotExists** definition och en **granska** definition. Den **DeployIfNotExists** definition används för att förbereda den virtuella datorn med gästen Configuration-agenten och andra komponenter för att stödja den [verifieringsverktyg](#validation-tools).

Den **DeployIfNotExists** principdefinitionen kontrollerar och korrigerar följande objekt:

- Verifiera den virtuella datorn har tilldelats en konfiguration som ska utvärderas. Om ingen tilldelning finns för närvarande kan hämta tilldelningen och förbereda den virtuella datorn med:
  - Autentisering till den virtuella datorn med en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installera den senaste versionen av den **Microsoft.GuestConfiguration** tillägg
  - Installera [verifieringsverktyg](#validation-tools) och beroenden, om det behövs

Om den **DeployIfNotExists** tilldelningen är icke-kompatibla, en [reparation uppgift](../how-to/remediate-resources.md#create-a-remediation-task) kan användas.

När den **DeployIfNotExists** tilldelningen är kompatibel, den **Audit** principtilldelning använder lokal validering-verktyg för att avgöra om tilldelningen konfiguration är kompatibel eller icke-kompatibel.
Verktyget verifiering ger resultatet till gäst-konfiguration-klienten. Klienten vidarebefordrar resultaten till gäst-tillägg, vilket gör dem tillgängliga via resursprovidern gäst-konfiguration.

Azure Policy använder resursen gäst konfigurationstjänst **complianceStatus** egenskapen att rapportera kompatibilitet i den **efterlevnad** noden. Mer information finns i [komma kompatibilitetsdata](../how-to/getting-compliance-data.md).

> [!NOTE]
> För varje gäst-konfigurationsdefinition både den **DeployIfNotExists** och **Audit** principdefinitioner måste finnas.

Alla inbyggda principer för gästen konfiguration ingår i ett initiativ till gruppen definitioner för modulen tilldelningar. Inbyggt *[förhandsversion]: Granska säkerhetsinställningarna för lösenord i Linux och Windows-datorer* initiativ innehåller 18 principer. Det finns sex **DeployIfNotExists** och **Audit** policy definition par för Windows och tre par för Linux.
För varje den **DeployIfNotExists** [principdefinitionsregeln](definition-structure.md#policy-rule) begränsar de system som utvärderas.

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](definition-structure.md)
- Granska [förstå effekterna av princip](effects.md)
- Förstå hur du [skapa principer programmässigt](../how-to/programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](../how-to/getting-compliance-data.md)
- Lär dig hur du [åtgärda icke-kompatibla resurser](../how-to/remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/index.md)