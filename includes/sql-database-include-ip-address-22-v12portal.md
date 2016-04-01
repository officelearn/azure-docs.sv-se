
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Manage server-level firewall rules through the new Azure portal
-->


1. Melden Sie sich über die [Azure Preview-Portal](http://portal.azure.com/), unter http://portal.azure.com/.

2. Klicken Sie im linken Banner auf **Alle durchsuchen**. Die **Durchsuchen** Blatt wird angezeigt.

3. Führen Sie einen Bildlauf aus, und klicken Sie auf **SQL Server**. Die **SQL Server** Blatt wird angezeigt. 

    ![Suchen Sie Ihren Azure SQL-Datenbankserver im Portal][b21-FindServerInPortal]

4. Der Einfachheit halber klicken Sie auf das Minimieren-Steuerelement auf der früheren **Durchsuchen** Blatt.

5. Geben Sie im Filtertextfeld zunächst den Namen des Servers ein. Die Zeile wird angezeigt.

6. Klicken Sie auf die Zeile für den Server. Ein Blatt für den Server wird angezeigt.

7. Klicken Sie auf die Server-Blade auf **Einstellungen**. Die **Einstellungen** Blatt wird angezeigt.

8. Klicken Sie auf **Firewall**. Der **Firewall Settings** Blatt wird angezeigt. 

    ![Klicken Sie auf "Einstellungen > Firewall"][b31-SettingsFirewallNavig]

9. Klicken Sie auf **Hinzufügen von Client-IP-**. Geben Sie einen Namen für die neue Regel in das erste Textfeld ein.

10. Geben Sie die niedrigen und hohen IP-Adresswerte für den Bereich ein, den Sie aktivieren möchten.
 - Es kann praktisch, wenn das Ende der niedrigen Wert mit **.0** und High mit **.255**. 

    ![Fügen Sie einen erlaubten IP-Adressbereich hinzu][b41-AddRange]

11. Klicken Sie auf **Speichern**.



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->


