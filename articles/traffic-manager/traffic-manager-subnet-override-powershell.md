---
title: Azure Traffic Manager-undernät åsidosätta med Azure PowerShell | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Traffic Manager-undernät åsidosättning används för att åsidosätta routningsmetoden för en Traffic Manager-profil för att dirigera trafik till en slutpunkt baserat på slutanvändarens IP-adress via fördefinierat IP-intervall till slutpunktsmappningar med Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938427"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Undernätsförehållning i Traffic Manager med Azure Powershell

Med undernätsförståningen i Traffic Manager kan du ändra routningsmetoden för en profil.  Tillägget av en åsidosättning dirigerar trafik baserat på slutanvändarens IP-adress med ett fördefinierat IP-intervall till slutpunktsmappning. 

## <a name="how-subnet-override-works"></a>Så här fungerar åsidosättning av undernät

När åsidosättningar i undernät läggs till i en traffic manager-profil kontrollerar Traffic Manager först om det finns en åsidosättning av undernätet för slutanvändarens IP-adress. Om en sådan hittas dirigeras användarens DNS-fråga till motsvarande slutpunkt.  Om en mappning inte hittas kommer Traffic Manager att återgå till profilens ursprungliga routningsmetod. 

IP-adressintervallen kan anges som antingen CIDR-intervall (till exempel 1.2.3.0/24) eller som adressintervall (till exempel 1.2.3.4-5.6.7.8). IP-intervallen som är associerade med varje slutpunkt måste vara unika för den slutpunkten. Eventuell överlappning av IP-intervall mellan olika slutpunkter gör att profilen avvisas av Traffic Manager.

Det finns två typer av routningsprofiler som stöder åsidosättningar i undernät:

* **Geografisk** - Om Traffic Manager hittar en åsidosättning av undernätet för DNS-frågans IP-adress dirigeras frågan till slutpunkten oavsett slutpunktens hälsotillstånd.
* **Prestanda** - Om Traffic Manager hittar en åsidosättning av undernätet för DNS-frågans IP-adress dirigeras trafiken bara till slutpunkten om den är felfri.  Traffic Manager kommer att falla tillbaka till prestanda routning heuristiska om undernät åsidosättning slutpunkten inte är felfri.

## <a name="create-a-traffic-manager-subnet-override"></a>Skapa en åsidosättning av Trafikhanteraren

Om du vill skapa en åsidosättning av Traffic Manager-undernät kan du använda Azure PowerShell för att lägga till undernäten för åsidosättningen i Traffic Manager-slutpunkten.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, 1.0.0 eller senare. Du kan `Get-Module -ListAvailable Az` köra för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Login-AzAccount` också köra för att logga in på Azure.


1. **Hämta slutpunkten för Traffic Manager:**

    Om du vill aktivera åsidosättningen av undernätet hämtar du den slutpunkt som du vill lägga till åsidosättningen i och lagrar den i en variabel med [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Ersätt namn, profilnamn och ResourceGroupName med värdena för slutpunkten som du ändrar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Lägg till IP-adressintervallet till slutpunkten:**
    
    Om du vill lägga till IP-adressintervallet i slutpunkten använder du [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) för att lägga till intervallet.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    När intervallen har lagts till använder [du Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) för att uppdatera slutpunkten.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Borttagning av IP-adressintervallet kan slutföras med hjälp av [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Hämta slutpunkten för Traffic Manager:**

    Om du vill aktivera åsidosättningen av undernätet hämtar du den slutpunkt som du vill lägga till åsidosättningen i och lagrar den i en variabel med [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Ersätt namn, profilnamn och ResourceGroupName med värdena för slutpunkten som du ändrar.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Ta bort IP-adressintervallet från slutpunkten:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     När intervallen har tagits bort använder du [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) för att uppdatera slutpunkten.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Nästa steg
Läs mer om [trafikstyrningsmetoder i](traffic-manager-routing-methods.md)Traffic Manager .

Lär dig mer om [trafikroutningsmetoden för undernät](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
