---
title: "Använda Azure IoT Suite anslutna factory OPC publisher | Microsoft Docs"
description: Hur du skapar och distribuerar anslutna factory OPC publisher referensimplementering.
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
ms.date: 10/16/2017
ms.author: dobett
ms.openlocfilehash: fd823194f6e51600b9d4ca1daa053db837871fef
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>OPC utgivaren för Azure IoT kant

Den här artikeln beskriver hur du använder OPC Publisher referensimplementering. Referensimplementeringen visar hur du använder Azure IoT kant till:

- Ansluta till befintliga OPC UA-servrar.
- Publicera JSON-kodade telemetridata från dessa servrar i OPC UA *Pub/Sub* format med en JSON-nyttolast till Azure IoT Hub. Du kan använda någon av transportprotokoll som har stöd för Azure IoT kant.

Den här referensprogrammet innehåller:

- En OPC UA *klienten* för att ansluta till befintliga OPC UA servrar i nätverket.
- En OPC UA *server* lyssnar på port 62222 som du kan använda för att hantera vad som publiceras.

Programmet har implementerats med .NET Core och kan köras på de plattformar som stöds av .NET Core.

Utgivaren implementerar logik när den upprättar anslutningar till slutpunkter. Utgivaren förväntar slutpunkter att svara inom ett angivet antal keep alive-begäranden. Denna logik aktiverar utgivaren för att identifiera villkor, till exempel ett strömavbrott för en OPC UA-server.

För varje distinkta publishing intervall till en OPC UA-server skapas en separat prenumeration under vilken alla noder med publishing intervallet har uppdaterats.

Om du vill minska nätverksbelastningen stöder utgivaren massbearbetning av data som skickas till IoT-hubb. En batch skickas till IoT-hubb endast när den konfigurerade paketstorleken har uppnåtts.

Det här programmet använder grunden OPC OPC UA referens stack och därför licensiering begränsningar gäller. Besök http://opcfoundation.github.io/UA-.NETStandardLibrary/ för OPC UA dokumentation och licensvillkoren.

