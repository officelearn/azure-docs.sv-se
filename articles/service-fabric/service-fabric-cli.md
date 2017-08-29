---
title: "Kom igång med Azure Service Fabric CLI (sfctl)"
description: "Lär dig hur du använder Azure Service Fabric CLI. Lär dig hur du ansluter till ett kluster och hanterar program."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: sv-se
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Azure Service Fabric-kommandorad

Azure Service Fabric CLI (sfctl) är ett kommandoradsverktyg för att interagera och hantera Azure Service Fabric-enheter. Sfctl kan användas med Windows- eller Linux-kluster. Sfctl fungerar på alla plattformar där python stöds.

## <a name="prerequisites"></a>Krav

Kontrollera att din miljö har både python och pip installerade före installationen. Mer information finns i [snabbstartsdokumentationen för pip](https://pip.pypa.io/en/latest/quickstart/) och den officiella [installationsdokumentationen för python](https://wiki.python.org/moin/BeginnersGuide/Download).

Både python 2.7 och 3.6 stöds, men du rekommenderas att använda python 3.6.

## <a name="install"></a>Installera

Azure Service Fabric CLI (sfctl) kommer som ett python-paket. Installera den senaste versionen genom att köra:

```bash
pip install sfctl
```

Efter installationen kan du köra `sfctl -h` för information om tillgängliga kommandon.

## <a name="cli-syntax"></a>CLI-syntax

Kommandon har alltid prefixet `sfctl`. För allmän information om alla kommandon som du kan använda, använd `sfctl -h`. För hjälp med ett enda kommando, använd `sfctl <command> -h`.

Kommandon följer en upprepningsbar struktur, där kommandots mål står före verbet eller åtgärden:

```azurecli
sfctl <object> <action>
```

I det här exemplet är `<object>` målet för `<action>`.

## <a name="select-a-cluster"></a>Välj ett kluster

Innan du kan utföra några åtgärder måste du välja ett kluster att ansluta till. Kör till exempel följande för att välja och ansluta till klustret med namnet `testcluster.com`.

> [!WARNING]
> Använd inte oskyddade Service Fabric-kluster i produktionsmiljöer.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

Klusterslutpunkten måste föregås av `http` eller `https`. Det måste innehålla porten för HTTP-gateway. Den här porten och adressen är samma som webbadressen för Service Fabric Explorer.

För kluster som skyddas med ett certifikat kan du ange ett PEM-kodat certifikat. Certifikatet kan anges som en enda fil eller ett par med certifikat och nyckel.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Mer information finns i [Ansluta till ett Azure Service Fabric-kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Grundläggande åtgärder

Klustrets anslutningsinformation bevaras mellan olika sfctl-sessioner. När du har valt ett Service Fabric-kluster kan du köra alla Service Fabric-kommandon på klustret.

Om du till exempel vill se hälsotillståndet för Service Fabric-klustret kör du följande kommando:

```azurecli
sfctl cluster health
```

Kommandot ger följande utdata:

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

Några förslag och tips för att lösa vanliga problem.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Konvertera ett certifikat från PFX till PEM

Service Fabric CLI har stöd för certifikat på klientsidan i form av PEM-filer (.pem-filtillägg). Om du använder PFX-filer från Windows måste du konvertera dessa certifikat till PEM-format. Om du vill konvertera en PFX-fil till en PEM-fil använder du följande kommando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Mer information finns i [OpenSSL-dokumentationen](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Anslutningsproblem

Vissa åtgärder kan generera följande meddelande:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Kontrollera att den angivna kluster-slutpunkten är tillgänglig och lyssnar. Kontrollera också att gränssnittet för Service Fabric Explorer kan nås på denna värd och port. Använd `sfctl cluster select` till att uppdatera slutpunkten.

### <a name="detailed-logs"></a>Detaljerade loggar

Detaljerade loggar är ofta användbara när du felsöker eller rapporterar problem. Det finns en global `--debug`-flagga som ökar loggfilernas detaljnivå.

### <a name="command-help-and-syntax"></a>Hjälp och syntax för kommandon

Hjälp med ett visst kommando eller en grupp med kommandon, använder du flaggan `-h`:

```azurecli
sfctl application -h
```

Ett annat exempel:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera ett program med Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Kom igång med Service Fabric på Linux](service-fabric-get-started-linux.md)

