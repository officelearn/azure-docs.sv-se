I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt _&lt;paste\_copied\_url\_here>_ med URL:en för fjärransluten Git som du sparade från [Skapa en webbapp](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
