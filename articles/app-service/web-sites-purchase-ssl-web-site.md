---
title: Köp och konfigurera ett SSL-certifikat från Azure - App Service | Microsoft Docs
description: Lär dig hur du köper en App Service-certifikat och bind den till App Service-appen
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: 784cb5248dab2b9554c67347e1b9b848e1a9e985
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820792"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Köp och konfigurera ett SSL-certifikat för Azure App Service

Den här självstudien visar hur du skyddar webbappen genom att skapa (köpa) ett App Service-certifikat i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) och kopplar den till en App Service-app.

> [!TIP]
> App Service-certifikat kan användas för alla Azure- eller icke - Azure-tjänster och är inte begränsat till App Services. Om du vill göra det måste du skapa en lokal PFX-kopia av en App Service-certifikat som du kan använda det var du vill. Mer information finns i [skapar en lokal PFX-kopia av ett App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="prerequisites"></a>Förutsättningar

Att följa den här guiden:

- [Skapa en App Service-app](/azure/app-service/)
- [Mappa ett domännamn till din webbapp](app-service-web-tutorial-custom-domain.md) eller [köp och konfigurera i Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Starta certifikatbeställning

Starta en App Service certificate order i den <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service Certificate Skapa sida</a>.

![Skapandet av certifikat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Använd följande tabell för att konfigurera certifikatet. Klicka på **Skapa** när du är klar.

| Inställning | Beskrivning |
|-|-|
| Namn | Ett eget namn för din App Service-certifikat. |
| Värdnamnets domän utan www | Om du anger här rotdomänen, får du ett certifikat som skyddar *både* rotdomän och `www` underdomänen. Att säkra en underdomän, ange det fullständigt kvalificerade domännamnet för underdomänen här (till exempel `mysubdomain.contoso.com`). |
| Prenumeration | Datacenter som är värd för webbappen. |
| Resursgrupp | Den resursgrupp som innehåller certifikatet. Du kan använda en ny resursgrupp eller Välj samma resursgrupp som din App Service-app, till exempel. |
| Certifikat-SKU | Avgör vilken typ av certifikat för att skapa, om ett standardcertifikat eller en [jokerteckencertifikat](https://wikipedia.org/wiki/Wildcard_certificate). |
| Juridiska villkor | Klicka för att bekräfta att du godkänner de juridiska villkoren. |

## <a name="store-in-azure-key-vault"></a>Store i Azure Key Vault

När certifikatet inköpsprocessen är klar, finns det några fler steg som du måste utföra innan du kan börja använda det här certifikatet. 

Välj certifikatet i den [App Service-certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sidan och klicka sedan på **Certifikatkonfigureringen** > **steg 1: Store**.

![Infoga bild av redo att lagra i KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) är en Azure-tjänst som hjälper till att skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Det här är den med för App Service-certifikat.

I den **Key Vault-Status** klickar du på **Key Vault-lagret** att skapa ett nytt valv eller välj ett befintligt valv. Använd följande tabell för att konfigurera valvet och klicka på Skapa om du vill skapa ett nytt valv. Se till att skapa nytt Key Vault i samma prenumeration och resursgrupp grupp.

| Inställning | Beskrivning |
|-|-|
| Namn | Ett unikt namn som består för alfanumeriska tecken och bindestreck. |
| Resursgrupp | Välj samma resursgrupp som din App Service-certifikat som en rekommendation. |
| Plats | Välj samma plats som din App Service-app. |
| Prisnivå | Mer information finns i [prisinformation för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Åtkomstprinciper| Definierar program och kunna komma till valvet-resurser. Du kan konfigurera den senare, följa stegen i [bevilja flera program åtkomst till key vault](../key-vault/key-vault-group-permissions-for-apps.md). |
| Virtual Network-åtkomst | Begränsa vault åtkomsten till vissa Azure-nätverk. Du kan konfigurera den senare, följa stegen i [konfigurera Azure Key Vault brandväggar och virtuella nätverk](../key-vault/key-vault-network-security.md) |

När du har valt valvet kan stänga den **Key Vault-lagret** sidan. Den **Store** alternativet ska visa en grön bock för att lyckas. Låt sidan öppet för nästa steg.

## <a name="verify-domain-ownership"></a>Verifiera domänägarskap

Från samma **Certifikatkonfigureringen** du använde i det sista steget, klickar du på **steg 2: Kontrollera**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Välj **App Service-verifiering**. Eftersom du redan har mappats till domänen till din webbapp (se [krav](#prerequisites)), har redan verifierats. Klicka bara på **Kontrollera** att slutföra det här steget. Klicka på den **uppdatera** knappen tills meddelandet **certifikatet har Domänverifierats** visas.

> [!NOTE]
> Stöds fyra typer av domän verifieringsmetoder: 
> 
> - **App Service** -det enklaste alternativet när domänen har redan mappats till en App Service-app i samma prenumeration. Den drar nytta av det faktum att App Service-appen har redan verifierats äger domänen.
> - **Domän** -Kontrollera en [App Service-domän som du har köpt från Azure](manage-custom-dns-buy-domain.md). Azure lägger till verifiering TXT-posten för dig och Slutför automatiskt.
> - **E-post** -verifiera domänen genom att skicka ett e-postmeddelande till domänadministratör. Anvisningar finns när du väljer alternativet.
> - **Manuell** -verifiera domänen med hjälp av en HTML-sida (**Standard** certifikat endast) eller en DNS TXT-post. Anvisningar finns när du väljer alternativet.

## <a name="bind-certificate-to-app"></a>Binda certifikatet till appen

I den  **[Azure-portalen](https://portal.azure.com/)**, i den vänstra menyn, Välj **Apptjänster** > **\<your_ app >**.

I det vänstra navigeringsfönstret i appen, Välj **SSL-inställningar** > **privata certifikat (.pfx)** > **importera App Service Certificate**.

![Infoga bild av Importera certifikat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Välj det certifikat du nyss köpt.

Nu när certifikatet har importerats måste att binda till en mappade domännamnet i din app. Välj **bindningar** > **Lägg till SSL-bindning**. 

![Infoga bild av Importera certifikat](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Använd följande tabell när du konfigurerar bindningen i den **SSL-bindningar** dialogrutan klickar **Lägg till bindning för**.

| Inställning | Beskrivning |
|-|-|
| Värdnamn | Att lägga till SSL-bindning för domännamn. |
| Tumavtryck för privat certifikat | Certifikat för att binda. |
| SSL-typ | <ul><li>**SNI SSL** -flera SNI-baserad SSL-bindningar kan läggas till. Med det här alternativet kan flera SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information om webbläsare som stöds finns i [Servernamnindikator](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP-baserad SSL** – Det går bara att lägga till en IP-baserad SSL-bindning. Med det här alternativet tillåts endast ett SSL-certifikat för att skydda en dedikerad offentlig IP-adress. När du konfigurerar bindningen, följer du stegen i [mappa om en post för IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Kontrollera åtkomst till HTTPS

Gå till din app med `HTTPS://<domain_name>` i stället för `HTTP://<domain_name>` att verifiera att certifikatet har konfigurerats korrekt.

## <a name="rekey-and-sync-certificate"></a>Nyckeluppdatering och synkronisering av certifikat

Om du behöver uppdatera ditt certifikat, väljer certifikatet i den [App Service-certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sidan och välj sedan **nyckeluppdatering och synkronisera** i det vänstra navigeringsfönstret.

Klicka på **uppdatera** för att starta processen. Den här processen kan ta 1 – 10 minuter att slutföra.

![Infoga bild av uppdatera SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Certifikatet samlar certifikatet med ett nytt certifikat som utfärdats av certifikatutfärdaren.

## <a name="renew-certificate"></a>Förnya certifikat

Om du vill aktivera automatisk förnyelse av certifikatet när som helst, väljer certifikatet i den [App Service-certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sidan och klicka sedan på **inställningarna för automatisk förnyelse** i det vänstra navigeringsfönstret. 

Välj **på** och klicka på **spara**. Certifikat kan starta förnyas automatiskt 60 dagar innan den upphör om du har automatisk förnyelse aktiverad.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Om du vill manuellt förnya certifikatet i stället, klickar du på **förnya manuellt**. Du kan begära för att förnya certifikatet manuellt 60 dagar före förfallodatum.

> [!NOTE]
> Det förnyade certifikatet är inte automatiskt bunden till din app, oavsett om du förnyade manuellt eller den förnyas automatiskt. Om du vill binda det till din app, se [förnya certifikat](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

## <a name="automate-with-scripts"></a>Automatisera med skript

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Fler resurser

* [Använda HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Kräv TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Använda ett SSL-certifikat i programkoden i Azure App Service](app-service-web-ssl-cert-load.md)
* [VANLIGA FRÅGOR OCH SVAR: App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
