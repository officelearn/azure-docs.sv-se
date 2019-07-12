---
title: Kör OPC Publisher - Azure | Microsoft Docs
description: Köra OPC Publisher
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3b386171afc7916e5e803c39a9c7b3520752e6fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603755"
---
# <a name="run-opc-publisher"></a>Köra OPC Publisher

Den här artikeln beskriver hur du kör ad debug OPC Publisher. Den behandlar också prestanda-och minne.

## <a name="command-line-options"></a>Kommandoradsalternativ

Programanvändning visas med hjälp av den `--help` kommandoradsalternativet på följande sätt:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
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
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Normalt kan du ange anslutningssträngen för IoT Hub-ägare, endast på den första körningen av programmet. Anslutningssträngen är krypteras och lagras i certifikatarkivet plattform. På senare körs läser programmet anslutningssträngen från certifikatarkivet. Om du anger anslutningssträngen på varje tas enhet som har skapats för programmet i IoT Hub-enhetsregistret bort och återskapas.

## <a name="run-natively-on-windows"></a>Köras internt i Windows

Öppna den **opcpublisher.sln** projektet med Visual Studio, bygg lösningen och publicera den. Du kan starta programmet i den **målkatalogen** du publicerat till enligt följande:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Använda en egen inbyggda behållaren

Skapa en egen behållare och starta den på följande sätt:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Använda en behållare från Microsoft Container Registry

Det finns en fördefinierade behållare i Microsoft Container Registry. Starta på följande sätt:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontrollera [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) att se de operativsystem som stöds och processorarkitekturer. Om din OS- och CPU-arkitektur stöds väljer automatiskt rätt fönster i Docker.

## <a name="run-as-an-azure-iot-edge-module"></a>Kör som en Azure IoT Edge-modul

OPC Publisher är redo att användas som en [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) modulen. När du använder OPC Publisher som IoT Edge-modul, det går endast att protokoll är **Amqp_Tcp_Only** och **Mqtt_Tcp_Only**.

Om du vill lägga till OPC Publisher som modulen för din IoT Edge-distribution, går du till din IoT Hub-inställningar i Azure portal och utför följande steg:

1. Gå till **IoT Edge** och skapa eller välj IoT Edge-enhet.
1. Välj **Ange moduler**.
1. Välj **lägga till** under **distribution moduler** och sedan **IoT Edge-modul**.
1. I den **namn** anger **publisher**.
1. I den **URI för avbildning** anger `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Du kan hitta de tillgängliga taggarna på [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Klistra in följande JSON till det **behållare skapa alternativ** fält:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Den här konfigurationen konfigurerar IoT Edge för att starta en behållare som kallas **publisher** med hjälp av OPC Publisher-avbildning. Värdnamnet för behållarens system är inställd på **publisher**. OPC Publisher kallas med följande prepareenroll: `--aa`. Med det här alternativet godkänner OPC Publisher certifikat av OPC UA-servrar som den ansluter till. Du kan använda alla OPC Publisher-kommandoradsalternativ. Den enda begränsningen är storleken på den **behållare skapa alternativ** stöds av IoT Edge.

1. Lämna de andra inställningarna som de är och välj **Spara**.
1. Om du vill bearbeta utdata från OPC Publisher lokalt med en annan IoT Edge-modul, går du tillbaka till den **ange moduler** sidan. Gå till den **ange vägar** fliken och lägga till en ny väg som ser ut som följande JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. I den **ange moduler** väljer **nästa**, tills du når den sista sidan i konfigurationen.
1. Välj **skicka** att skicka konfigurationen till IoT Edge.
1. När du har börjat IoT Edge på din edge-enhet och docker-behållaren **publisher** körs, du kan ta en titt loggutdata för OPC Publisher antingen med hjälp av `docker logs -f publisher` eller genom att kontrollera loggfilen. I exemplet ovan är loggfilen över `d:\iiotegde\publisher-publisher.log`. Du kan också använda den [iot-edge-opc-utgivaren-diagnostikverktyget för](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Gör konfigurationsfilerna som är tillgänglig på värden

Använd följande för att göra IoT Edge Modulkonfiguration filer tillgänglig i värdfilsystemet **behållare skapa alternativ**. I följande exempel har en distribution med Linux-behållare för Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Med dessa alternativ OPC Publisher läser in noder som det ska publicera från filen `./pn.json` och behållarens arbetskatalogen är inställt på `/appdata` vid start. Med de här inställningarna OPC Publisher läser in filen `/appdata/pn.json` från behållaren att hämta dess konfiguration. Utan den `--pf` alternativet OPC Publisher försöker läsa standardkonfigurationsfilen `./publishednodes.json`.

Loggfil, med standardnamnet `publisher-publisher.log`, skrivs till `/appdata` och `CertificateStores` katalogen skapas också i den här katalogen.

Frigör alla filerna i värdfilsystemet genom kräver konfigurationen av behållaren en bindning mount-volym. Den `d://iiotedge:/appdata` bind mappar katalogen `/appdata`, vilket är den aktuella arbetskatalogen på Start, till värdkatalogen `d://iiotedge`. Ingen fildata sparas utan det här alternativet när behållaren startas nästa gång.

