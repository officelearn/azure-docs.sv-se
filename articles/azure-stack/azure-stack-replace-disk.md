---
title: "Ersätta en fysisk disk i Azure-stacken | Microsoft Docs"
description: "Beskriver processen för hur du ersätter en fysisk disk i Azure-stacken."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 26e5e9f8882cc764922a2cbf0f39e7a3d1b6995b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Ersätta en fysisk disk i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Den här artikeln beskriver den allmänna processen för att ersätta en fysisk disk i Azure-stacken. Om en fysisk disk kraschar, ska du ersätta det så snart som möjligt.

Du kan använda den här proceduren för integrerade system och development kit distributioner som har diskar som växlas.

Faktiska diskbyte stegen varierar utifrån maskinvaruleverantören OEM-tillverkaren (OEM). Dokumentationen leverantörens fältet utbytbara enhet (FRU) för detaljerade anvisningar som är specifika för ditt system. 

## <a name="review-disk-alert-information"></a>Granska disk aviseringsinformation
När en disk kraschar kan du få ett meddelande som talar om att anslutningen har brutits till en fysisk disk. 

 ![Aviseringen visar anslutningen bröts till fysisk disk](media/azure-stack-replace-disk/DiskAlert.png)

Om du öppnar du aviseringen innehåller varningsbeskrivningen noden skala enhet och platsen för den disk som du måste ersätta exakta fysiska plats. Ytterligare Azure Stack hjälper dig att identifiera den skadade disken med hjälp av Indikator för indikatorn funktioner.

 ## <a name="replace-the-disk"></a>Ersätt disken

Följ anvisningarna för din OEM maskinvaruleverantören FRU för faktiska diskbyte.

För att förhindra användningen av en disk som inte stöds i ett integrerat system, blockerar systemet diskar som inte stöds av leverantören. Om du försöker använda en disk som inte stöds, om en ny avisering att en disk har har placerats i karantän på grund av en modell som inte stöds eller inbyggd programvara.

När du ersätter disken Azure Stack identifierar den nya disken och automatiskt startar reparationsprocessen virtuell disk.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Kontrollera status för reparation av virtuell disk
 
 När du ersätter disken kan du övervaka hälsotillståndet för virtuell disk och reparera jobbförloppet med hjälp av Privilegierade slutpunkten. Följ dessa steg från valfri dator som har en nätverksanslutning till Privilegierade slutpunkten.

1. Öppna Windows PowerShell-sessionen och Anslut till Privilegierade slutpunkten.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Kör följande kommando för att visa hälsan för den virtuella disken:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![PowerShell-utdata från kommandot Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Kör följande kommando för att visa aktuell jobbstatus för lagring:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![PowerShell-utdata från kommandot Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Felsöka reparation av virtuell disk

Om den virtuella disken reparera visas jobb låsta, kör följande kommando för att starta om jobbet:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
