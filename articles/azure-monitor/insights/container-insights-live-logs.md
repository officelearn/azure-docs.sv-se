---
title: Visa Azure Monitor för behållare loggar i real tid | Microsoft Docs
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
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389777"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Så här visar du loggar och händelser i real tid (för hands version)
Azure Monitor for containers innehåller en funktion, som för närvarande finns i för hands version, som ger en live-vy till dina AKS (Azure Kubernetes service)-behållar loggar (STDOUT/STDERR) och händelser utan att behöva köra kubectl-kommandon. När du väljer något av alternativen visas ett nytt fönster under tabellen prestanda data i vyn **noder**, **kontrollanter**och **behållare** . Den visar live-loggning och händelser som genererats av behållar motorn för att ytterligare hjälpa till med fel sökning av problem i real tid.

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure Kina. Den är för närvarande inte tillgänglig i Azure amerikanska myndigheter.

>[!NOTE]
>**Användar rollen Azure Kubernetes service Cluster** måste ha åtkomst till kluster resursen för att den här funktionen ska fungera. [Lär dig mer om användar rollen Azure Kubernetes-kluster](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

Live-loggar stöder tre olika metoder för att kontrol lera åtkomsten till loggarna:

1. AKS utan Kubernetes RBAC-auktorisering aktiverat
2. AKS aktive rad med Kubernetes RBAC-auktorisering
3. AKS aktiverat med Azure Active Directory (AD) SAML-baserad enkel inloggning

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-kluster utan RBAC-aktiverat
 
Om du har ett Kubernetes-kluster som inte har kon figurer ATS med Kubernetes RBAC-auktorisering eller integrerat med Azure AD enkel inloggning, behöver du inte följa dessa steg. Eftersom Kubernetes-auktorisering använder Kube-API: t krävs Läs behörighet.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC-auktorisering
Om du har aktiverat Kubernetes RBAC-auktorisering måste du tillämpa kluster roll bindning. Följande exempel visar hur du konfigurerar kluster roll bindning från den här yaml-konfigurations mal len. 

1. Kopiera och klistra in yaml-filen och spara den som LogReaderRBAC. yaml.  

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

2. Om du konfigurerar det för första gången tillämpar du kluster regel bindningen genom att köra följande kommando: `kubectl create -f LogReaderRBAC.yaml`. Om du tidigare har aktiverat stöd för för hands versionen av Live-loggar innan vi introducerade Live-händelseloggen, för att uppdatera konfigurationen, kör du följande kommando: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurera AKS med Azure Active Directory

AKS kan konfigureras för att använda Azure Active Directory (AD) för användarautentisering. Om du konfigurerar det för första gången kan du läsa [integrera Azure Active Directory med Azure Kubernetes-tjänsten](../../aks/azure-ad-integration.md). Under stegen för att skapa [klient programmet](../../aks/azure-ad-integration.md#create-the-client-application)anger du följande:

-  **Omdirigerings-URI**: två **webb** program typer måste skapas. Det första bas-URL-värdet ska vara `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och det andra bas-URL-värdet ska vara `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- När du har registrerat programmet väljer du **autentisering** i rutan till vänster på sidan **Översikt** . På sidan **autentisering** under **Avancerade inställningar** kan du **implicit tilldela åtkomsttoken** och **ID-token** och sedan spara ändringarna.

>[!NOTE]
>Om du använder den här funktionen i region region i Azure bör det första bas-URL-värdet vara `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och det andra bas-URL-värdet ska vara `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

>[!NOTE]
>Att konfigurera autentisering med Azure Active Directory för enkel inloggning kan bara utföras under den första distributionen av ett nytt AKS-kluster. Det går inte att konfigurera enkel inloggning för ett AKS-kluster som redan har distribuerats.
  
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
| Domänkontrollanter | Pod | Händelse | När ett pod är markerat filtreras händelser till sitt namn område. Rutans rubrik visar namn området för pod. |
| Domänkontrollanter | Domänkontrollant | Händelse | När en styrenhet väljs filtreras de till sitt namn område. Rutans namn visar namn området för kontrollanten. |
| Noder/styrenheter/behållare | Container | Loggar | Rutans rubrik visar namnet på pod som behållaren är grupperad med. |

Om AKS-klustret har kon figurer ATS med SSO med hjälp av AAD uppmanas du att autentisera vid första användningen under webbläsarsessionen. Välj ditt konto och slutför autentisering med Azure.  

När du har autentiserat visas live-logg fönstret i den nedre delen av fönstret i mitten. Om status indikatorn för hämtningen visar en grön bock markering, som är längst till höger i fönstret, innebär det att den kan hämta data.
    
  ![Data hämtades från Live-loggfiler](./media/container-insights-live-logs/live-logs-pane-01.png)  

I Sök fältet kan du filtrera efter nyckel ord för att markera texten i loggen eller händelsen, och i Sök fältet längst till höger visas hur många resultat som matchar filtret.

  ![Filter exempel för Live-loggfiler](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

När du visar händelser kan du dessutom begränsa resultaten med hjälp av **filtret** Pill som finns till höger om Sök fältet. Beroende på vilken resurs du har valt visar Pill en pod, ett namn område eller ett kluster att välja från.  

Om du vill pausa autorullningen och kontrol lera beteendet i fönstret och låta dig manuellt bläddra igenom de nya data, klickar du på **rullnings** alternativet. Om du vill aktivera autorullning igen klickar du bara på **rullnings** alternativet igen. Du kan också pausa hämtning av logg-eller händelse data genom att klicka på alternativet **pausa** och när du är redo att återuppta klickar du bara på **spela upp**.  

![Fönstret Live-loggar pausa Live-vyn](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Du kan gå till Azure Monitor loggar om du vill se historiken över behållar loggar genom att välja **Visa behållar loggar** i list rutan **i Analytics**.

## <a name="next-steps"></a>Nästa steg

- Om du vill fortsätta lära dig hur du använder Azure Monitor och övervakar andra aspekter av ditt AKS-kluster kan du läsa mer i [Visa Azure Kubernetes service Health](container-insights-analyze.md).

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.
