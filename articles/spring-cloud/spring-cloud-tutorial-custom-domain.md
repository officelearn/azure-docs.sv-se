---
title: Självstudie – mappa en befintlig anpassad domän till Azure våren Cloud
description: Mappa ett befintligt DNS-namn (Custom Distributed Name Service) till Azure våren Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176996"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mappa en befintlig anpassad domän till Azure våren Cloud
DNS (Distributed Name Service) är en teknik för att lagra namn på nätverks-noder i ett nätverk. Den här självstudien mappar en domän, till exempel www.contoso.com, med hjälp av en CNAME-post. Den säkrar den anpassade domänen med ett certifikat och visar hur du tvingar Transport Layer Security (TLS), även kallat Secure Sockets Layer (SSL). 

Certifikaten krypterar webb trafik. Dessa TLS/SSL-certifikat kan lagras i Azure Key Vault. 

## <a name="prerequisites"></a>Krav
* Ett program som distribueras till Azure våren Cloud (se [snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart-launch-app-portal.md)eller Använd en befintlig app).
* Ett domän namn med åtkomst till DNS-registret för domän leverantören, till exempel GoDaddy.
* Ett privat certifikat från en tredje parts leverantör. Certifikatet måste matcha domänen.
* En distribuerad instans av [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importera certifikatet 
Proceduren för att importera ett certifikat kräver att PEM-eller PFX-filen finns på disk och att du måste ha en privat nyckel. 

Så här överför du ditt certifikat till nyckel valvet:
1. Gå till din Key Vault-instans.
1. I det vänstra navigerings fönstret klickar du på **certifikat**.
1. Klicka på **generera/importera**på den övre menyn.
1. I dialog rutan **skapa ett certifikat** under **metoden för att skapa certifikat**väljer `Import`du.
1. Under **överför certifikat fil**, navigerar du till certifikat plats och väljer den.
1. Under **lösen ord**anger du den privata nyckeln för certifikatet.
1. Klicka på **Skapa**.

![Importera certifikat 1](./media/custom-dns-tutorial/import-certificate-a.png)

Importera certifikat till Azure våren-molnet:
1. Gå till din tjänst instans. 
1. I det vänstra navigerings fönstret i appen väljer du **TLS/SSL-inställningar**.
1. Klicka sedan på **importera Key Vault certifikat**.

![Importera certifikatet](./media/custom-dns-tutorial/import-certificate.png)

När du har importerat certifikatet ser du det i listan över **certifikat för privat nyckel**.

![Certifikat för privat nyckel](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
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

En app kan ha flera domäner, men en domän kan bara mappas till en enda app. När du har mappat din anpassade domän till appen visas den i den anpassade domän tabellen.

![Anpassad domän tabell](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> En **osäker etikett för** din anpassade domän innebär att den inte har bundits till något SSL-certifikat än. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning.

## <a name="add-ssl-binding"></a>Lägg till SSL-bindning
I tabellen anpassad domän väljer du **Lägg till SSL-bindning** som visas i föregående bild.  
1. Välj ditt **certifikat** eller importera det.
1. Klicka på **Spara**.

![Lägg till SSL-bindning](./media/custom-dns-tutorial/add-ssl-binding.png)

När du har lagt till SSL-bindningen är domän tillståndet säker: **felfri**. 

![Lägg till SSL-bindning](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Använda HTTPS
Som standard kan alla fortfarande komma åt din app med HTTP, men du kan omdirigera alla HTTP-förfrågningar till HTTPS-porten.

På din app-sida väljer du **anpassad domän**i det vänstra navigerings fältet. Ange sedan **https**till *True*.

![Lägg till SSL-bindning](./media/custom-dns-tutorial/enforce-http.png)

När åtgärden har slutförts navigerar du till någon av HTTPS-URL: erna som pekar på din app. Observera att HTTP-URL: er inte fungerar.

## <a name="see-also"></a>Se även
* [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importera ett certifikat](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Starta din våren Cloud-App med Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

