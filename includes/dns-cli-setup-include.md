## <a name="set-up-azure-cli-for-azure-dns"></a>Konfigurera Azure CLI för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Installera den senaste versionen av Azure CLI. Den finns tillgänglig för Windows, Linux och MAC. Mer information finns på [Installera Azure CLI](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna ett konsolfönster och autentisera med dina autentiseringsuppgifter. Mer information finns i [logga in på Azure från Azure CLI](/cli/azure/authenticate-azure-cli)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Växla CLI-läge

Azure DNS använder Azure Resource Manager. Se till att du byter CLI-läge så att du kan använda Azure Resource Manager-kommandon.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Välja prenumerationen

Kontrollera prenumerationerna för kontot.

```azurecli
azure account list
```

Välj vilka av dina Azure-prenumerationer som du vill använda.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registrera resursprovider

Azure DNS-tjänsten hanteras av Microsoft.Network-resursprovidern. Din Azure-prenumeration måste vara registrerad att använda den här resursprovidern innan du kan använda Azure DNS. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
azure provider register --namespace Microsoft.Network
```

