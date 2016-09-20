## Peering över prenumerationer

I det här scenariot skapar du en peering mellan två VNet som hör till olika prenumerationer.

![scenario för flera prenumerationer](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet-peering bygger på rollbaserad åtkomstkontroll (RBAC) för auktorisering. I scenariot för flera prenumerationer måste du först bevilja behörighet till användare som skapar peeringlänken: Obs! Om samma användare har behörighet över båda prenumerationerna kan du hoppa över steg 1-4 nedan. 


<!--HONumber=sep16_HO1-->


