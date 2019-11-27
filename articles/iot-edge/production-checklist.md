---
title: Förbereda enheter och distributioner för produktion - Azure IoT Edge | Microsoft Docs
description: Lär dig hur du tar din Azure IoT Edge-lösning från utveckling till produktion, inklusive hur du konfigurerar dina enheter med lämpliga certifikat och gör en distributions plan för framtida kod uppdateringar.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457317"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Förbereda för distribution av din IoT Edge-lösning i produktion

När du är redo att ta din IoT Edge-lösning från utveckling till produktion kan du kontrollera att den är konfigurerad för pågående prestanda.

Informationen i den här artikeln är inte lika stor. För att hjälpa dig att prioritera, börjar varje avsnitt med listor som delar in arbetet i två delar: **viktigt** att slutföra innan du går till produktion, eller **hjälper** dig att lära dig.

## <a name="device-configuration"></a>Enhetskonfiguration

IoT Edge-enheter kan vara allt från en Raspberry Pi till en bärbar dator till en virtuell dator som körs på en server. Du kan ha åtkomst till enheten fysiskt eller via en virtuell anslutning eller så kan vara isolerade under längre tidsperioder. Oavsett hur du vill kontrol lera att den är konfigurerad för att fungera korrekt. 

* **Viktigt**
    * Installera Produktionscertifikat
    * Ha en enhet
    * Använd Moby som behållare

* **Användbart**
    * Välj överordnad protokoll

### <a name="install-production-certificates"></a>Installera Produktionscertifikat

Varje IoT Edge-enhet i produktion behöver ett certifikat (certifikatutfärdare) certifikat installerat på den. Det CA-certifikatet deklareras sedan till IoT Edge-körning i filen config.yaml. Om du vill göra utvecklingen och testningen, IoT Edge skapar runtime tillfälliga certifikat om inga certifikat har deklarerats i filen config.yaml. Dessa tillfälliga certifikat upphör att gälla efter tre månader och är inte säker för produktionsscenarier. 