Om du kör Windows-behållare och syntaxen för den `Binds` parametern är olika. Vid start av behållare, arbetskatalogen är `c:\appdata`. Att placera konfigurationsfilen i katalogen `d:\iiotedge`på värden, anger du följande mappning i den `HostConfig` avsnittet:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Om du kör Linux-behållare på Linux, syntaxen för den `Binds` parametern skiljer sig igen. Vid start av behållare, arbetskatalogen är `/appdata`. Att placera konfigurationsfilen i katalogen `/iiotedge` på värden, anger du följande mappning i den `HostConfig` avsnittet:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Att tänka på när du använder en behållare

Följande avsnitt listar några saker att tänka på när du använder en behållare:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Åtkomst till OPC Publisher OPC UA-servern

OPC Publisher OPC UA-servern lyssnar på port 62222 som standard. Om du vill exponera den här inkommande port i en behållare, använder du följande kommando:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Aktivera intercontainer namnmatchning

Skapa en användare för att aktivera namnmatchning från inom behållaren till andra behållare, definiera docker nätverksbrygga och ansluter behållaren till det här nätverket med hjälp av den `--network` alternativet. Också tilldela behållaren ett namn med hjälp av den `--name` enligt följande:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Behållaren är nu kan nås med hjälp av namnet `publisher` av andra behållare i samma nätverk.

### <a name="access-other-systems-from-within-the-container"></a>Få åtkomst till andra system från i behållaren

Andra behållare kan nås på de parametrar som beskrivs i föregående avsnitt. Om operativsystemet där Docker är värd för DNS aktiverad, fungerar åtkomst till alla system som är kända för att DNS.

I nätverk som använder NetBIOS-namnmatchning, ge åtkomst till andra system genom att starta behållaren med den `--add-host` alternativet. Det här alternativet läggs en post effektivt till behållarens värdfilen:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Tilldela ett värdnamn

