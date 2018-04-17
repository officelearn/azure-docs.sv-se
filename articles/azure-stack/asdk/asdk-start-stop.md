---
title: Starta och stoppa Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Lär dig hur du startar och stänga ned Azure Stack Development Kit (ASDK).
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
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Starta och stoppa Azure Stack Development Kit (ASDK)
Det rekommenderas inte att starta om värddatorn ASDK. I stället bör du följa procedurerna i den här artikeln Stäng och starta om tjänsterna ASDK korrekt. 

## <a name="stop-azure-stack"></a>Stoppa Azure Stack 
Om du vill ha stängts ordentligt Azure Stack-tjänster och värddatorn ASDK, Använd följande PowerShell-kommandon:

1. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
2. Öppna PowerShell som administratör (inte PowerShell ISE).
3. Kör följande kommandon för att upprätta en session Privilegierade slutpunkt (program): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Använd sedan följande i program-session på **stoppa AzureStack** att stoppa tjänsterna Azure Stack och stänga av värddatorn ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Granska utdata PowerShell för att säkerställa att alla Azure Stack-tjänster har stängts av innan ASDK värddatorn stängs av. Avslutningsprocessen tar flera minuter.

## <a name="start-azure-stack"></a>Starta Azure-stacken 
ASDK tjänster ska startas automatiskt när värddatorn har startats. Dock varierar ASDK infrastruktur services starttiden beroende på prestandan för ASDK värden datorns maskinvarukonfiguration. Det kan ta flera timmar för alla tjänster som startas i vissa fall.

Oavsett hur ASDK stängdes, bör du använda följande steg för att kontrollera att alla tjänster som Azure-stacken är igång och fungerar när värddatorn är påslagen: 

1. Slå på värddatorn ASDK. 
2. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
3. Öppna PowerShell som administratör (inte PowerShell ISE).
4. Kör följande kommandon för att upprätta en session Privilegierade slutpunkt (program):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Kör följande kommandon för att kontrollera status för start av Azure-stacken tjänster i program-sessionen:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Granska utdata för att säkerställa att Azure Stack-tjänster har startats om korrekt.

Mer information om rekommenderade procedurerna för att korrekt Avsluta och starta om Azure-stacken tjänster finns [Start och stopp Azure Stack](.\.\azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Felsöka start och stopp 
Utför de här stegen om Azure Stack-tjänster har inte startar inom två timmar efter power på värddatorn ASDK:

1. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
2. Öppna PowerShell som administratör (inte PowerShell ISE).
3. Kör följande kommandon för att upprätta en session Privilegierade slutpunkt (program):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Kör följande kommandon för att kontrollera status för start av Azure-stacken tjänster i program-sessionen:

   ```powershell
   Test-AzureStack
   ```
5. Granska utdata och åtgärda eventuella fel. Mer information finns i [kör ett verifieringstest i Azure-stacken](.\.\azure-stack-diagnostic-test.md).
6. Starta om Azure Stack-tjänster från program-sessionen genom att köra den **Start AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

Om kör **Start AzureStack** resulterar i ett fel, finns det [Azure Stack Supportforum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) få ASDK felsökning. 

## <a name="next-steps"></a>Nästa steg 
Lär dig mer om Azure-stacken diagnosverktyg och utfärda loggning, se [Azure Stack diagnosverktyg](.\.\azure-stack-diagnostics.md).
