---
title: Självstudier för uppgradering av Service Fabric-app | Microsoft Docs
description: Den här artikeln beskriver upplevelsen av att distribuera ett Service Fabric-program, ändra koden och distribution av en uppgradering med hjälp av Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615184"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Service Fabric-program självstudier för uppgradering med Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric förenklar processen med att uppgradera molnprogram genom att se till att endast ändrade tjänster är uppgraderade och att programmets hälsotillstånd är övervakad under uppgraderingsprocessen. Den återställer också automatiskt programmet till den tidigare versionen på problem. Service Fabric programuppgraderingar är *Stilleståndstid*, eftersom programmet kan uppgraderas utan avbrott. Den här självstudien beskriver hur du utför en rullande uppgradering från Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Steg 1: Skapa och publicera exemplet visuella objekt
Hämta först det [visuella objekt](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) program från GitHub. Sedan skapar och publicerar programmet genom att högerklicka på programprojektet, **VisualObjects**, och välja den **publicera** i Service Fabric-menyalternativ.

![Snabbmenyn för ett Service Fabric-program][image1]

Att välja **publicera** öppnas ett popup-fönster och du kan ange den **målprofilen** till **PublishProfiles\Local.xml**. Fönstret bör se ut som följande innan du klickar på **publicera**.

![Publicera ett Service Fabric-program][image2]

Nu kan du klicka på **publicera** i dialogrutan. Du kan använda [Service Fabric Explorer för att visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet visuella objekt har en webbtjänst som du kan gå till genom att skriva [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) i adressfältet i webbläsaren.  Du bör se 10 flytande visuella objekt som flyttas runt på skärmen.

**Obs!** Om du distribuerar till `Cloud.xml` profil (Azure Service Fabric) programmet ska sedan vara tillgängliga på **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Kontrollera att du har `8081/TCP` konfigurerats i belastningsutjämnaren (hitta belastningsutjämnaren i samma resursgrupp som Service Fabric-instans).

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: Uppdatera exemplet visuella objekt
Du kanske märker att med den version som har distribuerats i steg 1, de visuella objekten inte rotera. Nu ska vi uppgradera det här programmet till en där de visuella objekten också rotera.

Välj VisualObjects.ActorService-projekt i lösningen VisualObjects och öppna den **VisualObjectActor.cs** fil. I filen, gå till metoden `MoveObject`, kommentera ut `visualObject.Move(false)`, och ta bort kommentarerna `visualObject.Move(true)`. Den här kodändring roterar objekten när tjänsten har uppgraderats.  **Nu kan du skapa (inte återskapa) lösningen**, vilket bygger de ändrade projekt. Om du väljer *återskapa alla*, du behöver uppdatera versioner för alla projekt.

Vi måste också version vårt program. Du ändrar versionen när du högerklickar på den **VisualObjects** projekt, du kan använda Visual Studio **redigera Manifest versioner** alternativet. Det här alternativet öppnas dialogrutan för edition-versioner på följande sätt:

![Dialogrutan för versionshantering][image3]

Uppdatera versioner för de ändrade projekt och deras kodpaket, tillsammans med programmet till version 2.0.0. När ändringarna sparas manifestet bör se ut så här (fetstil delar visa ändringarna):

![Uppdaterar versioner][image4]

Visual Studio-verktyg kan göra automatiska uppdateringar av versioner när du har valt **uppdatera program och tjänstversioner automatiskt**. Om du använder [SemVer](http://www.semver.org), måste du uppdatera koden och/eller paketet version enbart om detta alternativ har valts.

Spara ändringarna och kontrollera nu den **uppgradera programmet** box.

## <a name="step-3--upgrade-your-application"></a>Steg 3:  Uppgraderar tillämpningen
Bekanta dig med den [programuppgraderingsparametrar](service-fabric-application-upgrade-parameters.md) och [uppgraderingsprocessen](service-fabric-application-upgrade.md) att få en god förståelse av de olika Uppgraderingsparametrar, tidsgränser och health-villkor som kan vara tillämpas. Den här genomgången är leverantörsutvärdering för service health inställt på standardvärdet (oövervakat läge). Du kan konfigurera dessa inställningar genom att välja **konfigurera inställningar för uppgradering av** och sedan ändra parametrarna efter behov.

Nu vi kan börja uppgradera programmet genom att välja **publicera**. Det här alternativet uppgraderar ditt program till version 2.0.0, där objekten rotera. Service Fabric uppgraderar en uppdateringsdomän i taget (vissa objekt, uppdateras först, följt av andra) och tjänsten fortfarande är tillgänglig under uppgraderingen. Åtkomst till tjänsten kan kontrolleras via din klient (webbläsare).  

Nu, som uppgraderingen fortsätter programmet kan du övervaka den med Service Fabric Explorer med hjälp av den **pågående uppgraderingar** fliken under programmen.

Alla uppdateringsdomäner bör uppgraderas (slutfört) om några minuter och i utdatafönstret i Visual Studio ska också ange att uppgraderingen är klar. Och du bör hitta som *alla* de visuella objekten i webbläsarfönstret nu rotera!

Du kanske vill försök att ändra versionerna och flytta från version 2.0.0 till version 3.0.0 som en övning eller även från version 2.0.0 tillbaka till version 1.0.0. Experimentera med tidsgränser och hälsoprinciper att bli bekant med dem. När du distribuerar till ett Azure-kluster till skillnad från ett lokalt kluster, kanske de parametrar som används som skiljer sig åt. Vi rekommenderar att du har angett inställningarna var.

## <a name="next-steps"></a>Nästa steg
[Uppgradering av program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet uppgraderas med hjälp av [Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibel genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
