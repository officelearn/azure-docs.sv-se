## Tjänstlänkning – överföring via peerkopplat VNet
Även om användningen av systemvägar förenklar trafik automatiskt för din distribution så finns det fall där du kan vilja kontrollera routingen för paket genom en virtuell installation.
I det här scenariot finns det två VNet i en prenumeration, HubVNet och VNet1, så som beskrivs i nedanstående diagram. Du kan distribuera Network Virtual Appliance(NVA) i VNet HubVNet. När du har etablerat VNet-peering mellan HubVNet och VNet1 ställer du in användardefinierade vägar och anger nästa hopp till NVA i HubVNet.

![NVA-överföring](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [!NOTE]
> För enkelheten skull förutsätter vi här att alla VNet finns i samma prenumeration. Men det fungerar också mellan olika prenumerationer.
> 
> 

Nyckelegenskapen för att aktivera överföringsroutning är parametern "Tillåt vidarebefordrad trafik". Detta gör att trafik kan accepteras och skickas från/till NVA i den peerkopplade VNet:en.  

<!--HONumber=Sep16_HO4-->