Information om hur du förstår rollen för enhetens CA-certifikat finns i [hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

Mer information om hur du installerar certifikat på en IoT Edge-enhet och refererar till dem från filen config. yaml finns i [Konfigurera en IoT Edge enhet som fungerar som en transparent Gateway](how-to-create-transparent-gateway.md). Stegen för att konfigurera certifikat som är samma oavsett om enheten ska användas som en gateway eller inte. Den här artikeln tillhandahåller skript för att generera exempel certifikat endast för testning. Använd inte dessa exempel certifikat i produktion. 

### <a name="have-a-device-management-plan"></a>Ha en enhet

Innan du lägger till alla enheter i produktion bör du veta hur du ska hantera framtida uppdateringar. Listan med komponenter för att uppdatera kan vara för en IoT Edge-enhet:

* Enhetens inbyggda programvara
* Bibliotek för operativsystem
* Behållare-motorn, som Moby
* Daemon för IoT Edge
* CA-certifikat

Mer information finns i [uppdatera IoT Edge runtime](how-to-update-iot-edge.md). De aktuella metoderna för att uppdatera IoT Edge-daemon kräver fysisk eller SSH-åtkomst till IoT Edge-enhet. Om du har många enheter att uppdatera kan du överväga att lägga till uppdaterings stegen i ett skript eller använda ett Automation-verktyg som Ansible.

### <a name="use-moby-as-the-container-engine"></a>Använd Moby som behållare

En behållar motor är en förutsättning för alla IoT Edge enheter. Endast moby-engine stöds i produktion. Andra behållare-motorer, t.ex. Docker, fungerar med IoT Edge och det är ok om du vill använda dessa motorer för utveckling. Moby-motor som kan distribueras när det används med Azure IoT Edge och Microsoft tillhandahåller underhåll av den här motorn.

### <a name="choose-upstream-protocol"></a>Välj överordnad protokoll

Protokollet (och därmed vilken port som används) för överföring av överföring till IoT Hub kan konfigureras för både IoT Edge-agenten och IoT Edge Hub. Standardprotokollet är AMQP, men du kanske vill ändra det beroende på din nätverkskonfiguration. 

De två modulerna för körning har båda en **UpstreamProtocol** -miljö variabel. Giltiga värden för variabeln är: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurera variabeln UpstreamProtocol för IoT Edge agenten i filen config. yaml på själva enheten. Om din IoT Edge enhet till exempel ligger bakom en proxyserver som blockerar AMQP-portar kan du behöva konfigurera IoT Edge-agenten att använda AMQP över WebSocket (AMQPWS) för att upprätta den första anslutningen till IoT Hub. 

När du ansluter din IoT Edge-enhet, måste du fortsätta konfigurera variabeln UpstreamProtocol för båda moduler för körning i framtida distributioner. Ett exempel på den här processen finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribution

* **Användbart**
    * Stämma överens med överordnade protokoll
    * Konfigurera värd lagring för systemmoduler
    * Minska minnes utrymmet som används av IoT Edge Hub
    * Använd inte felsökningsversioner av modulen bilder

### <a name="be-consistent-with-upstream-protocol"></a>Stämma överens med överordnade protokoll

Om du har konfigurerat IoT Edge-agenten på IoT Edge-enheten för att använda ett annat protokoll än standard-AMQP, bör du deklarera samma protokoll i alla framtida distributioner. Till exempel om IoT Edge-enhet är bakom en proxyserver som blockerar AMQP portar kan konfigurerad du förmodligen att enheten ansluter via AMQP via WebSocket (AMQPWS). När du distribuerar moduler till enheten konfigurerar du samma AMQPWS-protokoll för IoT Edge-agenten och IoT Edge Hub, eller så åsidosätter standard AMQP inställningarna och hindrar dig från att ansluta igen. 

Du behöver bara konfigurera miljövariabeln UpstreamProtocol för IoT Edge agenten och IoT Edge Hub-moduler. Alla ytterligare moduler anta oavsett protokoll har angetts i moduler för körning. 

Ett exempel på den här processen finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Konfigurera värd lagring för systemmoduler

IoT Edge hubb och agent-moduler använder lokal lagring för att underhålla tillstånd och aktivera meddelanden mellan moduler, enheter och molnet. För bättre tillförlitlighet och prestanda konfigurerar du systemmodulerna för att använda lagring på värd fil systemet.

Mer information finns i [värd lagring för systemmoduler](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Minska minnes utrymmet som används av IoT Edge hubb

Om du distribuerar begränsade enheter med begränsat minne tillgängligt kan du konfigurera IoT Edge hubben så att den körs i en mer strömlinjeformad kapacitet och använder mindre disk utrymme. De här konfigurationerna begränsar IoT Edge hubbens prestanda, så hitta rätt saldo som fungerar för din lösning. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Inte optimera prestanda på begränsad enheter

IoT Edge hubben är optimerad för prestanda som standard och försöker allokera stora mängder minne. Den här konfigurationen orsaka instabilitet i mindre enheter som Raspberry Pi. Om du distribuerar enheter med begränsade resurser kanske du vill ange **OptimizeForPerformance** -miljövariabeln till **false** på IoT Edge Hub. 

När **OptimizeForPerformance** är inställt på **True**använder MQTT-protokollets huvud PooledByteBufferAllocator som har bättre prestanda men allokerar mer minne. Allokeraren fungerar inte bra på 32-bitars operativ system eller på enheter med ont om minne. När RocksDb allokeras för prestanda, allokerar dessutom mer minne för rollen som den lokala lagrings leverantören. 

Mer information finns i [stabilitets problem på resurs begränsade enheter](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Inaktivera oanvända protokoll

Ett annat sätt att optimera prestanda för IoT Edge hubben och minska dess minnes användning är att inaktivera protokoll huvudena för alla protokoll som du inte använder i lösningen. 

Protokoll huvuden konfigureras genom att ställa in booleska miljövariabler för modulen IoT Edge Hub i distributions manifesten. De här tre variablerna är:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alla tre variablerna har *två under streck* och kan anges till antingen sant eller falskt. 

#### <a name="reduce-storage-time-for-messages"></a>Minska tiden för lagring för meddelanden

Modulen IoT Edge Hub lagrar meddelanden tillfälligt om de inte kan levereras till IoT Hub av någon anledning. Du kan konfigurera hur länge IoT Edge hubben ska lagra i meddelanden som inte levererats innan de upphör att gälla. Om du har minnes problem på enheten kan du sänka **timeToLiveSecs** -värdet i IoT Edge Hub-modulen. 

Standardvärdet för parametern timeToLiveSecs är 7200 sekunder, vilket är två timmar. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Använd inte felsökningsversioner av modulen bilder

När du flyttar från testscenarier till produktionsscenarier, Kom ihåg att ta bort debug konfigurationer från distribution manifest. Kontrol lera att ingen av modulens bilder i distributions manifesten har **\.debug** -suffix. Om du har lagt till skapa alternativ för att exponera portar i moduler för felsökning, ta bort de skapa samt alternativ. 

## <a name="container-management"></a>Containerhantering

* **Viktigt**
    * Hantera åtkomst till ditt behållarregister
    * Använd taggar för att hantera versioner

### <a name="manage-access-to-your-container-registry"></a>Hantera åtkomst till ditt behållarregister

Innan du distribuerar moduler på IoT Edge-enheter för produktion bör du se till att kontrollera åtkomsten till ditt behållarregister så att utomstående inte kan komma åt eller göra ändringar i dina behållaravbildningar. Du kan använda ett privat, inte offentliga behållarregister för att hantera behållaravbildningar. 

Vi ber du kan använda samma autentiseringsuppgifter för container-registret på din IoT Edge-enhet när du använder på din utvecklingsdator i självstudierna och annan dokumentation. Dessa instruktioner är endast avsedda att hjälpa dig konfigurera miljöer för testning och utveckling enklare och inte ska följas i ett produktionsscenario för. Azure Container Registry rekommenderar att du [autentiserar med tjänstens huvud namn](../container-registry/container-registry-auth-service-principal.md) när program eller tjänster hämtar behållar avbildningar på ett automatiserat eller annars obevakat sätt, eftersom IoT Edge enheter gör. Skapa ett huvudnamn för tjänsten med skrivskyddad åtkomst till behållarregistret och ange det användarnamnet och lösenord i manifestet distribution.

### <a name="use-tags-to-manage-versions"></a>Använd taggar för att hantera versioner

En-tagg är ett Docker-koncept som du kan använda för att skilja mellan versioner av Docker-behållare. Taggar är suffix som **1,0** som finns i slutet av en behållar lagrings plats. Till exempel **MCR.Microsoft.com/azureiotedge-agent:1.0**. Taggar är föränderliga och kan ändras för att peka till en annan behållare när som helst, så att ditt team måste komma överens om en konvention att följa när du uppdaterar bilderna modulen framöver. 

Taggar hjälper dig att tillämpa uppdateringar på IoT Edge-enheter. När du överför en uppdaterad version av en modul till behållarregistret, öka taggen. Skicka sedan en ny distribution till dina enheter med taggen ökas. Motorn för behållaren ser ökar taggen som en ny version och hämtar den senaste modulversionen till din enhet. 

Ett exempel på en tag-konvention finns i [uppdatera IoT Edge runtime](how-to-update-iot-edge.md#understand-iot-edge-tags) för att lära dig hur IoT Edge använder rullande Taggar och vissa taggar för att spåra versioner. 

## <a name="networking"></a>Nätverk

* **Användbart**
    * Granska utgående/inkommande konfigurationen
    * Tillåt anslutningar från IoT Edge enheter
    * Konfigurera kommunikation via en proxyserver

### <a name="review-outboundinbound-configuration"></a>Granska utgående/inkommande konfigurationen

Kommunikationskanaler mellan Azure IoT Hub och IoT Edge är alltid konfigurerad för att vara utgående. De flesta scenarier med IoT Edge krävs bara tre anslutningar. Container-motorn måste ansluta med container registry (eller register) som innehåller modulen-avbildningar. IoT Edge-körningen behöver anslutning med IoT Hub att hämta konfigurationsinformation för enheten och för att skicka meddelanden och telemetri. Och om du använder Automatisk etablering, IoT Edge-daemon måste ansluta till Device Provisioning-tjänsten. Mer information finns i [brand Väggs-och port konfigurations regler](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Tillåt anslutningar från IoT Edge enheter

Om nätverks konfigurationen kräver att du uttryckligen tillåter anslutningar som görs från IoT Edge-enheter läser du följande lista över IoT Edge komponenter:

* **IoT Edge agent** öppnar en beständig AMQP/MQTT-anslutning till IoT Hub, möjligen över WebSockets. 
* **IoT Edge hubb** öppnar en enda beständig AMQP-anslutning eller flera MQTT-anslutningar för att IoT Hub, möjligen över WebSockets. 
* **IoT Edge daemon** gör tillfälliga https-anrop till IoT Hub. 

I samtliga tre fall matchar DNS-namnet mönstret \*. azure-devices.net. 

Dessutom gör **behållar motorn** anrop till behållar register via https. Om du vill hämta behållaravbildningar för IoT Edge-körning, är DNS-namnet mcr.microsoft.com. Behållare-motorn ansluter till andra register som konfigurerats i distributionen. 

Den här check listan är en start punkt för brand Väggs regler:

   | URL (\* = jokertecken) | Utgående TCP-portar | Användning |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS-åtkomst (valfritt) |
   | \*. azurecr.io | 443 | Personliga och tredje parts behållar register |
   | \*.blob.core.windows.net | 443 | Ladda ned Azure Container Registry avbildnings delta från Blob Storage  | 
   | \*. azure-devices.net | 5671, 8883, 443 | IoT Hub åtkomst |
   | \*. docker.io  | 443 | Docker Hub-åtkomst (valfritt) |

Vissa av brand Väggs reglerna ärvs från Azure Container Registry. Mer information finns i [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Konfigurera kommunikation via en proxyserver

Om dina enheter kommer att distribueras på ett nätverk som använder en proxyserver, som de behöver för att kunna kommunicera via proxy till IoT Hub och behållarregister. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Lösningshantering

* **Användbart**
    * Konfigurera loggar och diagnostik
    * Överväg att testerna och CI/CD-pipelines

### <a name="set-up-logs-and-diagnostics"></a>Konfigurera loggar och diagnostik

I Linux använder IoT Edge daemon journaler som standard driv rutin för loggning. Du kan använda kommando rads verktyget `journalctl` för att skicka frågor till daemon-loggarna. På Windows använder IoT Edge-daemon PowerShell diagnostik. Använd `Get-IoTEdgeLog` för att fråga efter loggar från daemonen. IoT Edge moduler använder JSON-drivrutinen för loggning, vilket är standardvärdet.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

När du testar en IoT Edge-distribution kan du normalt komma åt dina enheter för att hämta loggar och felsöka. Du kanske inte har alternativet i ett scenario för distribution. Överväg hur du ska samla in information om dina enheter i produktionen. Ett alternativ är att använda en loggningsmodul som samlar in information från andra moduler och skickar dem till molnet. Ett exempel på en loggningsmodul är [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), eller så kan du skapa en egen. 

### <a name="place-limits-on-log-size"></a>Placera gränser för logg storlek

Som standard anger Moby container Engine inte storleks gränser för behållar loggen. Med tiden kan detta leda till att enheten fyller i loggar och håller på att få slut på disk utrymme. Överväg följande alternativ för att förhindra detta:

**Alternativ: ange globala gränser som gäller för alla behållar moduler**

Du kan begränsa storleken på alla behållar logg fils loggar i behållar Motorns logg alternativ. I följande exempel anges logg driv rutinen till `json-file` (rekommenderas) med gränser för storlek och antal filer:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Lägg till (eller Lägg till) den här informationen i en fil med namnet `daemon.json` och placera den rätt plats för din enhets plattform.

| Plattform | Plats |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Behållar motorn måste startas om för att ändringarna ska börja gälla.

**Alternativ: justera logg inställningar för varje container module**

Du kan göra det i **createOptions** för varje modul. Till exempel:

```yml
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
```

**Ytterligare alternativ för Linux-system**

* Konfigurera behållar motorn att skicka loggar till `systemd` [Journal](https://docs.docker.com/config/containers/logging/journald/) genom att ställa in `journald` som standard driv rutin för loggning. 

* Ta regelbundet bort gamla loggar från enheten genom att installera ett logrotate-verktyg. Använd följande filspecifikationen: 

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
