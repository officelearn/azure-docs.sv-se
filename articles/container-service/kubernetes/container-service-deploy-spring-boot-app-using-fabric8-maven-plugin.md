---
title: "Distribuera en källan Start-app med Fabric8 Maven plugin-programmet"
description: "Den här självstudiekursen vägleder dig genom stegen för att distribuera ett program för vår Start i Microsoft Azure med hjälp av Fabric8 plugin-programmet för Apache Maven."
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.openlocfilehash: 6ec48caef0eebeacf260b0dbc91b175368faea69
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Distribuera en källan Start-app med Fabric8 Maven plugin-programmet

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

**[Fabric8]**  är en öppen källkod som bygger på  **[Kubernetes]**, vilket hjälper utvecklare skapa program i Linux-behållare.

Den här självstudiekursen vägleder dig genom med hjälp av plugin-program för Fabric8 för Maven för att utveckla för att distribuera ett program till en Linux-värd i den [Azure Container Service (ACS)].

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här självstudiekursen, måste du ha följande krav:

* En Azure-prenumeration; Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner] eller registrera dig för en [kostnadsfritt Azure-konto].
* Den [Azure-kommandoradsgränssnittet (CLI)].
* En uppdaterad [Java Developer Kit (JDK)].
* Apache's [Maven] skapa verktyget (Version 3).
* En [Git] klienten.
* En [Docker] klienten.

> [!NOTE]
>
> På grund av virtualisering kraven i den här självstudiekursen, kan du följa stegen i den här artikeln på en virtuell dator; Du måste använda en fysisk dator med aktiverat funktionerna för virtualisering.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Skapa källan Start på Docker komma igång webbprogram

Följande steg beskriver hur du skapar ett webbprogram i vår start och testa dem lokalt.

1. Öppna en kommandotolk och skapa en lokal katalog för att hålla ditt program och ändra till katalogen; Exempel:
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   -- eller--
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Klona den [Vårversionen Start på Docker komma igång] exempelprojektet till katalogen.
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Ändra katalogen till slutförda projektet. Exempel:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   -- eller--
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Använd Maven för att skapa och köra sample-appen.
   ```shell
   mvn clean package spring-boot:run
   ```

1. Testa webbappen genom att bläddra till http://localhost: 8080 eller med följande `curl` kommando:
   ```shell
   curl http://localhost:8080
   ```

   Du bör se en **Hello Docker World** meddelande som visas.

   ![Bläddra exempelprogrammet lokalt][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Installera kommandoradsgränssnittet Kubernetes och skapa en Azure-resursgrupp med hjälp av Azure CLI

1. Öppna en kommandotolk.

1. Skriv följande kommando för att logga in på ditt Azure-konto:
   ```azurecli
   az login
   ```
   Följ instruktionerna för att slutföra inloggningen
   
   Azure CLI visas en lista över dina konton; Exempel:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Om du inte redan har Kubernetes-kommandoradsgränssnittet (`kubectl`) installerat, kan du installera med hjälp av Azure CLI, till exempel:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Linux-användare kan ha som prefix i det här kommandot med `sudo` eftersom den distribuerar Kubernetes CLI till `/usr/local/bin`.
   >
   > Om du redan har `kubectl`) installerad och din version av `kubectl` är för gammal kan visas ett felmeddelande som liknar följande exempel när du försöker slutföra stegen som beskrivs senare i den här artikeln:
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > Om det händer kan du måste installera om `kubectl` att uppdatera din version.
   >

1. Skapa en resursgrupp för Azure-resurser som du ska använda i den här kursen; Exempel:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Där:  
      * *VingspetsLeksaker kubernetes* är ett unikt namn för resursgruppen  
      * *westeurope* är en lämplig geografisk plats för ditt program  

   Azure CLI visas resultatet av din resurs att skapa gruppen; Exempel:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Skapa ett Kubernetes-kluster med Azure CLI

1. Skapa ett Kubernetes-kluster i din nya resursgrupp; Exempel:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Där:  
      * *VingspetsLeksaker kubernetes* är namnet på resursgruppen från tidigare i den här artikeln  
      * *VingspetsLeksaker klustret* är ett unikt namn för klustret Kubernetes
      * *VingspetsLeksaker* är ett unikt namn DNS-namn för ditt program

   Azure CLI visas resultatet av dina kluster; Exempel:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Hämta autentiseringsuppgifterna för ditt nya kluster med Kubernetes; Exempel:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. Kontrollera din anslutning med följande kommando:
   ```shell 
   kubectl get nodes
   ```

   Du bör se en lista över noderna och status som i följande exempel:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Skapa en behållare för privat Azure-registret med hjälp av Azure CLI

