---
title: Självstudie – mappa en befintlig anpassad domän till Azure våren Cloud
description: Mappa ett befintligt DNS-namn (Custom Distributed Name Service) till Azure våren Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5892fd732a1e66b2b7dd4c1031cabfcbcc768c6d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326158"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mappa en befintlig anpassad domän till Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Domain Name Service (DNS) är en teknik för att lagra namn på nätverks-noder i ett nätverk. Den här självstudien mappar en domän, till exempel www.contoso.com, med hjälp av en CNAME-post. Den säkrar den anpassade domänen med ett certifikat och visar hur du tvingar Transport Layer Security (TLS), även kallat Secure Sockets Layer (SSL). 

Certifikaten krypterar webb trafik. Dessa TLS/SSL-certifikat kan lagras i Azure Key Vault. 

## <a name="prerequisites"></a>Förutsättningar
* Ett program som distribueras till Azure våren Cloud (se [snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart.md)eller Använd en befintlig app).
* Ett domän namn med åtkomst till DNS-registret för domän leverantören, till exempel GoDaddy.
* Ett privat certifikat (det vill säga ditt självsignerade certifikat) från en tredje parts leverantör. Certifikatet måste matcha domänen.
* En distribuerad instans av [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importera certifikatet 
Proceduren för att importera ett certifikat kräver att PEM-eller PFX-filen finns på disk och att du måste ha en privat nyckel. 

Så här överför du ditt certifikat till nyckel valvet:
1. Gå till din Key Vault-instans.
1. I det vänstra navigerings fönstret klickar du på **certifikat**.
1. Klicka på **generera/importera**på den övre menyn.
1. I dialog rutan **skapa ett certifikat** under **metoden för att skapa certifikat**väljer du `Import` .
1. Under **överför certifikat fil**, navigerar du till certifikat plats och väljer den.
1. Under **lösen ord**anger du den privata nyckeln för certifikatet.
1. Klicka på **Skapa**.

    ![Importera certifikat 1](./media/custom-dns-tutorial/import-certificate-a.png)

För att ge Azure våren Cloud åtkomst till ditt nyckel valv innan du importerar certifikat:
1. Gå till din Key Vault-instans.
1. Klicka på **åtkomst principer**i det vänstra navigerings fönstret.
1. I den övre menyn klickar du på **Lägg till åtkomst princip**.
1. Fyll i informationen och klicka på knappen **Lägg till** och **Spara** sedan åtkomst principer.

| Hemlig behörighet | Certifikat behörighet | Välj huvud konto |
|--|--|--|
| Hämta, lista | Hämta, lista | Azure våren Cloud-domän – hantering |

![Importera certifikat 2](./media/custom-dns-tutorial/import-certificate-b.png)

Du kan också använda Azure CLI för att ge Azure våren Cloud åtkomst till nyckel valvet.

Hämta objekt-ID: t via följande kommando.
```
az ad sp show --id <service principal id> --query objectId
```

Ge Azure våren Cloud Läs åtkomst till Key Vault, Ersätt objekt-ID: t i följande kommando.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list
``` 

Importera certifikat till Azure våren-molnet:
1. Gå till din tjänst instans. 
1. I det vänstra navigerings fönstret i appen väljer du **TLS/SSL-inställningar**.
1. Klicka sedan på **importera Key Vault certifikat**.

    ![Importera certifikatet](./media/custom-dns-tutorial/import-certificate.png)

Du kan också använda Azure CLI för att importera certifikatet:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Se till att ge Azure våren Cloud åtkomst till ditt nyckel valv innan du kör det tidigare import certifikat kommandot. Om du inte har gjort det kan du köra följande kommando för att ge åtkomst behörighet.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

När du har importerat certifikatet ser du det i listan över **certifikat för privat nyckel**.

![Certifikat för privat nyckel](./media/custom-dns-tutorial/key-certificates.png)

Du kan också använda Azure CLI för att visa en lista över certifikat:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande binda certifikatet till en speciell domän. Följ stegen i det här dokumentet under rubriken **Lägg till SSL-bindning**.

## <a name="add-custom-domain"></a>Lägg till anpassad domän
Du kan använda en CNAME-post för att mappa ett anpassat DNS-namn till Azure våren Cloud. 

> [!NOTE] 
> A-posten stöds inte. 

### <a name="create-the-cname-record"></a>Skapa CNAME-posten
Gå till DNS-providern och Lägg till en CNAME-post för att mappa din domän till <service_name>. azuremicroservices.io. Här <service_name> är namnet på din Azure våren Cloud-instans. Vi stöder domänen med jokertecken och under domänen. När du har lagt till CNAME kommer sidan DNS-poster att likna följande exempel: 

![Sida för DNS-poster](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mappa din anpassade domän till Azure våren Cloud-appen
Om du inte har ett program i Azure våren-molnet följer du anvisningarna i [snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Gå till program sidan.

1. Välj **anpassad domän**.
2. **Lägg sedan till anpassad domän**. 

    ![Anpassad domän](./media/custom-dns-tutorial/custom-domain.png)

3. Ange det fullständigt kvalificerade domän namnet som du har lagt till en CNAME-post för, till exempel www.contoso.com. Kontrol lera att post typen hostname är inställd på CNAME (<service_name>. azuremicroservices.io)
4. Klicka på **validera** för att aktivera knappen **Lägg till** .
5. Klicka på **Lägg till**.

    ![Lägg till anpassad domän](./media/custom-dns-tutorial/add-custom-domain.png)

Du kan också använda Azure CLI för att lägga till en anpassad domän:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

En app kan ha flera domäner, men en domän kan bara mappas till en enda app. När du har mappat din anpassade domän till appen visas den i den anpassade domän tabellen.

![Anpassad domän tabell](./media/custom-dns-tutorial/custom-domain-table.png)

Du kan också använda Azure CLI för att visa en lista över anpassade domäner:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```

> [!NOTE]
> En **osäker etikett för** din anpassade domän innebär att den inte har bundits till något SSL-certifikat än. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning.

## <a name="add-ssl-binding"></a>Lägg till SSL-bindning
I tabellen anpassad domän väljer du **Lägg till SSL-bindning** som visas i föregående bild.  
1. Välj ditt **certifikat** eller importera det.
1. Klicka på **Spara**.

    ![Lägg till SSL-bindning 1](./media/custom-dns-tutorial/add-ssl-binding.png)

Du kan också använda Azure CLI för att **lägga till SSL-bindning**:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

När du har lagt till SSL-bindningen är domän tillståndet säker: **felfri**. 

![Lägg till SSL-bindning 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Använda HTTPS
Som standard kan alla fortfarande komma åt din app med HTTP, men du kan omdirigera alla HTTP-förfrågningar till HTTPS-porten.

På din app-sida väljer du **anpassad domän**i det vänstra navigerings fältet. Ange sedan **https**till *True*.

![Lägg till SSL-bindning 3](./media/custom-dns-tutorial/enforce-http.png)

Du kan också använda Azure CLI för att genomdriva HTTPS:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```

När åtgärden har slutförts navigerar du till någon av HTTPS-URL: erna som pekar på din app. Observera att HTTP-URL: er inte fungerar.

## <a name="see-also"></a>Se även
* [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importera ett certifikat](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Starta din våren Cloud-App med hjälp av Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

