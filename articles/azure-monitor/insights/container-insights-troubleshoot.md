---
title: Hur du felsöker Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du kan felsöka och lösa problem med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403387"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Felsökning Azure Monitor för behållare

När du konfigurerar övervakning av ditt Azure Kubernetes Service (AKS)-kluster med Azure Monitor för behållare kan du kan stöta på ett problem som förhindrar insamling av data eller rapporterar status. Den här artikeln beskriver några vanliga problem och felsökning.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Auktoriseringsfel vid onboarding eller uppdaterings åtgärd
När du aktiverar Azure Monitor för behållare eller uppdaterar ett kluster för att stödja insamling av mått, kan det hända att du får ett fel som liknar följande – *klienten < användarens identitet > "med objekt-ID" < användarens objectId > "har inte behörighet att utföra åtgärden" Microsoft. Authorization/roleAssignments/Write "över omfattning*

Under onboarding-eller uppdaterings processen görs försök att tilldela **övervaknings mått utgivar** roll tilldelningen på kluster resursen. Användaren som initierar processen för att aktivera Azure Monitor för behållare eller uppdateringen för att stödja insamling av mått måste ha åtkomst till **Microsoft. Authorization/roleAssignments/Write-** behörigheten för kluster resurs omfånget i AKS. Endast medlemmar i de inbyggda rollerna **ägare** och **användar åtkomst administratör** beviljas åtkomst till den här behörigheten. Om dina säkerhets principer kräver att du tilldelar detaljerade nivå behörigheter rekommenderar vi att du visar [anpassade roller](../../role-based-access-control/custom-roles.md) och tilldelar dem till de användare som behöver den. 

Du kan också bevilja rollen manuellt från Azure Portalen genom att utföra följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. Skriv **Kubernetes**i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Azure-Kubernetes**.
3. I listan över Kubernetes-kluster väljer du en i listan.
2. I den vänstra menyn klickar du på **åtkomst kontroll (IAM)** .
3. Välj **+ Lägg** till för att lägga till en roll tilldelning och välj **utgivar rollen övervaknings mått** och välj **AKS** i rutan **Välj** typ för att filtrera resultaten för bara de kluster tjänstens huvud namn som definierats i prenumerationen. Välj den från listan som är speciell för klustret.
4. Välj **spara** Slutför tilldela rollen. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor för behållare har aktiverats men inte rapporterar någon information
Om Azure Monitor för behållare har Aktiver ATS och kon figurer ATS, men du inte kan visa statusinformation eller inga resultat returneras från en logg fråga, diagnostiserar du problemet genom att följa dessa steg: 

1. Kontrollera statusen för agenten genom att köra kommandot: 

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Kontrollera distributionsstatusen med agentversion *06072018* eller senare med kommandot:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna följande exempel, som anger att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Kontrollera status för poden att verifiera att den körs med hjälp av kommandot: `kubectl get pods --namespace=kube-system`

    Utdata bör likna följande exempel med statusen *kör* för omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Loggarna för agenten. När behållare agenten distribueras, kör en snabb kontroll genom att köra OMI kommandon och visar versionen av agenten och providern. 

5. Verifiera att agenten har distribuerats genom att köra kommandot: `kubectl logs omsagent-484hw --namespace=kube-system`

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

I tabellen nedan sammanfattas kända fel kan uppstå när du använder Azure Monitor för behållare.

| Felmeddelanden  | Åtgärd |  
| ---- | --- |  
| Felmeddelande `No data for selected filters`  | Det kan ta lite tid att upprätta övervakning dataflöde för nyligen skapade kluster. Tillåt minst 10 till 15 minuter innan data visas för klustret. |   
| Felmeddelande `Error retrieving data` | Även om Azure Kubernetes service-klustret konfigurerar för hälso-och prestanda övervakning upprättas en anslutning mellan klustret och Azure Log Analytics-arbetsytan. En Log Analytics-arbetsyta används för att lagra alla övervakningsdata för klustret. Det här felet kan inträffa när din Log Analytics-arbetsyta har tagits bort. Kontrol lera att arbets ytan har tagits bort och om den var måste du aktivera övervakning av klustret igen med Azure Monitor för behållare och ange en befintlig eller skapa en ny arbets yta. Om du vill aktivera igen måste du [inaktivera](container-insights-optout.md) övervakning av klustret och [Aktivera](container-insights-enable-new-cluster.md) Azure Monitor för behållare igen. |  
| `Error retrieving data` När du lägger till Azure Monitor för behållare via az aks cli | När du aktiverar övervakning med hjälp av `az aks cli`kan Azure Monitor för behållare inte distribueras korrekt. Kontrol lera om lösningen har distribuerats. Gör detta genom att gå till Log Analytics-arbetsytan och se om lösningen är tillgänglig genom att välja **lösningar** från fönstret till vänster. För att lösa problemet måste du distribuera lösningen igen genom att följa anvisningarna [så här distribuerar du Azure Monitor för behållare](container-insights-onboard.md) |  

För att diagnosticera problemet, har vi lagt till ett felsökning skript som är tillgängliga [här](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor för behållare agent ReplicaSet poddar har inte schemalagts för icke-Azure Kubernetes-kluster

Azure Monitor for containers agent ReplicaSet poddar har ett beroende av följande Node-väljare på Worker-noder (eller agent) för schemaläggning:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Om dina arbetsnoder inte har några Node-etiketter, kommer agenten ReplicaSet poddar inte att bli schemalagd. Läs mer i [Kubernetes tilldela etikett väljare](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) för instruktioner om hur du lägger till etiketten.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Prestanda diagram visar inte CPU eller minne för noder och behållare i ett icke-Azure-kluster

Azure Monitor for containers agent poddar använder cAdvisor-slutpunkten på Node-agenten för att samla in prestanda måtten. Kontrol lera att den behållar agenten på noden är konfigurerad att tillåta att `cAdvisor port: 10255` öppnas på alla noder i klustret för att samla in prestanda mått.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Icke-Azure Kubernetes-kluster visas inte i Azure Monitor för behållare

Om du vill visa icke-Azure Kubernetes-kluster i Azure Monitor för behållare, krävs Läs behörighet på arbets ytan Log Analytics som stöder den här insikten och på ContainerInsights för container Insights **-lösning (*arbets yta*)** .

## <a name="next-steps"></a>Nästa steg

Med övervakning aktiverat för att avbilda hälsomått för både noder i AKS och poddar är dessa health-mått tillgängliga i Azure-portalen. Läs hur du använder Azure Monitor för behållare i [visa Azure Kubernetes Service health](container-insights-analyze.md).
