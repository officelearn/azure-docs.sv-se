## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan använda följande skript för att skapa ett huvudnamn för tjänsten åtkomst till behållaren registret. Uppdatering av `ACR_NAME` variabeln med namnet på behållaren registret, och eventuellt den `--role` värde i den [az ad sp skapa-för-rbac] [ az-ad-sp-create-for-rbac] kommando för att ge olika behörigheter. Du måste ha den [Azure CLI](/cli/azure/install-azure-cli) installerat för att kunna använda det här skriptet.

När du har kört skriptet anteckna tjänstens huvudnamn **ID** och **lösenord**. När du har dess autentiseringsuppgifter kan konfigurera du dina program och tjänster för att autentisera till behållaren registret som tjänstens huvudnamn.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>Använd ett befintligt huvudnamn för tjänsten

Om du vill ge registeråtkomst till en befintlig tjänstens huvudnamn, måste du tilldela en ny roll till tjänstens huvudnamn. Du kan bevilja pull, push och pull och ägare åtkomst som skapar en ny tjänst huvudnamn.

Följande skript använder den [az rolltilldelning skapa] [ az-role-assignment-create] kommando för att ge *pull* behörigheter till ett huvudnamn för tjänsten som du anger i den `SERVICE_PRINCIPAL_ID` variabeln. Justera det `--role` värde om du vill ge olika nivåer av åtkomst.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create