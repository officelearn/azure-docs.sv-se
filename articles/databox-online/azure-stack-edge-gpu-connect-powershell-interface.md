---
title: Ansluta till och hantera Microsoft Azure Stack Edge-enhet via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och sedan hanterar Azure Stack Edge via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087189"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Hantera en Azure Stack Edge GPU-enhet via Windows PowerShell

Med Azure Stack Edge-lösning kan du bearbeta data och skicka dem via nätverket till Azure. I den här artikeln beskrivs några konfigurations-och hanterings uppgifter för din Azure Stack Edge-enhet. Du kan använda Azure Portal, det lokala webb gränssnittet eller Windows PowerShell-gränssnittet för att hantera enheten.

Den här artikeln fokuserar på hur du kan ansluta till PowerShell-gränssnittet på enheten och de uppgifter som du kan utföra med hjälp av det här gränssnittet. 


## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett support paket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Överför certifikat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Du kan också ladda upp IoT Edge certifikat för att aktivera en säker anslutning mellan din IoT Edge-enhet och de efterföljande enheterna som kan ansluta till den. Det finns tre IoT Edge-certifikat (*. pem* -format) som du måste installera:

- Rot certifikat utfärdare eller ägarens certifikat UTFÄRDAre
- Enhetens CA-certifikat
- Enhets nyckel certifikat

I följande exempel visas användningen av den här cmdleten för att installera IoT Edge certifikat:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
När du kör den här cmdleten uppmanas du att ange lösen ordet för nätverks resursen.

Om du vill ha mer information om certifikat går du till [Azure IoT Edge certifikat](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) eller [installerar certifikat på en gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Visa enhets information
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Visa information om GPU-drivrutinen

Om Compute-rollen har kon figurer ATS på din enhet kan du också hämta information om GPU-drivrutinen via PowerShell-gränssnittet. 

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-HcsGpuNvidiaSmi` för att hämta information om GPU-drivrutinen för enheten.

    I följande exempel visas användningen av denna cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Anteckna driv rutins informationen från exempel resultatet av denna cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Aktivera multi-process service (MPS)

En multi-process service (MPS) på NVIDIA GPU: er tillhandahåller en mekanism där GPU: er kan delas av flera jobb, där varje jobb har allokerat en viss procent andel av GPU-resurserna. Följ dessa steg om du vill aktivera MPS i Azure Stack Edge-enheten:

1. Innan du börjar ska du kontrol lera att: 

    1. Du har konfigurerat och [aktiverat din Azure Stack Edge-enhet](azure-stack-edge-gpu-deploy-activate.md) med en Azure Stack Edge/data Box gateway-resurs i Azure.
    1. Du har [konfigurerat Compute på den här enheten i Azure Portal](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
1. Använd följande kommando för att aktivera MPS på enheten.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Återställa din enhet

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Hämta beräknings loggar

Om Compute-rollen har kon figurer ATS på din enhet kan du också hämta beräknings loggarna via PowerShell-gränssnittet.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-AzureDataBoxEdgeComputeRoleLogs` för att hämta beräknings loggarna för din enhet.

    I följande exempel visas användningen av denna cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Här följer en beskrivning av parametrarna som används för cmdleten:
    - `Path`: Ange en nätverks Sök väg till den resurs där du vill skapa beräknings logg paketet.
    - `Credential`: Ange användar namnet för nätverks resursen. När du kör den här cmdleten måste du ange resurs lösen ordet.
    - `FullLogCollection`: Den här parametern säkerställer att logg paketet innehåller alla beräknings loggar. Som standard innehåller logg paketet bara en delmängd av loggarna.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Felsöka Kubernetes problem som rör IoT Edge

När Kubernetes-klustret skapas skapas även en standard användare `aseuser` som är associerad med ett system namn område `iotedge` . Om du vill felsöka problem som rör IoT Edge kan du använda den här användar-och system namn rymden.  

### <a name="create-config-file-for-system-namespace"></a>Skapa konfigurations fil för system namn område

Om du vill felsöka måste du först skapa `config` filen som motsvarar `iotedge` namn området med `aseuser` .

Kör `Get-HcsKubernetesUserConfig -AseUser` kommandot och spara utdata som `config` fil (inget fil namns tillägg). Spara filen i `.kube` mappen i din användar profil på den lokala datorn.

Följande är exempel på utdata från `Get-HcsKubernetesUserConfig` kommandot.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

På en Azure Stack Edge-enhet som har beräknings rollen konfigurerad, är alla `kubectl` kommandon tillgängliga för att övervaka eller felsöka moduler. Om du vill se en lista över tillgängliga kommandon kan du köra `kubectl --help` från kommando fönstret.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

En fullständig lista över `kubectl` kommandona finns på [ `kubectl` fusklapp](https://kubernetes.io/docs/reference/kubectl/cheatsheet/). 

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Så här hämtar du IP-adress eller modul som exponeras utanför Kubernetes-kluster

Om du vill hämta IP-adressen för en belastnings Utjämnings tjänst eller moduler som exponeras utanför Kubernetes kör du följande kommando:

`kubectl get svc -n iotedge`

Följande är ett exempel på utdata från alla tjänster eller moduler som exponeras utanför Kubernetes-klustret. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
IP-adressen i den externa IP-kolumnen motsvarar den externa slut punkten för tjänsten eller modulen. Du kan också [Hämta den externa IP-adressen i Kubernetes-instrumentpanelen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Kontrol lera om modulen har distribuerats korrekt

Compute-moduler är behållare som har en affärs logik implementerad. En Kubernetes-Pod kan ha flera behållare som kör. Kontrol lera att en Compute-modul har distribuerats genom att köra `get pods` kommandot och kontrol lera om behållaren (som motsvarar Compute-modulen) körs.

Kör följande kommando för att hämta en lista över alla poddar som körs i en angiven namnrymd:

`get pods -n <namespace>`

Följande är ett exempel på utdata från alla poddar som körs i `iotedge` namn området.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

Status **status** anger att alla poddar i namn området körs och att det är **klart** anger hur många behållare som distribueras i en pod. I föregående exempel körs alla poddar och alla moduler som distribueras i var och en av poddar körs. 

Du kan köra följande kommando för att få en mer utförlig utdata av en specifik Pod för en angiven namnrymd:

`kubectl describe pod <pod name> -n <namespace>` 

Exempel på utdata visas här.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Hämta behållar loggar

Kör följande kommando för att hämta loggarna för en modul:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Eftersom `all-containers` flagga kommer att dumpa alla loggar för alla behållare, är ett bra sätt att se de senaste felen är att använda alternativet `--tail 10` .

Följande är ett exempel på utdata. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Avsluta fjärrsessionen

Stäng PowerShell-fönstret om du vill avsluta PowerShell-sessionen.

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) i Azure Portal.