OPC Publisher använder värdnamnet för datorn som den körs på generering av certifikat- och slutpunkt. Docker väljer ett slumpmässigt värdnamn om inte som angetts av den `-h` alternativet. I följande exempel visas hur du ställer in interna värdnamnet för behållaren `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Använd bindning monterar (delade filsystem)

Du kan välja filsystem som ska lagra konfigurationsinformation och loggfiler för värden i stället för med filsystemet behållare. Om du vill konfigurera det här alternativet använder de `-v` möjlighet att `docker run` i bind mount-läge.

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509-certifikat

OPC UA använder X.509-certifikat för att autentisera OPC UA-klient och server när de upprättar en anslutning och för att kryptera kommunikationen mellan dem. OPC Publisher använder certifikatarkiv som underhålls av OPC UA-stacken för att hantera alla certifikat. På Start kontrollerar OPC Publisher om det finns ett certifikat för sig själva. Om det finns inget certifikat i certifikatarkivet och en är inte en överförs i på kommandoraden, OPC Publisher skapar ett självsignerat certifikat. Mer information finns i den **InitApplicationSecurityAsync** -metod i `OpcApplicationConfigurationSecurity.cs`.

Självsignerade certifikat ger inte någon säkerhet, eftersom de inte är signerade av en betrodd Certifikatutfärdare.

OPC Publisher innehåller kommandoradsalternativ för att:

- Hämta CSR-information för det aktuella programmet certifikatet som används av OPC Publisher.
- Etablera OPC Publisher med en Certifikatutfärdare självsignerade certifikat.
- Etablera OPC Publisher med ett nytt nyckelpar och matchar CA självsignerade certifikat.
- Lägga till certifikat till en betrodd peer eller certifikatarkivet för betrodda utfärdare.
- Lägg till en lista över återkallade certifikat.
- Ta bort ett certifikat från betrodda peer eller certifikatarkivet för betrodda utfärdare.

Alla dessa alternativ kan du skicka parametrar med hjälp av filer eller base64-kodade strängar.

Typ av store för alla certifikatarkiv är filsystem, som du kan ändra med hjälp av kommandoradsalternativ. Eftersom behållaren inte ger beständig lagring i filsystemet, måste du välja en annan lagringstyp. Använda Docker `-v` att behålla certifikatet lagras i värdfilsystemet eller på en Docker-volym. Om du använder en Docker-volym kan skicka du in certifikat med hjälp av base64-kodade strängar.

För körningsmiljön påverkar hur certifikat sparas. Undvik att och skapa nya certifikatarkiven varje gång du kör programmet:

- Körs internt på Windows kan du inte använda ett certifikatarkiv för program av typen `Directory` eftersom åtkomst till den privata nyckeln misslyckas. I så fall använda alternativet `--at X509Store`.
- Kör som Linux docker-behållare, kan du mappa med certifikatarkiv till värdfilsystemet med alternativet Kör docker `-v <hostdirectory>:/appdata`. Det här alternativet gör certifikatet permanent från programmet körningarna.
- Som körs som Linux docker-behållare och du vill använda en X509 lagra för programcertifikatet, använder du docker alternativet kör `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` och alternativet för program `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Prestanda och överväganden

Det här avsnittet beskrivs alternativ för hantering av minne och prestanda:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Kommandoradsparametrar som styr prestanda och minne

När du kör OPC Publisher, måste du känna till dina prestandakrav och minne på värden.

Minne och prestanda är beroende av varandra och beror på konfigurationen av hur många noder du konfigurera om du vill publicera. Kontrollera att följande parametrar uppfyller dina krav:

- IoT Hub skickar intervall: `--si`
- IoT Hub meddelandestorlek (standard `1`): `--ms`
- Övervakade objekt i kö kapacitet: `--mq`

Den `--mq` parametern styr den övre gränsen för kapaciteten för den interna kön som buffrar alla OPC-nod värdet ändringsmeddelanden. Om OPC Publisher inte kan skicka meddelanden till IoT Hub för snabb, den här kön buffertar meddelanden. Parametern anger antalet meddelanden som kan buffras. Om du ser hur många objekt i den här kön ökar i din testkörningar, sedan bör för att undvika att förlora meddelanden du:

- Minska IoT Hub skicka intervall
- Öka storleken för IoT Hub-meddelande

Den `--si` parametern tvingar OPC Publisher att skicka meddelanden till IoT Hub på det angivna intervallet. OPC Publisher skickar ett meddelande när meddelandestorleken anges av den `--ms` parametern har uppnåtts eller så snart som intervallet som anges av den `--si` parametern har nåtts. Om du vill inaktivera alternativet storlek, använda `--ms 0`. I det här fallet använder OPC Publisher största möjliga IoT Hub meddelandestorlek på 256 kB till batchdata.

Den `--ms` parametern kan du batch-meddelanden som skickas till IoT Hub. Det protokoll som du använder avgör om arbetet med att skicka ett meddelande till IoT Hub är hög jämfört med den faktiska tiden för att skicka nyttolasten. Om ditt scenario ger svarstiden när data matas in i IoT Hub, konfigurera OPC Publisher om du vill använda den största meddelandestorleken på 256 kB.

Innan du använder OPC Publisher i produktionsscenarier bör du testa prestanda och användning av minne under produktionsvillkor. Du kan använda den `--di` parametern för att ange intervallet, i sekunder som OPC Publisher skriver diagnostisk information.

