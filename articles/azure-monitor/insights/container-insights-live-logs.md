---
title: Visa Azure Monitor för behållare loggar i realtid | Microsoft Docs
description: Den här artikeln beskriver realtidsvy av behållarloggarna (stdout/stderr) och händelser utan att använda kubectl med Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: magoedte
ms.openlocfilehash: 8d4cc5e46066ad2f18d596d0484f62f478b4cc23
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514331"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Visa loggar och händelser i realtid (förhandsversion)
Azure Monitor för behållare innehåller en funktion som för närvarande är i förhandsversion, som tillhandahåller en livevy över din Azure Kubernetes Service (AKS) behållarloggarna (stdout/stderr) och händelser utan att behöva köra kubectl-kommandon. När du väljer något av alternativen, visas ett nytt fönster nedan datatabell prestanda på den **noder**, **domänkontrollanter**, och **behållare** vy. Den visar live loggning och händelser som genererats av motorn för behållaren för bättre hjälp vid felsökning av problem i realtid. 

>[!NOTE]
>**Deltagare** åtkomst till klusterresursen krävs för den här funktionen ska fungera.
>

Live loggar har stöd för tre olika metoder för att styra åtkomsten till loggarna:

1. AKS utan Kubernetes RBAC-auktorisering som aktiverats 
2. AKS aktiverad med Kubernetes RBAC-auktorisering
3. Aktiverat med Azure Active Directory (AD) SAML-baserad enkel inloggning på AKS 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-kluster utan aktiverad RBAC
 
Du behöver inte följa dessa steg om du har ett Kubernetes-kluster som inte är konfigurerad med Kubernetes RBAC-auktorisering eller integrerat med Azure AD enkel inloggning. Eftersom Kubernetes auktorisering använder kube-api, krävs skrivskyddad behörighet.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC-auktorisering
Om du har aktiverat Kubernetes RBAC-auktorisering kan behöver du tillämpa kluster roll bindning. Följande exempel visar hur du konfigurerar kluster roll bindningen från den här mallen för konfiguration av yaml. 

1. Kopiera och klistra in yaml-fil och spara den som LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. Om du konfigurerar den för första gången måste du skapa kluster regeln bindningen genom att köra följande kommando: `kubectl create -f LogReaderRBAC.yaml`. Om du tidigare har aktiverat stöd för live loggar Förhandsgranska innan vi introducerat live händelseloggar, om du vill uppdatera din konfiguration, kör du följande kommando: `kubectl apply -f LogReaderRBAC.yml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurera AKS med Azure Active Directory
AKS kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. Om du konfigurerar den för första gången, se [integrera Azure Active Directory med Azure Kubernetes Service](../../aks/azure-ad-integration.md). Under stegen för att skapa den [klientprogram](../../aks/azure-ad-integration.md#create-client-application), måste du ange två **omdirigerings-URI** poster. Två URI: er är:

- https://ininprodeusuxbase.microsoft.com/*
- https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html  

>[!NOTE]
>Konfigurera autentisering med Azure Active Directory för enkel inloggning kan bara utföras under första distributionen av ett nytt AKS-kluster. Du kan inte konfigurera enkel inloggning på för ett AKS-kluster som redan har distribuerats. Du måste konfigurera autentisering från **appregistrering (äldre)** alternativet i Azure AD för att stödja användning av jokertecken i URI: N och när du lägger till den i listan, registrera den som en **interna** app.
> 

## <a name="view-live-logs-and-events"></a>Visa live loggar och händelser

Du kan visa realtidsloggen händelser som de skapas av motorn behållare från den **noder**, **domänkontrollanter**, och **behållare** vy. Från egenskapsfönstret du väljer **visa realtidsdata (förhandsversion)** alternativet och en ruta visas nedan prestanda tabellen där du kan visa loggen och händelser i en ständig ström. 

![Visa live loggar alternativ för noden egenskaper](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Logg- och händelsedata meddelanden är begränsade baserat på vilken resurstyp väljs i vyn.

| Visa | Resurstyp | Loggen eller händelse | Informationen som presenteras |
|------|---------------|--------------|----------------|
| Noder | Nod | Händelse | När en nod väljs filtreras inte händelser och visa hela Kubernetes-händelser. I fönstret visar namnet på klustret. |
| Noder | Pod | Händelse | När en pod väljs filtreras händelser till dess namnområde. I fönstret visar namnområdet för din pod. | 
| Kontrollanter | Pod | Händelse | När en pod väljs filtreras händelser till dess namnområde. I fönstret visar namnområdet för din pod. |
| Kontrollanter | domänkontrollant | Händelse | När en domänkontrollant väljs filtreras händelser till dess namnområde. I fönstret visar namnområdet för kontrollenheten. |
| Domänkontrollanter-noder/behållare | Container | Loggar | I fönstret visar namnet på din pod behållaren är grupperade med. |

Om AKS-klustret har konfigurerats med enkel inloggning med AAD, uppmanas du att autentisera vid första användning under den webbläsarsessionen. Välj ditt konto och slutföra autentisering med Azure.  

Efter autentisering, visas live loggfönstret i avsnittet längst ned i den mellersta rutan. Om hämtning av status för indikatorn visas en grön bockmarkering som är längst till höger i fönstret, innebär det att den kan hämta data.
    
  ![Fönstret Hämta realtidsdata loggar](./media/container-insights-live-logs/live-logs-pane-01.png)  

Du kan filtrera efter nyckelord att markera texten i loggen eller händelse och i sökfältet längst till höger i sökfältet, visar hur många resultat som matchar ut filtret.   

  ![Live loggar fönstret filter, exempel](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Om du vill inaktivera automatisk rullning och styra beteendet för fönstret så att du kan bläddra manuellt för den nya data som läses, klickar du på den **Bläddra** alternativet. Om du vill återaktivera automatisk rullning, klickar du på den **Bläddra** igen. Du kan också pausa hämtning av loggen eller händelse data genom att klicka på den **pausa** och när du är redo att återuppta helt enkelt klicka på **spela upp**.  

![Live loggar fönstret pausa live-vyn](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Du kan gå till Azure Monitor-loggar att se historiska behållarloggarna genom att välja **visa behållarloggarna** från den nedrullningsbara listan **visa i analys**.

## <a name="next-steps"></a>Nästa steg
- Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md).
- Visa [logga fråga exempel](container-insights-log-search.md#search-logs-to-analyze-data) att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisera och analysera dina kluster.
