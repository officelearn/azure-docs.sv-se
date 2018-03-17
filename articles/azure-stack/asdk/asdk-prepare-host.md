---
title: "Förbereda värddatorn Azure Stack Development Kit (ASDK) | Microsoft Docs"
description: "Beskriver hur du förbereder värddatorn Azure Stack Development Kit (ASDK) för ASDK-installationen."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8b1a6298ab32dc364aa1543e4a8d5db47b02a098
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="prepare-the-asdk-host-computer"></a>Förbereda värddatorn ASDK
Innan du kan installera ASDK på värddatorn, måste du förbereda miljön ASDK för installation. När värddatorn development kit har förberetts, startas den från CloudBuilder.vhdx virtuella hårddisken för att påbörja ASDK distributionen.

## <a name="prepare-the-development-kit-host-computer"></a>Förbereda värddatorn development kit
Innan du kan installera ASDK på värddatorn, måste du förbereda datorn för ASDK värdmiljö.
1. Logga in som lokal administratör på värddatorn development kit.
2. Kontrollera att filen CloudBuilder.vhdx har flyttats till roten på enhet C:\ (C:\CloudBuilder.vhdx).
3. Kör följande skript för att hämta installationsfilen för development kit (asdk installer.ps1) från den [Azure Stack GitHub verktyg databasen](https://github.com/Azure/AzureStack-Tools) till den **C:\AzureStack_Installer** mapp på din värddatorn för Development kit:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Starta från en upphöjd PowerShell-konsol i **C:\AzureStack_Installer\asdk-installer.ps1** skript och klicka sedan på **förbereda miljön**.

    ![](media/asdk-prepare-host/1.PNG) 

5. På den **Välj Cloudbuilder vhdx** sidan i installationsprogrammet, bläddra till och välj den **cloudbuilder.vhdx** -fil som du har hämtat och extraherat i [i föregående steg](asdk-download.md). På den här sidan kan du kan också välja, att den **lägga till drivrutiner** kryssruta om du behöver lägga till ytterligare drivrutiner till värddatorn development kit. Klicka på **Nästa**.  

    ![](media/asdk-prepare-host/2.PNG)

6. På den **valfria inställningar** anger du den lokala administratören kontoinformationen för värddatorn development kit och klicka sedan på **nästa**. Du kan också ange värden för följande valfria inställningar:
  - **ComputerName**: det här alternativet anger namn för development kit värden. Namnet måste uppfylla krav för FQDN och måste vara högst 15 tecken eller färre. Standardvärdet är ett slumpmässigt datornamn som genererats av Windows.
  - **Tidszon**: Anger tidszonen för development kit värden. Standardvärdet är (UTC-8:00) Stillahavstid (USA och Kanada).
  - **Statisk IP-konfiguration**: Anger distributionen för att använda en statisk IP-adress. När installationsprogrammet startar om i cloudbuilder.vhx, annars konfigureras nätverksgränssnitten med DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Om du inte anger autentiseringsuppgifter för lokal administratör i det här steget, behöver du direkt eller KVM-åtkomst till värden efter omstart av datorn som en del av hur du konfigurerar i development kit.

7. Om du väljer en statisk IP-konfiguration i föregående steg, måste du nu:
    - Välj ett nätverkskort. Kontrollera att du kan ansluta till nätverkskortet innan du klickar på **nästa**.
    - Se till att den **IP-adress**, **Gateway**, och **DNS** värden är korrekta och klicka sedan på **nästa**.
13. Klicka på **nästa** att starta förberedelseprocessen.
14. När förberedelsen anger **slutförd**, klickar du på **nästa**.
15. Klicka på **starta om nu** att starta cloudbuilder.vhdx development kit värddatorn och [fortsätta distributionsprocessen](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>Nästa steg
[Installera ASDK](asdk-install.md)