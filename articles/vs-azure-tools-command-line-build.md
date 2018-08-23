---
title: Kommandoraden, skapar för Azure | Microsoft Docs
description: Kommandoraden, skapar för Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: c47da12feeef2a1536cdbfbe0187fe8991b3257d
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056994"
---
# <a name="building-azure-projects-from-the-command-line"></a>Att skapa Azure-projekt från kommandoraden
Med hjälp av Microsoft Build Engine (MSBuild), kan du skapa produkter i build-labbmiljöer där Visual Studio inte har installerats. MSBuild använder ett XML-format för projektfilerna med extensible och stöds fullt ut av Microsoft. Med hjälp av MSBuild-filformat, kan du beskriva vad objekt måste vara för en eller flera plattformar och konfigurationer.

Du kan också köra MSBuild på kommandoraden och det här avsnittet beskriver denna metod. Genom att ange egenskaperna på kommandoraden kan skapa du specifika konfigurationer för ett projekt. På samma sätt kan du också definiera de mål som bygger in MSBuild. Mer information om kommandoradsparametrar och MSBuild finns [MSBuild Kommandoradsreferens](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>MSBuild-parametrar
Det enklaste sättet att skapa ett paket är att köra MSBuild med den `/t:Publish` alternativet. Som standard det här kommandot skapar en katalog i förhållande till rotmappen för projektet, till exempel `<ProjectDirectory>\bin\Configuration\app.publish\`. När du skapar ett Azure-projekt, genereras två filer: paketet filen själva och tillhörande konfigurationsfil:

* Paketfil (`project.cspkg`)
* Konfigurationsfilen (`ServiceConfiguration.TargetProfile.cscfg`)

Som standard innehåller varje Azure-projekt som en tjänst-konfigurationsfilen för lokala (felsökning) versioner och en annan för molnet (mellanlagring eller produktion) versioner. Du kan dock lägga till eller ta bort tjänstkonfiguration filer efter behov. När du skapar ett paket i Visual Studio uppmanas du vilken service-konfigurationsfilen för att inkludera tillsammans med paketet. När du skapar ett paket med hjälp av MSBuild med lokala tjänst-konfigurationsfilen som standard. För att inkludera en annan tjänstkonfiguration fil, ange den `TargetProfile` egenskapen MSBuild-kommandot (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Om du vill använda en annan katalog för lagrat paket och konfigurationsfiler, ange sökvägen med den `/p:PublishDir=Directory\` alternativ, inklusive avslutande snedstreck avgränsare.

## <a name="next-steps"></a>Nästa steg
När paketet har skapats, kan du distribuera den till Azure.