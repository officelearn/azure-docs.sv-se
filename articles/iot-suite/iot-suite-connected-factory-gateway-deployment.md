---
title: Distribuera dina anslutna factory gateway - Azure | Microsoft Docs
description: "Så här distribuerar du en gateway på Windows eller Linux för att ansluta till till anslutna fabriken förkonfigurerade lösningen."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: dobett
ms.openlocfilehash: c9854c68a95c2c1cc584503eb2f0b0dba6091016
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>Distribuera en gräns-gatewayen för anslutna factory förkonfigurerade lösningen på Windows- eller Linux

Du behöver två programvarukomponenter att distribuera en gräns-gatewayen för det *anslutna factory* förkonfigurerade lösningen:

- Den *OPC Proxy* upprättar en anslutning till ansluten factory. OPC-Proxy väntar sedan kommando- och meddelanden från integrerade OPC webbläsaren som körs i den anslutna factory lösning portalen.

- Den *OPC Publisher* ansluter till befintliga lokala OPC UA servrar och vidarebefordrar telemetri från dem till anslutna factory. Du kan ansluta en OPC klassiska enheter med hjälp av den [OPC klassiska kortet för OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Båda komponenterna är öppen källkod och är tillgängliga som källa på GitHub och Docker behållare på DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC-Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC-Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Du behöver inte en offentlig IP-adress eller öppna ingående portar i brandväggen för gateway för någon komponent. OPC-Proxy och OPC utgivaren komponenter kan bara använda utgående port 443.

Stegen i den här artikeln visar hur du distribuerar en gräns-gatewayen med Docker i Windows eller Linux. Gatewayen kan ansluta till den anslutna factory förkonfigurerade lösningen. Du kan också använda komponenterna utan anslutna fabriken.

> [!NOTE]
> Du kan använda båda komponenterna som moduler i [Azure IoT kant](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Välj en gateway-enhet

Om du ännu inte har en gateway-enhet, rekommenderar Microsoft att du köper en kommersiell gateway från en av sina partner. En lista över alla gatewayenheter som är kompatibla med anslutna factory-lösningen finns i [Azure IoT-enhet katalogen](https://catalog.azureiotsuite.com/?q=opc). Följ instruktionerna som medföljer enheten för att konfigurera gatewayen.

Alternativt, Använd följande instruktioner för att manuellt konfigurera en befintlig gatewayenhet.

## <a name="install-and-configure-docker"></a>Installera och konfigurera Docker

Installera [Docker för Windows](https://www.docker.com/docker-windows) på Windows-baserade gateway-enheten eller Använd en package manager för att installera docker på Linux-baserade gateway-enhet.

Välj en enhet på värddatorn delar med Docker under installationen av Docker för Windows. Följande skärmbild visar delning av **D** enhet på Windows-systemet att ge åtkomst till värdenheten från inuti en dockerbehållare:

![Installera Docker för Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Du kan också utföra det här steget när du har installerat docker från den **inställningar** dialogrutan. Högerklicka på den **Docker** ikonen i systemfältet i Windows och välj **inställningar**.

Om du använder Linux, krävs ingen ytterligare konfiguration för att ge åtkomst till filsystemet.

Skapa en mapp på den enhet som du har delat med Docker i Windows, kan skapa en mapp under roten filsystemet på Linux. Den här genomgången refererar till den här mappen som `<SharedFolder>`.

När du refererar till den `<SharedFolder>` i en Docker-kommandot måste du använda den korrekta syntaxen för ditt operativsystem. Här är två exempel, en för Windows och en för Linux:

- Om du använder mappen `D:\shared` i Windows som din `<SharedFolder>`, kommandosyntaxen Docker är `//d/shared`.

- Om du använder mappen `/shared` på Linux som din `<SharedFolder>`, kommandosyntaxen Docker är `/shared`.

Mer information finns i [använda volymer](https://docs.docker.com/engine/admin/volumes/volumes/) docker modulreferens.

## <a name="configure-the-opc-components"></a>Konfigurera OPC-komponenter

Utför följande steg för att förbereda din miljö innan du installerar OPC-komponenter:

1. Du behöver för att slutföra gateway-distribution av **iothubowner** anslutningssträngen för IoT-hubben i distributionen anslutna fabriken. I den [Azure-portalen](http://portal.azure.com/), navigera till din IoT-hubb i resursgruppen som skapas när du har distribuerat anslutna factory-lösning. Klicka på **principer för delad åtkomst** att få åtkomst till den **iothubowner** anslutningssträngen:

    ![Hitta anslutningssträngen IoT-hubb](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    Kopiera den **sträng-primära anslutningsnyckel** värde.

1. För att tillåta kommunikation mellan docker-behållare, behöver du en användardefinierad brygga för nätverk. Om du vill skapa en brygga för nätverk för din behållare, kör du följande kommandon i Kommandotolken:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Att verifiera den **iot_edge** bridge nätverket har skapats genom att köra följande kommando:

    ```cmd/sh
    docker network ls
    ```

    Din **iot_edge** bridge nätverket ingår i listan över nätverk.

Om du vill köra OPC utgivaren kör du följande kommando i Kommandotolken:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- Den [OPC Publisher GitHub](https://github.com/Azure/iot-edge-opc-publisher) och [docker kör referens](https://docs.docker.com/engine/reference/run/) ger mer information om:

  - Docker kommandoradsalternativ angivet innan behållarens namn (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - Innebörden av OPC Publisher kommandoradsparametrarna anges efter behållarens namn (`microsoft/iot-edge-opc-publisher:2.1.3`).

- Den `<IoTHubOwnerConnectionString>` är den **iothubowner** delad åtkomst princip anslutningssträngen från Azure-portalen. Du har kopierat den här anslutningssträngen i föregående steg. Du behöver bara den här anslutningssträngen för den första körningen av OPC utgivare. På efterföljande körs bör du utelämna den eftersom det utgör en säkerhetsrisk.

- Den `<SharedFolder>` du använder och dess syntaxen beskrivs i avsnittet [installera och konfigurera Docker](#install-and-configure-docker). OPC utgivaren använder den `<SharedFolder>` att läsa konfigurationsfilen OPC utgivare, skriva loggfilen och kontrollera både dessa filer var tillgängliga utanför behållaren.

- OPC Publisher läser konfigurationen från den **publishednodes.json** fil som du bör placera i den `<SharedFolder>/docker` mapp. Den här konfigurationsfilen definierar vilka OPC UA noden data på en server som anges av OPC UA OPC utgivaren ska prenumerera på.

- När servern OPC UA meddelar OPC utgivaren av en ändring av data, skickas det nya värdet till IoT-hubb. Beroende på inställningarna för batching ackumuleras OPC utgivaren först data innan informationen skickas till IoT-hubb i en batch.

- Den fullständiga syntaxen för den **publishednodes.json** filen beskrivs på den [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) sidan på GitHub.

    Följande utdrag visar ett enkelt exempel på en **publishednodes.json** fil. Det här exemplet illustrerar hur du publicerar den **CurrentTime** värde från en OPC UA server med värdnamn **win10pc**:

    ```json
    [
      {
        "EndpointUrl": "opc.tcp://win10pc:48010",
        "OpcNodes": [
          {
            "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
          }
        ]
      }
    ]
    ```

    I den **publishednodes.json** -fil, den OPC UA-server har angetts av slutpunkts-URL. Om du anger värdnamnet som använder en etikett för värdnamn (exempelvis **win10pc**) som i föregående exempel i stället för en IP-adress adressmatchning nätverket i behållaren måste kunna matcha värdnamn etiketten till en IP-adress.

- Docker har inte stöd för NetBIOS-namnmatchning, endast DNS-namnmatchning. Om du inte har en DNS-server i nätverket kan använda du den lösning som visas i exemplet ovan kommandoraden. I föregående exempel kommandoraden används den `--add-host` parametern för att lägga till en post i värdfilen behållare. Den här posten gör hostname-sökning för den angivna `<OpcServerHostname>`, lösa den angivna IP-adressen `<IpAddressOfOpcServerHostname>`.

- OPC UA använder X.509-certifikat för autentisering och kryptering. Du måste placera OPC certifikat på OPC UA servern du ansluter till, så litar OPC utgivare. Certifikatarkivet OPC Publisher finns i den `<SharedFolder>/CertificateStores` mapp. Du kan hitta OPC Publisher certifikatet i den `trusted/certs` mapp i den `CertificateStores` mapp.

  Stegen för att konfigurera servern OPC UA beror på den enhet du använder. de här stegen är vanligtvis dokumenterade i Användarhandbok OPC UA-server.

Om du vill installera OPC-Proxy, kör du följande kommando i Kommandotolken:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Du behöver bara köra installationen en gång på ett system.

Använd följande kommando för att köra OPC-Proxy:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

OPC Proxy sparar anslutningssträngen under installationen. På efterföljande körs bör du utelämnar anslutningssträngen eftersom det utgör en säkerhetsrisk.

## <a name="enable-your-gateway"></a>Aktivera din gateway

Utför följande steg om du vill aktivera din gateway i anslutna factory förkonfigurerade lösningen:

1. När båda komponenterna körs, bläddra till den **ansluta din egen OPC UA Server** sidan på anslutna factory lösning portalen. Den här sidan är endast tillgänglig för administratörer i lösningen. Ange utgivare slutpunkts-URL (opc.tcp://publisher: 62222) och klicka på **Anslut**.

1. Upprätta en förtroenderelation mellan anslutna factory portal och OPC utgivare. När en certifikatvarning, klickar du på **Fortsätt**. Därefter kan du se ett fel att OPC utgivaren inte har förtroende för UA webbklienten. Lös problemet genom att kopiera den **UA webbklienten** certifikat från den `<SharedFolder>/CertificateStores/rejected/certs` mappen till den `<SharedFolder>/CertificateStores/trusted/certs` mappen på gateway. Du behöver inte starta om gatewayen.

Du kan ansluta till gatewayen nu från molnet och du är redo att lägga till OPC UA servrar i lösningen.

## <a name="add-your-own-opc-ua-servers"></a>Lägga till egna OPC UA-servrar

Att lägga till dina egna OPC UA servrar anslutna fabriken förkonfigurerade lösningen:

1. Bläddra till den **ansluta OPC UA servern** sidan på anslutna factory lösning portalen. Följ samma steg som i föregående avsnitt för att upprätta en förtroenderelation mellan anslutna factory-portalen och OPC UA-servern.

    ![Lösningsportal](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. Bläddra i OPC UA noder trädet serverns OPC UA, högerklicka på OPC-noder som du vill skicka till anslutna factory och välj **publicera**.

1. Telemetri nu flödar från gateway-enheten. Du kan visa telemetri i den **Factory platser** visa anslutna factory portalens under **nya Factory**.

## <a name="next-steps"></a>Nästa steg

Mer information om arkitekturen för den anslutna factory förkonfigurerade lösningen finns [anslutna factory förkonfigurerade lösningen genomgången](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough).

Lär dig mer om den [OPC Publisher referensimplementering](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).