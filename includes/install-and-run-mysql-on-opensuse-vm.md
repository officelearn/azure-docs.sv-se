
1. För att eskalera privilegier, skriver du:
   
        sudo -s
   
    Ange ditt lösenord
2. Om du vill installera MySQL Community Server edition, skriver du:
   
        zypper install mysql-community-server
   
    Vänta medan MySQL hämtas och installeras.
3. Om du vill ange MySQL att starta när systemet startas, skriver du:
   
        insserv mysql
4. Starta MySQL-daemon (mysqld) manuellt med det här kommandot:
   
        rcmysql start
   
    Om du vill kontrollera status för MySQL-daemon, skriver du:
   
        rcmysql status
   
    Om du vill stoppa MySQL-daemon, skriver du:
   
        rcmysql stop
   
   > [!IMPORTANT]
   > MySQL-rotlösenordet är tomt som standard efter installationen. Vi rekommenderar att du kör **mysql\_säker\_installation**, ett skript som hjälper till att säkra MySQL. Skriptet uppmanas du att ändra rotlösenordet MySQL, ta bort anonyma användarkonton, inaktivera fjärråtkomst rot-inloggningar, ta bort test databaser och läsa in tabellen privilegier. Vi rekommenderar att du svara Ja på alla dessa alternativ och ändra rotlösenordet.
   > 
   > 
5. Kör skriptet MySQL installationsskriptet skriver du:
   
        mysql_secure_installation
6. Logga in på MySQL:
   
        mysql -u root -p
   
    Ange rotlösenordet MySQL (som du har ändrat i föregående steg) och visas med en uppmaning där du kan utfärda SQL-instruktioner kan interagera med databasen.
7. Om du vill skapa en ny MySQL-användare, kör du följande kommando vid den **mysql >** prompten:
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Obs!, semikolon (;) i slutet av raden är avgörande för avslutning av kommandot.
8. Skapa en databas och ge den `mysqluser` användarbehörigheter, utfärda följande kommandon:
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Observera att databasen användarnamn och lösenord används endast av skript som ansluter till databasen.  Databasen användarkontonamn representerar inte nödvändigtvis faktiska användarkonton på systemet.
9. För att logga in från en annan dator, skriver du:
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    där `ip-address` är IP-adressen på den dator som du ansluter till MySQL.
10. Om du vill avsluta verktyget för administration av MySQL-databas, skriver du:
    
        quit

## <a name="add-an-endpoint"></a>Lägga till en slutpunkt
1. När MySQL har installerats kan behöver du konfigurera en slutpunkt för att få fjärråtkomst till MySQL. Logga in på den [Azure-portalen][AzurePortal]. Klicka på **virtuella datorer**, klicka på namnet på den nya virtuella datorn och klicka sedan på **slutpunkter**.
2. Klicka på **Lägg till** längst ned på sidan.
3. Lägga till en slutpunkt med namnet ”MySQL” med protokollet **TCP**, och **offentliga** och **privata** portar inställd på ”3306”.
4. För att fjärransluta till den virtuella datorn från datorn, skriver du:
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Till exempel med hjälp av den virtuella datorn som du skapade i den här självstudiekursen, ange följande kommando:
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://portal.azure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
