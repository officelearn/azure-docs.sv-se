---
title: Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services | Microsoft Docs
description: Lär dig hur du aktiverar kapslad virtualisering på en virtuell mall i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71982000"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Aktivera kapslad virtualisering på en virtuell mall på en virtuell dator i Azure Lab Services
Den här artikeln beskriver hur du konfigurerar kapslad virtualisering på en mall för virtuella datorer i Azure Lab Services. Kapslad virtualisering används i Azure Lab Services när varje student i en klass kräver flera datorer.
 
## <a name="considerations"></a>Överväganden
Här är några saker att tänka på innan du konfigurerar ett labb med kapslad virtualisering.

- När du skapar ett nytt labb väljer du **medium (kapslad virtualisering)** eller **stora** storlekar för den virtuella dator storleken. De här virtuella dator storlekarna stöder kapslad virtualisering. 
- Välj en storlek som ger bästa prestanda för både värd-och klientens virtuella datorer.  Kom ihåg att när du använder virtualisering måste den storlek du väljer vara tillräcklig för inte bara en dator, men både värden och alla klient datorer som måste köras samtidigt.
- Klientens virtuella datorer har inte åtkomst till Azure-resurser, till exempel DNS-servrar i det virtuella Azure-nätverket.
- Den virtuella värd datorn kräver att installations programmet tillåter att klient datorn har Internet anslutning. 
- Virtuella klient datorer licensieras som oberoende datorer. Se [Microsoft-licensiering](https://www.microsoft.com/licensing/default) för information om licensiering för Microsoft operation Systems och produkter. Kontrol lera licens avtal för all annan program vara som används innan du konfigurerar mallen Machine.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Aktivera kapslad virtualisering på en virtuell mall
Stegen i det här avsnittet fokuserar på hur du konfigurerar kapslad virtualisering för Windows Server 2016 eller Windows Server 2019. Du kommer att använda ett skript för att konfigurera en mall för datorer med Hyper-V. För en automatiserad lösning, se skript i [labb tjänster Hyper-V-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). Följande steg beskriver hur du använder skriptet.

1. Om du använder Internet Explorer kan du behöva lägga till `https://github.com` i listan över betrodda platser. 
    1. Öppna Internet Explorer.
    1. Välj kugg hjuls ikonen och välj **Internet alternativ**.  
    1. När dialog rutan **Internet alternativ** visas väljer du **säkerhet**, Välj **Betrodda platser**, klicka på **platser** .
    1. När dialog rutan **Betrodda platser** visas lägger du till `https://github.com` i listan betrodda webbplatser och väljer **Stäng**.

        ![Betrodda platser](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Hämta git-databasfilerna som beskrivs i följande steg.  Alternativt kan git-lagringsplatsen klonas från [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Gå till [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Klicka på knappen ***klona eller hämta** .
    1. Klicka på **Hämta zip**.
    1. Extrahera ZIP-filen
1. Starta **PowerShell** i **Administratörs** läge.
1. I PowerShell-fönstret navigerar du till mappen med det nedladdade skriptet. Om du navigerar från den översta mappen i databasfilerna finns skriptet på `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Du kanske måste ändra körnings principen för att kunna köra skriptet. Kör följande kommando:
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. Kör skriptet:
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skriptet kan kräva att datorn startas om. Följ instruktionerna från skriptet och kör skriptet igen tills **skriptet har slutförts** i utdata.
1. Glöm inte att återställa körnings principen. Kör följande kommando: 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Sammanfattning
Nu är din mall maskin redo att skapa virtuella Hyper-V-datorer. Instruktioner om hur du skapar virtuella Hyper-V-datorer finns i [skapa en virtuell dator i Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Se även [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) för att ta en titt på tillgängliga operativ system och program.  

## <a name="next-steps"></a>Nästa steg 
Se följande artiklar:

- [Så här aktiverar du kapslad virtualisering i en virtuell Azure-dator](../../virtual-machines/windows/nested-virtualization.md) 
- [Installera Hyper-V-rollen på Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Labb tjänster Hyper-V-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
