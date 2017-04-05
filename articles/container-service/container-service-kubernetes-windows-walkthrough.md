---
title: "Azure Kubernetes-kluster för Windows | Microsoft Docs"
description: "Distribuera och kom igång med ett Kubernetes-kluster för Windows-behållare i Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eb3af43b8a13eaaebfa9147848383ff889119d97
ms.lasthandoff: 04/03/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Kom igång med Kubernetes och Windows-behållare i Container Service


Den här artikeln beskriver hur du skapar ett Kubernetes-kluster i Azure Container Service som innehåller Windows-noder för att köra Windows-behållare. 

> [!NOTE]
> Stöd för Windows-behållare med Kubernetes i Azure Container Service är tillgängligt som en förhandsversion. Använd Azure-portalen eller en Resource Manager-mall om du vill skapa ett Kubernetes-kluster med Windows-noder. För närvarande stöds inte den här funktionen med Azure CLI 2.0.
>



Följande bild illustrerar arkitekturen i ett Kubernetes-kluster i Azure Container Service med en Linux-huvudnod och två Windows-agentnoder. 

* Linux-huvudnoden använder REST-API:et för Kubernetes och kan nås via SSH på port 22 eller `kubectl` på port 443. 
* Windows-agentnoderna är grupperade i en Azure-tillgänglighetsuppsättning och kör dina behållare. Windows-noderna kan nås via en RDP SSH-tunnel via huvudnoden. Azure-belastningsutjämningsregler läggs till dynamiskt i klustret beroende på vilka tjänster som exponeras.


