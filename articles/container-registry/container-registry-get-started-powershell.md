---
title: Snabbstart – skapa ett privat Docker-register i Azure med PowerShell
description: Lär dig snabbt att skapa ett privat Docker-containerregister i Azure med PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: fe21ab3e29b7a4cbf204be82246d37a4b91128e4
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856691"
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Snabbstart: Skapa ett Azure Container Registry med PowerShell

Azure Container Registry är en hanterad Docker-behållarregistertjänst för att bygga, lagra och hantera Docker-behållaravbildningar. I denna snabbstart kommer du att lära dig hur du skapar ett Azure-containerregister med hjälp av PowerShell. När du har skapat registret push-överför du en behållaravbildning till det och sedan distribuerar du behållaren från registret i Azure Container Instances (ACI).

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten kräver Azure PowerShell-modul version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att avgöra installerad version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Du måste också ha Docker installerat lokalt. Docker innehåller paket för [macOS][docker-mac]-, [Windows][docker-windows]- och [Linux][docker-linux]-system.

Eftersom Azure Cloud Shell inte innehåller alla nödvändiga Docker-komponenter (`dockerd`-daemon), kan du inte använda Cloud Shell för denna snabbstart.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzureRmAccount][Connect-AzureRmAccount] och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

När du är autentiserad med Azure, skapa en resursgrupp med [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. En resursgrupp är en logisk container där du kan distribuera och hantera dina Azure-resurser.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Skapa containerregister

Skapa sedan ett behållarregister i din nya resursgrupp med kommandot [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel skapas ett register med namnet ”myContainerRegistry007”. Ersätt *myContainerRegistry007* i följande kommando innan du kör det för att skapa registernyckeln:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Logga in till registret

Innan du skickar och hämtar containeravbildningar måste du logga in i ditt register. Använd kommandot [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] för att först hämta autentiseringsuppgifter som administratör för registret:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Kör därefter [docker-inloggning][docker-login] för att logga in:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

En lyckad inloggning returnerar `Login Succeeded`:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Push-överför avbildningen till registret

Nu när du är inloggad i registret, kan du push-överföra containeravbildningar till det. För att få en avbildning som du kan push-överföra till registret, hämtar du den offentliga [aci-helloworld][aci-helloworld-image]-avbildningen från Docker-hubben. [aci-helloworld][aci-helloworld-github]-avbildningen är ett litet Node.js-program som har en statisk HTML-sida som visar Azure Container Instances-logotypen.

```powershell
docker pull microsoft/aci-helloworld
```

När avbildningen hämtas liknar utdata följande:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Innan du kan push-överföra en avbildning till Azure-containerregistret, måste du tagga den med det fullständigt kvalificerade domännamnet (FQDN) för registret. FQDN för Azure-containerregistret anges i formatet *\<registry-name\>.azurecr.io*.

Fyll i en variabel med fullständig avbildningstagg. Inkludera inloggningsserver, databasens namn (”aci-helloworld”) och avbildningens version (”v1”):

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

Tagga nu avbildningen med [docker-taggen][docker-tag]:

```powershell
docker tag microsoft/aci-helloworld $image
```

Överför den slutligen med [docker-push][docker-push] till ditt register:

```powershell
docker push $image
```

Eftersom Docker-klienten push-överför avbildningen, bör utdata likna följande:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Grattis! Du har precis push-överfört din första containeravbildning till registret.

## <a name="deploy-image-to-aci"></a>Distribuera avbildningen till ACI

Med avbildningen i registret, distribuera en behållare direkt till Azure Container Instances om du vill se hur den körs i Azure.

Konvertera först registrets autentiseringsuppgifter till en *PSCredential*. `New-AzureRmContainerGroup`-kommandot som du använder för att skapa containerinstansen, kräver att det är i det här formatet.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Dessutom måste DNS-namnetiketten för din container vara unik inom den Azure-region där den skapades. Kör följande kommando för att fylla i en variabel med ett genererat namn:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Kör slutligen [New-AzureRmContainerGroup][New-AzureRmContainerGroup] för att distribuera en behållare från avbildningen i registret med 1 processorkärna och 1 GB minne:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Du bör få ett inledande svar från Azure med information om din container, och dess tillstånd är först ”väntande”:

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Om du vill övervaka statusen och fastställa när den körs, kör kommandot [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] några gånger. Det bör ta mindre än en minut för containern att starta.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Här kan du se att containerns ProvisioningState är först *Skapar* för att sedan gå över till tillståndet *Lyckad* när den är igång:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Visa program som körs

När distributionen till ACI har slutförts och din container är igång kan du gå till dess fullständigt kvalificerade domännamn (FQDN) i webbläsaren om du vill visa appen när den körs i Azure.

Hämta det fullständiga domännamnet för din behållare med [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

Utdata från kommandot är det fullständiga domännamnet för din containerinstans:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Med det fullständiga domännamnet till hands navigerar du till det i din webbläsare:

![Hello World-app i webbläsaren][qs-psh-01-running-app]

Grattis! Du har fått en container som kör ett program i Azure, distribuerad direkt från en containeravbildning i ditt privata Azure-containerregister.

## <a name="clean-up-resources"></a>Rensa resurser

När du har arbetat klart med resurserna som du skapade i den här snabbstarten, använd kommandot [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] för att ta bort resursgruppen, containerregistret och containerinstansen:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I snabbstarten skapade du ett Azure Container Registry med Azure CLI och startade en instans av den via Azure Container Instances. Fortsätt till självstudien om Azure Container Instances om du vill titta närmare på ACI.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
