---
title: Starta och stoppa Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Lär dig mer om att starta och stänga ned Azure Stack Development Kit (ASDK).
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e0b830a8c785ecab7e8f0e90cf9b9a702cbf25db
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250698"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Starta och stoppa Azure Stack Development Kit (ASDK)
Det rekommenderas inte att starta om värddatorn ASDK. I stället bör du följa procedurerna i den här artikeln för att stänga av och starta om tjänsterna ASDK korrekt. 

## <a name="stop-azure-stack"></a>Stoppa Azure Stack 
Använd följande PowerShell-kommandon för att stänga av Azure Stack-tjänster och värddatorn ASDK korrekt:

1. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
2. Öppna PowerShell som administratör (inte PowerShell ISE).
3. Kör följande kommandon för att upprätta en session för privilegierad slutpunkt (program): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. I program-session använder du sedan den **Stop-AzureStack** cmdlet för att stoppa Azure Stack-tjänster och stänga av värddatorn ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Granska PowerShell-utdata för att säkerställa att alla tjänster som Azure Stack är har stängts av innan ASDK värddatorn stängs av. Stäng av processen tar flera minuter.

## <a name="start-azure-stack"></a>Starta Azure Stack 
ASDK tjänster ska startas automatiskt när värddatorn har startats. Men varierar starttiden för ASDK infrastruktur tjänster beroende på prestanda för ASDK värden datorns maskinvarukonfiguration. Det kan ta flera timmar för alla tjänster som startas om i vissa fall.

Oavsett hur ASDK stängdes, använder du följande steg för att kontrollera att alla Azure Stack-tjänster som är igång och fullt fungerande när värddatorn är påslagen: 

1. Slå på värddatorn ASDK. 
2. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
3. Öppna PowerShell som administratör (inte PowerShell ISE).
4. Kör följande kommandon för att upprätta en session för privilegierad slutpunkt (program):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Kör följande kommandon för att kontrollera status för start av Azure Stack-tjänster i program som följer:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Granska utdata för att säkerställa att Azure Stack-tjänster har startat om utan.

Läs mer om rekommenderade procedurerna för att korrekt stänger och startar om Azure Stack-tjänster i [Start och stopp Azure Stack](../azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Felsöka start och avstängning 
Utför dessa steg om Azure Stack-tjänster har inte startar inom två timmar efter power på värddatorn ASDK:

1. Logga in som AzureStack\CloudAdmin på värddatorn ASDK.
2. Öppna PowerShell som administratör (inte PowerShell ISE).
3. Kör följande kommandon för att upprätta en session för privilegierad slutpunkt (program):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Kör följande kommandon för att kontrollera status för start av Azure Stack-tjänster i program som följer:

   ```powershell
   Test-AzureStack
   ```
5. Granska utdata och åtgärda eventuella fel. Mer information finns i [kör ett verifieringstest av Azure Stack](../azure-stack-diagnostic-test.md).
6. Starta om Azure Stack-tjänster från i program-session genom att köra den **Start AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

Om kör **Start AzureStack** orsakar, gå till den [Supportforum för Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) att få hjälp med felsökning ASDK. 

## <a name="next-steps"></a>Nästa steg 
Läs mer om Azure Stack diagnostikverktyg och utfärda loggning, se [diagnostiska verktyg för Azure Stack](../azure-stack-diagnostics.md).
