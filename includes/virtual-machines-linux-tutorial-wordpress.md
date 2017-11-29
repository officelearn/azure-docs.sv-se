## <a name="install-wordpress"></a>Installera WordPress

Om du vill prova traven installerar du en exempelapp. Exempelvis följande steg för att installera öppen källkod [WordPress](https://wordpress.org/) plattform för att skapa webbplatser och bloggar. Andra arbetsbelastningar försöka ta [Drupal](http://www.drupal.org) och [Moodle](https://moodle.org/). 

Den här WordPress-installationen är endast för konceptbeviset. Om du vill installera den senaste WordPress i produktion med rekommenderade säkerhetsinställningarna i [WordPress dokumentationen](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Installera WordPress-paket

Kör följande kommando:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Konfigurera WordPress

Konfigurera WordPress för att använda MySQL och PHP.

Skapa en textfil i arbetskatalogen, `wordpress.sql` att konfigurera MySQL-databas för WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Lägg till följande kommandon och ersätta ett lösenord för dina val för *yourPassword* (lämna övriga värden oförändrade). Om du tidigare konfigurerat en MySQL-säkerhetsprincip för att verifiera lösenordssäkerhet, kontrollera att lösenordet uppfyller styrka. Spara filen.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```

Kör följande kommando för att skapa databasen:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Eftersom filen `wordpress.sql` innehåller Databasautentiseringsuppgifter, ta bort den efter att använda:

```bash
sudo rm wordpress.sql
```

Om du vill konfigurera PHP, kör du följande kommando för att öppna en textredigerare önskat och skapa filen `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Kopiera följande rader i filen, ersätter lösenordet WordPress-databas för *yourPassword* (lämna övriga värden oförändrade). Spara sedan filen.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Flytta WordPress-installationen till dokumentroten web server:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Nu kan du slutför installationen av WordPress och publicera på plattformen. Öppna en webbläsare och gå till `http://yourPublicIPAddress/wordpress`. Ersätt offentliga IP-adressen för den virtuella datorn. Det bör likna den här avbildningen.

![Sidan för WordPress-installation](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)