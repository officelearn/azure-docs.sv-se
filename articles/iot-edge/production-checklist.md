---
title: Förbered att distribuera din lösning i produktion - Azure IoT Edge
description: Lär dig hur du tar din Azure IoT Edge-lösning från utveckling till produktion, inklusive att konfigurera dina enheter med lämpliga certifikat och göra en distributionsplan för framtida koduppdateringar.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5320c9d7f1ea5ae882c67ee631f5bbafbf97b039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530877"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Förbered att distribuera din IoT Edge-lösning i produktion

När du är redo att ta din IoT Edge-lösning från utveckling till produktion, se till att den är konfigurerad för löpande prestanda.

Informationen i den här artikeln är inte alla lika. För att hjälpa dig att prioritera börjar varje avsnitt med listor som delar upp arbetet i två avsnitt: **viktigt** att slutföra innan du går till produktion eller **användbart** för dig att veta.

## <a name="device-configuration"></a>Enhetskonfiguration

IoT Edge-enheter kan vara allt från en Raspberry Pi till en bärbar dator till en virtuell maskin som körs på en server. Du kan ha åtkomst till enheten antingen fysiskt eller via en virtuell anslutning, eller så kan den vara isolerad under längre tidsperioder. Hur som helst vill du vara säker på att den är konfigurerad för att fungera på rätt sätt.

* **Viktigt**
  * Installera produktionscertifikat
  * Ha en enhetshanteringsplan
  * Använd Moby som containermotor

* **Hjälp**
  * Välj uppströmsprotokoll

### <a name="install-production-certificates"></a>Installera produktionscertifikat

Varje IoT Edge-enhet i produktion behöver ett certifikatutfärdarcertifikat (CA) installerat på den. Ca-certifikatet deklareras sedan till IoT Edge-körningen i filen config.yaml. För utvecklings- och testscenarier skapar IoT Edge-körningen tillfälliga certifikat om inga certifikat deklareras i filen config.yaml. Dessa tillfälliga certifikat upphör dock att gälla efter tre månader och är inte säkra för produktionsscenarier.

