---
title: "Uppgradera självstudier för Service Fabric-app | Microsoft Docs"
description: "Den här artikeln beskriver hur erfarenhet av distribution av ett Service Fabric-program, ändra koden och distribution av en uppgradering med hjälp av Visual Studio."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3388257399ce8d2e0ac84b9bff746e1acf153312
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Service Fabric uppgradera självstudiekursen använder Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric förenklar processen med att uppgradera molnprogram genom att säkerställa att endast ändrade tjänster är uppgraderade och att programmets hälsotillstånd övervakas under uppgraderingsprocessen. Den återställer också automatiskt programmet till den tidigare versionen ska vidta när problem. Service Fabric programuppgraderingar är *noll avbrottstid*eftersom programmet kan uppgraderas utan avbrott. Den här självstudiekursen beskrivs hur du utför en uppgradering från Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Steg 1: Skapa och publicera visuella objekt exemplet
Hämta först den [visuella objekt](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) program från GitHub. Sedan skapa och publicera programmet genom att högerklicka på projektet för konsolprogrammet **VisualObjects**, och välja den **publicera** i menyalternativet Service Fabric.

![Snabbmenyn för ett Service Fabric-program][image1]

Att välja **publicera** öppnas ett popup-fönster, och du kan ange den **mål profil** till **PublishProfiles\Local.xml**. Fönstret bör se ut som följande innan du klickar på **publicera**.

![Publicera ett Service Fabric-program][image2]

Nu kan du klicka på **publicera** i dialogrutan. Du kan använda [Service Fabric Explorer för att visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet visuella objekt har en webbtjänst som du kan gå till genom att skriva [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) i adressfältet i webbläsaren.  Du bör se 10 flytande visuella objekt flytta runt på skärmen.

**Obs:** om distribuerar till `Cloud.xml` profil (Azure Service Fabric) programmet sedan ska vara tillgängligt vid **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Kontrollera att du har `8081/TCP` konfigurerats i belastningsutjämnaren (hitta belastningsutjämnare i samma resursgrupp som Service Fabric-instans).

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: Uppdatera visuella objekt-exempel
Du kan se att med den version som har distribuerats i steg 1 visuella objekt inte rotera. Vi uppgradera det här programmet till en där visual objekt också rotera.

Välj VisualObjects.ActorService-projekt i lösningen VisualObjects och öppna den **VisualObjectActor.cs** fil. I denna fil går du till metoden `MoveObject`, kommentera ut `visualObject.Move(false)`, och Avkommentera `visualObject.Move(true)`. Den här koden ändringen roterar objekten när tjänsten har uppgraderats.  **Nu kan du skapa (inte återskapa) lösningen**, som bygger ändrade projekt. Om du väljer *återskapa alla*, du behöver uppdatera versioner för alla projekt.

Vi måste också version vårt program. Ändra version när du högerklickar på den **VisualObjects** projekt, du kan använda Visual Studio **redigera Manifest versioner** alternativet. Det här alternativet öppnas dialogrutan för edition versioner på följande sätt:

![Dialogrutan för versionshantering][image3]

Uppdateringsversioner för ändrade projekt och deras kod paket, tillsammans med programmet till version 2.0.0. När ändringarna sparas manifestet bör se ut ungefär så här (fetstil delar visa ändringarna):

![Uppdatera versioner][image4]

Visual Studio-verktygen kan göra automatiska uppdateringar av versioner när du väljer **automatiskt uppdatera programmet och service versioner**. Om du använder [SemVer](http://www.semver.org), måste du uppdatera koden och/eller konfiguration paketet version enbart om alternativet är markerat.

Spara ändringarna och nu finns det **uppgradera programmet** rutan.

## <a name="step-3--upgrade-your-application"></a>Steg 3: Uppgradera ditt program
Bekanta dig med de [uppgradera applikationsparametrarna](service-fabric-application-upgrade-parameters.md) och [uppgraderingsprocessen](service-fabric-application-upgrade.md) att hämta en god förståelse av de olika uppgradera parametrar, timeout och hälsotillstånd kriterium som kan tillämpas. Tjänstens hälsa utvärdering kriterium har angetts till standardvärdet (oövervakade läge) för den här genomgången. Du kan konfigurera dessa inställningar genom att välja **konfigurera inställningar för uppgradering av** och sedan ändra parametrarna efter behov.

Nu vi är startinställningen programmet uppgraderingen genom att välja **publicera**. Det här alternativet om du uppgraderar ditt program till version 2.0.0, där objekt rotera. Service Fabric uppgraderar en uppdateringsdomän i taget (vissa objekt, uppdateras först, följt av andra) och tjänsten fortfarande är tillgänglig under uppgraderingen. Åtkomst till tjänsten kan kontrolleras via din klient (webbläsare).  

Nu som uppgraderingen fortsätter programmet du kan övervaka den med Service Fabric Explorer med hjälp av den **uppgraderingar pågår** på fliken program.

Alla domäner som uppdateringen ska uppgraderas (slutförd) om några minuter och Visual Studio utdatafönstret bör också ange att uppgraderingen har slutförts. Och du bör hitta som *alla* visual objekten i webbläsarfönstret nu rotera!

Du kanske vill prova att ändra versionerna och flytta från version 2.0.0 till version 3.0.0 som Övning eller även från version 2.0.0 tillbaka till version 1.0.0. Spela upp med tidsgränser och hälsoprinciper ska bli bekant med dem. När du distribuerar till ett Azure-kluster och lokala klustret kanske de parametrar som används skiljer sig åt. Vi rekommenderar att du anger timeout-alternativen hänsyn.

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet uppgraderas med hjälp av [Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade alternativ](service-fabric-application-upgrade-advanced.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
