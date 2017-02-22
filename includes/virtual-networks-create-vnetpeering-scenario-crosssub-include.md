## <a name="a-namex-subapeering-across-subscriptions"></a><a name="x-sub"></a>Peering över prenumerationer
I det här scenariot skapar du en peering mellan två virtuella nätverk som finns i olika prenumerationer.

![scenario för flera prenumerationer](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet-peering bygger på rollbaserad åtkomstkontroll (RBAC) för auktorisering. I ett scenario med anslutning mellan prenumerationer måste du först bevilja behörighet till användare som skapar peeringlänken.

> [!NOTE]
> Om samma användare har behörighet till båda prenumerationerna kan du hoppa över steg 1–4 nedan.
> 
> 



<!--HONumber=Feb17_HO1-->


