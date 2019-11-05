---
title: Azure Connected Machine agent CLI-gränssnitt
description: Referens dokumentation för Azure Connected Machine agent CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513203"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure Connected Machine agent CLI-gränssnitt

Verktyget `Azcmagent` (Azure Connected Machine agent) används för att konfigurera och felsöka en anslutning som inte är en Azure-dator till Azure.

Själva agenten är en daemon-process som kallas `himdsd` på Linux, och en Windows-tjänst som kallas `himds` i Windows.

I normal användning används `azcmagent connect` för att upprätta en anslutning mellan den här datorn och Azure, och `azcmagent disconnect` om du inte längre vill ha den anslutningen. De andra kommandona är för fel sökning eller andra specialfall.

## <a name="options"></a>Alternativ

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>SE ÄVEN

* [azcmagent Connect](#azcmagent-connect) – ansluter den här datorn till Azure
* [azcmagent från koppling](#azcmagent-disconnect) – kopplar från den här datorn från Azure
* [azcmagent reconnect](#azcmagent-reconnect) – återansluter datorn till Azure
* [azcmagent show](#azcmagent-show) -hämtar maskin-metadata och agent status. Detta är främst användbart vid fel sökning.
* [azcmagent-version](#azcmagent-version) – Visa versionen för Hybrid Management Agent

## <a name="azcmagent-connect"></a>azcmagent ansluta

Ansluter den här datorn till Azure

### <a name="synopsis"></a>Sammanfattning

Skapar en resurs i Azure som representerar den här datorn.

Detta använder de autentiseringsalternativ som finns för att skapa en resurs i Azure Resource Manager som representerar den här datorn. Resursen finns i prenumerations-och resurs gruppen som begärts, och data om datorn lagras i den Azure-region som anges av parametern location.
Standard resurs namnet är värd namnet för den här datorn om den inte åsidosätts.

Ett certifikat som motsvarar den systemtilldelade identiteten för den här datorn laddas ned och lagras lokalt. När det här steget är klart slutförs tjänsten **Azure Connected Machine metadata** och konfigurations agenten för gäster börjar synkronisera med Azure-molnet.

Autentiserings alternativ:

* Åtkomsttoken `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Tjänstens huvud namns-ID och hemlighet `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Enhets inloggning (interaktiv) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent från koppling

Kopplar från den här datorn från Azure

### <a name="synopsis"></a>Sammanfattning

Tar bort resursen i Azure som representerar den här servern.

Det här kommandot använder de autentiseringsalternativ som finns för att ta bort den Azure Resource Manager resursen som representerar den här datorn. Efter den här punkten kommer **Azure Connected-datorn metadata service** och gäst konfigurations agenten att kopplas från. Det här kommandot stoppar eller tar inte bort tjänsterna: ta bort paketet för att göra det.

Det här kommandot kräver högre privilegier än rollen "Azure-ansluten dator onboarding".

När en dator har kopplats från använder du `azcmagent connect`, inte `azcmagent reconnect` om du vill skapa en ny resurs för den i Azure.

Autentiserings alternativ:

* Åtkomsttoken `azcmagent disconnect --access-token <>`
* Tjänstens huvud namns-ID och hemlighet `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logga in på interaktiv enhet `azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>azcmagent Återanslut

Återansluter datorn till Azure

### <a name="synopsis"></a>Sammanfattning

Återanslut datorn med ogiltiga autentiseringsuppgifter till Azure.

Om en dator redan har en resurs i Azure men inte kan autentisera till den, kan den återanslutas med det här kommandot. Detta är möjligt om en dator har varit inaktive rad tillräckligt lång för att certifikatet ska gå ut (minst 45 dagar).

Om en dator har kopplats från med `azcmagent disconnect`använder du `azcmagent connect` i stället.

Det här kommandot använder de autentiseringsalternativ som finns för att hämta nya autentiseringsuppgifter som motsvarar den Azure Resource Manager resursen som representerar den här datorn.

Det här kommandot kräver högre privilegier än den **Azure Connected Machine onboarding** -rollen.

Autentiseringsalternativ

* Åtkomsttoken `azcmagent reconnect --access-token <>`
* Tjänstens huvud namns-ID och hemlighet `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Logga in på interaktiv enhet `azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent Visa

Hämtar datorns metadata och agent status. Detta är främst användbart vid fel sökning.

### <a name="synopsis"></a>Sammanfattning

Hämtar datorns metadata och agent status. Detta är främst användbart vid fel sökning.


### <a name="syntax"></a>Syntax

```
azcmagent show [flags]
```

### <a name="options"></a>Alternativ

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent-version

Visa versionen för Hybrid Management Agent

### <a name="synopsis"></a>Sammanfattning

Visa versionen för Hybrid Management Agent

### <a name="syntax"></a>Syntax

```none
azcmagent version [flags]
```

### <a name="options"></a>Alternativ

```none
  -h, --help   help for version
```
