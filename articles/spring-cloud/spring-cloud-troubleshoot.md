---
title: Felsökningsguide för Azure Spring Cloud | Microsoft-dokument
description: Felsökningsguide för Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277586"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Felsöka vanliga Problem med Azure Spring Cloud

Den här artikeln innehåller instruktioner för felsökning av utvecklingsproblem i Azure Spring Cloud. Mer information finns i [Vanliga frågor och svar om Azure Spring Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problem med tillgänglighet, prestanda och program

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Mitt program kan inte starta (till exempel kan slutpunkten inte anslutas eller returneras en 502 efter några försök)

Exportera loggarna till Azure Log Analytics. Tabellen för spring-programloggar heter *AppPlatformLogsforSpring*. Mer information finns i [Analysera loggar och mått med diagnostikinställningar](diagnostic-services.md).

Följande felmeddelande kan visas i dina loggar:

> "org.springframework.context.ApplicationContextException: Det går inte att starta webbservern"

Meddelandet anger ett av två troliga problem: 
* En av bönorna eller ett av dess beroenden saknas.
* En av egenskaperna för bönor saknas eller är ogiltig. I det här fallet visas troligen "java.lang.IllegalArgumentException".

Tjänstbindningar kan också orsaka programstartfel. Om du vill fråga loggarna använder du nyckelord som är relaterade till de bundna tjänsterna. Anta till exempel att ditt program har en bindning till en MySQL-instans som är inställd på lokal systemtid. Om programmet inte startar kan följande felmeddelande visas i loggen:

> "java.sql.SQLException: Servertidszonens värde "Coordinated Universal Time" är okänt eller representerar mer än en tidszon."

Lös det här felet `server parameters` genom att gå till mySQL-instansen `time_zone` och ändra värdet från *SYSTEM* till *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mina program kraschar eller genererar ett oväntat fel

När du felsöker kraschar programmet börjar du med att kontrollera programmets status och identifieringsstatus. Det gör du genom att gå till _Apphantering_ i Azure-portalen för att säkerställa att statusarna för alla program _körs_ och _UP_.

* Om statusen _körs_ men identifieringsstatusen inte är _UP_går du till avsnittet ["Mitt program kan inte registreras".](#my-application-cant-be-registered)

* Om identifieringsstatusen är _UP_går du till Mått för att kontrollera programmets hälsotillstånd. Kontrollera följande mått:


  - `TomcatErrorCount`_(tomcat.global.error):_ Alla spring-programundantag räknas här. Om det här numret är stort går du till Azure Log Analytics för att granska dina programloggar.

  - `AppMemoryMax`_(jvm.memory.max):_ Den maximala mängden minne som är tillgängligt för programmet. Beloppet kan vara odefinierat eller ändras med tiden om det har definierats. Om det definieras är mängden använt och engagerat minne alltid mindre än eller lika med max. En minnesallokering kan `OutOfMemoryError` dock misslyckas med ett meddelande om allokeringen försöker öka det använda minnet så att *det används > bekräftats*, även om *det används <= max* fortfarande är sant. I en sådan situation, försök att öka den `-Xmx` maximala heap storlek med hjälp av parametern.

  - `AppMemoryUsed`_(jvm.memory.used):_ Mängden minne i byte som för närvarande används av programmet. För en normal belastning Java-program, bildar denna metriska serien en *sågtooth* mönster, där minnesanvändningen stadigt ökar och minskar i små steg och plötsligt sjunker mycket, och sedan mönstret återkommer. Den här måttserien sker på grund av skräpinsamling inuti java-datorn, där insamlingsåtgärder representerar droppar på sågtandmönstret.
    
    Det här måttet är viktigt för att identifiera minnesproblem, till exempel:
    * En minnesexplosion i början.
    * Den överspänningsminnesallokering för en viss logiksökväg.
    * Gradvisa minnesläckor.

  Mer information finns i [Mått](spring-cloud-concept-metrics.md).

Mer information om Azure Log Analytics finns [i Komma igång med Logganalys i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mitt program använder stora mängder CPU- eller minnesresurser

Om ditt program får hög CPU- eller minnesanvändning är en av två saker sant:
* Alla appinstanser har hög CPU- eller minnesanvändning.
* Vissa appinstanser har hög CPU- eller minnesanvändning.

För att ta reda på vilken situation som gäller, gör följande:

1. Gå till **Mått**och välj sedan **antingen Procentandel av tjänstens CPU-användning** eller **Serviceminne som används**.
2. Lägg till ett **App=-filter** för att ange vilket program du vill övervaka.
3. Dela måtten efter **instans**.

Om *alla instanser* har hög CPU- eller minnesanvändning måste du antingen skala ut programmet eller skala upp processorn eller minnesanvändningen. Mer information finns i [Självstudiekurs: Skala ett program i Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Om *vissa instanser* har hög CPU- eller minnesanvändning kontrollerar du instansstatus och identifieringsstatus.

Mer information finns i [Mått för Azure Spring Cloud](spring-cloud-concept-metrics.md).

Om alla instanser är igång går du till Azure Log Analytics för att fråga dina programloggar och granska din kodlogik. Detta hjälper dig att se om någon av dem kan påverka skalningspartitionering. Mer information finns i [Analysera loggar och mått med diagnostikinställningar](diagnostic-services.md).

Mer information om Azure Log Analytics finns [i Komma igång med Logganalys i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Fråga loggarna med hjälp av [frågespråket Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Checklista för distribution av ditt fjäderprogram till Azure Spring Cloud

Innan du går in på programmet ska du se till att det uppfyller följande kriterier:

* Programmet kan köras lokalt med den angivna Java-körningsversionen.
* Miljökonfigurationen (CPU/RAM/Instances) uppfyller minimikravet som fastställts av programleverantören.
* Konfigurationsobjekten har sina förväntade värden. Mer information finns i [Config Server](spring-cloud-tutorial-config-server.md).
* Miljövariablerna har sina förväntade värden.
* JVM-parametrarna har sina förväntade värden.
* Vi rekommenderar att du inaktiverar eller tar bort de inbäddade _Config Server-_ och _Spring Service-registertjänsterna_ från programpaketet.
* Om några Azure-resurser ska bindas via _tjänstbindning_ kontrollerar du att målresurserna är igång.

## <a name="configuration-and-management"></a>Konfiguration och hantering

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Jag stötte på ett problem med att skapa en Azure Spring Cloud-tjänstinstans

När du konfigurerar en Azure Spring Cloud-tjänstinstans med hjälp av Azure-portalen utför Azure Spring Cloud valideringen åt dig.

Men om du försöker konfigurera Azure Spring Cloud-tjänstinstansen med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) eller [Azure Resource Manager-mallen](https://docs.microsoft.com/azure/azure-resource-manager/)kontrollerar du att:

* Prenumerationen är aktiv.
* Platsen [stöds](spring-cloud-faq.md) av Azure Spring Cloud.
* Resursgruppen för instansen har redan skapats.
* Resursnamnet överensstämmer med namngivningsregeln. Den får bara innehålla gemener, siffror och bindestreck. Det första tecknet måste vara en bokstav. Det sista tecknet måste vara en bokstav eller en siffra. Värdet måste innehålla mellan 2 och 32 tecken.

Om du vill konfigurera Azure Spring Cloud-tjänstinstansen med hjälp av resource manager-mallen läser du först [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

Namnet på Azure Spring Cloud-tjänstinstansen används för att `azureapps.io`begära ett underdomännamn under , så installationen misslyckas om namnet står i konflikt med ett befintligt. Du kan hitta mer information i aktivitetsloggarna.

### <a name="i-cant-deploy-a-jar-package"></a>Jag kan inte distribuera ett JAR-paket

Du kan inte ladda upp JAR-/källpaketet (Java Archive File)/source med hjälp av Azure-portalen eller Resource Manager-mallen.

När du distribuerar ditt programpaket med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)avsökningar Azure CLI regelbundet distributionsstatus och i slutändan visar det distributionsresultatet.

Om avsökningen avbryts kan du fortfarande använda följande kommando för att hämta distributionsloggarna:

`az spring-cloud app show-deploy-log -n <app-name>`

Kontrollera att ditt program är förpackat i rätt [körbart JAR-format](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Om den inte är korrekt paketerad visas ett felmeddelande som liknar följande:

> "Fel: Ogiltig eller korrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Jag kan inte distribuera ett källpaket

Du kan inte ladda upp JAR/source-paketet med hjälp av Azure-portalen eller Resource Manager-mallen.

När du distribuerar ditt programpaket med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)avsökningar Azure CLI regelbundet distributionsstatus och i slutändan visar det distributionsresultatet.

Om avsökningen avbryts kan du fortfarande använda följande kommando för att hämta bygg- och distributionsloggarna:

`az spring-cloud app show-deploy-log -n <app-name>`

Observera dock att en Azure Spring Cloud-tjänstinstans bara kan utlösa ett byggjobb för ett källpaket samtidigt. Mer information finns i [Distribuera ett program](spring-cloud-quickstart-launch-app-portal.md) och Konfigurera en [mellanlagringsmiljö i Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Min ansökan kan inte registreras

I de flesta fall uppstår den här situationen när *obligatoriska beroenden* och *tjänstidentifiering* inte är korrekt konfigurerade i din POM-fil (Project Object Model). När den har konfigurerats injiceras den inbyggda serverslutpunkten för tjänstregister som en miljövariabel med ditt program. Program registrerar sig sedan med serviceregisterservern och upptäcker andra beroende mikrotjänster.

Vänta minst två minuter innan en nyregistrerad instans börjar ta emot trafik.

Om du migrerar en befintlig Spring Cloud-baserad lösning till Azure kontrollerar du att dina ad _hoc-tjänstregister-_ och _config server-instanser_ tas bort (eller inaktiveras) för att undvika att de hanteras instanser som tillhandahålls av Azure Spring Cloud kan stå i konflikt med de hanterade instanserna som tillhandahålls av Azure Spring Cloud.

Du kan också kontrollera _tjänstregisterklientloggarna_ i Azure Log Analytics. Mer information finns i [Analysera loggar och mått med diagnostikinställningar](diagnostic-services.md)

Mer information om Azure Log Analytics finns [i Komma igång med Logganalys i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Fråga loggarna med hjälp av [frågespråket Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Jag vill inspektera programmets miljövariabler

Miljövariabler informerar Azure Spring Cloud-ramverket, så att Azure förstår var och hur du konfigurerar de tjänster som utgör ditt program. Att se till att dina miljövariabler är korrekta är ett nödvändigt första steg för att felsöka potentiella problem.  Du kan använda slutpunkten för fjäderstartställdon för att granska dina miljövariabler.  

> [!WARNING]
> Den här proceduren exponerar dina miljövariabler med hjälp av testslutpunkten.  Fortsätt inte om testslutpunkten är allmänt tillgänglig eller om du har tilldelat ditt program ett domännamn.

1. Gå till `https://<your application test endpoint>/actuator/health`.  
    - Ett svar `{"status":"UP"}` som liknar anger att slutpunkten har aktiverats.
    - Om svaret är negativt inkluderar du följande beroende i *filen POM.xml:*

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Med slutpunkten för Spring Boot Actuator aktiverat går du till Azure-portalen och letar efter konfigurationssidan för ditt program.  Lägg till en miljövariabel med namnet `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` och värdet `*` . 

1. Starta om programmet.

1. Gå `https://<your application test endpoint>/actuator/env` till och inspektera svaret.  Det bör se ut så här:

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

Leta efter den underordnade noden som heter `systemEnvironment`.  Den här noden innehåller programmets miljövariabler.

> [!IMPORTANT]
> Kom ihåg att vända exponeringen av dina miljövariabler innan du gör ditt program tillgängligt för allmänheten.  Gå till Azure-portalen, leta efter konfigurationssidan för ditt `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`program och ta bort den här miljövariabeln: .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Jag kan inte hitta mått eller loggar för mitt program

Gå till **Apphantering** för att säkerställa att programstatusarna _körs_ och _UPP_.

Om du kan se mått från _JVM_ men inga mått från `spring-boot-actuator` _Tomcat_kontrollerar du om beroendet är aktiverat i programpaketet och att det har startar upp.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Om dina programloggar kan arkiveras till ett lagringskonto men inte skickas till Azure Log Analytics kontrollerar du om du [har konfigurerat arbetsytan korrekt](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Om du använder en kostnadsfri nivå i Azure Log Analytics bör du tänka på att [den kostnadsfria nivån inte tillhandahåller ett servicenivåavtal (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
