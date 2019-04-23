---
title: Visa Azure Monitor för behållare loggar i realtid | Microsoft Docs
description: Den här artikeln beskriver realtid vy över behållarloggarna (stdout/stderr) utan att använda kubectl med Azure Monitor för behållare.
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
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: c8baa4d2355adf99ce188d632ac50901db29a758
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997707"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Visa behållarens loggar realtid med Azure Monitor för behållare (förhandsgranskning)
Den här funktionen, som finns för närvarande i förhandsversion, tillhandahåller en vy i realtid i Azure Kubernetes Service (AKS)-behållarloggarna (stdout/stderr) utan att behöva köra kubectl-kommandon. När du väljer det här alternativet kan nya rutan visas nedan datatabell för behållare prestanda på den **behållare** vy.  Den visar live loggning som genererats av motorn för behållaren för bättre hjälp vid felsökning av problem i realtid. **Deltagare** åtkomst till klusterresursen krävs för den här funktionen ska fungera.

Live-loggar har stöd för tre olika metoder för att styra åtkomsten till loggarna:

1. AKS utan Kubernetes RBAC-auktorisering som aktiverats 
2. AKS aktiverad med Kubernetes RBAC-auktorisering
3. AKS aktiverad med Azure Active Directory (AD) SAML utifrån enkel inloggning 

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
        resources: ["pods/log"] 
        verbs: ["get"] 
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

2. Skapa kluster regeln bindningen genom att köra följande kommando: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurera AKS med Azure Active Directory
AKS kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. Om du konfigurerar detta för första gången, se [integrera Azure Active Directory med Azure Kubernetes Service](../../aks/azure-ad-integration.md). Under stegen för att skapa den [klientprogram](../../aks/azure-ad-integration.md#create-client-application) och ange den **omdirigerings-URI**, du måste lägga till en annan URI i listan `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>Konfigurera autentisering med Azure Active Directory för enkel inloggning kan bara utföras under första distributionen av ett nytt AKS-kluster. Du kan inte konfigurera enkel inloggning på för ett AKS-kluster som redan har distribuerats.  
> 

## <a name="view-live-logs"></a>Visa live loggar
När du visar **behållare**, kan du **Zobrazit Kontejner loggar** eller **visa live behållarloggarna**.  När du väljer **visa live behållarloggarna**, visas ett nytt fönster nedan datatabell för behållare prestanda och som visar live-loggning som genererats av motorn för behållaren för bättre hjälp vid felsökning av problem i realtid.  
1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Från den **Microsoft Azure** menyn och välj **övervakaren** och välj sedan **behållare**.  
3. Välj en behållare i listan under den **övervakas behållare** vy.  
4. Välj den **behållare** vy och på egenskapspanelen för en vald behållare, länken **visa live behållarloggarna** visas.  
5. Om AKS-klustret har konfigurerats med enkel inloggning med AAD, uppmanas du att autentisera vid första användning under den webbläsarsessionen. Välj ditt konto och slutföra autentisering med Azure.  

Efter autentisering, visas live loggfönstret i avsnittet längst ned i den mellersta rutan. Om hämtning av status för indikatorn visas en grön bockmarkering som är längst till höger i fönstret, innebär det att den kan hämta data.
    
  ![Fönstret Hämta realtidsdata loggar](./media/container-insights-live-logs/live-logs-pane-01.png)  

Du kan filtrera efter nyckelord att markera texten i loggen i sökfältet.   

  ![Live loggar fönstret filter, exempel](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Om du vill inaktivera automatisk rullning och styra beteendet för fönstret så att du kan manuellt Bläddra igenom nya loggdata läsa, klickar du på den **Bläddra** alternativet.  Om du vill återaktivera automatisk rullning, klickar du på den **Bläddra** igen.  Du kan också pausa hämtning av loggdata genom att klicka på den **pausa** och när du är redo att återuppta helt enkelt klicka på **spela upp**.  

![Live loggar fönstret pausa live-vyn](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Nästa steg
- Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md).
- Visa [logga fråga exempel](container-insights-log-search.md#search-logs-to-analyze-data) att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisera och analysera dina kluster.
