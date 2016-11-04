Varje blobb i Azure-lagring måste befinna sig i en behållare. Behållaren utgör en del av blobbnamnet. `mycontainer` är till exempel namnet på behållaren i de här exempelblobb-URI:erna:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Ett behållarnamn måste vara ett giltigt DNS-namn som överensstämmer med följande namngivningsregler:

1. Behållarnamnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).
2. Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. Flera bindestreck i följd är inte tillåtna i behållarnamn.
3. Alla bokstäver i ett behållarnamn måste vara gemener.
4. Behållarnamn måste vara mellan 3 och 63 tecken långa.

> [!IMPORTANT]
> Observera att behållarnamn får innehålla endast gemener. Om du tar med en versal i ett behållarnamn, eller på annat sätt bryter mot namngivningsreglerna, kan du få ett 400-fel (felaktig begäran). 
> 
> 

<!--HONumber=Sep16_HO3-->


