---
title: Kommandoradsverktyget build för Azure | Microsoft Docs
description: Kommandoradsverktyget build för Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: 7d0138abb07aea46ad8d0069c87964b393347dcf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791434"
---
# <a name="building-azure-projects-from-the-command-line"></a>Skapa Azure-projekt från kommandoraden
Med Microsoft skapa Engine (MSBuild) kan skapa du produkter i build-labbmiljöer där Visual Studio inte har installerats. MSBuild använder ett XML-format för projektfiler som extensible och stöds fullt ut av Microsoft. Använder formatet MSBuild, kan du beskriva vad objekt måste vara inbyggda för en eller flera plattformar och konfigurationer.

Du kan också köra MSBuild på kommandoraden och det här avsnittet beskrivs den metoden. Du kan skapa specifika konfigurationer av ett projekt genom att ange egenskaper på kommandoraden. På samma sätt kan definiera du också de mål som MSBuild-versioner. Mer information om kommandoradsparametrar och MSBuild finns [MSBuild Kommandoradsreferens](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>MSBuild-parametrar
Det enklaste sättet att skapa ett paket är att köra MSBuild med den `/t:Publish` alternativet. Som standard det här kommandot skapar en katalog i förhållande till rotmappen för projektet som `<ProjectDirectory>\bin\Configuration\app.publish\`. När du skapar ett Azure-projekt genereras två filer: paketet filen sig själv och tillhörande konfigurationsfil:

* Appaketfil (`project.cspkg`)
* Konfigurationsfilen (`ServiceConfiguration.TargetProfile.cscfg`)

Som standard innehåller en service-konfigurationsfilen för lokala (felsökning) versioner och ett annat för molnet (mellanlagring eller produktion) versioner varje Azure-projekt. Du kan lägga till eller ta bort service configuration-filer efter behov. När du skapar ett paket i Visual Studio, tillfrågas du vilka tjänstkonfiguration filen för att inkludera tillsammans med paketet. När du skapar ett paket med hjälp av MSBuild med den lokala tjänst-konfigurationsfilen som standard. För att inkludera en annan tjänstkonfiguration fil, ange den `TargetProfile` egenskapen för MSBuild-kommandot (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Om du vill använda en alternativ katalog för lagrade paket- och konfigurationsfiler, ställa in sökvägen med hjälp av den `/p:PublishDir=Directory\` alternativ, inklusive avslutande omvänt avgränsare.

## <a name="next-steps"></a>Nästa steg
När paketet har skapats kan du distribuera den till Azure.