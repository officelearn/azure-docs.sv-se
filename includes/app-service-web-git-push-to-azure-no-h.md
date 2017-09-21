I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <URI from previous step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Föregående kommando visar information liknande den i följande exempel:
