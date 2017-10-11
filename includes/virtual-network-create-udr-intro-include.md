Även om användningen av systemvägar förenklar trafik automatiskt för din distribution så finns det fall där du kan vilja kontrollera routingen för paket genom en virtuell installation. Du kan göra det genom att skapa användardefinierade vägar som anger next hop för paket som flödar till ett specifikt undernät så att de istället går till din virtuella installation och som aktiverar IP-vidarebefordring för VM:en som kör den virtuella installationen.

Några av de fall där virtuella installationer kan användas är:

* Övervaka trafik med ett intrång identifiering system (ID)
* Kontrollera trafik med en brandvägg

Mer information om UDR och IP-vidarebefordran finns [användardefinierade vägar och IP-vidarebefordring](../articles/virtual-network/virtual-networks-udr-overview.md).

