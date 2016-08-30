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
   ms.date="04/18/2016"
   ms.author="ryanwi"/>

# Förbereda utvecklingsmiljön
 För att kunna skapa och köra [Azure Service Fabric-program][1] på en utvecklingsdator måste du installera runtime, SDK och verktyg. Du måste även aktivera körning av Windows PowerShell-skript som ingår i SDK.

## Krav
### Operativsystemversioner som stöds
Följande operativsystemversioner stöds för utveckling:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 innehåller bara Windows PowerShell 2.0 som standard. Du måste installera PowerShell 3.0 eller senare för att kunna använda PowerShell-cmdlets för Service Fabric. Du kan [ladda ned Windows PowerShell 5.0][powershell5-download] från Microsoft Download Center.

## Installera runtime, SDK och verktyg

Web Platform Installer tillhandahåller tre konfigurationer för Service Fabric-utveckling:

- [Installera runtime och SDK för Service Fabric samt verktyg för Visual Studio 2015][full-bundle-vs2015]
- [Installera runtime och SDK för Service Fabrics samt verktyg för Visual Studio ”15” Preview][full-bundle-dev15]
- [Installera endast runtime och SDK för Service Fabric (inga Visual Studio-verktyg)][core-sdk]


## Aktivera körning av PowerShell-skript

Service Fabric använder Windows PowerShell-skript för att skapa ett lokalt utvecklingskluster och för att distribuera program från Visual Studio. Som standard blockerar Windows dessa skript så att de inte kan köras. För att aktivera dem måste du ändra PowerShell-körningsprincipen. Öppna PowerShell som administratör och ange följande kommando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Nästa steg
Nu när utvecklingsmiljön har konfigurerats kan du börja bygga och köra program.

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
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK "Länk till Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=jun16_HO2-->