1. Skapa en registernyckel för privat Azure-behållaren i resursgruppen som värd för din Docker-bild. Exempel:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Där:  
      * *VingspetsLeksaker kubernetes* är namnet på resursgruppen från tidigare i den här artikeln  
      * *wingtiptoysregistry* är ett unikt namn för din privata registret
      * *westeurope* är en lämplig geografisk plats för ditt program  

   Azure CLI visas resultatet från genereringen av din registret; Exempel:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. Hämta lösenordet för behållaren registret från Azure CLI.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   Azure CLI visas lösenordet för ditt registret. Exempel:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Navigera till konfigurationskatalogen för Maven-installationen (standard ~/.m2/ eller C:\Users\username\.m2) och öppna den *settings.xml* fil med en textredigerare.

1. Lägg till ditt Azure-behållare registret URL, användarnamn och lösenord till en ny `<server>` samling i den *settings.xml* fil.
Den `id` och `username` är namnet på registret. Använd den `password` värde från det föregående kommandot (utan citattecken).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start (till exempel ”*C:\SpringBoot\gs-spring-boot-docker\complete*” eller ”*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete* ”), och öppna den *pom.xml* fil med en textredigerare.

1. Uppdatering av `<properties>` samling i den *pom.xml* fil med inloggningen servervärdet för registernyckeln din Azure-behållare.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Uppdatera den `<plugins>` samling i den *pom.xml* filen så att den `<plugin>` innehåller inloggningen adress och registret servernamnet för din Azure-behållare registernyckeln.

   ```xml
   <plugin>
     <groupId>com.spotify</groupId>
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start och kör följande Maven-kommando för att skapa Docker-behållaren och push-avbildningen i registret:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   Maven visas resultatet av dina build; Exempel:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Konfigurera källan Start appen om du vill använda plugin-programmet för Fabric8 Maven

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start (till exempel ”:*C:\SpringBoot\gs-spring-boot-docker\complete*” eller ”*/home/GenaSoto/SpringBoot/gs-spring-boot-docker / fullständig*”), och öppna den *pom.xml* fil med en textredigerare.

1. Uppdatera den `<plugins>` samling i den *pom.xml* fil att lägga till plugin-programmet för Fabric8 Maven:

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Navigera till huvudsakliga källkatalogen för tillämpningsprogrammet källan start (till exempel ”:*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*” eller ”*/home/GenaSoto/SpringBoot/gs-spring-boot-docker / slutföra/src/main*”), och skapa en ny mapp med namnet”*fabric8*”.

1. Skapa tre YAML fragment filer i den nya *fabric8* mapp:

   a. Skapa en fil med namnet **deployment.yml** med följande innehåll:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Skapa en fil med namnet **secrets.yml** med följande innehåll:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. Skapa en fil med namnet **service.yml** med följande innehåll:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Kör följande Maven-kommando för att skapa listan resursfilen Kubernetes:

   ```shell
   mvn fabric8:resource
   ```

   Det här kommandot sammanfogar alla Kubernetes yaml resursfiler under den *src/main/fabric8* mappen till en YAML-fil som innehåller en Kubernetes resurslista som kan tillämpas på Kubernetes kluster direkt eller export till ett helm diagram.

   Maven visas resultatet av dina build; Exempel:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Kör följande Maven-kommando för att tillämpa lista resursfilen Kubernetes klustret:

   ```shell
   mvn fabric8:apply
   ```

   Maven visas resultatet av dina build; Exempel:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. När appen har distribuerats till klustret, fråga den externa IP-adress med hjälp av den `kubectl` program, till exempel:
   ```shell
   kubectl get svc -w
   ```

   `kubectl`Visar dina interna och externa IP-adresser. Exempel:
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   Du kan använda den externa IP-adressen för att öppna programmet i en webbläsare.

   ![Bläddra externt exempelprogrammet][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Ta bort Kubernetes-kluster

När Kubernetes klustret inte längre behövs, kan du använda den `az group delete` kommando för att ta bort resursgruppen som tar bort alla dess relaterade resurser, till exempel:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder värdet startprogram i Azure finns i följande artiklar:

* [Distribuera ett program för start av källan till Azure App Service](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Distribuera en källan startprogrammet på Linux i Azure Container Service](container-service-deploy-spring-boot-app-on-linux.md)
* [Distribuera ett program för start av värdet på ett Kubernetes kluster i Azure Container Service](container-service-deploy-spring-boot-app-on-kubernetes.md)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center] och i [Java Tools för Visual Studio Team Services].

Mer information om källan Start på Docker exempelprojektet finns [Vårversionen Start på Docker komma igång].

Hjälp med att komma igång med din egen källan startprogram, finns det **Vårversionen Initializr** på <https://start.spring.io/>.

Mer information om att komma igång med att skapa ett enkelt källan Start-program finns i vår Initializr på <https://start.spring.io/>.

Ytterligare exempel för hur du använder anpassade Docker-avbildningar med Azure finns [med hjälp av en anpassad Docker-avbildning för Azure Web App på Linux].

<!-- URL List -->

[Azure-kommandoradsgränssnittet (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[med hjälp av en anpassad Docker-avbildning för Azure Web App på Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools för Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Vårversionen Start på Docker komma igång]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png
