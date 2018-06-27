Anvisningarna för att avregistrera en processerver är olika beroende på dess anslutningsstatus till konfigurationsservern.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Avregistrera en processerver som är i kopplat läge

1. Fjärranslut till processervern som administratör.
2. Starta **Kontrollpanelen** och öppna **Program > Avinstallera ett program**
3. Avinstallera ett program med namnet **Microsoft Azure Site Recovery Configuration/Process Server** (Microsoft Azure Site Recovery konfigurations-/processerver)
4. När steg 3 är slutfört kan du avinstallera **Microsoft Azure Site Recovery Configuration/Process Server Dependencies** (Beroenden för Microsoft Azure Site Recovery konfigurations-/processerver)

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Avregistrera en processerver som är i frånkopplat läge

> [!WARNING]
> Stegen nedan ska användas om det inte finns något sätt att återskapa den virtuella datorn som processervern installerades på.

1. Logga in på av konfigurationsservern som administratör.
2. Öppna en administrativ kommandotolk och gå till katalogen `%ProgramData%\ASR\home\svsystems\bin`.
3. Kör kommandot nu.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Information om processervern rensas från systemet.
