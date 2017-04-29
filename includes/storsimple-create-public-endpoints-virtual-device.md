#### <a name="to-create-public-endpoints-on-the-virtual-device"></a>Skapa offentliga slutpunkter på den virtuella enheten

1. Logga in på den klassiska Azure-portalen.
2. Klicka på **Virtual Machines** och välj sedan den virtuella datorn som används som din virtuella enhet.
3. Klicka på **Slutpunkter**. **Slutpunkter**-sidan listar alla slutpunkter för den virtuella datorn.
4. Klicka på **Lägg till**. Dialogrutan **Lägg till slutpunkt** visas. Klicka på pilen för att fortsätta.
5. Som **namn** anger du följande namn för slutpunkten: **WinRMHttps**.
6. Som **protokoll**, anger du **TCP**.
7. Som **offentlig port**, anger du de portnummer som du vill använda för anslutningen.
8. Som **privat port**, anger du **5986**.
9. Klicka på kryssmarkeringen för att skapa slutpunkten.

När slutpunkten har skapats kan du se information om den för att avgöra den offentliga virtuella IP-adressen (VIP). Anteckna den adressen.

