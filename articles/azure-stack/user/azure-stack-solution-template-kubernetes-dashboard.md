---
title: Komma åt Kubernetes-instrumentpanelen i Azure Stack | Microsoft Docs
description: Lär dig att komma åt Kubernetes-instrumentpanelen i Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: fa663cce10a39ef60a0efa5838b81b257fd02b46
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255945"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Komma åt Kubernetes-instrumentpanelen i Azure Stack 

*Gäller för Integrerade Azure Stack-system och Azure Stack Development Kit* 
> [!Note]   
> Kubernetes på Azure Stack är en förhandsversion. Azure Stack-frånkopplade scenariot stöds inte för närvarande av förhandsversionen. 

Kubernetes innehåller en webbinstrumentpanel som du kan använda för grundläggande hanteringsåtgärder. Den här instrumentpanelen kan du visa grundläggande hälsostatus och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du ställer in Kubernetes-instrumentpanelen på Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Krav för Kubernetes-instrumentpanelen

* Azure Stack Kubernetes-kluster

    Du måste ha distribuerat ett Kubernetes-kluster till Azure Stack. Mer information finns i [distribuera Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* SSH-klient

    Du behöver en SSH-klient till security ansluta till den överordnade noden i klustret. Om du använder Windows kan du använda [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Du behöver den privata nyckeln som används när du har distribuerat ett Kubernetes-kluster.

* FTP (PSCP)

    Du kan också behöva en FTP-klient som har stöd för SSH- och SSH File Transfer Protocol för att överföra certifikat från den överordnade noden till din dator för hantering av Azure Stack. Du kan använda [FileZilla](https://filezilla-project.org/download.php?type=client). Du behöver den privata nyckeln som används när du har distribuerat ett Kubernetes-kluster.

## <a name="overview-of-steps-to-enable-dashboard"></a>Översikt över stegen för att aktivera instrumentpanel

1.  Exportera Kubernetes-certifikat från den överordnade noden i klustret. 
2.  Importera certifikat till din dator för hantering av Azure Stack.
2.  Öppna Kubernetes web-instrumentpanelen. 

## <a name="export-certificate-from-the-master"></a>Exportera certifikatet från huvuddatabasen 

Du kan hämta URL: en för instrumentpanelen från den överordnade noden i klustret.

1. Hämta den offentliga IP-adress och användarnamn för ditt huvudkonto för klustret från instrumentpanelen för Azure Stack. Hämta den här informationen:

    - Logga in på den [Azure Stack-portalen](https://portal.local.azurestack.external/)
    - Välj **alla tjänster** > **alla resurser**. Hitta huvudservern i klustret resursgruppen. Huvudservern heter `k8s-master-<sequence-of-numbers>`. 

2. Öppna huvudnoden i portalen. Kopiera den **offentlig IP-adress** adress. Klicka på **Connect** att hämta ditt användarnamn i den **logga in med lokalt konto för virtuell dator** box. Det här är samma användarnamn som du anger när du skapar klustret. Använd den offentliga IP-adressen i stället för privata IP-adress som anges i connect-bladet.

3.  Öppna en SSH-klient för att ansluta till huvudservern. Om du arbetar med Windows kan du använda [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) att skapa anslutningen. Du använder den offentliga IP-adressen för huvudnoden, username, och lägga till den privata nyckeln som du använde när klustret skapas.

4.  När terminalen ansluter skriver `kubectl` att öppna Kubernetes kommandoradsklient.

5. Kör följande kommando:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Hitta URL: en för instrumentpanelen. Exempel:  `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extrahera det självsignerade certifikatet och konvertera den till PFX-format. Kör följande kommando:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Hämta en lista över hemligheterna i den **kube system** namnområde. Kör följande kommando:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Anteckna kubernetes-instrumentpanelen-token -\<XXXXX > värde. 

8.  Hämta token och spara den. Uppdatera den `kubernetes-dashboard-token-<####>` med det hemliga värdet från föregående steg.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importera certifikatet

1. Öppna Filezilla och ansluta till huvudnoden. Du behöver den:

    - den offentliga IP-huvudnod
    - användarnamnet
    - den privata hemlighet
    - Använd **SFTP - SSH File Transfer Protocol**

2. Kopiera `/etc/kubernetes/certs/client.pfx` och `/etc/kubernetes/certs/ca.crt` till din dator för hantering av Azure Stack.

3. Anteckna filsökvägar. Uppdatera skriptet med platserna och sedan öppna PowerShell med en upphöjd kommandotolk. Kör det uppdaterade skriptet:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Öppna Kubernetes-instrumentpanelen 

1. Inaktivera programmet på din webbläsare.

2. Punkt webbläsaren till URL som anges när du körde kommandot `kubectl cluster-info`. Till exempel: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard: / proxy 
3. Välj klientcertifikatet.
4. Ange token. 
5. Återansluta till kommandoraden bash på huvudnoden och ge behörighet att `kubernetes-dashboard`. Kör följande kommando:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Skriptet ger `kubernetes-dashboard` administratörsbehörighet i molnet. Mer information finns i [för RBAC-aktiverade kluster](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Du kan använda instrumentpanelen. Mer information om Kubernetes-instrumentpanelen finns i [Kubernetes-instrumentpanel för Webbgränssnitt](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Kubernetes-instrumentpanelen för Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Nästa steg 

[Distribuera Kubernetes till Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Lägga till ett Kubernetes-kluster i Marketplace (för Azure Stack-operatör)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
