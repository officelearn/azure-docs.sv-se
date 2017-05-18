## <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare  

För FTP och lokal Git måste du ha en distributionsanvändare konfigurerad på servern för att autentisera din distribution.

> [!NOTE]
> En distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp.
> `username` och `password` är på kontonivå. De skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration.
>

Kör kommandot [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) för att skapa dina autentiseringsuppgifter för distribution.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

Användarnamnet måste vara unikt och lösenordet måste vara starkt. Om du ser felet ` 'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet ` 'Bad Request'. Details: 400` ska du använda ett starkare lösenord.

Du behöver bara skapa distributionsanvändaren en gång. Du kan använda den för alla Azure-distributioner.

Anteckna användarnamnet och lösenordet, eftersom de används i ett senare skede när du distribuerar appen.