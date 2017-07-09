## <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Lägg till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <URI from previous step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen. Du uppmanas att ange lösenordet som du skapade tidigare när du skapade distributionsanvändaren. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user), inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Föregående kommando visar information liknande den i följande exempel:
