---
title: Utvärdera Azure Stack Development Kit | Microsoft Docs
description: Lär dig hur du distribuerar Azure Stack Development Kit i utvärderingssyfte.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Snabbstart: Utvärdera Azure Stack Development Kit
Den [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) är en miljö för testning och utveckling som du kan distribuera om du vill utvärdera och visa Azure Stack-funktioner och tjänster. Kom igång med ASDK, måste du förbereda värden datormaskinvara och köra några skript (installationen tar flera timmar). Därefter kan du logga in på portalerna administratörs- och att börja använda Azure-stacken.

## <a name="prerequisites"></a>Förutsättningar 
Innan du installerar ASDK, måste du förbereda den dator som är värd för development kit (development kit värd). Värddatorn development kit måste uppfylla minimikraven för maskinvara, programvara och nätverkskrav. 

Du måste också välja mellan att använda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som identitetslösning för din distribution. 

Var noga med att development kit värden uppfyller minimikraven på maskinvara och att du har gjort din identitet lösning beslut om innan du påbörjar distributionen så att installationen körs utan problem. 

**[Granska överväganden vid planering av ASDK distributionen](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> Du kan använda den [Azure Stack distributionskrav Kontrollera verktyget](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) när du har installerat operativsystemet på värddatorn development kit för att bekräfta att maskinvaran uppfyller alla krav.

## <a name="download-and-extract-the-deployment-package"></a>Ladda ned och extrahera distributionspaketet
När du har kontrollerat att värddatorn development kit uppfyller de grundläggande kraven för att installera ASDK är nästa steg att ladda ned och extrahera ASDK distributionspaketet. Distributionspaketet innehåller filen Cloudbuilder.vhdx, som är en virtuell hårddisk som innehåller ett startbart operativsystem och Azure Stack-installationsfilerna.

Du kan hämta distributionspaketet för development kit värden eller en annan dator. De extraherade filerna tar upp 60 GB ledigt diskutrymme, så använder en annan dator kan minska maskinvarukraven för development kit värden.

**[Ladda ned och extrahera Azure Stack Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Förbereda värddatorn development kit
Innan du kan installera ASDK på värddatorn, måste du förbereda miljön och systemets konfigurerade för start från VHD. När värddatorn development kit har förberetts, startar från CloudBuilder.vhdx virtuella hårddisken så att du kan börja ASDK distribution.

**[Förbereda värddatorn ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installera ASDK på värddatorn
När du har förberett värddatorn development kit kan ASDK distribueras till CloudBuilder.vhdx-bild. ASDK kan distribueras med hjälp av ett grafiskt användargränssnitt (GUI) som angavs genom att hämta och köra PowerShell-skript asdk installer.ps1 eller helt från [kommandoraden](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Du kan också när värddatorn har startats in i CloudBuilder.vhdx, kan du konfigurera [Azure Stack telemetriinställningar](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *innan* installerar ASDK.


**[Installera Azure-stacken Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Utför konfigurationer efter distributionen
När du har installerat ASDK finns några rekommenderade efter installationen kontroller och konfigurationsändringar som ska göras. Du kan verifiera installationen har installerats kan använda cmdleten test-AzureStack och installera Azure-stacken PowerShell- och GitHub. 

Du måste aktivera både Azure Stack-administratör och klienten portaler när distributioner som använder Azure AD. Den här aktiveringen samtycker till att ge Azure-Stack-portalen och Azure Resource Manager rätt behörigheter (som visas på sidan medgivande) för alla användare av katalogen.

Du bör också återställa förfalloprincipen för lösenord för att se till att lösenordet för development kit värden inte upphör att gälla innan din utvärderingsversion har upphört att gälla.

> [!NOTE]
> Du kan också konfigurera [Azure Stack telemetriinställningar](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *när* installerar ASDK.

**[Post ASDK distributionsuppgifter](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrera med Azure
Du måste registrera Azure stacken med Azure så att du kan [hämta Azure marketplace-objekt](.\asdk\asdk-marketplace-item.md) till Azure-stacken.

**[Registrera Azure stacken med Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Nästa steg
Grattis! När du slutfört de här stegen har du en development kit miljö med både [administratör](https://adminportal.local.azurestack.external) och [användaren](https://portal.local.azurestack.external) portaler. 
