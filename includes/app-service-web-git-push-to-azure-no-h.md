I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Den här Azure remote skapades för dig i [skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Det här kommandot kan ta några minuter att köra. När du kör visas information som liknar följande exempel:
