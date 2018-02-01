## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan använda följande skript för att skapa ett huvudnamn för tjänsten åtkomst till behållaren registret. Uppdatering av `ACR_NAME` variabeln med namnet på behållaren registret, och eventuellt den `--role` värde i den [az ad sp skapa-för-rbac] [ az-ad-sp-create-for-rbac] kommando för att ge olika behörigheter. Du måste ha den [Azure CLI](/cli/azure/install-azure-cli) installerat för att kunna använda det här skriptet.

När du har kört skriptet anteckna tjänstens huvudnamn **ID** och **lösenord**. När du har dess autentiseringsuppgifter kan konfigurera du dina program och tjänster för att autentisera till behållaren registret som tjänstens huvudnamn.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Använd ett befintligt huvudnamn för tjänsten

Om du vill ge registeråtkomst till en befintlig tjänstens huvudnamn, måste du tilldela en ny roll till tjänstens huvudnamn. Du kan bevilja pull, push och pull och ägare åtkomst som skapar en ny tjänst huvudnamn.

Följande skript använder den [az rolltilldelning skapa] [ az-role-assignment-create] kommando för att ge *pull* behörigheter till ett huvudnamn för tjänsten som du anger i den `SERVICE_PRINCIPAL_ID` variabeln. Justera det `--role` värde om du vill ge olika nivåer av åtkomst.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

Du kan hitta båda dessa skript på GitHub, samt versioner för PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
