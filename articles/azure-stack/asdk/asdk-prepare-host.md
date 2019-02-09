---
title: Förbereda värddatorn Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Beskriver hur du förbereder Azure Stack Development Kit (ASDK) värddatorn för ASDK installation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 36012c023025b8304dfaf9cc63997f600ef6cbe8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962731"
---
# <a name="prepare-the-asdk-host-computer"></a>Förbereda ASDK värddatorn
Innan du kan installera ASDK på värddatorn, måste ASDK värden förberedas för installation. När värddatorn development kit har förberetts, att den startas från CloudBuilder.vhdx virtuella hårddisken till börjar ASDK distributionen.

## <a name="prepare-the-development-kit-host-computer"></a>Förbereda värddatorn development kit
Innan du kan installera ASDK på värddatorn, måste du förbereda datorn för ASDK värdmiljön.
1. Logga in som lokal administratör till värddatorn development kit.
2. Kontrollera att filen CloudBuilder.vhdx har flyttats till roten C:\ enheten (C:\CloudBuilder.vhdx).
3. Kör följande skript för att ladda ned installationsfilen för development kit (asdk installer.ps1) från den [Azure Stack GitHub-lagringsplatsen för verktyg](https://github.com/Azure/AzureStack-Tools) till den **C:\AzureStack_Installer** mapp på din värddator för Development kit:

   > [!IMPORTANT]
   > Glöm inte att hämta filen asdk installer.ps1 varje gång som du installerar ASDK. Frekventa ändringar i det här skriptet och den senaste versionen som ska användas för varje ASDK-distribution. Äldre versioner av skriptet kanske inte fungerar med den senaste versionen.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Starta från en upphöjd PowerShell-konsol på **C:\AzureStack_Installer\asdk-installer.ps1** skript för och klicka sedan på **förbereda miljön**.

    ![](media/asdk-prepare-host/1.PNG) 

5. På den **Välj Cloudbuilder vhdx** i installationsprogrammet, bläddra till och välj den **cloudbuilder.vhdx** -fil som du har hämtat och extraherat i [de föregående stegen](asdk-download.md). På den här sidan kan du också om du vill aktivera den **lägga till drivrutiner** markerar du kryssrutan om du vill lägga till ytterligare drivrutiner till värddatorn development kit. Klicka på **Nästa**.  

    ![](media/asdk-prepare-host/2.PNG)

6. På den **valfria inställningar** anger du den lokala administratören kontoinformation för värddator för development kit och klicka sedan på **nästa**.<br><br>Om du inte anger autentiseringsuppgifter för lokal administratör i det här steget behöver du direkt eller KVM-åtkomst till värden efter omstart av datorn som en del av hur du konfigurerar i development kit.

   ![](media/asdk-prepare-host/3.PNG)

    Du kan också ange värden för följande valfria inställningar:
    - **ComputerName**: Det här alternativet anger namnet för development kit värden. Namnet måste vara kompatibel med FQDN krav och måste vara högst 15 tecken eller färre. Standardvärdet är ett slumpmässigt datornamn som genererats av Windows.
    - **Statisk IP-konfiguration**: Anger distributionen för att använda en statisk IP-adress. När installationsprogrammet startar om i cloudbuilder.vhdx, annars konfigureras nätverksgränssnitt med DHCP. Om du väljer att använda en statisk IP-konfiguration visas ytterligare alternativ där du måste också:
      - Välj ett nätverkskort. Kontrollera att du kan ansluta till nätverkskortet innan du klickar på **nästa**.
      - Se till att det visade **IP-adress**, **Gateway**, och **DNS** värden är korrekta och klicka sedan på **nästa**.
13. Klicka på **nästa** att starta av förberedelseprocessen.
14. När förberedelserna anger **slutförd**, klickar du på **nästa**.

    ![](media/asdk-prepare-host/4.PNG)

15. Klicka på **starta om nu** starta värddatorn development kit i cloudbuilder.vhdx och [fortsätter distributionsprocessen](asdk-install.md).

    ![](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Nästa steg
[Installera ASDK](asdk-install.md)
