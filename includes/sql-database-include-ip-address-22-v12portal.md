
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Logga in på den [Azure-portalen](https://portal.azure.com/) på http://portal.azure.com/.
2. Klicka i den vänstra banderollen **Bläddra bland alla**. Den **Bläddra** bladet visas.
3. Bläddra och klicka på **SQL-servrar**. Den **SQL-servrar** bladet visas.
   
    ![Hitta din Azure SQL Database-server i portalen][b21-FindServerInPortal]
4. Klicka på Minimera-kontrollen på den tidigare för enkelhetens skull **Bläddra** bladet.
5. Börja skriva namnet på servern i filter-textrutan. Raden visas.
6. Klicka på raden för servern. Ett blad för servern visas.
7. Klicka på din server-bladet **inställningar**. Den **inställningar** bladet visas.
8. Klicka på **brandväggen**. Den **brandväggsinställningar** bladet visas.
   
    ![Klicka på Inställningar > brandväggen][b31-SettingsFirewallNavig]
9. Klicka på **Lägg till klient IP**. Skriv ett namn för din nya regel i den första textrutan.
10. Ange de lägsta och högsta IP-adressvärdena för intervallet som du vill aktivera.
    
    * Det kan vara praktiskt att ha lågt värde för end med **.0** och hög med **.255**.
    
    ![Lägg till ett IP-adressintervall för att tillåta][b41-AddRange]
11. Klicka på **Spara**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
