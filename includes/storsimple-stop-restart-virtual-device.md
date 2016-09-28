#### Stoppa och starta en virtuell enhet
Om du vill stoppa en virtuell enhet, klickar du på namnet och klickar sedan på **Stäng av**. När den virtuella enheten stängs av, är dess status **Stoppar**. När den virtuella enheten har stängts av, är dess status **Stoppad**.

Du kan använda följande cmdletar för att stoppa och starta en virtuell enhet.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### Starta om en virtuell enhet

När en virtuell enhet är igång och du vill starta om den, klickar du på dess namn och klickar sedan på **Starta om**. Medan den virtuella enheten startas om är dess status **Startar om**. När den virtuella enheten är klar för användning, är dess status **Igång**.

Du kan använda följande cmdletar för att starta om en virtuell enhet.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`






<!--HONumber=Sep16_HO3-->


