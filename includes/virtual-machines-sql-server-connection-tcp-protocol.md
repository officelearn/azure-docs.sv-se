1. Sök efter **Configuration Manager** medan du fortfarande är fjärrskrivbordsansluten till den virtuella datorn:

    ![Öppna SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Expandera **SQL Server-nätverkskonfiguration** i konsolfönstret i Konfigurationshanteraren för SQL Server.

1. Klicka på **Protokoll för MSSQLSERVER** (standardinstansnamnet) i konsolfönstret. Högerklicka på **TCP** i informationsfönstret och klicka på **Aktivera** om det inte redan har aktiverats.

    ![Aktivera TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klicka på **SQL Server-tjänster** i konsolfönstret. Högerklicka på **SQL Server (*instansnamnet*)** (standardinstansen är **SQL Server (MSSQLSERVER)**) i informationsfönstret och klicka sedan på **Starta om** för att stoppa och starta om instansen av SQL Server.

    ![Starta om databasmotorn](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Stäng Konfigurationshanteraren för SQL Server.

Mer information om hur du aktiverar protokoll för SQL Server-databasmotorn finns i [Enable or Disable a Server Network Protocol](http://msdn.microsoft.com/library/ms191294.aspx) (Aktivera eller inaktivera ett servernätverksprotokoll).
