1. Anslut till den virtuella processerverdatorn via Anslutning till fjärrskrivbord.
2. När inloggningen är klar öppnas konfigurationsverktyget för processervern automatiskt och du ombeds ange
  * Konfigurationsserverns fullständiga domännamn eller IP-adress
  * Porten som konfigurationsservern lyssnar på. Värdet bör vara 443
  * Lösenfras för anslutningen till konfigurationsservern.
  * Dataöverföringsporten som ska konfigureras för den här processervern. Använd standardvärdet, såvida du inte har bytt till en annan port i din miljö.

    ![Registrera processervern](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Klicka på knappen Spara för att spara konfigurationen.
4. När registreringen är klar börjar processervern visas under din konfigurationsserver och kan nu användas för återställning efter fel.

> [!TIP]
> Du kan starta konfigurationsverktyget för processervern genom att dubbelklicka på genvägen **cspsconfigtool** på skrivbordet på den virtuella datorn.


<!--HONumber=Feb17_HO1-->


