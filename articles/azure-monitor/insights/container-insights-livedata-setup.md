---
title: Konfigurera Azure Monitor för behållare Live Data (förhandsversion) | Microsoft-dokument
description: I den här artikeln beskrivs hur du ställer in realtidsvyn för behållarloggar (stdout/stderr) och händelser utan att använda kubectl med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275378"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Konfigurera funktionen Live Data (preview)

Om du vill visa Live Data (förhandsversion) med Azure Monitor för behållare från AKS-kluster (Azure Kubernetes Service) måste du konfigurera autentisering för att bevilja behörighet att komma åt dina Kubernetes-data. Den här säkerhetskonfigurationen ger realtidsåtkomst till dina data via Kubernetes API direkt i Azure-portalen.

Den här funktionen stöder följande metoder för att styra åtkomsten till loggar, händelser och mått:

- AKS utan Kubernetes RBAC-auktorisering aktiverad
- AKS aktiverat med Kubernetes RBAC-auktorisering
    - AKS konfigurerat med **klusterrollbindningsklustretÖvervakaeAnvändare [clusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS aktiverat med Azure Active Directory (AD) SAML-baserad enkel inloggning

Dessa instruktioner kräver både administrativ åtkomst till kubernetes-klustret och om du konfigurerar för att använda Azure Active Directory (AD) för användarautentisering, administrativ åtkomst till Azure AD.  

I den här artikeln beskrivs hur du konfigurerar autentisering för att styra åtkomsten till livedatafunktionen (förhandsversion) från klustret:

- Rollbaserad åtkomstkontroll (RBAC) aktiverat AKS-kluster
- Azure Active Directory-integrerat AKS-kluster. 

>[!NOTE]
>AKS-kluster som aktiveras som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. Den här funktionen är beroende av direkt åtkomst till Kubernetes API via en proxyserver från din webbläsare. Om du aktiverar nätverkssäkerhet för att blockera Kubernetes-API:et från den här proxyn blockeras den här trafiken. 

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure China. Den är för närvarande inte tillgänglig i Azure US Government.

## <a name="authentication-model"></a>Autentiseringsmodell

Funktionerna Live Data (förhandsversion) använder Kubernetes `kubectl` API, identiskt med kommandoradsverktyget. Kubernetes API-slutpunkter använder ett självsignerat certifikat som din webbläsare inte kan validera. Den här funktionen använder en intern proxy för att validera certifikatet med AKS-tjänsten, vilket säkerställer att trafiken är betrodd.

Azure-portalen uppmanar dig att validera dina inloggningsuppgifter för ett Azure Active Directory-kluster och omdirigera dig till klientregistreringskonfigurationen under klusterskapande (och konfigureras om i den här artikeln). Det här problemet liknar den `kubectl`autentiseringsprocess som krävs av . 

>[!NOTE]
>Auktorisering till klustret hanteras av Kubernetes och den säkerhetsmodell som den är konfigurerad med. Användare som använder den här funktionen kräver behörighet att hämta Kubernetes-konfigurationen *(kubeconfig),* som liknar körning `az aks get-credentials -n {your cluster name} -g {your resource group}`. Den här konfigurationsfilen innehåller auktoriserings- och autentiseringstoken för **Azure Kubernetes Service Cluster User Role**, när det gäller Azure RBAC-aktiverade och AKS-kluster utan RBAC-auktorisering aktiverad. Den innehåller information om Azure AD och klientregistreringsinformation när AKS är aktiverat med Azure Active Directory (AD) SAML-baserad en inloggning.

>[!IMPORTANT]
>Användare av de här funktionerna kräver [Azure Kubernetes Cluster User Role](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) till klustret för att hämta och använda den `kubeconfig` här funktionen. Användare kräver **inte** deltagare åtkomst till klustret för att använda den här funktionen. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Använda clusterMonitoringUser med RBAC-aktiverade kluster

För att eliminera behovet av att tillämpa ytterligare konfigurationsändringar för att ge Kubernetes **användarrollbindningskluster** åtkomst till funktionen Live Data (förhandsversion) efter att rbac-auktorisering [aktiverats,](#configure-kubernetes-rbac-authorization) har AKS lagt till en ny Kubernetes-klusterrollbindning som kallas **clusterMonitoringUser**. Den här klusterrollbindningen har alla nödvändiga behörigheter direkt för att komma åt Kubernetes API och slutpunkterna för att använda funktionen Live Data (förhandsversion).

För att kunna använda funktionen Live Data (förhandsversion) med den här nya användaren måste du vara medlem i [rollen Deltagare](../../role-based-access-control/built-in-roles.md#contributor) på AKS-klusterresursen. Azure Monitor för behållare, när det är aktiverat, är konfigurerat för att autentisera med den här användaren som standard. Om rollbindningen clusterMonitoringUser inte finns i ett kluster används **clusterUser** för autentisering i stället.

AKS släppte den nya rollbindningen i januari 2020, så kluster som skapats före januari 2020 har den inte. Om du har ett kluster som skapades före januari 2020 kan det nya **clusterMonitoringUser** läggas till i ett befintligt kluster genom att utföra en PUT-åtgärd i klustret eller utföra någon annan åtgärd i klustret tha utför en PUT-åtgärd på klustret, till exempel uppdatera klusterversionen.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes kluster utan RBAC aktiverat

Om du har ett Kubernetes-kluster som inte är konfigurerat med Kubernetes RBAC-auktorisering eller integrerat med Azure AD-enkel inloggning, behöver du inte följa dessa steg. Detta beror på att du har administratörsbehörighet som standard i en icke-RBAC-konfiguration.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurera Kubernetes RBAC-auktorisering

När du aktiverar Kubernetes RBAC-auktorisering används två användare: **clusterUser** och **clusterAdmin** för att komma åt Kubernetes API. Detta liknar att `az aks get-credentials -n {cluster_name} -g {rg_name}` köra utan det administrativa alternativet. Det innebär att **clusterUser** måste beviljas åtkomst till slutpunkterna i Kubernetes API.

Följande exempelsteg visar hur du konfigurerar klusterrollbindning från den här yaml-konfigurationsmallen.

1. Kopiera och klistra in yaml-filen och spara den som LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
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

2. Om du vill uppdatera konfigurationen `kubectl apply -f LogReaderRBAC.yaml`kör du följande kommando: .

>[!NOTE] 
> Om du har använt en `LogReaderRBAC.yaml` tidigare version av filen i klustret uppdaterar du den genom att kopiera och klistra in den nya koden som visas i steg 1 ovan och kör sedan kommandot som visas i steg 2 för att tillämpa den på klustret.

## <a name="configure-ad-integrated-authentication"></a>Konfigurera AD-integrerad autentisering 

Ett AKS-kluster som konfigurerats för att använda Azure Active Directory (AD) för användarautentisering använder inloggningsuppgifterna för den person som använder den här funktionen. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av din Azure AD-autentiseringstoken.

Azure AD-klientregistrering måste konfigureras om så att Azure-portalen kan omdirigera auktoriseringssidor som en betrodd omdirigerings-URL. Användare från Azure AD beviljas sedan åtkomst direkt till samma Kubernetes API-slutpunkter via **ClusterRoles** och **ClusterRoleBindings**. 

Mer information om avancerade säkerhetsinställningar i Kubernetes finns i [Kubernetes dokumentation](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Om du skapar ett nytt RBAC-aktiverat kluster läser du [Integrera Azure Active Directory med Azure Kubernetes Service](../../aks/azure-ad-integration.md) och följer stegen för att konfigurera Azure AD-autentisering. Under stegen för att skapa klientprogrammet belyser en anteckning i det avsnittet de två omdirigeringsadresser som du behöver skapa för Azure Monitor för behållare som matchar dem som anges i steg 3 nedan.

### <a name="client-registration-reconfiguration"></a>Omkonfigurering av klientregistrering

1. Leta reda på klientregistreringen för kubernetes-klustret i Azure AD under **Azure Active Directory > Appregistreringar** i Azure-portalen.

2. Välj **Autentisering** i den vänstra rutan. 

3. Lägg till två omdirigeringsadresser i den här listan som **webbprogramtyper.** Det första bas-URL-värdet ska vara `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`det andra bas-URL-värdet ska vara .

    >[!NOTE]
    >Om du använder den här funktionen i Azure Kina `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` ska det första bas-URL-värdet vara och det andra bas-URL-värdet ska vara `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. När du har registrerat omdirigeringsadresserna väljer du alternativen **Access-token** och **ID-token** under **Implicit beviljande**och sparar sedan ändringarna.

>[!NOTE]
>Konfigurera autentisering med Azure Active Directory för enkel inloggning kan endast utföras under den första distributionen av ett nytt AKS-kluster. Du kan inte konfigurera enkel inloggning för ett AKS-kluster som redan har distribuerats.
  
>[!IMPORTANT]
>Om du konfigurerade om Azure AD för användarautentisering med den uppdaterade URI:n rensar du webbläsarens cacheminne för att säkerställa att den uppdaterade autentiseringstoken hämtas och tillämpas.

## <a name="grant-permission"></a>Bevilja behörighet

Varje Azure AD-konto måste beviljas behörighet till lämpliga API:er i Kubernetes för att komma åt livedata (förhandsversion). Stegen för att bevilja Azure Active Directory-kontot liknar stegen som beskrivs i avsnittet [Kubernetes RBAC-autentisering.](#configure-kubernetes-rbac-authorization) Innan du använder yaml-konfigurationsmallen på klustret ersätter du **clusterUser** under **ClusterRoleBinding** med önskad användare. 

>[!IMPORTANT]
>Om användaren som du beviljar RBAC-bindningen för finns i samma Azure AD-klient, tilldela behörigheter baserat på userPrincipalName. Om användaren finns i en annan Azure AD-klient frågar du efter och använder egenskapen objectId.

Mer information om hur du konfigurerar **AKS-klusterklusteretRoleBinding**finns i [Skapa RBAC-bindning](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerad autentisering kan du visa [mått,](container-insights-livedata-metrics.md) [distributioner](container-insights-livedata-deployments.md)och [händelser och loggar](container-insights-livedata-overview.md) i realtid från klustret.
