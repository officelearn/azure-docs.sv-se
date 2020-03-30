---
title: Felsöka Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan felsöka och lösa problem med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403387"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Felsöka Azure Monitor för behållare

När du konfigurerar övervakning av AKS-klustret (Azure Kubernetes Service) med Azure Monitor för behållare kan det uppstå ett problem som förhindrar datainsamling eller rapporteringsstatus. I den här artikeln beskrivs några vanliga problem och felsökningssteg.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Auktoriseringsfel vid introduktion eller uppdatering
När du aktiverar Azure Monitor för behållare eller uppdaterar ett kluster för att stödja insamling av mått kan du få ett felmeddelande som liknar följande - *Klienten <användarens identitet> med objekt-ID "<användarens objectId>" har inte behörighet att utföra åtgärden "Microsoft.Authorization/roleAssignments/write" över omfånget*

Under introduktions- eller uppdateringsprocessen försöker du bevilja tilldelningen **av publisher-rollen för övervakningsmått** på klusterresursen. Användaren som initierar processen för att aktivera Azure Monitor för behållare eller uppdateringen för att stödja insamlingen av mått måste ha åtkomst till **Microsoft.Authorization/roleAssignments/write-behörigheten** för AKS-klusterresursomfattningen. Endast medlemmar i de inbyggda rollerna **ägare** och **användaråtkomstadministratör** beviljas åtkomst till den här behörigheten. Om dina säkerhetsprinciper kräver att du tilldelar detaljerade behörigheter rekommenderar vi att du visar [anpassade roller](../../role-based-access-control/custom-roles.md) och tilldelar dem som behöver dem. 

Du kan också manuellt bevilja den här rollen från Azure-portalen genom att utföra följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. Skriv **Kubernetes**i listan över resurser . När du börjar skriva filtreras listan baserat på det du skriver. Välj **Azure Kubernetes**.
3. Välj en i listan över Kubernetes-kluster i listan.
2. Klicka på **Åtkomstkontroll (IAM)** på menyn till vänster .
3. Välj **+ Lägg till** om du vill lägga till en rolltilldelning och välj rollen **Övervakningsmått utgivare** och under rutan **Välj** skriver **DU AKS** för att filtrera resultaten på bara klustertjänstens huvudnamn som definierats i prenumerationen. Välj den i listan som är specifik för det klustret.
4. Välj **Spara** om du vill slutföra tilldelningen av rollen. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor för behållare är aktiverat men rapporterar ingen information
Om Azure Monitor for containers har aktiverats och konfigurerats, men du inte kan visa statusinformation eller om inga resultat returneras från en loggfråga, diagnostiserar du problemet genom att följa dessa steg: 

1. Kontrollera agentens status genom att köra kommandot: 

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata ska likna följande, vilket indikerar att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Kontrollera distributionsstatus med agentversion *06072018* eller senare med kommandot:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna följande exempel, som anger att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Kontrollera status för podden för att kontrollera att den körs med kommandot:`kubectl get pods --namespace=kube-system`

    Utdata ska likna följande exempel med *statusen Kör* för omsagensen:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Kolla agentloggarna. När den containeriserade agenten distribueras körs en snabbkontroll genom att köra OMI-kommandon och visar versionen av agenten och leverantören. 

5. Om du vill kontrollera att agenten har distribuerats kör du kommandot:`kubectl logs omsagent-484hw --namespace=kube-system`

    Statusen bör likna följande exempel:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Felmeddelanden

Tabellen nedan sammanfattar kända fel som kan uppstå när du använder Azure Monitor för behållare.

| Felmeddelanden  | Åtgärd |  
| ---- | --- |  
| Felmeddelande`No data for selected filters`  | Det kan ta lite tid att upprätta övervakningsdataflödet för kluster som skapats nyligen. Tillåt minst 10 till 15 minuter för att data ska visas för klustret. |   
| Felmeddelande`Error retrieving data` | Medan Azure Kubernetes Service-klustret konfigureras för hälso- och prestandaövervakning upprättas en anslutning mellan klustret och Azure Log Analytics-arbetsytan. En Log Analytics-arbetsyta används för att lagra alla övervakningsdata för klustret. Det här felet kan uppstå när logganalysarbetsytan har tagits bort. Kontrollera om arbetsytan har tagits bort och om den var det måste du återaktivera övervakningen av klustret med Azure Monitor för behållare och ange en befintlig eller skapa en ny arbetsyta. Om du vill aktivera igen måste du [inaktivera](container-insights-optout.md) övervakning för klustret och [aktivera](container-insights-enable-new-cluster.md) Azure Monitor för behållare igen. |  
| `Error retrieving data`efter att ha lagt till Azure Monitor för behållare via az aks cli | När du `az aks cli`aktiverar övervakning med kan det hända att Azure Monitor för behållare inte har distribuerats korrekt. Kontrollera om lösningen har distribuerats. Det gör du genom att gå till arbetsytan Log Analytics och se om lösningen är tillgänglig genom att välja **Lösningar** från fönstret till vänster. För att lösa problemet måste du distribuera om lösningen genom att följa instruktionerna om [hur du distribuerar Azure Monitor för behållare](container-insights-onboard.md) |  

För att diagnostisera problemet har vi tillhandahållit ett felsökningsskript som finns [här](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor för behållare agent ReplicaSet Pods är inte schemalagda i icke-Azure Kubernetes-kluster

Azure Monitor för behållare agent ReplicaSet Pods har ett beroende av följande nodväljare på arbetar-(eller agent) noderna för schemaläggningen:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Om dina arbetsnoder inte har bifogade nodetiketter schemaläggs inte agentreplikuppsättningspodden. Se [Kubernetes tilldela etikettväljare](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) för instruktioner om hur du bifogar etiketten.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Prestandadiagram visar inte CPU eller minne av noder och behållare i ett kluster som inte är Azure

Azure Monitor för behållare agent Pods använder cAdvisor-slutpunkten på nodagenten för att samla in prestandamåtten. Kontrollera att den behållaren på noden `cAdvisor port: 10255` är konfigurerad så att den kan öppnas på alla noder i klustret för att samla in prestandamått.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Klustret utanför Azure Kubernetes visas inte i Azure Monitor för behållare

Om du vill visa icke-Azure Kubernetes-klustret i Azure Monitor för behållare krävs läsåtkomst på arbetsytan Log Analytics som stöder den här insikten och på behållarstatistiklösningsresursen **ContainerInsights (*arbetsytan*).**

## <a name="next-steps"></a>Nästa steg

Med övervakning aktiverad för att samla in hälsomått för både AKS-klusternoder och poddar är dessa hälsomått tillgängliga i Azure-portalen. Mer information om hur du använder Azure Monitor för behållare finns i [Visa Azure Kubernetes Service health](container-insights-analyze.md).
