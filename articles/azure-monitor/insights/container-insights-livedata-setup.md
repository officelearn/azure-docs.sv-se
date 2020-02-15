---
title: Konfigurera Azure Monitor för behållare Live-data (för hands version) | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar real tids visningen av behållar loggar (STDOUT/STDERR) och händelser utan att använda kubectl med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 91f035b98a57fd9a37203cc48b3cc5d685967a13
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251795"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Så här ställer du in funktionen Live data (för hands version)

Om du vill visa real tids data (för hands version) med Azure Monitor för behållare från Azure Kubernetes service (AKS)-kluster, måste du konfigurera autentisering för att ge åtkomst till dina Kubernetes-data. Med den här säkerhets konfigurationen får du åtkomst till dina data i real tid via Kubernetes-API: et direkt i Azure Portal.

Den här funktionen stöder följande metoder för att kontrol lera åtkomsten till loggar, händelser och mått:

- AKS utan Kubernetes RBAC-auktorisering som aktiverats
- AKS aktiverad med Kubernetes RBAC-auktorisering
    - AKS som kon figurer ATS med kluster rollen binding  **[clusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS aktiverat med Azure Active Directory (AD) SAML-baserad enkel inloggning

Dessa instruktioner kräver både administrativ åtkomst till ditt Kubernetes-kluster och om du konfigurerar att använda Azure Active Directory (AD) för användarautentisering, administrativ åtkomst till Azure AD.  

Den här artikeln förklarar hur du konfigurerar autentisering för att kontrol lera åtkomsten till funktionen Live data (för hands version) från klustret:

- Rollbaserad åtkomst kontroll (RBAC) aktiverat AKS-kluster
- Azure Active Directory integrerat AKS-kluster. 

>[!NOTE]
>AKS-kluster som är aktiverade som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. Den här funktionen använder direkt åtkomst till Kubernetes-API: et via en proxyserver från din webbläsare. Om du aktiverar nätverks säkerhet för att blockera Kubernetes-API: et från den här proxyn blockeras trafiken. 

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure Kina. Den är för närvarande inte tillgänglig i Azure amerikanska myndigheter.

## <a name="authentication-model"></a>Autentiserings modell

Funktionerna för Live data (för hands version) använder Kubernetes-API: et, som är identiskt med kommando rads verktyget `kubectl`. Kubernetes API-slutpunkter använder ett självsignerat certifikat som webbläsaren inte kan verifiera. Den här funktionen använder en intern proxy för att validera certifikatet med AKS-tjänsten, vilket säkerställer att trafiken är betrodd.

Azure Portal uppmanas du att verifiera dina inloggnings uppgifter för ett Azure Active Directory kluster och omdirigera dig till klient registrerings konfigurationen när klustret skapas (och omkonfigureras i den här artikeln). Det här beteendet liknar den autentiseringsprocess som krävs av `kubectl`. 

>[!NOTE]
>Auktorisering till klustret hanteras av Kubernetes och säkerhets modellen som den har kon figurer ATS med. Användare som har åtkomst till den här funktionen kräver behörighet att ladda ned Kubernetes-konfigurationen (*kubeconfig*), ungefär som att köra `az aks get-credentials -n {your cluster name} -g {your resource group}`. Den här konfigurations filen innehåller auktoriserings-och autentiseringstoken för **användar rollen Azure Kubernetes service-kluster**, om Azure RBAC-aktiverade och AKS-kluster utan RBAC-auktorisering har Aktiver ATS. Den innehåller information om Azure AD-och klient registrerings information när AKS har Aktiver ATS med Azure Active Directory (AD) SAML-baserad enkel inloggning.

>[!IMPORTANT]
>Användare av de här funktionerna kräver [Azure Kubernetes-kluster användar rollen](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) i klustret för att kunna hämta `kubeconfig` och använda den här funktionen. Användare behöver **inte** deltagar åtkomst till klustret för att använda den här funktionen. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Använda clusterMonitoringUser med RBAC-aktiverade kluster

För att eliminera behovet av att tillämpa ytterligare konfigurations ändringar för att tillåta Kubernetes- **clusterUser** åtkomst till funktionen Live data (för hands version) när du har [aktiverat RBAC](#configure-kubernetes-rbac-authorization) -auktorisering har AKS lagt till en ny Kubernetes kluster roll bindning som kallas **clusterMonitoringUser**. Den här kluster roll bindningen har alla nödvändiga behörigheter som är färdiga att komma åt Kubernetes-API: et och slut punkterna för att använda funktionen Live data (för hands version). 

För att kunna använda funktionen Live data (för hands version) med den nya användaren måste du vara medlem i rollen [deltagare](../../role-based-access-control/built-in-roles.md#contributor) i AKS-klusterresursen. Azure Monitor för behållare konfigureras för att autentisera med den här användaren som standard när den är aktive rad. Om clusterMonitoringUser-rolltjänsten inte finns i ett kluster används **clusterUser** för autentisering i stället.

AKS frigjorde den här nya roll bindningen i januari 2020, vilket innebär att kluster som skapats före januari 2020 inte har den. Om du har ett kluster som har skapats före januari 2020, kan nya **clusterMonitoringUser** läggas till i ett befintligt kluster genom att utföra en åtgärds åtgärd i klustret eller utföra andra åtgärder på klustret Tha utför en åtgärd i klustret, till exempel uppdatering av kluster versionen.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-kluster utan aktiverad RBAC

Du behöver inte följa dessa steg om du har ett Kubernetes-kluster som inte är konfigurerad med Kubernetes RBAC-auktorisering eller integrerat med Azure AD enkel inloggning. Detta beror på att du har administratörs behörighet som standard i en icke-RBAC-konfiguration.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurera Kubernetes RBAC-auktorisering

När du aktiverar Kubernetes RBAC-auktorisering används två användare: **clusterUser** och **clusterAdmin** för att få åtkomst till Kubernetes-API: et. Detta liknar att köra `az aks get-credentials -n {cluster_name} -g {rg_name}` utan alternativet administration. Det innebär att **clusterUser** måste beviljas åtkomst till slut punkterna i Kubernetes-API: et.

Följande exempel visar hur du konfigurerar kluster roll bindningen från den här mallen för konfiguration av yaml.

1. Kopiera och klistra in yaml-fil och spara den som LogReaderRBAC.yaml.  

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

2. Kör följande kommando för att uppdatera konfigurationen: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Om du har tillämpat en tidigare version av `LogReaderRBAC.yaml`-filen på klustret uppdaterar du den genom att kopiera och klistra in den nya koden som visas i steg 1 ovan. kör sedan kommandot som visas i steg 2 för att tillämpa det på klustret.

## <a name="configure-ad-integrated-authentication"></a>Konfigurera AD-integrerad autentisering 

Ett AKS-kluster som kon figurer ATS för att använda Azure Active Directory (AD) för användarautentisering använder inloggnings uppgifterna för den person som har åtkomst till den här funktionen. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av din Azure AD-autentiseringstoken.

Azure AD client Registration måste konfigureras på nytt för att tillåta att Azure Portal omdirigerar behörighets sidor som en betrodd omdirigerings-URL. Användare från Azure AD beviljas sedan åtkomst direkt till samma Kubernetes API-slutpunkter via **ClusterRoles** och **ClusterRoleBindings**. 

Mer information om avancerade säkerhets inställningar i Kubernetes finns i Kubernetes- [dokumentationen](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Om du skapar ett nytt RBAC-aktiverat kluster går du till [integrera Azure Active Directory med Azure Kubernetes-tjänsten](../../aks/azure-ad-integration.md) och följer stegen för att konfigurera Azure AD-autentisering. Under stegen för att skapa klient programmet visar en anteckning i avsnittet de två omdirigerings-URL: er som du måste skapa för att Azure Monitor för behållare som matchar de som anges i steg 3 nedan.

### <a name="client-registration-reconfiguration"></a>Omkonfiguration av klient registrering

1. Leta upp klient registreringen för ditt Kubernetes-kluster i Azure AD under **Azure Active Directory > Appregistreringar** i Azure Portal.

2. Välj **autentisering** i det vänstra fönstret. 

3. Lägg till två omdirigerings-URL: er till den här listan som **webb** program typer. Det första bas-URL-värdet ska vara `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och det andra bas-URL-värdet ska vara `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Om du använder den här funktionen i Azure Kina ska det första bas-URL-värdet vara `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och det andra bas-URL-värdet ska vara `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. När du har registrerat URL: erna för omdirigering väljer du alternativet **åtkomsttoken** och **ID-token** under **implicit tilldelning**och sparar sedan ändringarna.

>[!NOTE]
>Att konfigurera autentisering med Azure Active Directory för enkel inloggning kan bara utföras under den första distributionen av ett nytt AKS-kluster. Du kan inte konfigurera enkel inloggning på för ett AKS-kluster som redan har distribuerats.
  
>[!IMPORTANT]
>Om du har konfigurerat om Azure AD för användarautentisering med hjälp av den uppdaterade URI: n rensar du webbläsarens cacheminne för att se till att den uppdaterade autentiseringstoken hämtas och tillämpas.

## <a name="grant-permission"></a>Bevilja behörighet

Varje Azure AD-konto måste beviljas behörighet till lämpliga API: er i Kubernetes för att få åtkomst till funktionen Live data (för hands version). Stegen för att bevilja Azure Active Directory-kontot liknar de steg som beskrivs i avsnittet [KUBERNETES RBAC-autentisering](#configure-kubernetes-rbac-authorization) . Innan du tillämpar yaml på klustret ersätter du **clusterUser** under **ClusterRoleBinding** med önskad användare. 

>[!IMPORTANT]
>Om användaren som du beviljar RBAC-bindningen för finns i samma Azure AD-klient tilldelar du behörigheter baserat på userPrincipalName. Om användaren finns i en annan Azure AD-klient frågar du efter och använder egenskapen objectId.

Mer hjälp om hur du konfigurerar AKS- **ClusterRoleBinding**finns i [skapa RBAC-bindning](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat autentisering kan du Visa [mått](container-insights-livedata-metrics.md), [distributioner](container-insights-livedata-deployments.md)och [händelser och loggar](container-insights-livedata-overview.md) i real tid från klustret.
