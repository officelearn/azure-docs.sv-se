---
title: "Kom igång med Service Fabric och Azure CLI 2.0"
description: "Så använder du Service Fabric-kommandomodulen i Azure CLI version 2.0 för bland annat anslutning till kluster och hantering av program"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: sv-se
ms.lasthandoff: 07/01/2017

---
# <a name="service-fabric-and-azure-cli-20"></a>Service Fabric och Azure CLI 2.0

Nya Azure CLI 2.0 innehåller kommandon för hantering av Service Fabric-kluster. Den här dokumentationen innehåller steg som gör att du kommer igång med Azure CLI.

## <a name="install-azure-cli-20"></a>Installera Azure CLI 2.0

Azure CLI innehåller nu kommandon för att interagera med och hantera Service Fabric-kluster. Du kan få senaste Azure CLI genom att följa [standardinstallationsprocessen](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Mer information finns i [dokumentationen till Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview)

## <a name="cli-syntax"></a>CLI-syntax

Alla Azure Service Fabric-kommandon har prefixet `az sf` i Azure CLI. Om du vill ha mer information om tillgängliga kommandon kan du köra `az sf -h`. Du kan också köra `az sf <command> -h` om du vill ha detaljerad information om ett visst kommando.

Azure Service Fabric-kommandon i CLI följer ett namnvigningsmönster

```azurecli
az sf <object> <action>
```

Här är `<object>` målet för `<action>`.

## <a name="selecting-a-cluster"></a>Välja ett kluster

Innan du kan utföra några åtgärder måste du välja ett kluster att ansluta till. Här är ett kodfragment för anslutning till ett oskyddat kluster.

> [!WARNING]
> Använd inte oskyddade Service Fabric-kluster i produktionsmiljöer

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

Klusterslutpunkten måste ha prefixet `http` eller `https` och bör inkludera porten för HTTP-gatewayen. Den här porten och adressen är samma som webbadressen för Service Fabric Explorer.

För kluster som skyddas med certifikat kan du använda antingen okrypterade `pem`-filer, eller `crt`- och `key`-filer.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Mer information finns i [dokumentet om att ansluta till skyddade kluster](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> Kommandot select utför inga begäranden innan resultatet returneras. Om du vill verifiera att ett kluster har angetts korrekt ska du köra ett kommando som `az sf cluster health`, och kontrollera att kommandot inte returnerar några fel.

## <a name="performing-basic-operations"></a>Utföra grundläggande åtgärder

Klustrets anslutningsinformation bevaras mellan olika Azure CLI-sessioner. När du har valt ett Service Fabric-kluster kan du köra alla Service Fabric-kommandon.

Om du till exempel vill se hälsotillståndet för Service Fabric-klustret kör du följande kommando

```azurecli
az sf cluster health
```

Kommandot resulterar i följande utdata, förutsatt att JSON-utdata har angetts i konfigurationen av Azure CLI

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-faq"></a>Tips samt vanliga frågor och svar

Här är lite information som kan vara användbar om du får problem med Service Fabric-kommandon i Azure CLI

### <a name="converting-a-certificate-from-pfx-to-pem"></a>Konvertera ett certifikat från PFX till PEM

Azure CLI har stöd för certifikat på klientsidan i form av PEM-filer (filtillägg `.pem`). Om du använder PFX-filer från Windows måste du konvertera dessa till PEM-format. Om du vill konvertera en PFX-fil till en PEM-fil använder du följande kommando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Läs mer i [dokumentationen för OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html).

### <a name="connection-issues"></a>Anslutningsproblem

När du utför åtgärder kan du stöta på följande fel:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

I det här fallet dubbelkollar du att den angivna klusterslutpunkten kan nås och lyssnar. Kontrollera också att gränssnittet för Service Fabric Explorer kan nås på denna värd och port. Använd `az sf cluster select` till att uppdatera slutpunkten.

### <a name="getting-detailed-logs"></a>Hämta detaljerade loggar

När du felsöker eller rapporterar ett problem är det bra att ta med detaljerade loggar. Azure CLI innehåller en global `--debug`-flagga som ökar loggarnas detaljnivå.

### <a name="command-help-and-syntax"></a>Hjälp och syntax för kommandon

Service Fabric-kommandona följer samma regler som i Azure CLI. Ange flaggan `-h` om du vill ha hjälp om ett visst kommando eller flera kommandon. Exempel:

```azurecli
az sf application -h
```

eller

```azurecli
az sf application create -h
```

