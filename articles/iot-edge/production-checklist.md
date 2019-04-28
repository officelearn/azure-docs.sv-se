---
title: Förbereda enheter och distributioner för produktion - Azure IoT Edge | Microsoft Docs
description: Lär dig ta din Azure IoT Edge-lösning från utveckling till produktion, och konfigurera dina enheter med tillämpliga certifikat och göra en distributionsplan för framtida uppdateringar.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c64db6b35aa2f1daa4484f137c8505b1415c5a0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998463"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Förbereda för distribution av din IoT Edge-lösning i produktion

När du är redo att ta din IoT Edge-lösning från utveckling till produktion kan du kontrollera att den är konfigurerad för pågående prestanda.

Informationen i den här artikeln är inte alla lika. För att hjälpa dig att prioritera, varje avsnitt börjar med listor som delar upp arbetet i två delar: **viktiga** ska slutföras innan du fortsätter till produktion, eller **användbara** att du vet.

## <a name="device-configuration"></a>Enhetskonfiguration

IoT Edge-enheter kan vara allt från en Raspberry Pi till en bärbar dator till en virtuell dator som körs på en server. Du kan ha åtkomst till enheten fysiskt eller via en virtuell anslutning eller så kan vara isolerade under längre tidsperioder. I båda fallen som du vill kontrollera att den är konfigurerad för att utföra på rätt sätt. 

* **Viktigt**
    * Installera Produktionscertifikat
    * Ha en enhet
    * Använd Moby som behållare

* **Användbara**
    * Välj överordnad protokoll

### <a name="install-production-certificates"></a>Installera Produktionscertifikat

Varje IoT Edge-enhet i produktion behöver ett certifikat (certifikatutfärdare) certifikat installerat på den. Det CA-certifikatet deklareras sedan till IoT Edge-körning i filen config.yaml. Om du vill göra utvecklingen och testningen, IoT Edge skapar runtime tillfälliga certifikat om inga certifikat har deklarerats i filen config.yaml. Dessa tillfälliga certifikat upphör att gälla efter tre månader och är inte säker för produktionsscenarier. 

