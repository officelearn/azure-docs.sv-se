I det här steget kan testa du tillgänglighetsgruppens lyssnare med hjälp av ett klientprogram som körs på samma nätverk.

Klientanslutning har följande krav:

* Klientanslutningar till lyssnaren måste komma från datorer som finns i en annan molntjänst än den som är värd för tillgänglighetsrepliker alltid på.
* Om Always On-repliker är i olika undernät, måste klienter ange *MultisubnetFailover = True* i anslutningssträngen. Det här villkoret resulterar i parallella anslutningsförsök till repliker i olika undernät. Det här scenariot omfattar en mellan region alltid på tillgänglighet distribution.

Ett exempel är att ansluta till lyssnaren från någon av de virtuella datorerna i samma virtuella Azure-nätverket (men inte som värd för en replik). Ett enkelt sätt att slutföra det här testet är att försöka ansluta SQL Server Management Studio till tillgänglighetsgruppens lyssnare. En annan enkel metod är att köra [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), enligt följande:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Om värdet för EndpointPort är *1433*, du behöver inte ange i anropet. Det föregående anropet förutsätter också att klientdatorn är ansluten till samma domän och att anroparen har beviljats behörigheter för databasen med hjälp av Windows-autentisering.
> 
> 

Glöm inte att redundansväxla tillgänglighetsgruppen att se till att klienter kan ansluta till lyssnaren för växling vid fel när du testar lyssnaren.

