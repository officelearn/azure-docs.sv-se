---
title: Azure Connected Machine Agent CLI-gränssnitt
description: Referensdokumentation för Azure Connected Machine-agenten CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513203"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure Connected Machine Agent CLI-gränssnitt

Verktyget `Azcmagent` (Azure Connected Machine Agent) används för att konfigurera och felsöka en icke-azure-datoranslutning till Azure.

Agenten i sig är en `himdsd` demon process som kallas `himds` på Linux, och en Windows-tjänst som kallas på Windows.

Vid normal `azcmagent connect` användning används för att upprätta en anslutning `azcmagent disconnect` mellan den här datorn och Azure, och om du bestämmer dig för att du inte längre vill ha anslutningen. De andra kommandona är för felsökning eller andra specialfall.

## <a name="options"></a>Alternativ

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>SE ÄVEN.

* [azcmagent connect](#azcmagent-connect) - Ansluter den här datorn till Azure
* [azcmagent disconnect](#azcmagent-disconnect) - Kopplar från den här datorn från Azure
* [azcmagent återanslut](#azcmagent-reconnect) - Återansluter den här datorn till Azure
* [azcmagent show](#azcmagent-show) - Hämtar datorns metadata och agentstatus. Detta är främst användbart för felsökning.
* [azcmagent version](#azcmagent-version) - Visa Hybrid Management Agent version

## <a name="azcmagent-connect"></a>azcmagent ansluta

Ansluter den här datorn till Azure

### <a name="synopsis"></a>Synopsis

Skapar en resurs i Azure som representerar den här datorn.

Detta använder de autentiseringsalternativ som tillhandahålls för att skapa en resurs i Azure Resource Manager som representerar den här datorn. Resursen finns i den begärda prenumerations- och resursgruppen och data om datorn lagras i Azure-regionen som anges av platsparametern.
Standardresursnamnet är den här datorns värdnamn om det inte åsidosätts.

Ett certifikat som motsvarar den systemtilldelade identiteten på den här datorn hämtas och lagras sedan lokalt. När det här steget är klart börjar **Azure Connected Machine Metadata** Service och Gästkonfigurationsagenten synkronisera med Azure-molnet.

Autentiseringsalternativ:

* Åtkomsttoken`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Tjänstens huvud-ID och hemliga`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Logga in på enheten (interaktivt)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Syntax

```none
azcmagent connect [flags]
```

### <a name="options"></a>Alternativ

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent koppla

Kopplar från den här datorn från Azure

### <a name="synopsis"></a>Synopsis

Tar bort resursen i Azure som representerar den här servern.

Det här kommandot använder de autentiseringsalternativ som anges för att ta bort Azure Resource Manager-resursen som representerar den här datorn. Efter denna punkt kommer **Azure Connected Machine Metadata Service** och Guest Configuration Agent att kopplas från. Det här kommandot stoppar eller tar inte bort tjänsterna: ta bort paketet för att göra det.

Det här kommandot kräver högre behörighet än rollen "Azure Connected Machine Onboarding".

När en dator har `azcmagent connect`kopplats `azcmagent reconnect` från använder du , inte om du vill skapa en ny resurs för den i Azure.

Autentiseringsalternativ:

* Åtkomsttoken`azcmagent disconnect --access-token <>`
* Tjänstens huvud-ID och hemliga`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logga in interaktiv enhet`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Syntax

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Alternativ

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent återansluta

Återansluter den här datorn till Azure

### <a name="synopsis"></a>Synopsis

Återanslut dator med ogiltiga autentiseringsuppgifter till Azure.

Om en dator redan har en resurs i Azure men inte kan autentisera till den, kan den återanslutas med det här kommandot. Detta är möjligt om en dator har stängts av tillräckligt länge för att certifikatet ska upphöra att gälla (minst 45 dagar).

Om en dator kopplades från med `azcmagent disconnect`använder du `azcmagent connect` i stället.

Det här kommandot använder de autentiseringsalternativ som anges för att hämta nya autentiseringsuppgifter som motsvarar Azure Resource Manager-resursen som representerar den här datorn.

Det här kommandot kräver högre behörighet än azure **connected machine onboarding-rollen.**

Autentiseringsalternativ

* Åtkomsttoken`azcmagent reconnect --access-token <>`
* Tjänstens huvud-ID och hemliga`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logga in interaktiv enhet`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Syntax

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Alternativ

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent visa

Hämtar datorns metadata och agentstatus. Detta är främst användbart för felsökning.

### <a name="synopsis"></a>Synopsis

Hämtar datorns metadata och agentstatus. Detta är främst användbart för felsökning.


### <a name="syntax"></a>Syntax

```
azcmagent show [flags]
```

### <a name="options"></a>Alternativ

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent version

Visa hybridhanteringsagentversionen

### <a name="synopsis"></a>Synopsis

Visa hybridhanteringsagentversionen

### <a name="syntax"></a>Syntax

```none
azcmagent version [flags]
```

### <a name="options"></a>Alternativ

```none
  -h, --help   help for version
```
