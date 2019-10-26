---
title: Fel söknings guide för Azure våren Cloud | Microsoft Docs
description: Fel söknings guide för Azure våren Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 546c97421fdb3a581a22e34f6110986a1a0732b6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929152"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Fel söknings guide för vanliga problem

I den här artikeln beskrivs några vanliga problem och fel söknings steg för utvecklare som arbetar i Azure våren-molnet. Vi rekommenderar även att läsa vår [artikel om vanliga frågor och svar](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Tillgänglighets-, prestanda-och program problem

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Det går inte att starta programmet (till exempel slut punkten kan inte vara ansluten eller returnera 502 efter några återförsök)

Exportera loggarna till _Azure Log Analytics_. Tabellen för Spring-programloggar heter `AppPlatformLogsforSpring`. Om du vill veta mer kan du gå [till analysera loggar och mått med diagnostikinställningar](diagnostic-services.md)

Om du söker efter följande fel i loggarna visas ett av två sannolika problem:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Något av bönorna eller något av dess beroenden saknas.
* En av egenskaperna för bönor saknas eller är ogiltig. Du kommer troligen att se `java.lang.IllegalArgumentException` i det här fallet.

Tjänst bindningar kan också orsaka fel i program starten. Använd nyckelord som är relaterade till de bundna tjänsterna för att köra frågor mot loggarna.  Anta till exempel att ditt program har en bindning till en MySQL-instans inställd på lokal system tid. Om programmet inte startar kan du hitta följande fel i loggen:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Åtgärda felet genom att gå till `server parameters` av MySql-instansen och ändra `time_zone` från `SYSTEM` till `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mina program kraschar eller genererar ett oväntat fel

När du felsöker program krascher startar du genom att kontrol lera programmets körnings status och identifierings status. Gå till _app Management_ i Azure Portal för att se till att alla program _körs_ och _är igång._

* Om status är _igång_ men identifierings statusen inte är _upp_ [går det inte att registrera programmet](#my-application-cannot-be-registered).

* Om identifierings statusen är _upp_går du till _mått_ för att kontrol lera programmets hälso tillstånd. Kontrol lera följande mått:


  - `TomcatErrorCount` (_Tomcat. global. error_): alla våren Application-undantag kommer att räknas här. Om det här talet är stort går du till _Azure Log Analytics_ för att kontrol lera program loggarna.

  - `AppMemoryMax` (_JVM. Memory. Max_): den maximala mängden minne som är tillgängligt för programmet. Det kan vara odefinierat eller ändra över tid om det har definierats. Mängden använt och allokerat minne är alltid mindre än eller lika med max om den har definierats. Dock kan en minnesallokering misslyckas med `OutOfMemoryError` om den försöker öka det använda minnet så att använt minne > allokerat, även om använt <= max fortfarande gäller. I sådana fall kan du försöka öka den maximala heap-storleken via parametern `-Xmx`.

  - `AppMemoryUsed` (_JVM. Memory. används_): mängden minne i byte som för närvarande används av programmet. För ett Java-program med normal belastning bildar den här måttserien ett ”sågtandsmönster” där minnesanvändningen stadigt ökar och minskar i små steg och sedan minskas kraftigt och plötsligt. Detta mönster upprepas. Detta beror på skräp insamlingen i Java Virtual Machine, där samlings åtgärder representeras på "sawteeth".
    Det här måttet är viktigt för att identifiera minnes problem, t. ex.: * minnes explosion i det allra första början * överspännings minnes tilldelning för en angiven logisk sökväg * gradvis minnes läckor

  Mer information finns på [mått](spring-cloud-concept-metrics.md).

Besök [den här kom igång-artikeln](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) för att komma igång med _Azure Log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mitt program använder stora mängder CPU- eller minnesresurser

Om ditt program upplever hög processor-/minnes användning, är något av två saker sanna:
* Alla App-instanser upplever hög processor-/minnes användning, eller
* Några av app-instanserna upplever hög processor-/minnes användning.

För att bekräfta vilken situation det är,

1. Gå till _Mått_ och välj antingen `Service CPU Usage Percentage` eller `Service Memory Used`.
2. Lägg till ett `App=`-filter för att ange vilket program du vill övervaka.
3. Dela måtten med `Instance`.

Om alla instanser har hög processor/minne, måste du antingen skala ut programmet eller skala upp processor/minne. Om du vill ha mer information kan du besöka [skala program](spring-cloud-tutorial-scale-manual.md)

Om några av instanserna har hög processor/minne kontrollerar du instans statusen och dess identifierings status.

Mer information finns på [mått](spring-cloud-concept-metrics.md).

Om alla instanser är igång går du till _Azure Log Analytics_ för att skicka frågor till program loggarna och granska kod logiken för att se om någon av dem kan påverka skalningen av partitionen. Mer information finns [i analysera loggar och mät värden med diagnostikinställningar](diagnostic-services.md).

Besök [den här kom igång-artikeln](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) för att komma igång med _Azure Log Analytics_. Fråga loggarna med [Kusto-frågespråket](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Check lista innan du registrerar ditt våren-program på Azure våren Cloud

* Programmet kan köras lokalt med den angivna Java Runtime-versionen.
* Miljö konfigurationen (CPU/RAM/instanser) uppfyller minimi kravet som angetts av program leverantören.
* Konfigurationsobjekten har förväntade värden. Mer information finns i [konfigurations Server](spring-cloud-tutorial-config-server.md).
* Miljövariablerna har förväntade värden.
* JVM-parametrarna har förväntade värden.
* Vi rekommenderar att du inaktiverar/tar bort den inbäddade _konfigurations servern_ och tjänsten för _tjänst register_ från programpaketet.
* Om några Azure-resurser ska bindas via _tjänstbindning_ kontrollerar du att målresurserna är igång.

## <a name="configuration-and-management"></a>Konfiguration och hantering

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Jag har stött på ett problem med att skapa en Azure våren Cloud Service-instans

När du försöker etablera en _Azure våren Cloud_ service-instans via portalen kommer Azure våren-molnet att utföra verifieringen åt dig.

Men om du försöker etablera _Azure våren Cloud_ service-instansen via [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) eller [Resource Manager-mallen](https://docs.microsoft.com/azure/azure-resource-manager/)kontrollerar du följande:

* Prenumerationen är aktiv.
* Platsen [stöds](spring-cloud-faq.md) av _Azure våren Cloud_.
* Resurs gruppen för instansen har redan skapats.
* Resurs namnet följer namngivnings regeln. (Det får bara innehålla gemena bokstäver, siffror och bindestreck. Det första tecknet måste vara en bokstav. Det sista tecknet måste vara en bokstav eller en siffra. Värdet måste vara mellan 2 och 32 tecken långt.)

Om du försöker etablera _Azure våren Cloud_ service-instansen via Resource Manager-mallen går du till https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates för att kontrol lera mallens syntax.

Namnet på _Azure våren Cloud_ service-instansen kommer att användas för att begära ett under domän namn under `azureapps.io`, så det går inte att etablera om namnet står i konflikt med en befintlig. Det finns mer information i aktivitetsloggarna.

### <a name="i-cannot-deploy-a-jar-package"></a>Jag kan inte distribuera ett JAR-paket

Du kan inte överföra JAR/source-paket via portalen eller Resource Manager-mallen.

När du distribuerar programpaketet med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)avsöker det regelbundet distributions förloppet, och i slutet visas distributions resultatet.

Om avsökningen avbryts kan du fortfarande använda följande kommando för att hämta distributionsloggarna:

`az spring-cloud app show-deploy-log -n <app-name>`

Kontrollera att programmet är paketerat i korrekt [körbart jar-format](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Annars visas ett felmeddelande som liknar följande:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Jag kan inte distribuera ett källpaket

Du kan inte överföra JAR/source-paket via portalen eller Resource Manager-mallen.

När du distribuerar programpaketet via [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) avsöker det regelbundet distributionsförloppet, och i slutet visar det distributionsresultatet.

Om avsökningen avbryts kan du fortfarande använda följande kommando för att hämta bygg- och distributionsloggarna:

`az spring-cloud app show-deploy-log -n <app-name>`

Observera dock att en _Azure våren Cloud_ service-instans bara kan utlösa ett build-jobb för ett käll paket på en gång. Mer information finns i [distribuera en](spring-cloud-quickstart-launch-app-portal.md) guide för program och [mellanlagrings miljö](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Det går inte att registrera mitt program

I de flesta fall händer detta när nödvändiga beroenden/tjänst identifiering inte har kon figurer ATS korrekt i din POM-fil. När den har kon figurer ATS matas den inbyggda slut punkten för tjänst register servern in som en miljö variabel med ditt program. Programmen registreras sedan tillsammans med tjänstens register Server och identifierar andra beroende mikrotjänster.

Vänta minst 2 minuter innan en nyligen registrerad instans börjar ta emot trafik.

Om du migrerar en befintlig våren Cloud-baserad lösning till Azure ska du se till att ad hoc- _tjänsteregistret_ och _konfigurations Server_ instanserna tas bort (eller inaktiverade) för att undvika konflikter med de hanterade instanser som tillhandahålls av _Azure våren Cloud_ .

Du kan också kontrol lera klient loggar för _tjänst registret_ i _Azure Log Analytics_. Mer information finns [i analysera loggar och mät värden med diagnostikinställningar](diagnostic-services.md)

Besök [den här kom igång-artikeln](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) för att komma igång med _Azure Log Analytics_. Fråga loggarna med [Kusto-frågespråket](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Jag vill kontrol lera miljö variabler för mitt program

Miljövariabler meddelar Azure våren Cloud Framework, så att Azure förstår var och hur du konfigurerar de tjänster som utgör ditt program.  Att se till att dina miljövariabler är korrekta är ett nödvändigt första steg i fel sökning av eventuella problem.  Du kan använda den fjädrande start manövrerings punkten för att granska miljövariablerna.  

> [!WARNING]
> Den här proceduren visar dina miljövariabler med hjälp av test slut punkten.  Fortsätt inte om din test-slutpunkt är offentligt tillgänglig eller om du har tilldelat ett domän namn till ditt program.

1. Navigera till denna URL: `https://<your application test endpoint>/actuator/health`.  
    - Ett svar som liknar `{"status":"UP"}` anger att slut punkten har Aktiver ATS.
    - Om svaret är negativt inkluderar du följande beroende i `POM.xml`:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. När Start punktens slut punkt är aktive rad går du till Azure Portal och söker efter konfigurations sidan för ditt program.  Lägg till en miljö variabel med namnet `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` och värdet `*`. 

1. Starta om programmet.

1. Navigera till `https://<the test endpoint of your app>/actuator/env` och kontrol lera svaret.  Det bör se ut så här:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Hitta den underordnade noden som heter `systemEnvironment`.  Den här noden innehåller programmets miljövariabler.

> [!IMPORTANT]
> Kom ihåg att återställa miljö variablernas exponering innan du gör programmet tillgängligt för allmänheten.  Gå till Azure Portal, leta upp konfigurations sidan för ditt program och ta bort denna miljö variabel: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Jag hittar inga mått eller loggar för mitt program

Gå till _app Management_ för att kontrol lera att programmet _körs_ och _är igång._

Om du kan se mått från _JVM_ men inga mått från _Tomcat_kontrollerar du om det `spring-boot-actuator` beroendet är aktiverat i programpaketet och startas.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Om dina programloggar kan arkiveras till ett lagringskonto men inte skickas till _Azure Log Analytics_ kontrollerar du om du har [konfigurerat arbetsytan korrekt](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Om du använder en kostnads fri nivå av _Azure Log Analytics_, Observera att [den kostnads fria nivån inte tillhandahåller SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).