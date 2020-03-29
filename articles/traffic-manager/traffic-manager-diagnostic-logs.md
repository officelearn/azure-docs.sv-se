---
title: Aktivera diagnostikloggning i Azure Traffic Manager
description: Lär dig hur du aktiverar diagnostikloggning för Traffic Manager-profilen och kommer åt loggfilerna som skapas som ett resultat.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938679"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Aktivera diagnostikloggning i Azure Traffic Manager

I den här artikeln beskrivs hur du aktiverar diagnostikloggning och åtkomstloggdata för en Traffic Manager-profil.

Diagnostikloggar i Azure Traffic Manager kan ge insikt i beteendet hos Traffic Manager-profilresursen. Du kan till exempel använda profilens loggdata för att avgöra varför enskilda avsökningar har tajmats ut mot en slutpunkt.

## <a name="enable-diagnostic-logging"></a>Aktivera diagnostisk loggning

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, 1.0.0 eller senare. Du kan `Get-Module -ListAvailable Az` köra för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Login-AzAccount` också köra för att logga in på Azure.

1. **Hämta Traffic Manager-profilen:**

    För att aktivera diagnostikloggning behöver du ID för en Traffic Manager-profil. Hämta den Traffic Manager-profil som du vill aktivera diagnostikloggning för med [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Utdata innehåller Traffic Manager-profilens ID-information.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Aktivera diagnostikloggning för Traffic Manager-profilen:**

    Aktivera diagnostikloggning för Traffic Manager-profilen med hjälp av ID som erhölls i föregående steg med [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Följande kommando lagrar utförliga loggar för Traffic Manager-profilen till ett angivet Azure Storage-konto. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verifiera diagnostikinställningar:**

      Verifiera diagnostikinställningar för Traffic [Manager-profilen med Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). I följande kommando visas de kategorier som loggas för en resurs.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Kontrollera att alla loggkategorier som är associerade med Traffic Manager-profilresursen visas som aktiverade. Kontrollera också att lagringskontot är korrekt inställt.

## <a name="access-log-files"></a>Komma åt loggfiler
1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till ditt Azure Storage-konto i portalen.
2. På **sidan Översikt för** ditt Azure-lagringskonto väljer du **Blobbar**under **Tjänster** .
3. För **Behållare**väljer du **insights-logs-probehealthstatusevents**och navigerar ned till filen PT1H.json och klickar på **Hämta** för att hämta och spara en kopia av loggfilen.

    ![Komma åt loggfiler för din Traffic Manager-profil från en blob-lagring](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Loggschema för Traffic Manager

Alla diagnostikloggar som är tillgängliga via Azure Monitor delar ett gemensamt schema på den högsta nivån, med flexibilitet för varje tjänst att avge unika egenskaper för sina egna händelser. Schema för diagnostikloggar på den högsta nivån finns i [Tjänster, scheman och kategorier som stöds för Azure Diagnostic Logs](../azure-monitor/platform/tutorial-dashboards.md).

Följande tabell innehåller loggschema som är specifikt för profilresursen Azure Traffic Manager.

|||||
|----|----|---|---|
|**Fältnamn**|**Fälttyp**|**Definition**|**Exempel**|
|Slutpunktnamn|String|Namnet på slutpunkten för Traffic Manager vars hälsostatus registreras.|*myPrimaryEndpoint*|
|Status|String|Hälsostatusen för slutpunkten för Traffic Manager som avsökdes. Statusen kan antingen vara **upp** eller **ned**.|**Upp**|
|||||

## <a name="next-steps"></a>Nästa steg

* Läs mer om [övervakning av Trafikhanteraren](traffic-manager-monitoring.md)

