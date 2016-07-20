#### Skapa offentliga slutpunkter på den virtuella enheten

1. Logga in på den klassiska Azure-portalen.

- Klicka på **Virtual Machines** och välj sedan den virtuella datorn som används som din virtuella enhet.

- Klicka på **Slutpunkter**. **Slutpunkter**-sidan listar alla slutpunkter för den virtuella datorn.

- Klicka på **Lägg till**. Dialogrutan **Lägg till slutpunkt** visas. Klicka på pilen för att fortsätta.

- Som **namn** anger du följande namn för slutpunkten: **WinRMHttps**.

- Som **protokoll**, anger du **TCP**.

- Som **offentlig port**, anger du de portnummer som du vill använda för anslutningen.

- Som **privat port**, anger du **5986**.

- Klicka på kryssmarkeringen för att skapa slutpunkten.

När slutpunkten har skapats kan du se information om den för att avgöra den offentliga virtuella IP-adressen (VIP). Anteckna den adressen.


<!--HONumber=Jun16_HO2-->


