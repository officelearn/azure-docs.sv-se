---
title: Hantera en privat slutpunktsanslutning i Azure
description: Lär dig hur du hanterar privata slutpunktsanslutningar i Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452969"
---
# <a name="manage-a-private-endpoint-connection"></a>Hantera en privat slutpunktsanslutning
Azure Private Link fungerar på en modell för godkännandesamtalsflöde där private link-tjänstkonsumenten kan begära en anslutning till tjänsteleverantören för att ha förbrukat tjänsten. Tjänsteleverantören kan sedan bestämma om konsumenten ska kunna ansluta eller inte. Azure Private Link gör det möjligt för tjänsteleverantörer att hantera den privata slutpunktsanslutningen på sina resurser. Den här artikeln innehåller instruktioner om hur du hanterar de privata slutpunktsanslutningarna.

![Hantera privata slutpunkter](media/manage-private-endpoint/manage-private-endpoint.png)

Det finns två metoder för godkännande av anslutningar som en privat länktjänstkonsument kan välja mellan:
- **Automatisk**: Om tjänstekonsumenten har RBAC-behörigheter för tjänsteleverantörens resurs kan konsumenten välja den automatiska godkännandemetoden. I det här fallet, när begäran når tjänsteleverantörens resurs, krävs ingen åtgärd från tjänsteleverantören och anslutningen godkänns automatiskt. 
- **Handbok**: Tvärtom, om tjänstekonsumenten inte har RBAC-behörigheter för tjänsteleverantörens resurs, kan konsumenten välja den manuella godkännandemetoden. I det här fallet visas anslutningsbegäran på tjänstresurserna som **Väntande**. Tjänsteleverantören måste godkänna begäran manuellt innan anslutningar kan upprättas. I manuella fall kan servicekonsumenten också ange ett meddelande med begäran om att ge mer kontext till tjänsteleverantören. Tjänsteleverantören har följande alternativ att välja mellan för alla privata slutpunktsanslutningar: **Godkänd**, **Avvisa**, **Ta bort**.

Tabellen nedan visar de olika tjänstprovideråtgärderna och de resulterande anslutningstillstånden för privata slutpunkter.  Tjänsteleverantören kan också ändra anslutningstillståndet för privat slutpunktsanslutning vid ett senare tillfälle utan konsumentintervention. Åtgärden uppdaterar slutpunktens tillstånd på konsumentsidan. 


|Åtgärd för tjänsteleverantören   |Privat slutpunktstillstånd för tjänstkonsument   |Beskrivning   |
|---------|---------|---------|
|Inget    |    Väntande åtgärder     |    Anslutningen skapas manuellt och väntar på godkännande av private link-resursägaren.       |
|Godkänn    |  Godkända       |  Anslutningen godkändes automatiskt eller manuellt och är klar att användas.     |
|Avvisa     | Avvisad        | Anslutningen avvisades av ägaren till den privata länkresursen.        |
|Ta bort    |  Frånkopplad       | Anslutningen togs bort av ägaren till den privata länkresursen, den privata slutpunkten blir informativ och bör tas bort för rensning.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Hantera privata slutpunktsanslutningar på Azure PaaS-resurser
Portal är den metod som föredras för att hantera privata slutpunktsanslutningar på Azure PaaS-resurser. För närvarande har vi inte PowerShell/CLI-stöd för hantering av anslutningar på Azure PaaS-resurser.
1. Logga in på Azure Portal på https://portal.azure.com.
2. Navigera till Private Link Center.
3. Under **Resurser**väljer du den resurstyp som du vill hantera de privata slutpunktsanslutningarna.
4. För var och en av resurstypen kan du visa antalet privata slutpunktsanslutningar som är associerade med den. Du kan filtrera resurserna efter behov.
5. Välj privata slutpunktsanslutningar.  Under anslutningarna i listan väljer du den anslutning som du vill hantera. 
6. Du kan ändra anslutningens tillstånd genom att välja bland alternativen högst upp.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Hantera privata slutpunktsanslutningar på en kund/partnerägd Private Link-tjänst

Azure PowerShell och Azure CLI är de metod som föredras för att hantera privata slutpunktsanslutningar på Microsoft Partner Services eller kundägda tjänster. För närvarande har vi inget portalstöd för hantering av anslutningar på en Private Link-tjänst.  
 
### <a name="powershell"></a>PowerShell 
  
Använd följande PowerShell-kommandon för att hantera privata slutpunktsanslutningar.  
#### <a name="get-private-link-connection-states"></a>Hämta anslutningstillstånd för Privat länk 
Använd `Get-AzPrivateLinkService` cmdlet för att hämta de privata slutpunktsanslutningarna och deras tillstånd.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slutpunktsanslutning 
 
Använd `Approve-AzPrivateEndpointConnection` cmdleten för att godkänna en privat slutpunktsanslutning. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Neka privat slutpunktsanslutning 
 
Använd `Deny-AzPrivateEndpointConnection` cmdleten för att avvisa en privat slutpunktsanslutning. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Ta bort privat slutpunktsanslutning 
 
Använd `Remove-AzPrivateEndpointConnection` cmdleten för att ta bort en privat slutpunktsanslutning. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Används `az network private-link-service update` för att hantera dina privata slutpunktsanslutningar. Anslutningstillståndet anges i ```azurecli connection-status``` parametern. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Nästa steg
- [Läs mer om privata slutpunkter](private-endpoint-overview.md)
 
