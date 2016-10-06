<properties
   pageTitle="Konfigurera utvecklingsmiljön | Microsoft Azure"
   description="Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/13/2016"
   ms.author="ryanwi"/>


# Förbereda utvecklingsmiljön

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 För att kunna skapa och köra [Azure Service Fabric-program][1] på en utvecklingsdator ska du installera runtime, SDK och verktyg. Du måste även aktivera körning av Windows PowerShell-skript som ingår i SDK.

## Krav
### Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 innehåller bara Windows PowerShell 2.0 som standard. Service Fabric PowerShell-cmdletar kräver PowerShell 3.0 eller senare. Du kan [ladda ned Windows PowerShell 5.0][powershell5-download] från Microsoft Download Center.

## Installera runtime, SDK och verktyg

Web Platform Installer tillhandahåller två konfigurationer för Service Fabric-utveckling:

- [Installera runtime och SDK för Service Fabric samt verktyg för Visual Studio 2015 (kräver Visual Studio 2015 Uppdatering 2 eller senare)][full-bundle-vs2015]
- [Installera endast runtime och SDK för Service Fabric (inga Visual Studio-verktyg)][core-sdk]

## Aktivera körning av PowerShell-skript

Service Fabric använder Windows PowerShell-skript för att skapa ett lokalt utvecklingskluster och för att distribuera program från Visual Studio. Som standard blockerar Windows dessa skript så att de inte kan köras. För att aktivera dem måste du ändra PowerShell-körningsprincipen. Öppna PowerShell som administratör och ange följande kommando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Nästa steg
Nu när du har konfigurerat utvecklingsmiljön ska du börja bygga och köra program.

- [Skapa ditt första Service Fabric-program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Lär dig hur du distribuerar och hanterar program i ditt lokala kluster](service-fabric-get-started-with-a-local-cluster.md)
- [Lär dig mer om programmeringsmodeller: Reliable Services och Reliable Actors](service-fabric-choose-framework.md)
- [Kolla in Service Fabric-kodexemplen på GitHub](https://aka.ms/servicefabricsamples)
- [Visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Följ utbildningsvägen för Service Fabric för en bred introduktion till plattformen](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric-kampanjsida"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Länk till VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Länk till Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Länk till Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Sep16_HO4-->


