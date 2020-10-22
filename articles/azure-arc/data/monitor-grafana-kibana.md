---
title: Visa loggar och mått med hjälp av Kibana och Grafana
description: Visa loggar och mått med hjälp av Kibana och Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360385"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Visa loggar och mått med hjälp av Kibana och Grafana

Kibana- och Grafana-webbinstrumentpaneler tillhandahålls för att få insyn och klarhet i de Kubernetes-namnrymder som används av Azure Arc-aktiverade datatjänster.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Hämta klustrets IP-adress

För att få åtkomst till instrument panelerna måste du hämta klustrets IP-adress. Metoden för att hämta rätt IP-adress varierar beroende på hur du har valt att distribuera Kubernetes. Gå igenom alternativen nedan om du vill hitta rätt namn.

### <a name="azure-virtual-machine"></a>Virtuell Azure-dator

Använd följande kommando för att hämta den offentliga IP-adressen:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm-kluster

Använd följande kommando för att hämta klustrets IP-adress:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS eller annat belastnings Utjämnings kluster

Om du vill övervaka din miljö i AKS eller andra belastningsutjämnade kluster måste du hämta IP-adressen för hanterings-proxyn. Använd det här kommandot för att hämta den **externa IP-** adressen:

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Ytterligare brand Väggs konfiguration

Du kanske upptäcker att du måste öppna portarna i brand väggen för att få åtkomst till Kibana-och Grafana-slutpunkterna.

Nedan visas ett exempel på hur du gör detta för en virtuell Azure-dator. Du måste göra detta om du har distribuerat Kubernetes med hjälp av skriptet.

Stegen nedan visar hur du skapar en NSG-regel för Kibana-och Grafana-slutpunkterna:

### <a name="find-the-name-of-the-nsg"></a>Hitta NSG-namnet

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Lägg till NSG-regeln

När du har namnet på NSG kan du lägga till en regel med hjälp av följande kommando:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Övervaka Azure SQL Managed instances på Azure-bågen

Nu när du har IP-adressen och exponerat portarna bör du kunna komma åt Grafana och Kibana.

> [!NOTE]
>  När du uppmanas att ange ett användar namn och lösen ord anger du det användar namn och lösen ord som du angav vid den tidpunkt då du skapade data styrenheten för Azure-bågen.

> [!NOTE]
>  Du uppmanas att ange en certifikat varning eftersom de certifikat som används i för hands versionen är självsignerade certifikat.

Använd följande URL-mönster för att få åtkomst till instrument paneler för loggning och övervakning för Azure SQL-hanterad instans:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Relevanta instrumentpaneler är:

* "Mått för Azure SQL-hanterad instans"
* "Mått för värd nod"
* "Värden för poddar-mått"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Övervaka Azure Database for PostgreSQL-skalning – Azure-båge

Använd följande URL-mönster för att komma åt instrument paneler för loggning och övervakning för PostgreSQL-storskalig:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Relevanta instrumentpaneler är:

* "Postgres-mått"
* "Postgres tabell mått"
* "Mått för värd nod"
* "Värden för poddar-mått"

## <a name="next-steps"></a>Nästa steg
- Försök [att ladda upp mått och loggar till Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Läs om Grafana:
   - [Komma igång](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana fundament ALS](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana-självstudier](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Läs om Kibana
   - [Introduktion](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana-guide](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introduktion till instrument panels detalj nivå med data visualiseringar i Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Så här skapar du Kibana-instrumentpaneler](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

