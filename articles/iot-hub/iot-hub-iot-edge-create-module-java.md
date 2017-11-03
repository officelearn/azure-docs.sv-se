---
title: Skapa ett Azure IoT-Edge-modulen med Java | Microsoft Docs
description: "Den här kursen visar hur du skriver en TIVERA data konverteraren modul med de senaste Azure IoT kant Maven-paket."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: eddeb5cc13aac7ab33305adcd266465a5b143462
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Skapa ett Azure IoT-Edge-modulen med Java

Den här kursen visar hur en kan skapa en modul för Azure IoT gränsen i Java.

I den här självstudiekursen kommer vi att gå igenom miljökonfiguration och hur du skriver en [TIVERA](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) data konverteraren modul med de senaste Azure IoT kant Maven-paket.

## <a name="prerequisites"></a>Krav

I det här avsnittet ska du konfigurera din miljö för utveckling av IoT kant modulen. Det gäller både *64-bitars Windows* och *64-bitars Linux (8 Ubuntu/Debian)* operativsystem.

Följande programvara krävs:

* [Git klienten](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Öppna ett kommandoradsfönster terminal och klona lagringsplatsen för följande:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Övergripande arkitektur

Azure IoT kant-plattformen kraftigt antar den [Von Neumann arkitektur](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Vilket innebär att hela kant för Azure IoT-arkitekturen är ett system som bearbetar indata och utdata; och att varje enskild modul är en liten input-output-undersystemet. I den här självstudiekursen kommer vi introducerar följande två moduler:

1. En modul som tar emot en simulerad [TIVERA](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) signalerar och konverterar den till en formaterad [JSON](https://en.wikipedia.org/wiki/JSON) meddelande.
2. En modul som skriver ut den mottagna [JSON](https://en.wikipedia.org/wiki/JSON) meddelande.

Följande bild visar den vanliga slutpunkt till slutpunkt-dataflöde för det här projektet:

![Dataflöde mellan tre moduler](media/iot-hub-iot-edge-create-module/dataflow.png "indata: simulerade TIVERA modulen. Processor: Konverterare modulen. Utdata: Skrivare modul")

## <a name="understanding-the-code"></a>Förstå koden

### <a name="maven-project-structure"></a>Struktur för maven-projekt

Eftersom Azure IoT kant paket baseras på Maven, vi behöver du skapa en typisk Maven-projekt-struktur som innehåller en `pom.xml` fil.

POM ärver från den `com.microsoft.azure.gateway.gateway-module-base` paket som deklarerar alla beroenden som krävs av en modul-projekt som innehåller runtime-binärfiler, filsökväg för gateway-konfiguration och körningsbeteende. Detta innebär att vi sparar mycket tid och eliminera behovet av att skriva och skriva om hundratals rader med kod upprepade gånger.

Vi behöver uppdatera filen pom.xml genom att fastställa den nödvändiga beroenden/plugin-program och namnet på konfigurationsfilen som ska användas av vårt modulen som visas i följande kodavsnitt.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Grundläggande förståelse för en gräns för Azure IoT-modul

Du kan hantera en Azure IoT kant-modul som en data-processor vars uppgift är att: indata, behandlas och resultat.

Indata kan vara data från maskinvara (till exempel en rörelsedetektor), ett meddelande från andra moduler eller något annat (till exempel ett slumptal som genererats med jämna mellanrum av en timer).

Utdata liknar indata, den kan utlösa beteendet för maskinvara (till exempel blinkande LED), ett meddelande till andra moduler eller något annat (till exempel utskrift i konsolen).

Moduler som kommunicerar med varandra med hjälp av `com.microsoft.azure.gateway.messaging.Message` klass. Den **innehåll** av en `Message` är en bytematris som klarar av som representerar alla typer av data som du vill. **Egenskaper för** är också tillgängliga i den `Message` och är helt enkelt en string-string-mappning. Du kan tänka dig **egenskaper** som rubriker i en HTTPS-begäran eller metadata för en fil.

För att utveckla en gräns för Azure IoT-modul i Java, måste du skapa en ny modul-klass som ärver från `com.microsoft.azure.gateway.core.GatewayModule` och genomföra de nödvändiga abstrakta metoderna `receive()` och `destroy()`. Du kan nu även välja att implementera den valfria `start()` eller `create()` samt metoder. Följande kodavsnitt visar hur du kommer igång redigering av en Azure IoT kant-modul.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Konverteraren modul

| Indata                    | Processor                              | Resultat                 | Källfilen            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Meddelande för temperatur-data | Analysera och skapa ett nytt JSON-meddelande | Strukturen JSON-meddelande | `ConverterModule.java` |

Denna modul är en typisk Azure IoT kant-modul. Den godkänner temperatur meddelanden från andra moduler (maskinvara modulen, eller i det här fallet våra simulerade Bell-modulen); och normaliserar temperatur meddelande i en strukturerad JSON-meddelandet (inklusive bifoga meddelande-ID, ställer in egenskapen för om vi behöver utlösa aviseringen temperatur och så vidare).

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Modul för skrivare

| Indata                          | Processor | Resultat                     | Källfilen          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Alla meddelanden från andra moduler | Saknas       | Logga meddelandet-konsolen | `PrinterModule.java` |

Det här är en enkel, självförklarande, modul som matar ut de mottagna meddelandena till fönstret terminal.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Azure IoT kant-konfiguration

Det sista steget innan du kör modulerna som är att konfigurera Azure IoT kant och upprätta anslutningar mellan moduler.

Vi måste först deklarera våra Java-inläsaren (eftersom Azure IoT kant stöder inläsare med olika språk) som kan refereras till av dess `name` i avsnitten efteråt.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

När vi har deklarerat våra inläsare, behöver vi även deklarera samt våra moduler. Precis som deklarerar inläsare, de kan också refereras av deras `name` attribut. När du deklarerar en modul, vi behöver ange ska det använda inläsaren (som ska vara det som vi definierade innan) och startpunkten (ska vara normaliserade klassnamnet för våra modulen) för varje modul. Den `simulated_device` modul är en inbyggd modul som ingår i Azure IoT kant core runtime-paketet. Du bör alltid innehålla `args` i JSON-filen även om det är `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

I slutet av konfigurationen upprätta vi anslutningar. Varje anslutning uttrycks av `source` och `sink`. De bör både referera en fördefinierad modul. Det utgående meddelandet i `source` modulen ska vidarebefordras till indata för `sink` modul.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Kör moduler

Använd `mvn package` att skapa allt i den `target/` mapp. `mvn clean package`rekommenderas för Rensa.

Använd `mvn exec:exec` att köra Azure IoT kant och du bör se temperatur data och alla egenskaper som skrivs ut på konsolen till en fast kostnad.

Om du vill avsluta programmet trycker du på `<Enter>` nyckel.

> [!IMPORTANT]
> Det rekommenderas inte att använda Ctrl + C för att avsluta IoT Edge gateway-programmet. Eftersom detta kan orsaka processen att avbrytas onormalt.

