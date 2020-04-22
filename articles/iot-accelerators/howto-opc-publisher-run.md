---
title: Kör OPC Publisher – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du kör och felsöker OPC Publisher. Den tar också upp prestanda och minne överväganden.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: c664d4859a306387b4eafa2f19ab5877ccf6eb1b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686964"
---
# <a name="run-opc-publisher"></a>Köra OPC Publisher

I den här artikeln beskrivs hur du kör annonsfelsök OPC Publisher. Den tar också upp prestanda och minne överväganden.

## <a name="command-line-options"></a>Kommandoradsalternativ

Programanvändning visas med `--help` kommandoradsalternativet enligt följande:

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

Vanligtvis anger du anslutningssträngen för IoT Hub-ägare endast vid den första körningen av programmet. Anslutningssträngen krypteras och lagras i plattformscertifikatarkivet. Vid senare körningar läser programmet av anslutningssträngen från certifikatarkivet. Om du anger anslutningssträngen på varje körning tas enheten som har skapats för programmet i IoT Hub-enhetsregistret bort och återskapas.

## <a name="run-natively-on-windows"></a>Köra internt i Windows

Öppna **opcpublisher.sln-projektet** med Visual Studio, bygg lösningen och publicera det. Du kan starta programmet i katalogen **Mål** som du publicerade på följande sätt:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Använda en egenbyggd behållare

Bygg din egen behållare och starta den på följande sätt:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Använda en behållare från Microsoft Container Registry

Det finns en fördefinierad behållare i Microsoft Container Registry. Starta den på följande sätt:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontrollera [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) för att se operativsystem och processorarkitekturer som stöds. Om os- och CPU-arkitekturen stöds väljer Docker automatiskt rätt behållare.

## <a name="run-as-an-azure-iot-edge-module"></a>Köra som en Azure IoT Edge-modul