![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Alla virtuella datorer finns i samma privata virtuella nätverk och kan nås av varandra. Alla virtuella datorer kör en kubelet, Docker och en proxy.

## <a name="prerequisites"></a>Krav


* **Offentlig SSH RSA-nyckel**: Om du distribuerar med hjälp av portalen eller en av Azures snabbstartmallar måste du ange en offentlig SSH RSA-nyckel för autentisering mot virtuella datorer i Azure Container Service. Information om hur du skapar SSH (Secure Shell) RSA-nycklar finns i hjälpartiklarna för [OS X och Linux](../virtual-machines/linux/mac-create-ssh-keys.md) eller [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Klient-ID och hemlighet för tjänstobjekt**: Mer information och riktlinjer finns i [Om Azure Active Directory-tjänstens huvudnamn för ett Kubernetes-kluster](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Skapa klustret

Du kan använda Azure-portalen för att [skapa ett Kubernetes-kluster](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) med Windows-agentnoder. Tänk på följande inställningar när du skapar klustret:

* På bladet **Basics** i **Orchestrator** markerar du **Kubernetes**. 

  ![Välj Kubernetes Orchestrator](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* På bladet **Konfiguration av huvudservrar** anger du autentiseringsuppgifterna och autentiseringsuppgifter för tjänstens huvudnamn för överordnade Linux-noder. Välj 1, 3 eller 5 masterenheter.

* På bladet **Agentkonfiguration** i **Operativsystem**, väljer du **Windows (preview)** (Windows (förhandsversion)). Ange administratörsautentiseringsuppgifter för Windows-agentnoder.

  ![Välj Windows-agenter](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

Mer information finns i [Distribuera ett Azure Container Service-kluster](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Använd kommandoradsverktyget `kubectl` för att ansluta från den lokala datorn till huvudnoden i Kubernetes-klustret. Anvisningar för hur du installerar och konfigurerar `kubectl` finns i [Ansluta till ett Azure Container Service-kluster](container-service-connect.md#connect-to-a-kubernetes-cluster). Du kan använda `kubectl`-kommandon för att komma åt Kubernetes webbgränssnitt och för att skapa och hantera Windows-behållararbetsbelastningar.

## <a name="create-your-first-kubernetes-service"></a>Skapa din första Kubernetes-tjänst

När klustret har skapats och anslutits till `kubectl` kan du prova att starta en enkel Windows-webbapp och exponera den för Internet. I det här exemplet anger du behållarresurserna med hjälp av en YAML-fil och skapar den sedan med `kubctl apply`.

1. Om du vill visa en lista över noderna skriver du `kubectl get nodes`. Om du vill visa fullständig information om noderna skriver du:  

    ```
    kubectl get nodes -o yaml
    ```

2. Skapa en fil med namnet `simpleweb.yaml` och kopiera följande. Den här filen konfigurerar en webbapp med hjälp av den Windows Server 2016 Server Core-baserade OS-avbildningen från [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/).  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> Konfigurationen innehåller `type: LoadBalancer`. Den här inställningen gör att tjänsten exponeras för Internet via en Azure-belastningsutjämnare. Mer information finns i [Load balance containers in a Kubernetes cluster in Azure Container Service](container-service-kubernetes-load-balancing.md) (Belastningsutjämna behållare i ett Kubernetes-kluster i Azure Container Service).
>

## <a name="start-the-application"></a>Starta programmet

1. Om du vill starta programmet skriver du:  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. Om du vill verifiera distributionen av tjänsten (som tar ungefär 30 sekunder) skriver du:  

    ```
    kubectl get pods
    ```

3. När tjänsten körs skriver du följande om du vill visa tjänstens interna och externa IP-adresser:

    ```
    kubectl get svc
    ``` 
  
    ![IP-adresser för Windows-tjänst](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    Det tar flera minuter innan den externa IP-adressen läggs till. Innan belastningsutjämnaren konfigurerar den externa adressen visas den som `<pending>`.

4. När den externa IP-adressen är tillgänglig kan du nå tjänsten i din webbläsare.

    ![Windows Server-app i webbläsare](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Komma åt Windows-noderna
Windows-noder kan nås från en lokal Windows-dator via Anslutning till fjärrskrivbord. Vi rekommenderar att du använder en RDP SSH-tunnel via huvudnoden. 

Det finns flera olika sätt att skapa SSH-tunnlar i Windows. Det här avsnittet beskriver hur du använder PuTTY för att skapa tunneln.

1. [Ladda ned PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) till din Windows-dator.

2. Kör appen.

3. Ange ett värdnamn som består av administratörsanvändarnamnet för klustret och det offentliga DNS-namnet på den första huvudservern i klustret. **Värdnamnet** liknar `adminuser@PublicDNSName`. Ange 22 som **Port**.

  ![PuTTY-konfiguration 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Välj **SSH > Aut**. Lägg till en sökväg till filen för privat nyckel (PPK-format) för autentisering. Du kan använda ett verktyg som [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) för att skapa den här filen från SSH-nyckeln som används för att skapa klustret.

  ![PuTTY-konfiguration 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Välj **SSH > Tunnlar** och konfigurera de vidarebefordrade portarna. Eftersom den lokala Windows-datorn redan använder port 3389 rekommenderar vi att du använder följande inställningar för att nå Windows-nod 0 och Windows-nod 1. (Fortsätta med samma mönster för ytterligare Windows-noder.)

    **Windows-nod 0**

    * **Källport:** 3390
    * **Mål:** 10.240.245.5:3389

    **Windows-nod 1**

    * **Källport:** 3391
    * **Mål:** 10.240.245.6:3389

    ![Bild av Windows RDP-tunnlar](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. När du är klar klickar du på **Session > Spara** för att spara anslutningskonfigurationen.

7. Anslut till PuTTY-sessionen genom att klicka på **Öppna**. Slutför anslutningen till huvudnoden.

8. Starta Anslutning till fjärrskrivbord. Anslut den första Windows-noden genom att ange `localhost:3390` för **Dator** och klicka på **Anslut**. (För att ansluta till den andra anger du `localhost:3390` osv.) Slutför anslutningen genom att ange det lokala administratörslösenordet för Windows som du konfigurerade under distributionen.


## <a name="next-steps"></a>Nästa steg

Här är en lista med rekommenderade länkar till mer information om Kubernetes:

* [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) – visar hur du distribuerar, skalar, uppdaterar och felsöker program som använder behållare.
* [Användarhandboken för Kubernetes](http://kubernetes.io/docs/user-guide/) – innehåller information om hur du kör program i ett befintligt Kubernetes-kluster.
* [Kubernetes-exempel](https://github.com/kubernetes/kubernetes/tree/master/examples) – innehåller exempel på hur du kör verkliga program med Kubernetes.
