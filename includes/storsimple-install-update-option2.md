<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Installera uppdatering 1.2 från den klassiska Azure-portalen
1. I den klassiska Azure-portalen går du till den **enheter** och välja din enhet.
2. Gå till **enheter** > **konfigurera**.
3. Under **nätverksgränssnitt**, kontrollera först att du har minst ett nätverksgränssnitt som är iSCSI-aktiverade. Leta upp nätverksgränssnitt (förutom DATA 0) som har en gateway som har tilldelats.
4. Inaktivera nätverksgränssnittet som har en tilldelad gateway och spara den ändrade konfigurationen. Observera gränssnitt för nätverksinställningar bevaras och så när du återaktivera det här nätverksgränssnittet senare, portalen kommer återgå till de ursprungliga inställningarna.
5. Du kan nu [använda den klassiska Azure-portalen för att installera uppdateringen 1.2](#install-update-12-via-the-azure-classic-portal). Följ instruktionerna från steg 3 i den här proceduren. När du har installerat alla uppdateringar kan återaktivera du det nätverksgränssnitt som du inaktiverade.