OPC Publisher är klar att användas som en [Azure IoT](https://docs.microsoft.com/azure/iot-edge) Edge-modul. När du använder OPC Publisher som IoT Edge-modul är de enda transportprotokoll som stöds **Amqp_Tcp_Only** och **Mqtt_Tcp_Only**.

Om du vill lägga till OPC Publisher som modul i IoT Edge-distributionen går du till inställningarna för IoT Hub i Azure-portalen och utför följande steg:

1. Gå till **IoT Edge** och skapa eller välj din IoT Edge-enhet.
1. Välj **Ange moduler**.
1. Välj **Lägg till** under **distributionsmoduler** och sedan **IoT Edge Module**.
1. Ange **utgivare**i fältet **Namn** .
1. I fältet **Bild-URI** anger du`mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Du hittar de tillgängliga taggarna på [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Klistra in följande JSON i fältet **Alternativ för att skapa behållare:**

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Den här konfigurationen konfigurerar IoT Edge för att starta en behållare som kallas **utgivare** med OPC Publisher-avbildningen. Värdnamnet för behållarens system är inställt **på utgivare**. OPC Publisher anropas med följande kommandoradsargument: `--aa`. Med det här alternativet litar OPC Publisher på certifikaten för de OPC UA-servrar som den ansluter till. Du kan använda alla kommandoradsalternativ för OPC Publisher. Den enda begränsningen är storleken på **alternativen för att skapa behållare** som stöds av IoT Edge.

1. Lämna de andra inställningarna som de är och välj **Spara**.
1. Om du vill bearbeta utdata för OPC Publisher lokalt med en annan IoT Edge-modul går du tillbaka till sidan **Ange moduler.** Gå sedan till fliken **Ange rutter** och lägg till en ny rutt som ser ut som följande JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Tillbaka på sidan **Ange moduler** väljer du **Nästa**tills du når den sista sidan i konfigurationen.
1. Välj **Skicka om** du vill skicka konfigurationen till IoT Edge.
1. När du har startat IoT Edge på edge-enheten och docker-containerutgivaren körs kan du `docker logs -f publisher` kolla in loggutdata för OPC Publisher antingen genom att använda eller genom att kontrollera loggfilen. **publisher** I föregående exempel är loggfilen `d:\iiotegde\publisher-publisher.log`ovanför . Du kan också använda [iot-edge-opc-publisher-diagnostics-verktyget](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Göra konfigurationsfilerna tillgängliga på värden

Om du vill göra konfigurationsfilerna för IoT Edge-modulen tillgängliga i värdfilsystemet använder du följande **alternativ för att skapa behållare**. Följande exempel är en distribution med Linux Containers för Windows:

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

Med dessa alternativ läser OPC Publisher de noder `./pn.json` som den ska publicera från `/appdata` filen och behållarens arbetskatalog är inställd på vid start. Med dessa inställningar läser OPC `/appdata/pn.json` Publisher filen från behållaren för att få dess konfiguration. Utan `--pf` alternativet försöker OPC Publisher läsa standardkonfigurationsfilen `./publishednodes.json`.

Loggfilen, med standardnamnet, `publisher-publisher.log`skrivs `/appdata` till `CertificateStores` och katalogen skapas också i den här katalogen.

För att göra alla dessa filer tillgängliga i värdfilsystemet kräver behållarkonfigurationen en bindmonteringsvolym. Bindningen `d://iiotedge:/appdata` mappar `/appdata`katalogen , som är den aktuella arbetskatalogen vid containerstart, till värdkatalogen `d://iiotedge`. Utan det här alternativet sparas inga fildata när behållaren startar nästa gång.

Om du kör Windows-behållare är syntaxen för parametern `Binds` annorlunda. Vid start av behållaren `c:\appdata`är arbetskatalogen . Om du vill placera `d:\iiotedge`konfigurationsfilen i katalogen på `HostConfig` värden anger du följande mappning i avsnittet:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Om du kör Linux-behållare på Linux `Binds` är syntaxen för parametern återigen annorlunda. Vid start av behållaren `/appdata`är arbetskatalogen . Om du vill placera `/iiotedge` konfigurationsfilen i katalogen på `HostConfig` värden anger du följande mappning i avsnittet:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Överväganden när du använder en behållare

I följande avsnitt visas några saker att tänka på när du använder en behållare:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Tillgång till OPC Publisher OPC UA-servern

Som standard lyssnar OPC Publisher OPC UA-servern på port 62222. Om du vill visa den här inkommande porten i en behållare använder du följande kommando:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Aktivera namnmatchning mellan dem

Om du vill aktivera namnmatchning från behållaren till andra behållare skapar du ett användardefinierande dockerbryggnät och ansluter behållaren till det här nätverket med `--network` hjälp av alternativet . Tilldela även behållaren ett `--name` namn med alternativet på följande sätt:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Behållaren kan nu nås `publisher` med namnet av andra behållare i samma nätverk.

### <a name="access-other-systems-from-within-the-container"></a>Få tillgång till andra system inifrån behållaren

Andra behållare kan nås med hjälp av de parametrar som beskrivs i föregående avsnitt. Om operativsystemet som Docker finns på är DNS-aktiverat fungerar dns-åtkomsten till alla system som är kända för DNS.

I nätverk som använder NetBIOS-namnmatchning aktiverar du åtkomst `--add-host` till andra system genom att starta behållaren med alternativet. Det här alternativet lägger effektivt till en post i behållarens värdfil:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Tilldela ett värdnamn

OPC Publisher använder värdnamnet för den dator som körs på för certifikat- och slutpunktsgenerering. Docker väljer ett slumpmässigt värdnamn om ett `-h` inte anges av alternativet. I följande exempel visas hur du ställer in `publisher`behållarens interna värdnamn till :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Använd bindningsfästen (delat filsystem)

I stället för att använda behållarfilsystemet kan du välja värdfilsystemet för att lagra konfigurationsinformation och loggfiler. Om du vill konfigurera `-v` det `docker run` här alternativet använder du alternativet i bindmonteringsläge.

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509 certifikat

OPC UA använder X.509-certifikat för att autentisera OPC UA-klienten och servern när de upprättar en anslutning och för att kryptera kommunikationen mellan dem. OPC Publisher använder certifikatarkiv som underhålls av OPC UA-stacken för att hantera alla certifikat. Vid start kontrollerar OPC Publisher om det finns ett certifikat för sig själv. Om det inte finns något certifikat i certifikatarkivet och ett inte skickas in på kommandoraden, skapar OPC Publisher ett självsignerat certifikat. Mer information finns i metoden **InitApplicationSecurityAsync** i `OpcApplicationConfigurationSecurity.cs`.

Självsignerade certifikat ger ingen säkerhet eftersom de inte är signerade av en betrodd certifikatutfärdare.

OPC Publisher innehåller kommandoradsalternativ för att:

- Hämta CSR-information för det aktuella programcertifikatet som används av OPC Publisher.
- Etablera OPC Publisher med ett certifikatutfärdarsignerat certifikat.
- Etablera OPC Publisher med ett nytt nyckelpar och matchande certifikatutfärdare signerat certifikat.
- Lägg till certifikat i ett betrott peer- eller betrott certifikatarcertifikatarkiv.
- Lägg till en lista över återkallade system.
- Ta bort ett certifikat från certifikatarkivet för betrodda peer- eller betrodda utfärdare.

Alla dessa alternativ kan du skicka in parametrar med hjälp av filer eller base64 kodade strängar.

Standardarkivtypen för alla certifikatarkiv är filsystemet, som du kan ändra med kommandoradsalternativ. Eftersom behållaren inte tillhandahåller beständig lagring i filsystemet måste du välja en annan lagringstyp. Använd alternativet `-v` Docker för att bevara certifikatarkiven i värdfilsystemet eller på en Docker-volym. Om du använder en Docker-volym kan du skicka in certifikat med bas64-kodade strängar.

Körningsmiljön påverkar hur certifikat sparas. Undvik att skapa nya certifikatarkiv varje gång du kör programmet:

- När du kör internt i Windows kan du inte `Directory` använda ett programcertifikatarkiv av typen eftersom åtkomsten till den privata nyckeln misslyckas. I så fall använder `--at X509Store`du alternativet .
- Kör som Linux docker-behållare kan du mappa certifikatarkiven `-v <hostdirectory>:/appdata`till värdfilsystemet med dockerkörningsalternativet . Det här alternativet gör certifikatet beständigt över programkörningar.
- Kör som Linux docker-behållare och du vill använda ett X509-arkiv `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` för programcertifikatet, använd dockerkörningsalternativet och programalternativet`--at X509Store`

## <a name="performance-and-memory-considerations"></a>Prestanda- och minnesöverväganden

I det här avsnittet beskrivs alternativ för att hantera minne och prestanda:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Kommandoradsparametrar för att styra prestanda och minne

När du kör OPC Publisher måste du vara medveten om dina prestandakrav och de minnesresurser som är tillgängliga på värden.

Minne och prestanda är beroende av varandra och båda beror på konfigurationen av hur många noder du konfigurerar för att publicera. Se till att följande parametrar uppfyller dina krav:

- IoT Hub skickar intervall:`--si`
- Meddelandestorlek för IoT `1`Hub (standard):`--ms`
- Övervakad objekt kö kapacitet:`--mq`

Parametern `--mq` styr den övre gränsen för kapaciteten för den interna kön, som buffrar alla OPC-nodvärdesändringsmeddelanden. Om OPC Publisher inte kan skicka meddelanden till IoT Hub tillräckligt snabbt buffrar den här kön meddelandena. Parametern anger antalet meddelanden som kan buffras. Om du ser hur många objekt i den här kön ökar i testkörningarna bör du undvika att förlora meddelanden:

- Minska skickaintervallet för IoT Hub
- Öka meddelandestorleken för IoT Hub

Parametern `--si` tvingar OPC Publisher att skicka meddelanden till IoT Hub med det angivna intervallet. OPC Publisher skickar ett meddelande så snart den `--ms` meddelandestorlek som anges av parametern har `--si` uppnåtts, eller så snart det intervall som anges av parametern har uppnåtts. Om du vill inaktivera `--ms 0`alternativet meddelandestorlek använder du . I det här fallet använder OPC Publisher största möjliga IoT Hub-meddelandestorlek på 256 kB för att batchdata.

Med `--ms` parametern kan du batchmeddelanden som skickas till IoT Hub. Protokollet du använder avgör om omkostnaderna för att skicka ett meddelande till IoT Hub är hög jämfört med den faktiska tiden för att skicka nyttolasten. Om ditt scenario tillåter svarstid när data används av IoT Hub konfigurerar du OPC Publisher så att den största meddelandestorleken på 256 kB används.

Innan du använder OPC Publisher i produktionsscenarier testar du prestanda- och minnesanvändningen under produktionsförhållanden. Du kan `--di` använda parametern för att ange intervallet, i sekunder, som OPC Publisher skriver diagnostikinformation.

### <a name="test-measurements"></a>Provningsmätningar

Följande exempeldiagnostik visar mätningar med `--si` olika `--ms` värden för och parametrar som publicerar 500 noder med ett OPC-publiceringsintervall på 1 sekund.  Testet använde en OPC Publisher-felsökningsversion som bygger på Windows 10 inbyggt i 120 sekunder. IoT Hub-protokollet var standard-MQTT-protokollet.

#### <a name="default-configuration---si-10---ms-262144"></a>Standardkonfiguration (--si 10 --ms 262144)

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

Standardkonfigurationen skickar data till IoT Hub var 10:e sekund, eller när 256 kB data är tillgängliga för IoT Hub att äta. Den här konfigurationen lägger till en måttlig svarstid på cirka 10 sekunder, men har lägst sannolikhet att förlora data på grund av den stora meddelandestorleken. Diagnostikutdata visar att det inte finns några `monitored item notifications enqueue failure: 0`förlorade OPC-noduppdateringar: .

#### <a name="constant-send-interval---si-1---ms-0"></a>Konstant sändningsintervall (--si 1 --ms 0)

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

När meddelandestorleken är inställd på 0 batchar OPC Publisher internt data med den största IoT Hub-meddelandestorleken som stöds, vilket är 256 kB. Diagnostikutgången visar att den genomsnittliga meddelandestorleken är 115 019 byte. I den här konfigurationen förlorar OPC Publisher inga OPC-nodvärdesuppdateringar och jämfört med standardvärdet har lägre svarstid.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Skicka varje OPC-nodvärdesuppdatering (--si 0 --ms 0)

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

Den här konfigurationen skickar för varje OPC-nodvärde ändra ett meddelande till IoT Hub. Diagnostiken visar att den genomsnittliga meddelandestorleken är 234 byte, vilket är litet. Fördelen med den här konfigurationen är att OPC Publisher inte lägger till någon svarstid. Antalet förlorade OPC-nodvärdesuppdateringar (`monitored item notifications enqueue failure: 44624`) är högt, vilket gör den här konfigurationen olämplig för scenarier med stora volymer telemetri som ska publiceras.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximal batching (--si 0 --ms 262144)

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

Den här konfigurationen batchar så många OPC-nodvärdesuppdateringar som möjligt. Den maximala IoT Hub-meddelandestorleken är 256 kB, som är konfigurerad här. Inget sändningsintervall begärs, vilket innebär att mängden data för IoT Hub som ska intas avgör svarstiden. Den här konfigurationen har minst sannolikhet att förlora opc-nodvärden och är lämplig för publicering av ett stort antal noder. När du använder den här konfigurationen ska du se till att ditt scenario inte har några villkor där hög latens introduceras om meddelandestorleken på 256 kB inte uppnås.

## <a name="debug-the-application"></a>Felsök programmet

Om du vill felsöka programmet öppnar du lösningsfilen **opcpublisher.sln** med Visual Studio och använder visual studio-felsökningsverktygen.

Om du behöver komma åt OPC UA-servern i OPC Publisher kontrollerar du att brandväggen ger åtkomst till den port som servern lyssnar på. Standardporten är: 62222.

## <a name="control-the-application-remotely"></a>Fjärrstyr programmet

Konfigurera noderna att publicera kan göras med hjälp av IoT Hub direkt metoder.

OPC Publisher implementerar några ytterligare IoT Hub direkt metodanrop att läsa:

- Allmän information.
- Diagnostikinformation om OPC-sessioner, prenumerationer och övervakade objekt.
- Diagnostikinformation om IoT Hub-meddelanden och händelser.
- Startloggen.
- De sista 100 raderna i loggen.
- Stäng av programmet.

Följande GitHub-databaser innehåller verktyg för att [konfigurera noderna för att publicera](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) och läsa [diagnostikinformationen](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Båda verktygen finns även som behållare i Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Använda en exempel-OPC UA-server

Om du inte har en riktig OPC UA-server kan du använda [exemplet OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) för att komma igång. Detta exempel PLC finns även på Docker Hub.

Den implementerar ett antal taggar, som genererar slumpmässiga data och taggar med avvikelser. Du kan utöka exemplet om du behöver simulera ytterligare taggvärden.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kör OPC Publisher är de rekommenderade nästa stegen att lära dig mer om [OPC Twin](overview-opc-twin.md) och [OPC Vault](overview-opc-vault.md).
