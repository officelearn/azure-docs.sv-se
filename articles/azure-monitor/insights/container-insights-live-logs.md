---
title: Visa Azure Monitor för behållare loggar i realtid | Microsoft Docs
description: I den här artikeln beskrivs real tids visning av behållar loggar (STDOUT/STDERR) och händelser utan att använda kubectl med Azure Monitor för behållare.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 968ee4c8bb5d7e09ef3c345c46f6c7b839e0e25a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990043"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Så här visar du loggar och händelser i real tid (för hands version)
Azure Monitor for containers innehåller en funktion, som för närvarande finns i för hands version, som ger en live-vy till dina AKS (Azure Kubernetes service)-behållar loggar (STDOUT/STDERR) och händelser utan att behöva köra kubectl-kommandon. När du väljer något av alternativen visas ett nytt fönster under tabellen prestanda data i vyn **noder**, **kontrollanter**och **behållare** . Den visar live-loggning och händelser som genererats av behållar motorn för att ytterligare hjälpa till med fel sökning av problem i real tid.

>[!NOTE]
>**Deltagar** åtkomst till kluster resursen krävs för att den här funktionen ska fungera.
>

Live-loggar stöder tre olika metoder för att kontrol lera åtkomsten till loggarna:

1. AKS utan Kubernetes RBAC-auktorisering som aktiverats
2. AKS aktiverad med Kubernetes RBAC-auktorisering
3. AKS aktiverat med Azure Active Directory (AD) SAML-baserad enkel inloggning

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-kluster utan aktiverad RBAC
 
Du behöver inte följa dessa steg om du har ett Kubernetes-kluster som inte är konfigurerad med Kubernetes RBAC-auktorisering eller integrerat med Azure AD enkel inloggning. Eftersom Kubernetes-auktorisering använder Kube-API: t krävs Läs behörighet.

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

2. Om du konfigurerar det för första gången använder du kluster regel bindningen genom att köra följande kommando: `kubectl create -f LogReaderRBAC.yaml`. Om du tidigare har aktiverat stöd för för hands versionen av Live-loggar innan vi introducerade Live-händelseloggen, uppdatera konfigurationen genom att `kubectl apply -f LogReaderRBAC.yaml`köra följande kommando:.

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurera AKS med Azure Active Directory

AKS kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. Om du konfigurerar det för första gången kan du läsa [integrera Azure Active Directory med Azure Kubernetes-tjänsten](../../aks/azure-ad-integration.md). Under stegen för att skapa [klient programmet](../../aks/azure-ad-integration.md#create-the-client-application)anger du följande:

- **Omdirigerings-URI (valfritt)** : Detta är en **webb** program typ och bas-URL-värdet ska `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`vara.
- När du har registrerat programmet väljer du **autentisering** i rutan till vänster på sidan **Översikt** . På sidan **autentisering** under **Avancerade inställningar** kan du **implicit tilldela åtkomsttoken** och **ID-token** och sedan spara ändringarna.

>[!NOTE]
>Att konfigurera autentisering med Azure Active Directory för enkel inloggning kan bara utföras under den första distributionen av ett nytt AKS-kluster. Du kan inte konfigurera enkel inloggning på för ett AKS-kluster som redan har distribuerats.
  
>[!IMPORTANT]
>Om du har konfigurerat om Azure AD för användarautentisering med hjälp av den uppdaterade URI: n rensar du webbläsarens cacheminne för att se till att den uppdaterade autentiseringstoken hämtas och tillämpas.   

## <a name="view-live-logs-and-events"></a>Visa Live-loggar och händelser

Du kan Visa logg händelser i real tid när de genereras av behållar motorn från vyn **noder**, **kontrollanter**och **behållare** . I fönstret Egenskaper väljer du alternativet **Visa live data (förhands granskning)** och ett fönster visas under tabellen prestanda data där du kan Visa logg och händelser i en kontinuerlig data ström.

![Fönstret Egenskaper för Node Visa Live logs-alternativ](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Logg-och händelse meddelanden är begränsade utifrån vilken resurs typ som valts i vyn.

| Visa | Resurstyp | Logg eller händelse | Data som presenteras |
|------|---------------|--------------|----------------|
| Noder | Nod | Händelse | När en nod är markerad, filtreras och visas inte Kubernetes händelser för hela klustret. Rutans rubrik visar namnet på klustret. |
| Noder | Pod | Händelse | När ett pod är markerat filtreras händelser till sitt namn område. Rutans rubrik visar namn området för pod. | 
| Kontrollanter | Pod | Händelse | När ett pod är markerat filtreras händelser till sitt namn område. Rutans rubrik visar namn området för pod. |
| Kontrollanter | Domänkontrollant | Händelse | När en styrenhet väljs filtreras de till sitt namn område. Rutans namn visar namn området för kontrollanten. |
| Noder/styrenheter/behållare | Container | Logs | Rutans rubrik visar namnet på pod som behållaren är grupperad med. |

Om AKS-klustret har konfigurerats med enkel inloggning med AAD, uppmanas du att autentisera vid första användning under den webbläsarsessionen. Välj ditt konto och slutföra autentisering med Azure.  

Efter autentisering, visas live loggfönstret i avsnittet längst ned i den mellersta rutan. Om hämtning av status för indikatorn visas en grön bockmarkering som är längst till höger i fönstret, innebär det att den kan hämta data.
    
  ![Fönstret Hämta realtidsdata loggar](./media/container-insights-live-logs/live-logs-pane-01.png)  

I Sök fältet kan du filtrera efter nyckel ord för att markera texten i loggen eller händelsen, och i Sök fältet längst till höger visas hur många resultat som matchar filtret.

  ![Live loggar fönstret filter, exempel](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

När du visar händelser kan du dessutom begränsa resultaten med hjälp av **filtret** Pill som finns till höger om Sök fältet. Beroende på vilken resurs du har valt visar Pill en pod, ett namn område eller ett kluster att välja från.  

Om du vill pausa autorullningen och kontrol lera beteendet i fönstret och låta dig manuellt bläddra igenom de nya data, klickar du  på rullnings alternativet. Om du vill aktivera autorullning igen klickar du bara  på rullnings alternativet igen. Du kan också pausa hämtning av logg-eller händelse data genom att klicka på alternativet **pausa** och när du är redo att återuppta klickar du bara på **spela upp**.  

![Live loggar fönstret pausa live-vyn](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Du kan gå till Azure Monitor loggar om du vill se historiken över behållar loggar genom att välja **Visa behållar loggar** i list rutan **i Analytics**.

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md).

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.
