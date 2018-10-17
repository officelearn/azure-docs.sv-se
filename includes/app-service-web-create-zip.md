## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

Kontrollera att du fortfarande befinner dig i rotkatalogen för exempelprojektet. Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Senare kan du överföra den här ZIP-filen till Azure och distribuera den till App Service.