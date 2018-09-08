---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: 6055b70c7df2704a334b7f14c9365863ddafbd5a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164562"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Spara filer i Azure Cloud Shell
Cloudshell använder Azure File storage för att spara filer mellan sessioner. På första start uppmanas Cloud Shell du att associera en ny eller befintlig filresurs för att spara filer mellan sessioner.

> [!NOTE]
> Dela samma filresurs Bash och PowerShell. Endast en filresurs kan associeras med automatisk montering i Cloud Shell.

## <a name="create-new-storage"></a>Skapa ny lagring

När du använder grundläggande inställningar och välj endast en prenumeration, skapar Cloud Shell tre resurser åt dig i den region som stöds som är närmast dig:
* Resursgrupp: `cloud-shell-storage-<region>`
* Lagringskonto: `cs<uniqueGuid>`
* Filresurs: `cs-<user>-<domain>-com-<uniqueGuid>`

![Inställning för Molnprenumeration](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Filresursen monterar som `clouddrive` i din `$Home` directory. Detta är en engångsåtgärd och filresursen monterar automatiskt i framtida sessioner. 

> [!NOTE]
> Av säkerhetsskäl bör varje användare tillhandahålla egna storage-konto.  För rollbaserad åtkomstkontroll (RBAC), måste användare har deltagarbehörighet eller ovan på lagringen konto nivå.

Filresursen innehåller också en 5 GB-avbildning som har skapats för dig som automatiskt kvarstår data i din `$Home` directory. Detta gäller både Bash och PowerShell.

## <a name="use-existing-resources"></a>Använda befintliga resurser

Du kan koppla befintliga resurser med hjälp av avancerade alternativet. När storage installationsprogrammet uppmaning visas väljer du **visa avancerade inställningar** att visa ytterligare alternativ. Alternativ fylls i automatiskt storage filter för lokalt redundant lagring (LRS), geo-redundant lagring (GRS) och konton för zonredundant lagring (ZRS). Gå [här vill veta mer](https://docs.microsoft.com/azure/storage/common/storage-redundancy#choosing-a-replication-option) om replikeringsalternativen för Azure Storage-konton.

![Inställningen i resursen](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

När du väljer en Cloud Shell-region måste du välja för att montera ett lagringskonto för säkerhetskopiering i den regionen också.

### <a name="supported-storage-regions"></a>Stöds storage-regioner
Associerade konton måste finnas i samma region som den Cloud Shell-dator som du montera dem till Azure-lagring. Du hittar din aktuella region som du kan köra `env` i Bash och leta upp variabeln `ACC_LOCATION`. Filresurser får en 5 GB-avbildning som skapats för dig att spara din `$Home` directory.

Cloud Shell-datorer finns i följande regioner:
|Område|Region|
|---|---|
|Nord- och Sydamerika|Östra USA, södra centrala USA, västra USA|
|Europa|Norra Europa, västra Europa|
|Asien och stillahavsområdet|Indien, centrala, sydöstra Asien|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Begränsa resurser får skapas med en princip för Azure-resurs
Storage-konton som du skapar i Cloud Shell är märkta med `ms-resource-usage:azure-cloud-shell`. Om du vill neka användare från att skapa storage-konton i Cloud Shell, skapar en [Azure resurspolicyn för taggar](../articles/azure-policy/json-samples.md) som utlöses av den här specifika taggen.
