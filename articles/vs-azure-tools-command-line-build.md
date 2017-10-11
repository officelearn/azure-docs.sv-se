---
title: "Kommandoradsverktyget build för Azure | Microsoft Docs"
description: "Kommandoradsverktyget build för Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: kraigb
ms.openlocfilehash: 5fe910e2757dd5ec783538e23e7f52e2f5725b39
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
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
När paketet har skapats kan du distribuera den till Azure. En genomgång som visar hur du automatiserar processen, se [kontinuerlig leverans för molntjänster i Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).