Information om hur du förstår rollen för enhetscertifikatutfärdarens certifikat finns i [Hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

Mer information om hur du installerar certifikat på en IoT Edge-enhet och refererar till dem från filen config.yaml finns i [Installera produktionscertifikat på en IoT Edge-enhet](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Ha en enhetshanteringsplan

Innan du sätter någon enhet i produktion bör du veta hur du ska hantera framtida uppdateringar. För en IoT Edge-enhet kan listan över komponenter som ska uppdateras innehålla:

* Enhetens inbyggda programvara
* Operativsystembibliotek
* Containermotor, som Moby
* IoT Kant daemon
* CA-Certifikat

Mer information finns i [Uppdatera IoT Edge-körningen](how-to-update-iot-edge.md). De aktuella metoderna för att uppdatera IoT Edge-demonen kräver fysisk åtkomst eller SSH-åtkomst till IoT Edge-enheten. Om du har många enheter att uppdatera kan du överväga att lägga till uppdateringsstegen i ett skript eller använda ett automatiseringsverktyg som Ansible.

### <a name="use-moby-as-the-container-engine"></a>Använd Moby som containermotor

En behållarmotor är en förutsättning för alla IoT Edge-enheter. Endast moby-motor stöds i produktionen. Andra containermotorer, som Docker, fungerar med IoT Edge och det är ok att använda dessa motorer för utveckling. Moby-motorn kan distribueras om när den används med Azure IoT Edge, och Microsoft tillhandahåller service för den här motorn.

### <a name="choose-upstream-protocol"></a>Välj uppströmsprotokoll

Du kan konfigurera protokollet (som bestämmer vilken port som används) för kommunikation uppströms till IoT Hub för både IoT Edge-agenten och IoT Edge-hubben. Standardprotokollet är AMQP, men du kanske vill ändra det beroende på nätverkskonfigurationen.

De två runtime-modulerna har båda en **UpstreamProtocol-miljövariabel.** De giltiga värdena för variabeln är:

* MQTT
* AMQP
* MQTTWS
* AMQPWS (AMQPWS)

Konfigurera upstreamProtocol-variabeln för IoT Edge-agenten i filen config.yaml på själva enheten. Om din IoT Edge-enhet till exempel ligger bakom en proxyserver som blockerar AMQP-portar kan du behöva konfigurera IoT Edge-agenten så att AMQP över WebSocket (AMQPWS) upprättas för att upprätta den första anslutningen till IoT Hub.

När din IoT Edge-enhet ansluter måste du fortsätta konfigurera upstreamprotocol-variabeln för båda körningsmodulerna i framtida distributioner. Ett exempel på den här processen finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribution

* **Hjälp**
  * Var konsekvent med uppströmsprotokoll
  * Konfigurera värdlagring för systemmoduler
  * Minska minnesutrymmet som används av IoT Edge-hubben
  * Använd inte felsökningsversioner av modulavbildningar

### <a name="be-consistent-with-upstream-protocol"></a>Var konsekvent med uppströmsprotokoll

Om du har konfigurerat IoT Edge-agenten på din IoT Edge-enhet för att använda ett annat protokoll än standard-AMQP, bör du deklarera samma protokoll i alla framtida distributioner. Om din IoT Edge-enhet till exempel ligger bakom en proxyserver som blockerar AMQP-portar har du förmodligen konfigurerat enheten för att ansluta via AMQP över WebSocket (AMQPWS). När du distribuerar moduler till enheten konfigurerar du samma AMQPWS-protokoll för IoT Edge-agenten och IoT Edge-hubben, annars åsidosätter standard-AMQP inställningarna och hindrar dig från att ansluta igen.

Du behöver bara konfigurera miljövariabeln UpstreamProtocol för IoT Edge-agenten och IoT Edge-hubbmodulerna. Eventuella ytterligare moduler antar det protokoll som anges i runtime-modulerna.

Ett exempel på den här processen finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Konfigurera värdlagring för systemmoduler

IoT Edge-hubben och agentmodulerna använder lokal lagring för att upprätthålla tillståndet och aktivera meddelanden mellan moduler, enheter och molnet. För bättre tillförlitlighet och prestanda konfigurerar du systemmodulerna så att de använder lagring i värdfilsystemet.

Mer information finns i [Värdlagring för systemmoduler](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Minska minnesutrymmet som används av IoT Edge-hubben

Om du distribuerar begränsade enheter med begränsat tillgängligt minne kan du konfigurera IoT Edge-hubben så att den körs i en mer strömlinjeformad kapacitet och använder mindre diskutrymme. Dessa konfigurationer begränsar dock prestanda för IoT Edge-hubben, så hitta rätt balans som fungerar för din lösning.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Optimera inte för prestanda på begränsade enheter

IoT Edge-hubben är optimerad för prestanda som standard, så den försöker allokera stora minnesbitar. Den här konfigurationen kan orsaka stabilitetsproblem på mindre enheter som Raspberry Pi. Om du distribuerar enheter med begränsade resurser kanske du vill ange miljövariabeln **OptimizeForPerformance** till **false** på IoT Edge-hubben.

När **OptimizeForPerformance** är inställt på **true**använder MQTT-protokollhuvudet PooledByteBufferAllocator, som har bättre prestanda men allokerar mer minne. Allokeraren fungerar inte bra på 32-bitars operativsystem eller på enheter med lite minne. Dessutom, när optimerad för prestanda, tilldelar RocksDb mer minne för sin roll som lokal lagringsleverantör.

Mer information finns i [Stabilitetsproblem på resursbegränsade enheter](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Inaktivera oanvända protokoll

Ett annat sätt att optimera prestanda för IoT Edge-hubben och minska dess minnesanvändning är att stänga av protokollhuvudena för alla protokoll som du inte använder i din lösning.

Protokollhuvuden konfigureras genom att ange booleska miljövariabler för IoT Edge-hubbmodulen i distributionsmanifesten. De tre variablerna är:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alla tre variabler har *två understreck* och kan ställas in på antingen sant eller falskt.

#### <a name="reduce-storage-time-for-messages"></a>Minska lagringstiden för meddelanden

IoT Edge-hubbmodulen lagrar meddelanden tillfälligt om de inte kan levereras till IoT Hub av någon anledning. Du kan konfigurera hur länge IoT Edge-hubben håller på att olevererade meddelanden innan de upphör att gälla. Om du har minnesproblem på enheten kan du sänka **värdet timeToLiveSecs** i IoT Edge-hubbmodultvillingen.

Standardvärdet för parametern timeToLiveSecs är 7200 sekunder, vilket är två timmar.

### <a name="do-not-use-debug-versions-of-module-images"></a>Använd inte felsökningsversioner av modulavbildningar

När du flyttar från testscenarier till produktionsscenarier, kom ihåg att ta bort felsökningskonfigurationer från distributionsmanifest. Kontrollera att ingen av modulavbildningarna i distributionsmanifesten har ** \.felsökningssuffixet.** Om du har lagt till skapa alternativ för att exponera portar i modulerna för felsökning tar du också bort dessa skapa-alternativ.

## <a name="container-management"></a>Hantering av behållare

* **Viktigt**
  * Hantera åtkomst till behållarregistret
  * Använda taggar för att hantera versioner

### <a name="manage-access-to-your-container-registry"></a>Hantera åtkomst till behållarregistret

Innan du distribuerar moduler för att skapa IoT Edge-enheter ska du kontrollera åtkomsten till behållarregistret så att utomstående inte kan komma åt eller göra ändringar i behållaravbildningarna. Använd ett privat, inte offentligt, behållarregister för att hantera behållaravbildningar.

I självstudierna och annan dokumentation instruerar vi dig att använda samma autentiseringsuppgifter för behållarregister på din IoT Edge-enhet som du använder på din utvecklingsmaskin. Dessa instruktioner är endast avsedda att hjälpa dig att ställa in test- och utvecklingsmiljöer lättare och bör inte följas i ett produktionsscenario. Azure Container Registry rekommenderar [autentisering med tjänsthuvudnamn](../container-registry/container-registry-auth-service-principal.md) när program eller tjänster hämtar behållaravbildningar på ett automatiserat eller på annat sätt obevakat sätt, som IoT Edge-enheter gör. Skapa ett tjänsthuvudnamn med skrivskyddad åtkomst till behållarregistret och ange användarnamn och lösenord i distributionsmanifestet.

### <a name="use-tags-to-manage-versions"></a>Använda taggar för att hantera versioner

En tagg är ett docker-koncept som du kan använda för att skilja mellan versioner av dockerbehållare. Taggar är suffix som **1.0** som hamnar i slutet av en behållarlagringsplats. Till exempel **mcr.microsoft.com/azureiotedge-agent:1.0**. Taggar är föränderliga och kan ändras så att de pekar på en annan behållare när som helst, så ditt team bör komma överens om en konvention att följa när du uppdaterar modulavbildningarna framåt.

Taggar hjälper dig också att genomdriva uppdateringar på dina IoT Edge-enheter. När du skickar en uppdaterad version av en modul till behållarregistret ökar du taggen. Skicka sedan en ny distribution till dina enheter med taggen inkrementerad. Behållarmotorn känner igen den inkrementerade taggen som en ny version och drar den senaste modulversionen ner till enheten.

Ett exempel på en taggkonvention finns i [Uppdatera IoT Edge-körningen](how-to-update-iot-edge.md#understand-iot-edge-tags) för att lära dig hur IoT Edge använder rullande taggar och specifika taggar för att spåra versioner.

## <a name="networking"></a>Nätverk

* **Hjälp**
  * Granska utgående/inkommande konfiguration
  * Tillåt anslutningar från IoT Edge-enheter
  * Konfigurera kommunikation via en proxy

### <a name="review-outboundinbound-configuration"></a>Granska utgående/inkommande konfiguration

Kommunikationskanaler mellan Azure IoT Hub och IoT Edge är alltid konfigurerade för att vara utgående. För de flesta IoT Edge-scenarier är endast tre anslutningar nödvändiga. Behållarmotorn måste ansluta till behållarregistret (eller register) som innehåller modulavbildningarna. IoT Edge-körningen måste ansluta med IoT Hub för att hämta enhetskonfigurationsinformation och för att skicka meddelanden och telemetri. Och om du använder automatisk etablering måste IoT Edge-demonen ansluta till enhetsetableringstjänsten. Mer information finns i [Regler för brandväggs- och portkonfiguration](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Tillåt anslutningar från IoT Edge-enheter

Om nätverkskonfigurationen kräver att du uttryckligen tillåter anslutningar från IoT Edge-enheter läser du följande lista över IoT Edge-komponenter:

* **IoT Edge-agenten** öppnar en beständig AMQP/MQTT-anslutning till IoT Hub, eventuellt via WebSockets.
* **IoT Edge-hubben** öppnar en enda beständig AMQP-anslutning eller flera MQTT-anslutningar till IoT Hub, eventuellt via WebSockets.
* **IoT Edge-demon** gör återkommande HTTPS-anrop till IoT Hub.

I alla tre fallen matchar DNS-namnet mönstret \*.azure-devices.net.

Dessutom **anropar containermotorn** behållarregister via HTTPS. Om du vill hämta behållaravbildningarna för IoT Edge-körningen är DNS-namnet mcr.microsoft.com. Behållarmotorn ansluter till andra register som konfigurerats i distributionen.

Den här checklistan är en utgångspunkt för brandväggsregler:

   | URL\* ( = jokertecken) | Utgående TCP-portar | Användning |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft-behållarregister |
   | global.azure-devices-provisioning.net  | 443 | DPS-åtkomst (valfritt) |
   | \*.azurecr.io | 443 | Personal- och tredjepartscontainerregister |
   | \*.blob.core.windows.net | 443 | Hämta Azure Container Registry-avbildningsdeltat från blob-lagring |
   | \*.azure-devices.net | 5671, 8883, 443 | IoT-hubbåtkomst |
   | \*.docker.io  | 443 | Docker Hub-åtkomst (valfritt) |

Vissa av dessa brandväggsregler ärvs från Azure Container Registry. Mer information finns i [Konfigurera regler för åtkomst till ett Azure-behållarregister bakom en brandvägg](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Konfigurera kommunikation via en proxy

Om dina enheter ska distribueras i ett nätverk som använder en proxyserver måste de kunna kommunicera via proxyn för att nå IoT Hub och behållarregister. Mer information finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Hantering av lösningar

* **Hjälp**
  * Konfigurera loggar och diagnostik
  * Överväg tester och CI/CD-rörledningar

### <a name="set-up-logs-and-diagnostics"></a>Konfigurera loggar och diagnostik

På Linux använder IoT Edge-demonen journaler som standardloggningsdrivrutin. Du kan använda kommandoradsverktyget `journalctl` för att fråga om demonloggarna. I Windows använder IoT Edge-demonen PowerShell-diagnostik. Används `Get-IoTEdgeLog` för att fråga efter loggar från demonen. IoT Edge-moduler använder JSON-drivrutinen för loggning, vilket är standard.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

När du testar en IoT Edge-distribution kan du vanligtvis komma åt dina enheter för att hämta loggar och felsöka. I ett distributionsscenario kanske du inte har det alternativet. Fundera över hur du ska samla in information om dina enheter i produktion. Ett alternativ är att använda en loggningsmodul som samlar in information från de andra modulerna och skickar den till molnet. Ett exempel på en loggningsmodul är [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), eller så kan du designa din egen.

### <a name="place-limits-on-log-size"></a>Placera gränser för loggstorlek

Som standard anger moby-behållarmotorn inte storleksbegränsningar för behållarloggen. Med tiden kan detta leda till att enheten fylls med loggar och på diskutrymme. Tänk på följande alternativ för att förhindra detta:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Alternativ: Ange globala gränser som gäller för alla behållarmoduler

Du kan begränsa storleken på alla behållarloggfiler i alternativen för behållarmotorloggen. I följande exempel anges `json-file` att loggdrivrutinen (rekommenderas) med begränsningar för storlek och antal filer:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Lägg till (eller lägg till) `daemon.json` den här informationen i en fil som heter och placera den på rätt plats för din enhetsplattform.

| Plattform | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Behållarmotorn måste startas om för att ändringarna ska börja gälla.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Alternativ: Justera logginställningar för varje behållarmodul

Du kan göra det i **createOptions** för varje modul. Ett exempel:

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

#### <a name="additional-options-on-linux-systems"></a>Ytterligare alternativ på Linux-system

* Konfigurera behållarmotorn så `systemd` att loggarna skickas till [journalen](https://docs.docker.com/config/containers/logging/journald/) genom att ange `journald` som standardloggningsdrivrutin.

* Ta regelbundet bort gamla loggar från enheten genom att installera ett logrotate-verktyg. Använd följande filspecifikation:

   ```txt
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

### <a name="consider-tests-and-cicd-pipelines"></a>Överväg tester och CI/CD-rörledningar

För det mest effektiva IoT Edge-distributionsscenariot bör du överväga att integrera din produktionsdistribution i dina test- och CI/CD-pipelines. Azure IoT Edge stöder flera CI/CD-plattformar, inklusive Azure DevOps. Mer information finns i [Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [automatisk distribution av IoT Edge](module-deployment-monitoring.md).
* Se hur IoT Edge stöder [kontinuerlig integrering och kontinuerlig distribution](how-to-ci-cd.md).
