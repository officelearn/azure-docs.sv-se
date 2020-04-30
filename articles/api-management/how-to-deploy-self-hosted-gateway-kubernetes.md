---
title: Distribuera egen värd-Gateway till Kubernetes | Microsoft Docs
description: Lär dig hur du distribuerar den egna värdbaserade Gateway-komponenten i Azure API Management till Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205110"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Distribuera egen värd-Gateway till Kubernetes

Den här artikeln beskriver stegen för att distribuera den lokala gateway-komponenten i Azure API Management till ett Kubernetes-kluster.

## <a name="prerequisites"></a>Krav

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa ett Kubernetes-kluster.
> [!TIP]
> [Kluster med en nod](https://kubernetes.io/docs/setup/#learning-environment) fungerar bra i utvecklings-och utvärderings syfte. Använd [Kubernetes Certified](https://kubernetes.io/partners/#conformance) multi-Node-kluster lokalt eller i molnet för produktions arbets belastningar.
- [Etablera en lokal gateway-resurs i din API Management-instans](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Distribuera till Kubernetes

1. Välj **gateways** från under **distribution och infrastruktur**.
2. Välj den lokal gateway-resurs som du vill distribuera.
3. Välj **distribution**.
4. Observera att en åtkomsttoken i text rutan **token** genererades automatiskt när du använder standard värdena för **förfallo datum** och **hemlig nyckel** . Om det behövs väljer du önskade värden i någon av eller båda kontrollerna för att generera en ny token.
5. Välj fliken **Kubernetes** under **distributions skript**.
6. Klicka på fil länken **<Gateway-name>. yml** och ladda ned yaml-filen.
7. Välj **Kopiera** -ikonen i det nedre högra hörnet **av text rutan** för att spara `kubectl` kommandona i Urklipp.
8. Klistra in kommandon i terminalfönstret (eller kommando fönstret). Det första kommandot skapar en Kubernetes-hemlighet som innehåller åtkomsttoken som genererades i steg 4. Andra kommandot tillämpar konfigurations filen som hämtades i steg 6 till Kubernetes-klustret och förväntar sig att filen finns i den aktuella katalogen.
9. Kör kommandona för att skapa de nödvändiga Kubernetes-objekten i [standard namn området](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) och starta Pod för egen värd från [behållar avbildningen](https://aka.ms/apim/sputnik/dhub) som hämtats från Microsoft container Registry.
10. Kör kommandot som visas nedan för att kontrol lera om distributionen har slutförts. Observera att det kan ta lite tid för alla objekt att skapas och för att Pod ska initieras.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Kör kommandot som visas nedan för att kontrol lera om tjänsten har skapats. Observera att tjänstens IP-adresser och portar är olika.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Gå tillbaka till Azure Portal och välj **Översikt**.
13. **Status** som visar en grön bock ikon följt av antalet noder som matchar antalet repliker som anges i yaml-filen, bekräftar att distribuerad lokal gateway-poddar kommunicerar med den API Management tjänsten och har en vanlig "pulsslag".

![Gateway-status](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Kör <code>kubectl logs deployment/<gateway-name></code> kommando för att visa loggar från ett slumpmässigt valt Pod om det finns fler än ett.
> Kör <code>kubectl logs -h</code> en fullständig uppsättning kommando alternativ, t. ex. om du vill visa loggar för en speciell POD eller behållare.

## <a name="production-deployment-considerations"></a>Överväganden vid produktions distribution

### <a name="access-token"></a>Åtkomsttoken
Utan en giltig Gateway för lokal åtkomst-token kan inte komma åt och hämta konfigurationen från slut punkten för konfigurations data för den associerade API Managements tjänsten. Åtkomsttoken kan vara giltig i högst 30 dagar. Det måste återskapas och klustret konfigureras med en ny token antingen manuellt eller via Automation innan det går ut. Använd den här hanterings-API- [åtgärden](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) för att generera en ny token vid automatisk uppdatering av token. Följ den här [länken](https://kubernetes.io/docs/concepts/configuration/secret) om du vill ha information om hur du hanterar Kubernetes hemligheter.

### <a name="namespace"></a>Namnområde
Kubernetes- [namnområden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) hjälper till att dela upp ett enskilt kluster mellan flera team, projekt eller program. Namn rymder ger en omfattning för resurser och namn och kan associeras med en resurs kvot och principer för åtkomst kontroll.
Kommandon som finns i Azure Portal skapa egna gateway-resurser i **standard** namn området som skapas automatiskt, som finns i alla kluster och inte kan tas bort.
Överväg att [skapa och distribuera](https://kubernetesbyexample.com/ns/) egen värd-gateway i ett separat namn område i produktionen.

### <a name="number-of-replicas"></a>Antal repliker
Minimalt antal repliker som är lämpliga i produktion är två.

Som standard distribueras den egna gatewayen med en **RollingUpdate** distributions [strategi](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy). Granska standardvärdena och Överväg att uttryckligen ange [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) -och [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) -fält, särskilt när du använder ett högt antal repliker.

### <a name="container-resources"></a>Container resurser
Som standard anger YAML-filen i Azure Portal inte behållar resurs förfrågningar.

Det är omöjligt att förutse och rekommendera mängden processor-och minnes resurser per behållare och antalet repliker som krävs för att stödja en viss arbets belastning på grund av många faktorer i spelet, t. ex.:

- Speciell maskin vara som klustret körs på
- Förekomst och typ av virtualisering
- Antal och frekvens för samtidiga klient anslutningar
- Begär ande frekvens
- Typ av och antal konfigurerade principer
- Nytto Last storlek och om nytto laster buffras eller strömmas
- Svars tid för backend-tjänsten

Vi rekommenderar att du ställer in resurs förfrågan till 2 kärnor och 2 GiB som en start punkt, utför ett belastnings test och skalar upp/ned eller ner/i baserat på resultaten.

### <a name="container-image-tag"></a>Etikett för container avbildning
YAML-filen som tillhandahölls i Azure Portal använder den **senaste** taggen som alltid refererar till den senaste versionen av den egna värdbaserade Gateway-behållar avbildningen.

Överväg att använda en speciell versions tagg i produktion för att undvika oavsiktlig uppgradering till en nyare version.

Följ den här [länken](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) om du vill se en fullständig lista över tillgängliga taggar.

### <a name="dns-policy"></a>DNS-princip
DNS-namnmatchning spelar en viktig roll i lokal gateways möjlighet att ansluta till beroenden i Azure och skicka API-anrop till backend-tjänster.

YAML-filen som anges i Azure Portal tillämpar standard principen för [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) som gör att namn matchnings begär Anden som inte matchas av kluster-DNS kan vidarebefordras till den överordnade DNS-servern som ärvts från noden.

Följ den här [länken](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) om du vill veta mer om namn matchning i Kubernetes och Överväg att anpassa [DNS-principen](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) eller D[ns-konfigurationen](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) efter behov.

### <a name="configuration-backup"></a>Säkerhets kopiering av konfiguration
Följ den här [länken](self-hosted-gateway-overview.md#connectivity-to-azure) om du vill veta mer om Gateway-beteende med egen värd i närvaro av ett tillfälligt anslutnings avbrott i Azure.
Konfigurera den lokala lagrings volymen för den lokala gateway-behållaren, så att den kan spara en säkerhets kopia av den senaste nedladdade konfigurationen och, om anslutningen är avstängd, använda den vid omstart. Volymens monterings Sök väg <code>/apim/config</code>måste vara. Se ett exempel [här](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Följ den här [länken](https://kubernetes.io/docs/concepts/storage/volumes/)om du vill veta mer om lagring i Kubernetes.

### <a name="local-logs-and-metrics"></a>Lokala loggar och mått
Lokal gateway skickar telemetri till [Azure Monitor](api-management-howto-use-azure-monitor.md) och [Azure Application insikter](api-management-howto-app-insights.md) per konfigurations inställningar i den associerade API Managements tjänsten.
När [anslutningen till Azure](self-hosted-gateway-overview.md#connectivity-to-azure) är tillfälligt bruten avbryts flödet av telemetri till Azure och data går förlorade under tiden för avbrottet.
Överväg att [Konfigurera lokal övervakning](how-to-configure-local-metrics-logs.md) för att se till att API-trafiken iakttas och förhindra att telemetri går förlorade under Azures anslutnings avbrott.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)