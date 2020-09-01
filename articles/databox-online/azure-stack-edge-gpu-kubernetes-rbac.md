---
title: Förstå Kubernetes-rollbaserade Access Control på Azure Stack Edge-enhet | Microsoft Docs
description: Beskriver hur Kubernetes-rollbaserade Access Control sker på en Azure Stack Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 310fde15a850214aa1741c9cb587c0edcf570a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085381"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-device"></a>Kubernetes rollbaserade Access Control på din Azure Stack Edge-enhet


När du konfigurerar Compute-rollen på din Azure Stack Edge-enhet skapas ett Kubernetes-kluster. Du kan använda Kubernetes rollbaserad åtkomst kontroll (RBAC) för att begränsa åtkomsten till kluster resurserna på enheten.

De här artiklarna ger en översikt över RBAC-systemet som tillhandahålls av Kubernetes, och hur är Kubernetes RBAC implementerad på din Azure Stack Edge-enhet. 

## <a name="rbac-for-kubernetes"></a>RBAC för Kubernetes

Med Kubernetes RBAC kan du tilldela användare eller grupper av användare behörighet att göra saker som att skapa eller ändra resurser, eller Visa loggar från att köra program arbets belastningar. Dessa behörigheter kan begränsas till ett enda namn område eller beviljas i hela klustret. 

När du konfigurerar Kubernetes-klustret skapas en enskild användare som motsvarar det här klustret och kallas för kluster administratörs användaren.  En `kubeconfig` fil är kopplad till kluster administratörs användaren. `kubeconfig`Filen är en textfil som innehåller all konfigurations information som krävs för att ansluta till klustret för att autentisera användaren. 

### <a name="namespaces-and-users"></a>Namn områden och användare

I den verkliga världen är det viktigt att dela upp klustret i flera namn områden. 

- **Flera användare**: om du har flera användare kommer flera namn områden att tillåta dessa användare att distribuera sina program och tjänster i sina egna namn rymder i isoleringen från varandra. 
- **Enskild användare**: även om det finns en enskild användare, tillåter flera namn områden att användaren kör flera versioner av programmen i samma Kubernetes-kluster.

### <a name="roles-and-rolebindings"></a>Roller och RoleBindings

Kubernetes har begreppet roll-och roll bindning som gör att du kan ge behörigheter till användare eller resurser på en namn områdes nivå och på kluster nivå. 

- **Roller**: du kan definiera behörigheter till användare som en **roll** och sedan använda **roller** för att bevilja behörigheter inom ett namn område. 
- **RoleBindings**: när du har definierat rollerna kan du använda **RoleBindings** för att tilldela roller för en viss namnrymd. 

Med den här metoden kan du logiskt särskilja ett enda Kubernetes-kluster, där användare bara kan komma åt program resurserna i sitt tilldelade namn område. 


## <a name="rbac-on-azure-stack-edge"></a>RBAC på Azure Stack Edge

I den aktuella implementeringen av RBAC gör Azure Stack Edge att du kan vidta följande åtgärder från en begränsad PowerShell-körnings utrymme:

- Skapa namn områden.  
- Skapa ytterligare användare.
- Ge dig administratörs åtkomst till de namn områden som du har skapat. Tänk på att du inte har åtkomst till rollen kluster administratör eller en vy över resurserna i klustret.
- Hämta `kubeconfig` filen med information för att få åtkomst till Kubernetes-klustret.


Azure Stack Edge-enheten har flera system namn rymder och du kan skapa användar namn områden med `kubeconfig` filer för att komma åt dessa namn områden. Användarna har fullständig kontroll över dessa namn områden och kan skapa eller ändra användare eller bevilja användare åtkomst. Endast kluster administratören har fullständig åtkomst till system namn rymder och resurser i hela klustret. En `aseuser` har skrivskyddad åtkomst till system namn rymder.

Här är ett diagram som illustrerar implementeringen av RBAC på Azure Stack Edge-enhet.

![RBAC på Azure Stack Edge-enhet](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

I det här diagrammet har Alice, Bob och Klas endast åtkomst till tilldelade användar namn områden, som i det här fallet är `ns1` , `ns2` `ns3` respektive. I dessa namn områden har de administratörs åtkomst. Kluster administratören å andra sidan har administratörs behörighet för system namn rymder och resurser i hela klustret.

Du kan använda `kubectl` kommandon för att skapa namn områden, tilldela användare, tilldela användare eller ladda `kubeconfig` ned filer. Här är ett arbets flöde med hög nivå:

1. Skapa ett namn område och en användare.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Skapa en användare.  

    `New-HcsKubernetesUser -UserName`  

3. Koppla namn området till den användare som du skapade.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Spara användar konfigurationen till `C:\Users\<username>\.kube` .  

5. Installera `kubectl` och börja distribuera program till `kubectl` . 

Detaljerade steg-för-steg-anvisningar finns i [komma åt Kubernetes-kluster via kuebctl på din Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


När du arbetar med namn områden och användare på dina Azure Stack Edge-enheter gäller följande varningar:

- Du får inte utföra några åtgärder, till exempel skapa användare, bevilja eller återkalla namn områdes åtkomst till användare, för alla system namn rymder. Exempel på system namn rymder är,,,, `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` , `kube-public` . System namn rymder innehåller också de namn områden som är reserverade för distributions typer som `iotedge` (IoT Edge namnrymd) och `azure-arc` (Azure Arc-namnrymd).
- Du kan skapa användar namn rymder och inom dessa namn områden, skapa ytterligare användare och bevilja eller återkalla namn områdes åtkomst till dessa användare.
- Du får inte skapa några namn rymder med namn som är identiska med de för alla system namn rymder. Namnen på system namn rymder är reserverade.  
- Du får inte skapa några användar namn rymder med namn som redan används av andra användar namn områden. Om du till exempel har skapat en `test-ns` som du har skapat kan du inte skapa en annan `test-ns` namnrymd.
- Du får inte skapa användare med namn som redan har reserver ATS. Till exempel `aseuser` är en reserverad kluster administratör och kan inte användas.

Mer information om Azure Stack Edge-namnområden finns i [namn områdes typer](azure-stack-edge-gpu-kubernetes-workload-management.md#namespaces-types).


<!--To deploy applications on an Azure Stack Edge device, use the following :
 
- First, you will use the PowerShell runspace to create a user, create a namespace, and grant user access to that namespace.
- Next, you will use the Azure Stack Edge resource in the Azure portal to create persistent volumes using either static or dynamic provisioning for the stateful applications that you will deploy.
- Finally, you will use the services to expose applications externally and within the Kubernetes cluster.-->

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en användare, skapar ett namn område och beviljar användar åtkomst till namn området finns i [komma åt ett Kubernetes-kluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

