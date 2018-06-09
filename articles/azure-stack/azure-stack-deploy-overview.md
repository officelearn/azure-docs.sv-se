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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235193"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Snabbstart: utvärdera Azure Stack Development Kit

Den [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) är en miljö för testning och utveckling som du kan distribuera om du vill utvärdera och visa Azure Stack-funktioner och tjänster. Kom igång med ASDK, måste du förbereda värden datormaskinvara och köra några skript (installationen tar flera timmar). Därefter kan du logga in på portalerna administratör eller användare att börja använda Azure-stacken.

## <a name="prerequisites"></a>Förutsättningar

### <a name="asdk-host-computer-requirements"></a>Krav för ASDK värden datorer

Innan du installerar ASDK, måste du förbereda den dator som är värd för development kit. Värddatorn development kit måste uppfylla maskinvara, programvara och krav som beskrivs i  **[granska ASDK distributionen överväganden vid planering av](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Du kan använda den [Azure Stack distributionskrav Kontrollera verktyget](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) när du har installerat operativsystemet på värddatorn development kit för att bekräfta att maskinvaran uppfyller alla krav.

### <a name="account-requirements"></a>Kontokrav

Du måste också välja mellan att använda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som identitetslösning för din distribution. Granska kontokraven för i  **[överväganden för planering av distribution](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Ladda ned och extrahera distributionspaketet

När du har förberett development kit värddatorn, ladda ned och extrahera ASDK distributionspaketet. Distributionspaketet innehåller filen Cloudbuilder.vhdx, som är en virtuell hårddisk (VHD) med ett startbart operativsystem och Azure Stack-installationsfilerna.

Du kan hämta distributionspaketet för development kit värden eller en annan dator. De extraherade filerna tar upp 60 GB ledigt diskutrymme, så använder en annan dator kan användas för att minska utrymmeskraven på development kit värden.

**[Ladda ned och extrahera Azure Stack Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Förbereda värddatorn

Innan du kan installera ASDK värdmiljön måste förberedas och systemet är konfigurerat för start från development kit VHD. När du startar om värden, den startar från CloudBuilder.vhdx och du kan börja distribuera ASDK.

**[Förbereda värddatorn ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installera ASDK på värddatorn

Efter att värddatorn startar från den virtuella Hårddisken, kan du distribuera development kit till den virtuella miljön Cloudbuilder. Du kan distribuera ASDK med det grafiska användargränssnittet (GUI) som angavs genom att köra PowerShell-skriptet asdk installer.ps1 eller från [PowerShell-kommandorad](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> När värden har startats från Cloudbuilder.vhdx-avbildning, har du möjlighet att konfigurera [Azure Stack telemetriinställningar](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *innan* installerar ASDK.

**[Installera Azure-stacken Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Utför konfigurationer efter distributionen

När du har installerat ASDK finns några rekommenderade efter installationen kontroller och konfigurationsändringar som ska göras.

**Verktyg**

Installera Azure-stacken PowerShell- och GitHub och kontrollera lyckade installationen med hjälp av cmdleten test-AzureStack.

**Identitetslösning**

Du måste aktivera både Azure Stack-administratör och klienten portaler för en distribution som använder Azure AD. Den här aktiveringen samtycker till att ge Azure-Stack-portalen och Azure Resource Manager rätt behörigheter (som visas på sidan medgivande) för alla användare av katalogen.

**Lösenordets giltighetstid**

Du ska återställa förfalloprincipen för lösenord för att se till att lösenordet för development kit värden inte upphör att gälla innan din utvärderingsversion har upphört att gälla.

> [!NOTE]
> Du har också möjlighet att konfigurera [Azure Stack telemetriinställningar](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *när* installerar ASDK.

**[Post ASDK distributionsuppgifter](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrera med Azure

Du måste registrera Azure stacken med Azure så att du kan [hämta Azure marketplace-objekt](.\asdk\asdk-marketplace-item.md) till Azure-stacken.

**[Registrera Azure stacken med Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Nästa steg

Grattis! Genom att slutföra stegen i den här snabbstarten du har en ASDK miljö med en [administratör](https://adminportal.local.azurestack.external) portal och en [användaren](https://portal.local.azurestack.external) portal.
