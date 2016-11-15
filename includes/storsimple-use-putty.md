<!--author=SharS last changed: 9/17/15-->

#### <a name="to-connect-through-the-serial-console"></a>Anslut via seriekonsolen
1. Ansluta din seriekabel till enheten (direkt eller via en USB-serieadapter).
2. Öppna **Kontrollpanelen** och öppna sedan **Enhetshanteraren**.
3. Identifiera COM-portarna som det visas i följande bild.
   
     ![Anslut via seriekonsol](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Starta PuTTY. 
5. I den högra rutan, ändrar du **Anslutningstyp** till **Seriell**.
6. I den högra rutan, skriver du in lämplig COM-port. Kontrollera att de parametrarna för seriekonfigurationen är inställda på följande sätt:
   
   * Hastighet: 115 200
   * Databitar: 8
   * Stoppbitar: 1
   * Paritet: ingen
   * Flödeskontroll: ingen
     
     Inställningarna visas i följande bild.
     
     ![PuTTY-inställningar](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Om standardinställningen för flödeskontroll inte fungerar, testa att ställa in flödeskontrollen på XON/XOFF.
     > 
     > 
7. Klicka på **Öppna**, för att starta en seriesession.



<!--HONumber=Nov16_HO2-->


