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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.custom: mvc
ms.reviewer: misainat
ms.openlocfilehash: 8b33937a4da807578d371a95f2c06de451404036
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037472"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Snabbstart: utvärdera Azure Stack Development Kit

Den [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) är en miljö för testning och utveckling som du kan distribuera för att utvärdera och demonstrera Azure Stack-funktioner och tjänster. Om du vill komma igång med ASDK, måste du förbereda värden datormaskinvara och sedan köra några skript (installationen tar flera timmar). Efter det kan du logga in på portalerna administratör eller användare att börja använda Azure Stack.

## <a name="prerequisites"></a>Förutsättningar

### <a name="asdk-host-computer-requirements"></a>ASDK värdkrav för dator

Innan du installerar ASDK, måste du förbereda den dator som är värd för i development kit. Värddatorn development kit måste uppfylla maskinvara, programvara och krav som beskrivs i  **[granska ASDK distributionen saker att tänka på](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Du kan använda den [distributionskrav för Azure Stack Kontrollera verktyget](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) när du har installerat operativsystemet på värddatorn development kit att bekräfta att maskinvaran uppfyller alla krav.

### <a name="account-requirements"></a>-Kontokrav

Du måste också välja mellan att använda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som ID-lösning för din distribution. Granska kontokraven för i  **[distribution saker att tänka på](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Ladda ned och extrahera distributionspaketet

När du har förberett datorn development kit värden, hämta och extrahera ASDK distributionspaketet. Distributionspaketet innehåller filen Cloudbuilder.vhdx, som är en virtuell hårddisk (VHD) med en startbar operativsystem, och Azure Stack-installationsfilerna.

Du kan hämta distributionspaketet till development kit värden eller till en annan dator. Extraherade distributionsfiler ta 60 GB ledigt diskutrymme, så att använda en annan dator kan hjälpa att minska utrymmeskraven på development kit värden.

**[Ladda ned och extrahera Azure Stack Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Förbereda värddatorn

Innan du kan installera ASDK, måste du förbereda värdmiljön och systemet konfigurerade för start från development kit VHD. När du startar om värden, den startar från CloudBuilder.vhdx och du kan börja distribuera ASDK.

**[Förbereda ASDK värddatorn](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installera ASDK på värddatorn

När värddatorn har startats från den virtuella Hårddisken, kan du distribuera i development kit till den virtuella miljön Cloudbuilder. Du kan distribuera ASDK med hjälp av det grafiska användargränssnittet (GUI) som tillhandahålls genom att köra PowerShell-skriptet asdk installer.ps1 eller från [PowerShell-kommandorad](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> När värden startas från Cloudbuilder.vhdx bild, har du möjlighet att konfigurera [Azure Stack-telemetriinställningar](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *innan* installerar ASDK.

**[Installera Azure Stack Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Utför efter distributionen konfigurationer

När du har installerat ASDK, finns det några rekommenderade efter installationen kontroller och konfigurationsändringar som ska göras.

**Verktyg**

Installera Azure Stack PowerShell- och GitHub och kontrollera installationen med hjälp av cmdleten test-AzureStack framgång.

**ID-lösning**

Du måste aktivera båda Azure Stack-administratör och klient portaler för en distribution som använder Azure AD. Den här aktiveringen godkänner ger Azure Stack-portalen och Azure Resource Manager rätt behörigheter (som visas på sidan medgivande) för alla användare av katalogen.

**Lösenordets giltighetstid**

Du bör återställa förfalloprincipen för lösenord för att se till att lösenordet för development kit värden inte går ut innan din utvärderingsversion har upphört att gälla.

> [!NOTE]
> Du har också möjlighet att konfigurera [Azure Stack-telemetriinställningar](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *när* installerar ASDK.

**[Post-ASDK distributionsuppgifter](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrera med Azure

Du måste registrera Azure Stack med Azure så att du kan [ladda ned Azure marketplace-objekt](.\asdk\asdk-marketplace-item.md) till Azure Stack.

**[Registrera Azure Stack med Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Nästa steg

Grattis! Om du har slutfört stegen i den här snabbstarten har du en ASDK-miljö med en [administratör](https://adminportal.local.azurestack.external) portal och en [användaren](https://portal.local.azurestack.external) portal.
