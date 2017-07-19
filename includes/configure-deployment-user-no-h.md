Skapa autentiseringsuppgifter för distribution med kommandot [az webapp deployment user set](/cli/azure/webapp/deployment/user#set).

En distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp. Användarnamn och lösenord är på kontonivå. De skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration.

I följande kommando ersätter du  *\<användarnamn >* och  *\<lösenord >* med ett nytt användarnamn och lösenord.

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Användarnamnet måste vara unikt. Lösenordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror, symboler. Om du ser felet ` 'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet ` 'Bad Request'. Details: 400` ska du använda ett starkare lösenord.

Du behöver bara skapa distributionsanvändaren en gång. Du kan använda den för alla Azure-distributioner.

> [!NOTE]
> Anteckna användarnamn och lösenord. Du kommer att behöva dem för att distribuera webbappen.
>
>