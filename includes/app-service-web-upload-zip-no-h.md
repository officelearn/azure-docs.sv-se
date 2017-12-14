I den [Azure-portalen](https://portal.azure.com), klickar du på **resursgrupper** > **moln-shell-lagring –\<your_region >**  >   **\<storage_account_name >**.

![Hitta molnet Shell storage-konto](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

I den **översikt** sidan på lagringskontot, Välj **filer**.

Välj automatiskt genererade filresursen och **överför**. Filresursen är monterat i molnet Shell som `clouddrive`.

![Hitta knappen Skicka](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Klickar du på filen Väljaren och välj ZIP-filen och klicka på **överför**. 

I molnet-gränssnittet använder `ls` att verifiera att du kan se den överförda ZIP-filen i standard `clouddrive` delar.

```azurecli-interactive
ls clouddrive
```