### <a name="test-measurements"></a>Testa mätning av faktisk användning

Följande exempel diagnostiken visa mått med olika värden för `--si` och `--ms` parametrar som publicerar 500 noder med en OPC-Publiceringsintervall på 1 sekund.  Testet används en felsökningsversion för OPC Publisher i Windows 10 internt för 120 sekunder. IoT Hub-protokollet har standard MQTT-protokollet.

#### <a name="default-configuration---si-10---ms-262144"></a>Standardkonfiguration (--si 10 – ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Standardkonfigurationen skickar data till IoT Hub var tionde sekund eller när 256 kB data är tillgängliga för IoT Hub för att mata in. Den här konfigurationen lägger till en begränsad fördröjning på cirka 10 sekunder, men har lägsta sannolikheten för att förlora data på grund av den stora meddelandestorleken. Diagnostik-utdata visar att det finns inga förlorade uppdateringar för OPC-nod: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Konstant skicka intervall (--si 1--ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

När meddelandestorleken är inställd på 0 sedan OPC Publisher internt slår ihop data med hjälp av den största stödda IoT Hub meddelandestorleken, vilket är 256 kB. Diagnostikdata visar genomsnittlig meddelandestorlek är 115,019 byte. I den här konfigurationen OPC Publisher förlora inte några OPC-nod värdet uppdateringar och jämfört med standard har kortare svarstider.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Skicka varje OPC-nod värdet uppdatering (--si 0 – ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Den här konfigurationen skickar för varje värde för OPC-nod ändra ett meddelande till IoT Hub. Diagnostik visa genomsnittlig meddelandestorlek är 234 byte, vilket är små. Fördelen med den här konfigurationen är att OPC Publisher inte läggs alla svarstid. Antal uppdateringar värdet förloras OPC-nod (`monitored item notifications enqueue failure: 44624`) är hög, vilket gör den här konfigurationen inte lämpliga för scenarier med stora mängder telemetri som ska publiceras.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximal batchbearbetning (--si 0 – ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Den här konfigurationen slår ihop så många OPC värdet uppdateringar som möjligt. IoT Hub maximala meddelandestorleken är 256 kB, som konfigureras här. Det finns inget skicka intervall begärd, vilket innebär att mängden data för IoT Hub för att mata in anger svarstiden. Den här konfigurationen har minst sannolikheten för att förlora alla värden för OPC-nod och lämpar sig för att publicera ett stort antal noder. När du använder den här konfigurationen kan du kontrollera att ditt scenario inte har villkor där långa svarstider introduceras om meddelandestorlek på 256 kB inte nås.

## <a name="debug-the-application"></a>Felsök programmet

Om du vill felsöka programmet, öppna den **opcpublisher.sln** lösningen med Visual Studio och använder Visual Studio felsökningsverktyg.

Om du behöver åtkomst till OPC UA-servern i OPC-utgivaren kan du kontrollera att brandväggen tillåter åtkomst till den port som servern lyssnar på. Standardporten är: 62222.

## <a name="control-the-application-remotely"></a>Fjärrstyra programmet

Konfigurera noderna som publiceras kan göras med hjälp av IoT Hub direkta metoder.

OPC Publisher implementerar några ytterligare IoT Hub direkt metodanrop att läsa:

- Allmän information.
- Diagnostisk information på OPC-sessioner, prenumerationer och övervakade objekt.
- Diagnostisk information på meddelanden från IoT Hub och händelser.
- Start-loggen.
- De senaste 100 raderna i loggen.
- Stänga av programmet.

Följande GitHub-databaser innehåller verktyg för att [konfigurera noderna för att publicera](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) och [läsa diagnostikinformation](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Båda verktygen finns också som behållare i Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Använd exempel OPC UA-servern

Om du inte har en verklig OPC UA-server kan du använda den [exempel OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) att komma igång. Det här exemplet PLC finns också på Docker Hub.

Den implementerar ett antal taggar som genererar slumpmässiga data och taggar med avvikelser. Du kan utöka exemplet om du vill simulera ytterligare taggvärden.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kör OPC Publisher rekommenderade nästa steg är att lära dig om [OPC-Twin](overview-opc-twin.md) och [OPC Vault](overview-opc-vault.md).
