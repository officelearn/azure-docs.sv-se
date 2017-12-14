Du kan skapa virtuella datorer (VM) i Azure och exponera dem till Internet med hjälp av en offentlig IP-adress. Som standard offentliga IP-adresser är dynamiska och den adress som är associerade med dem kan ändras när den virtuella datorn tas bort eller stoppats/frigjorts. För att garantera att den virtuella datorn alltid använder samma offentliga IP-adress, måste du skapa en statisk offentlig IP. 

Innan du kan implementera statiska offentliga IP-adresser i virtuella datorer, är det nödvändigt att förstå när du kan använda statiska offentliga IP-adresser och hur de används. Läs den [IP-adressering översikt](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) att lära dig mer om IP-adresser i Azure.