Information om rollen för enhetens CA-certifikat finns i [hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

Läs mer om hur du installerar certifikat på en IoT Edge-enhet och referera till dem från filen config.yaml [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md). Stegen för att konfigurera certifikat som är samma oavsett om enheten ska användas som en gateway eller inte. Den här artikeln tillhandahåller skript för att generera exempel certifikat endast för testning. Använd inte dessa exempel certifikat i produktion. 

### <a name="have-a-device-management-plan"></a>Ha en enhet

Innan du lägger till alla enheter i produktion bör du veta hur du ska hantera framtida uppdateringar. Listan med komponenter för att uppdatera kan vara för en IoT Edge-enhet:

* Enhetens inbyggda programvara
* Bibliotek för operativsystem
* Behållare-motorn, som Moby
* Daemon för IoT Edge
* CA-certifikat

Anvisningar om att uppdatera daemonen IoT Edge finns i [uppdatera IoT Edge-körningen](how-to-update-iot-edge.md). De aktuella metoderna för att uppdatera IoT Edge-daemon kräver fysisk eller SSH-åtkomst till IoT Edge-enhet. Om du har många enheter kan du lägga till update-åtgärder till ett skript för att uppdatera eller använda ett automatiseringsverktyg som Ansible för att utföra uppdateringar i skala.

### <a name="use-moby-as-the-container-engine"></a>Använd Moby som behållare

Med en motor för behållare om en enhet är en förutsättning för alla IoT Edge-enheter. Endast moby-engine stöds i produktion. Andra behållare-motorer, t.ex. Docker, fungerar med IoT Edge och det är ok om du vill använda dessa motorer för utveckling. Moby-motor som kan distribueras när det används med Azure IoT Edge och Microsoft tillhandahåller underhåll av den här motorn. Med andra behållare motorer på en IoT Edge-enhet stöds inte.

### <a name="choose-upstream-protocol"></a>Välj överordnad protokoll

Protokollet (och därför den port som används) för överordnade kommunikation till IoT Hub kan konfigureras för både Edge-agenten och Edge hub. Standardprotokollet är AMQP, men du kanske vill ändra det beroende på din nätverkskonfiguration. 

De två runtime modulerna som både har en **UpstreamProtocol** miljövariabeln. Giltiga värden för variabeln är: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurera variabeln UpstreamProtocol för Edge-agenten i filen config.yaml på själva enheten. Om din IoT Edge-enhet är bakom en proxyserver som blockerar AMQP portar kan behöva du till exempel konfigurera Edge-agenten för att använda AMQP via WebSocket (AMQPWS) för att etablera den initiala anslutningen till IoT Hub. 

När du ansluter din IoT Edge-enhet, måste du fortsätta konfigurera variabeln UpstreamProtocol för båda moduler för körning i framtida distributioner. Ett exempel på den här processen finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribution

* **Användbara**
    * Stämma överens med överordnade protokoll
    * Minska minne som används av Edge hub
    * Använd inte felsökningsversioner av modulen bilder

### <a name="be-consistent-with-upstream-protocol"></a>Stämma överens med överordnade protokoll

Om du har konfigurerat Edge-agenten på IoT Edge-enhet du använder ett annat protokoll än standardvärdet AMQP, bör du deklarera samma protokoll i alla efterföljande distributioner. Till exempel om IoT Edge-enhet är bakom en proxyserver som blockerar AMQP portar kan konfigurerad du förmodligen att enheten ansluter via AMQP via WebSocket (AMQPWS). När du distribuerar moduler på enheten om du inte konfigurerar APQPWS samma protokoll för Edge-agent och Edge hub kan åsidosätta inställningarna standard AMQP och hindrar dig från att ansluta igen. 

Du behöver bara konfigurera miljövariabeln UpstreamProtocol för Edge-agent och Edge hub-moduler. Alla ytterligare moduler anta oavsett protokoll har angetts i moduler för körning. 

Ett exempel på den här processen finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Minska minne som används av Edge hub

Om du distribuerar begränsad enheter med begränsat minne som är tillgängligt, kan du konfigurera Edge hub om du vill köra i en mer effektiv kapacitet och mindre diskutrymme. De här konfigurationerna begränsar prestandan för Edge-hubben, men, så att hitta rätt balans som fungerar för din lösning. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Inte optimera prestanda på begränsad enheter

Edge hub är optimerad för prestanda som standard, så görs ett försök att allokera stora mängder minne. Den här konfigurationen orsaka instabilitet i mindre enheter som Raspberry Pi. Om du distribuerar enheter med begränsade resurser, kan du ange den **OptimizeForPerformance** miljövariabeln till **FALSKT** på Edge hub. 

Mer information finns i [stabilitetsproblem på resursen begränsad enheter](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Inaktivera oanvända protokoll

Ett annat sätt att optimera prestandan för Edge hub och minska dess minnesanvändning är att stänga av protokollet nytta för alla protokoll som du inte använder i din lösning. 

Protokollet huvuden konfigureras genom att ange booleskt miljövariabler för Edge hub-modul i din distribution manifest. De här tre variablerna är:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alla tre variabler har *två understreck* och kan anges till true eller false. 

#### <a name="reduce-storage-time-for-messages"></a>Minska tiden för lagring för meddelanden

Edge hub-modul lagrar tillfälligt meddelanden om de inte kan levereras till IoT Hub av någon anledning. Du kan konfigurera hur länge Edge hub innehåller inte har meddelanden innan du ger dem upphör att gälla. Om du har problem med minne på din enhet kan du sänka den **timeToLiveSecs** värdet i Edge hub modultvillingen. 

Standardvärdet för parametern timeToLiveSecs är 7200 sekunder, vilket är två timmar. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Använd inte felsökningsversioner av modulen bilder

När du flyttar från testscenarier till produktionsscenarier, Kom ihåg att ta bort debug konfigurationer från distribution manifest. Kontrollera att ingen av modulen bilder i manifesten distribution har den  **\.felsöka** suffix. Om du har lagt till skapa alternativ för att exponera portar i moduler för felsökning, ta bort de skapa samt alternativ. 

## <a name="container-management"></a>Behållarhantering

* **Viktigt**
    * Hantera åtkomst till ditt behållarregister
    * Använd taggar för att hantera versioner

### <a name="manage-access-to-your-container-registry"></a>Hantera åtkomst till ditt behållarregister

Innan du distribuerar moduler på IoT Edge-enheter för produktion bör du se till att kontrollera åtkomsten till ditt behållarregister så att utomstående inte kan komma åt eller göra ändringar i dina behållaravbildningar. Du kan använda ett privat, inte offentliga behållarregister för att hantera behållaravbildningar. 

Vi ber du kan använda samma autentiseringsuppgifter för container-registret på din IoT Edge-enhet när du använder på din utvecklingsdator i självstudierna och annan dokumentation. Dessa instruktioner är endast avsedda att hjälpa dig konfigurera miljöer för testning och utveckling enklare och inte ska följas i ett produktionsscenario för. Azure Container Registry rekommenderar [autentisering med tjänstens huvudnamn](../container-registry/container-registry-auth-service-principal.md) när program eller tjänster hämta behållaravbildningar i automatiserade eller på annat sätt oövervakat, som gör IoT Edge-enheter. Skapa ett huvudnamn för tjänsten med skrivskyddad åtkomst till behållarregistret och ange det användarnamnet och lösenord i manifestet distribution.

### <a name="use-tags-to-manage-versions"></a>Använd taggar för att hantera versioner

En tagg är en docker-begrepp som du kan använda för att skilja mellan versioner av docker-behållare. Taggar är suffix som **1.0** som går i slutet av en lagringsplats för behållaren. Till exempel **mcr.microsoft.com/azureiotedge-agent:1.0**. Taggar är föränderliga och kan ändras för att peka till en annan behållare när som helst, så att ditt team måste komma överens om en konvention att följa när du uppdaterar bilderna modulen framöver. 

Taggar hjälper dig att tillämpa uppdateringar på IoT Edge-enheter. När du överför en uppdaterad version av en modul till behållarregistret, öka taggen. Skicka sedan en ny distribution till dina enheter med taggen ökas. Motorn för behållaren ser ökar taggen som en ny version och hämtar den senaste modulversionen till din enhet. 

Ett exempel på en tagg konvention finns i [uppdatera IoT Edge-körningen](how-to-update-iot-edge.md#understand-iot-edge-tags) att lära dig hur IoT Edge använder löpande taggar och särskilda taggar för att spåra versioner. 

## <a name="networking"></a>Nätverk

* **Användbara**
    * Granska utgående/inkommande konfigurationen
    * Lista över tillåtna anslutningar
    * Konfigurera kommunikation via en proxyserver

### <a name="review-outboundinbound-configuration"></a>Granska utgående/inkommande konfigurationen

Kommunikationskanaler mellan Azure IoT Hub och IoT Edge är alltid konfigurerad för att vara utgående. De flesta scenarier med IoT Edge krävs bara tre anslutningar. Container-motorn måste ansluta med container registry (eller register) som innehåller modulen-avbildningar. IoT Edge-körningen behöver anslutning med IoT Hub att hämta konfigurationsinformation för enheten och för att skicka meddelanden och telemetri. Och om du använder Automatisk etablering, IoT Edge-daemon måste ansluta till Device Provisioning-tjänsten. Mer information finns i [brandvägg och port konfigurationsregler](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Lista över tillåtna anslutningar

Om ditt nätverk installationen kräver att du uttryckligen lista över tillåtna anslutningar görs från IoT Edge-enheter, kan du läsa följande lista med IoT Edge-komponenter:

* **IoT Edge-agenten** öppnar en beständig AMQP/MQTT-anslutning till IoT Hub, eventuellt över WebSockets. 
* **IoT Edge hub** öppnar en enda beständiga AMQP-anslutning eller flera MQTT-anslutningar till IoT Hub, eventuellt över WebSockets. 
* **IoT Edge-daemon** gör tillfälligt HTTPS-anrop till IoT Hub. 

I samtliga tre fall DNS-namnet matchar mönstret \*.azure devices.net. 

Dessutom kan den **Container engine** anropar behållarregister via HTTPS. Om du vill hämta behållaravbildningar för IoT Edge-körning, är DNS-namnet mcr.microsoft.com. Behållare-motorn ansluter till andra register som konfigurerats i distributionen. 

Den här checklistan är en startpunkt för brandväggsregler:

   | URL: en (\* = jokertecken) | Utgående TCP-portar | Användning |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft behållarregister |
   | global.azure-devices-provisioning.net  | 443 | DPS-åtkomst (valfritt) |
   | \*.azurecr.io | 443 | Personliga och 3 part behållarregister |
   | \*.blob.core.windows.net | 443 | Nedladdningen av bilden deltan | 
   | \*.azure-devices.net | 5671, 8883, 443 | IoT Hub access |
   | \*.docker.io  | 443 | Docker Hub-åtkomst (valfritt) |

### <a name="configure-communication-through-a-proxy"></a>Konfigurera kommunikation via en proxyserver

Om dina enheter kommer att distribueras på ett nätverk som använder en proxyserver, som de behöver för att kunna kommunicera via proxy till IoT Hub och behållarregister. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Lösningshantering

* **Användbara**
    * Konfigurera loggar och diagnostik
    * Överväg att testerna och CI/CD-pipelines

### <a name="set-up-logs-and-diagnostics"></a>Konfigurera loggar och diagnostik

På Linux använder IoT Edge-daemon journaler som standard loggning av drivrutinen. Du kan använda kommandoradsverktyget `journalctl` att fråga daemon loggar. På Windows använder IoT Edge-daemon PowerShell diagnostik. Använd `Get-WinEvent` till fråga loggar från daemon. IoT Edge-moduler använda JSON-drivrutin för loggning, vilket är standard.  

När du testar en IoT Edge-distribution kan du normalt komma åt dina enheter för att hämta loggar och felsöka. Du kanske inte har alternativet i ett scenario för distribution. Överväg hur du ska samla in information om dina enheter i produktionen. Ett alternativ är att använda en loggningsmodul som samlar in information från andra moduler och skickar dem till molnet. Ett exempel på en loggningsmodul är [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), eller du kan utforma dina egna. 

### <a name="place-limits-on-log-size"></a>Ange gränser för loggstorleken

Som standard inställt Moby behållare motorn inte storleksgränser för behållaren log. Detta kan leda till att enheten svämmar över med loggar och få slut på diskutrymme över tid. Överväg följande alternativ för att förhindra detta:

**Alternativ: Ange globala begränsningar som gäller för alla moduler i behållare**

Du kan begränsa storleken på alla behållare logfiles i loggalternativ för container-motorn. I följande exempel anger log-drivrutinen till `json-file` (rekommenderas) med begränsningar i storlek och antalet filer:

    {
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "10m",
            "max-file": "3"
        }
    }

Lägg till (eller Lägg till) den här informationen till en fil med namnet `daemon.json` och placera den på rätt plats för din enhetsplattform.

| Plattform | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby-data\config\` |

Behållare-motorn måste startas om för att ändringarna ska börja gälla.

**Alternativ: Justera inställningar för varje container-modul**

Du kan göra i den **createOptions** för varje modul. Exempel:

    "createOptions": {
        "HostConfig": {
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "10m",
                    "max-file": "3"
                }
            }
        }
    }


**Ytterligare alternativ på Linux-system**

* Konfigurera container-motorn för att skicka loggar till `systemd` [journalen](https://docs.docker.com/config/containers/logging/journald/) genom att ange `journald` som Standarddrivrutin för loggning. 

* Regelbundet ta bort gamla loggarna från din enhet genom att installera en logrotate-verktyget. Använd följande filspecifikationen: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Överväg att testerna och CI/CD-pipelines

Överväg att integrera din Produktionsdistribution i din testning och CI/CD-pipelines för det mest effektiva IoT Edge-distributionsscenariot. Azure IoT Edge stöder flera CI/CD-plattformar, inklusive Azure DevOps. Mer information finns i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [IoT Edge automatisk distribution](module-deployment-monitoring.md).
* Se hur IoT Edge stöder [kontinuerlig integrering och kontinuerlig distribution](how-to-ci-cd.md).
