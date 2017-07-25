---
title: "Kom igång med Azure Service Fabric och Azure CLI 2.0"
description: "Lär dig hur du använder kommandomodulen Azure Service Fabric i Azure CLI version 2.0. Lär dig hur du ansluter till ett kluster och hanterar program."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: sv-se
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Service Fabric och Azure CLI 2.0

Azure kommandoradsverktyget (Azure CLI) version 2.0 innehåller kommandon för att hjälpa dig att hantera Azure Service Fabric-kluster. Kom igång med Azure Service Fabric och Azure CLI.

## <a name="install-azure-cli-20"></a>Installera Azure CLI 2.0

Azure CLI 2.0 innehåller nu kommandon för att interagera med och hantera Service Fabric-kluster. För att få den senaste versionen av Azure CLI följer du standardanvisningarna för [Azure CLI 2.0-installation](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Mer information finns i [Översikt över Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Azure CLI-syntax

Alla Service Fabric-kommandon har prefixet `az sf` i Azure CLI. För allmän information om de kommandon som du kan använda, använd `az sf -h`. För hjälp med ett enda kommando, använd `az sf <command> -h`.

Service Fabric-kommandon i CLI följer detta namngivningsmönster:

```azurecli
az sf <object> <action>
```

`<object>` är målet för `<action>`.

## <a name="select-a-cluster"></a>Välj ett kluster

Innan du kan utföra några åtgärder måste du välja ett kluster att ansluta till. Följande kod är ett exempel. Koden ansluter till ett oskyddat kluster.

> [!WARNING]
> Använd inte oskyddade Service Fabric-kluster i produktionsmiljöer.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

Klusterslutpunkten måste föregås av `http` eller `https`. Det måste innehålla porten för HTTP-gateway. Den här porten och adressen är samma som webbadressen för Service Fabric Explorer.

Du kan antingen använda okrypterade .pem-filer, eller .crt- och .key-filer, för kluster som är säkrade med ett certifikat. Exempel:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Mer information finns i [Ansluta till ett Azure Service Fabric-kluster](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> Kommandot `select` inverkar inte på anrop innan den returnerar. Om du vill kontrollera att du har angett ett kluster korrekt använder du ett kommando som `az sf cluster health`. Kontrollera att kommandot inte returnerar några fel.

## <a name="basic-operations"></a>Grundläggande åtgärder

Klustrets anslutningsinformation bevaras mellan olika Azure CLI-sessioner. När du har valt ett Service Fabric-kluster kan du köra alla Service Fabric-kommandon på klustret.

Om du till exempel vill se hälsotillståndet för Service Fabric-klustret kör du följande kommando:

```azurecli
az sf cluster health
```

Kommandot resulterar i följande utdata (förutsatt att JSON-utdata har angetts i konfigurationen av Azure CLI):

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

## <a name="tips-and-troubleshooting"></a>Felsökning och tips

Följande information kan vara till hjälp om du stöter på problem när du använder Service Fabric-kommandon i Azure CLI.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Konvertera ett certifikat från PFX till PEM

Azure CLI har stöd för certifikat på klientsidan i form av PEM-filer (.pem-filtillägg). Om du använder PFX-filer från Windows måste du konvertera dessa certifikat till PEM-format. Om du vill konvertera en PFX-fil till en PEM-fil använder du följande kommando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Mer information finns i [OpenSSL-dokumentationen](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Anslutningsproblem

Vissa åtgärder kan generera följande meddelande:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Kontrollera att den angivna kluster-slutpunkten är tillgänglig och lyssnar. Kontrollera också att gränssnittet för Service Fabric Explorer kan nås på denna värd och port. Använd `az sf cluster select` till att uppdatera slutpunkten.

### <a name="detailed-logs"></a>Detaljerade loggar

Detaljerade loggar är ofta användbara när du felsöker eller rapporterar problem. Azure CLI innehåller en global `--debug`-flagga som ökar loggfilernas detaljnivå.

### <a name="command-help-and-syntax"></a>Hjälp och syntax för kommandon

Service Fabric-kommandona följer samma regler som i Azure CLI. Hjälp med ett visst kommando eller en grupp med kommandon, använder du flaggan `-h`:

```azurecli
az sf application -h
```

Här är ett annat exempel:

```azurecli
az sf application create -h
```