Du hittar källkoden OPC utgivare i den [OPC utgivaren för Azure IoT kant](https://github.com/Azure/iot-edge-opc-publisher) GitHub-lagringsplatsen.

## <a name="prerequisites"></a>Krav

För att skapa programmet måste den [.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk) för ditt operativsystem.

## <a name="build-the-application"></a>Skapa programmet

### <a name="as-native-windows-application"></a>Som interna Windows-program

Öppna den `OpcPublisher.sln` projekt med Visual Studio 2017 och skapa lösningen genom att träffa F7.

### <a name="as-docker-container"></a>Som dockerbehållare

För att bygga program som en Windows-dockerbehållare använder den `Dockerfile.Windows` konfigurationsfilen.

För att bygga program som en Linux-dockerbehållare använder den `Dockerfile` konfigurationsfilen.

Från databasen på utvecklingsdatorn roten, skriver du följande kommando i konsolfönstret:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

Den `-f` för `docker build` är valfritt och standardvärdet är att använda den `Dockerfile` konfigurationsfilen.

Docker kan du skapa direkt från en git-lagringsplats. Du kan skapa en Linux dockerbehållare med följande kommando:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Konfigurera OPC UA noder att publicera

Skapa en JSON-formaterad konfigurationsfil för att konfigurera vilka OPC UA noder ska ha sina värden publicerats till Azure IoT Hub. Standardnamnet för den här konfigurationsfilen är `publishednodes.json`. Programmet uppdateras och sparar den här konfigurationsfilen när den använder OPC UA servermetoder **PublishNode** eller **UnpublishNode**.

Syntaxen för konfigurationsfilen är följande:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Köra programmet

### <a name="command-line-options"></a>Kommandoradsalternativ

Om du vill se hur programmet använder den `--help` kommandoradsalternativ. I följande exempel visar strukturen för ett kommando:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname`är namnet på programmet OPC UA ska användas. Den här parametern krävs. Programnamnet används också för att registrera utgivaren i enhetsregistret IoT-hubb.

`IoT Hubconnectionstring`är anslutningssträngen för IoT-hubb ägare. Den här parametern är valfri.

Stöd för följande alternativ:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

Du kan använda följande miljövariabler för att styra programmet:
- `_HUB_CS`: Anger anslutningssträngen för IoT-hubb ägare
- `_GW_LOGP`: Anger namn på loggfilen för att använda
- `_TPC_SP`: Anger sökvägen för lagring av certifikat från betrodda stationer
- `_GW_PNFP`: Anger filnamnet för publishing konfigurationsfilen

Kommandoradsargument före miljövariabelinställningar.

Normalt kan du ange anslutningssträngen för IoT-hubb ägare endast på den första starten av programmet. Anslutningssträngen krypteras och lagras i certifikatarkivet för den plattformen.

Anslutningssträngen är på efterföljande anrop, läsa från plattformens certifikatarkiv och återanvänds. Om du anger anslutningssträngen vid varje start tas enhet i enhetsregistret IoT-hubb bort och återskapas varje gång.

### <a name="native-on-windows"></a>Inbyggt i Windows

Om du vill köra programmet internt i Windows, öppna den `OpcPublisher.sln` projektet med Visual Studio 2017, skapa lösningen och publicerar den. Du kan starta programmet i den **målkatalogen** du har publicerat med:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Använda en egen inbyggd behållare

Om du vill köra programmet i en egen inbyggd behållare, skapa och starta din egen behållare:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Använda en behållare från hub.docker.com

Det finns en fördefinierad behållare på DockerHub. Starta den kör du följande kommando:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Viktigt när du använder en behållare

#### <a name="access-to-the-publisher-opc-ua-server"></a>Åtkomst till utgivaren OPC UA-servern

Publisher OPC UA servern som standard lyssnar på port 62222. Om du vill exponera inkommande porten i en behållare, måste du använda den `docker run` alternativet `-p`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Aktivera intercontainer namnmatchning

Om du vill aktivera namnmatchning från i behållaren till andra behållare, måste du:

- Skapa en användardefinierad docker bridge nätverk.
- Anslut behållaren till nätverk med den `--network`alternativet.
- Tilldela behållaren ett namn som använder den `--name` alternativet.

I följande exempel visas dessa konfigurationsalternativ:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Behållaren kan nås av andra behållare via nätverket med namnet `publisher`.

#### <a name="assign-a-hostname"></a>Tilldela ett värdnamn

Värdnamnet för datorn som kör på generering av certifikat och slutpunkten används. Docker väljer ett slumpmässigt värdnamn om du inte anger en med den `-h` alternativet. Här ett exempel för att ange interna värdnamnet för behållaren `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Med hjälp av bind monterar (delade filsystem)

I vissa scenarier som du vill läsa konfigurationsinformation från eller skriva loggfiler till platser på värden i stället för med filsystemet behållare. Om du vill konfigurera den här funktionen använder den `-v` möjlighet att `docker run` i bindning mount-läge. Exempel:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Store för X509 certifikat

Lagra X509 certifikat fungerar inte med bind monteringar eftersom behörigheterna för sökvägen till arkivet måste vara `rw` för ägare. I stället måste du använda den `-v` möjlighet att `docker run` i volym-läge.

## <a name="debug-the-application"></a>Felsöka programmet

### <a name="native-on-windows"></a>Inbyggt i Windows

Öppna den `OpcPublisher.sln` projektet med Visual Studio 2017 och starta appen genom att träffa F5-felsökning.

### <a name="in-a-docker-container"></a>I en dockerbehållare

Visual Studio-2017 stöder felsökning program i en dockerbehållare med hjälp av `docker-compose`. Den här metoden tillåter dock inte att du överföra kommandoradsparametrar.

Ett alternativ som alternativet som har stöd för VS2017 är att felsöka över `ssh`. Du kan använda docker build-konfigurationsfilen `Dockerfile.ssh` i roten av databasen för att skapa en behållare för SSH aktiverad:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Nu kan du starta behållare för att felsöka utgivaren:

```cmd/sh
docker run -it publisherssh
```

I behållaren måste du starta den **ssh** daemon manuellt:

```cmd/sh
service ssh start
```

Nu kan du skapa en **ssh** session som användaren `root` med lösenord `Passw0rd`.

För att förbereda för att felsöka program i behållaren, utför följande åtgärder:

1. På en värd-sida och skapa en `launch.json` fil:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Skapa projektet och publicera den till en katalog som du väljer.

1. Använd ett verktyg som `WinSCP` att kopiera de publicerade filerna till katalogen `/root/publisher` i behållaren. Om du väljer att använda en annan katalog, uppdatera det `cdw` egenskap i den `launch.json` filen.

Du kan nu starta felsökning med hjälp av följande kommando i Kommandotolken Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Nästa steg

Ett förslag nästa steg är att lära dig hur du [distribuera en gateway på Windows- eller Linux för anslutna factory förkonfigurerade lösningen](iot-suite-connected-factory-gateway-deployment.md).