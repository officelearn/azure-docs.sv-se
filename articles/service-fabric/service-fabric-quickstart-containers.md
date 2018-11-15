---
title: Skapa en Windows-containerapp för Service Fabric i Azure | Microsoft Docs
description: I den här snabbstarten skapar du ditt första Windows-containerprogram i Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 2855d28a3d5414413ca1657a7bef9c060f6d4424
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300344"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Snabbstart: Distribuera Windows-containers till Service Fabric

Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och containrar.

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-container i ett Service Fabric-kluster. Den här snabbstarten beskriver hur du distribuerar en fördefinierad Docker-containeravbildning i ett Service Fabric-program. När du har slutfört kursen har du en fungerande Windows Server 2016-baserad Nano Server- och IIS-container. Den här snabbstarten beskriver hur du distribuerar en Windows-container. Läs [den här snabbstarten](service-fabric-quickstart-containers-linux.md) om du vill distribuera en Linux-container.

![IIS-standardwebbsidan][iis-default]

I den här snabbstarten lär du dig att:

* Paketera en Docker-avbildningscontainer
* Konfigurera kommunikation
* Utveckla och distribuera ett Service Fabric-program
* Distribuera containerprogrammet till Azure

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration (du kan skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* En utvecklingsdator som kör:
  * Visual Studio 2015 eller Visual Studio 2017.
  * [Service Fabric SDK och verktyg](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Paketera en Docker-avbildningscontainer med Visual Studio

Service Fabric SDK och verktygen innehåller en tjänstmall som hjälper dig att distribuera en container till ett Service Fabric-kluster.

Starta Visual Studio som Administratör.  Välj **Arkiv** > **Nytt** > **Projekt**.

Välj **Service Fabric-programmet**, ge det namnet "MyFirstContainer" och klicka på **OK**.

Välj **Behållare** från mallarna **Hosted Containers and Applications** (Värdbaserade behållare och program).

I **Avbildningsnamn** anger du "microsoft/iis:nanoserver", [Windows Server Nano Server och IIS-avbildningsnamn](https://hub.docker.com/r/microsoft/iis/).

Konfigurera containerns portmappning från port till värd så att inkommande begäranden till tjänsten på port 80 mappas till port 80 i containern.  Ge **Containerport** värdet 80 och **Värdport** värdet 80.  

Ge tjänsten namnet ”MyContainerService” och klicka på **OK**.

![Dialogrutan Ny tjänst][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Ange operativsystemets version för containeravbildningen
Containrar som skapats med en viss Windows Server-version kan kanske inte köras på en värd som kör en annan Windows Server-version. Containrar som t.ex. har skapats med version 1709 av Windows Server kan inte köras på värdar som kör Windows Server 2016. Mer information finns i [Kompatibilitet mellan operativsystem för Windows Server-containrar och värdoperativsystem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Med version 6.1 och senare av Service Fabric Runtime kan du ange flera operativsystemsavbildningar per container och tagga var och en med den version av operativsystemet som de ska distribueras till. Detta hjälper dig att säkerställa att programmet kan köras på värdar som kör olika versioner av Windows operativsystem. Mer information finns i [Ange specifika containeravbildningar för operativsystemet](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Microsoft publicerar olika avbildningar för de olika versioner av IIS som har skapats på olika versioner av Windows Server. Om du vill säkerställa att Service Fabric distribuerar en container som är kompatibel med den version av Windows Server som körs på de klusternoder där dina program distribueras, så lägger du till följande rader i filen *ApplicationManifest.xml*. Versionen för Windows Server 2016 är 14393, och versionen för Windows Server version 1709 är 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Tjänstmanifestet fortsätter att ange endast en avbildning för nanoservern, `microsoft/iis:nanoserver`. 

## <a name="create-a-cluster"></a>Skapa ett kluster

Om du vill distribuera programmet till ett kluster i Azure, kan du ansluta till ett partykluster. Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster i Azure som körs av Service Fabric-teamet där vem som helst kan distribuera program och lära sig mer om plattformen.  Klustret använder ett enda självsignerade certifikat för nod-till nod- samt klient-till-nod-säkerhet. Partykluster stöder containrar. Om du vill ställa in och använda ditt eget kluster, måste klustret köras på en SKU som stöder containrar (till exempel Windows Server 2016 Datacenter med Containers).

Logga in och [ansluta till ett Windows-kluster](https://aka.ms/tryservicefabric). Hämta PFX-certifikatet till datorn genom att klicka på **PFX**-länken. Klicka på länken **Hur ansluter man till ett säkert partkluster?** och kopiera lösenordet för certifikatet. Certifikatet, certifikatlösenordet och värdet **Anslutningsslutpunkt** används i följande steg.

![PFX och klientanslutningsslutpunkt](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Det finns ett begränsat antal tillgängliga partkluster per timme. Om du får ett felmeddelande när du försöker registrera dig för ett partkluster, kan du vänta en stund och försöka igen, eller följa stegen i självstudien [Distribuera en .NET-app](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) som hjälper dig att skapa ett Service Fabric-kluster i din Azure-prenumeration och distribuera programmet till den. Klustret som skapats med Visual Studio stöder containrar. När du har distribuerat och verifierat programmet i ditt kluster kan du hoppa vidare till [Fullständigt Service Fabric-exempelprogram och tjänstmanifest](#complete-example-service-fabric-application-and-service-manifests) i denna snabbstart.
>

På en Windows-dator ska du installera PFX i certifikatarkivet *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Kom ihåg tumavtrycket för följande steg.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Distribuera programmet till Azure med hjälp av Visual Studio

Nu när programmet är redo kan du distribuera det till ett kluster direkt från Visual Studio.

Högerklicka på **MyFirstContainer** i Solution Explorer och välj **Publicera**. Dialogrutan Publicera visas.

Kopiera **Anslutningsslutpunkten** för partyklustret till fältet **Anslutningsslutpunkt**. Till exempel `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Klicka på **Avancerade anslutningsparametrar** och kontrollera anslutningsinformationen för parametern.  Värdena *FindValue* och *ServerCertThumbprint* måste matcha tumavtrycket för certifikatet som installerades i föregående steg.

![Dialogrutan Publicera](./media/service-fabric-quickstart-containers/publish-app.png)

Klicka på **Publicera**.

Varje program i klustret måste ha ett unikt namn.  Partkluster är en offentlig, delad miljö men det kan finnas en konflikt med ett befintligt program.  Om det finns en namnkonflikt byter du namn på Visual Studio-projektet och distribuerar igen.

Öppna en webbläsare och gå till den **Anslutningsslutpunkt** som anges på partyklustersidan. Alternativt kan du lägga till schemaidentifierare `http://` innan och lägga till porten `:80` efter i URL:en. Till exempel http://zwin7fh14scd.westus.cloudapp.azure.com:80. Du bör se IIS-standardwebbsidan: ![IIS-standardwebbsidan][iis-default]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Paketera en Docker-avbildningscontainer
* Konfigurera kommunikation
* Utveckla och distribuera ett Service Fabric-program
* Distribuera containerprogrammet till Azure

Om du vill veta mer om att arbeta med Windows-containrar i Service Fabric, kan du fortsätta till självstudien om Windows-containerappar.

> [!div class="nextstepaction"]
> [Skapa en Windows-containerapp](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
