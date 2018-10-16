---
title: Köp och konfigurera ett SSL-certifikat för Azure App Service | Microsoft Docs
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
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 38f7f82d293409a49c41381cedaa1f7600068cd3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319412"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Köp och konfigurera ett SSL-certifikat för din Azure Apptjänst

Den här självstudien visar hur du skyddar webbappen genom att köpa ett SSL-certifikat för din  **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** på ett säkert sätt lagra det i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), och koppla det till en anpassad domän.

## <a name="step-1---sign-in-to-azure"></a>Steg 1 – Logga i till Azure

Logga in på Azure Portal på http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Steg 2 – lägga en beställning med SSL-certifikat

Du kan placera en order för SSL-certifikat genom att skapa en ny [App Service Certificate](https://portal.azure.com/#create/Microsoft.SSL) i den **Azure-portalen**.

![Skapandet av certifikat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Ange ett eget **namn** för SSL-certifikat och ange den **domännamn**

> [!NOTE]
> Det här steget är en av de viktigaste delarna i inköpsprocessen. Se till att ange rätt värdnamn (anpassad domän) som du vill skydda med det här certifikatet. **INTE** åtkomstgruppen värdnamn med WWW. 
>

Välj din **prenumeration**, **resursgrupp**, och **SKU-certifikat**

> [!TIP]
> App Service-certifikat kan användas för alla Azure- eller icke - Azure-tjänster och är inte begränsat till App Services. Om du vill göra det måste du skapa en lokal PFX-kopia av en App Service-certifikat som du kan använda det var du vill. Mer information finns i [skapar en lokal PFX-kopia av ett App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Steg 3 – Store certifikatet i Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) är en Azure-tjänst som hjälper till att skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster.
>

När SSL-certifikat köpet har slutförts, måste du öppna den [App Service-certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sidan.

![Infoga bild av redo att lagra i KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Certifikatets status är **”väntar på att utfärdas”** eftersom det inte finns några fler steg som du måste utföra innan du kan börja använda det här certifikatet.

Klicka på **Certifikatkonfigureringen** i Egenskaper för certifikat och klicka på **steg 1: Store** att lagra certifikatet i Azure Key Vault.

Från den **Key Vault-Status** klickar du på **Key Vault-lagret** att välja en befintlig Key Vault för att lagra certifikatet **eller skapa nytt Key Vault** att skapa nytt Key Vault inom samma prenumeration och resursgrupp.

> [!NOTE]
> Azure Key Vault har minimal avgifter för lagring av det här certifikatet.
> Mer information finns i  **[prisinformation för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

När du har valt Key Vault-lagringsplats för det här certifikatet i, den **Store** alternativet ska visa lyckades.

![Infoga bild store framgång i KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Steg 4 – Kontrollera vem som äger

Från samma **Certifikatkonfigureringen** du använde i steg3, klickar du på **steg 2: Kontrollera**.

Välj önskad domän verifieringsmetod. 

Det finns tre typer av domänverifiering som stöds av App Service-certifikat: App Service, domän och manuell verifiering. Typerna verifiering beskrivs mer detaljerat i de [avancerade avsnitt](#advanced).

> [!NOTE]
> **App Service-verifiering** är det enklaste alternativet när du vill verifiera domänen är redan mappad till en App Service-app i samma prenumeration. Den drar nytta av det faktum att App Service-appen har redan verifierats äger domänen.
>

Klicka på **Kontrollera** knappen för att slutföra det här steget.

![Infoga bild av domänverifiering](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

När du klickar på **Kontrollera**, använda den **uppdatera** knappen tills den **Kontrollera** alternativet ska visa lyckades.

![Infoga bild av kontrollerar du i KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Steg 5: tilldela certifikatet till App Service-App

> [!NOTE]
> Innan du utför stegen i det här avsnittet, måste du har associerat ett anpassat domännamn med din app. Mer information finns i  **[konfigurera ett anpassat domännamn för en webbapp.](app-service-web-tutorial-custom-domain.md)**
>

I den  **[Azure-portalen](https://portal.azure.com/)**, klickar du på den **Apptjänst** alternativet till vänster på sidan.

Klicka på namnet på den app du vill koppla certifikatet till.

I den **inställningar**, klickar du på **SSL-inställningar**.

Klicka på **importera App Service Certificate** och välj det certifikat som du nyss köpt.

![Infoga bild av Importera certifikat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

I den **ssl-bindningar** avsnittet Klicka på **lägga till bindningar**, och Använd listrutorna för att välja domännamnet som ska skyddas med SSL och certifikaten som ska användas. Du kan också välja om du vill använda **[(Servernamnindikator)](http://en.wikipedia.org/wiki/Server_Name_Indication)** eller IP-baserad SSL.

![Infoga bild av SSL-bindningar](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Klicka på **Lägg till bindning för** att spara ändringarna och aktivera SSL.

> [!NOTE]
> Om du har valt **IP-baserad SSL** och din anpassade domän är konfigurerad med en A-post, måste du utföra följande åtgärder. Dessa beskrivs i mer information finns i den [avancerade avsnitt](#Advanced).

Du ska nu kunna gå till din app med `HTTPS://` i stället för `HTTP://` att verifiera att certifikatet har konfigurerats korrekt.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Steg 6 – hanteringsuppgifter

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Advanced

### <a name="verifying-domain-ownership"></a>Verifiera Domänägarskap

Det finns två typer av domänverifiering som stöds av App service-certifikat: domänverifiering och manuell verifiering.

#### <a name="domain-verification"></a>Domänverifiering

Välj det här alternativet endast för [en App Service-domän som du har köpt från Azure.](custom-dns-web-site-buydomains-web-app.md). Azure lägger till verifiering TXT-posten för dig och Slutför automatiskt.

#### <a name="manual-verification"></a>Manuell verifiering

> [!IMPORTANT]
> HTML-webbsida verifiering (fungerar bara i Standard certifikat-SKU)
>

1. Skapa en HTML-fil med namnet **”starfield.html”**

1. Innehållet i den här filen bör vara det exakta namnet på Domänverifieringstoken. (Du kan kopiera token från sidan Domänverifieringsstatus)

1. Ladda upp den här filen i roten på webbservern som värd för din domän `/.well-known/pki-validation/starfield.html`

1. Klicka på **uppdatera** att uppdatera statusen för certifikatet när verifieringen är klar. Det kan ta några minuter innan verifieringen.

> [!TIP]
> Verifiera i en terminal med `curl -G http://<domain>/.well-known/pki-validation/starfield.html` ska svaret innehålla den `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Verifiering av DNS TXT-post

1. Med hjälp av DNS-hanteraren, skapa en TXT-post på den `@` underdomän med värdet som är lika med Domänverifieringstoken.
1. Klicka på **”uppdatera”** att uppdatera statusen för certifikatet när verifieringen är klar.

> [!TIP]
> Du måste skapa en TXT-post på `@.<domain>` med värdet `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Tilldela certifikat till App Service-App

Om du har valt **IP-baserad SSL** och din anpassade domän är konfigurerad med en A-post, måste du utföra följande åtgärder:

När du har konfigurerat en IP-baserad SSL-bindning, en dedikerad IP-adress har tilldelats din app. Du hittar den här IP-adressen på den **Custom domain** sidan under inställningar för din app, precis ovanför den **värdnamn** avsnittet. Är listat som **extern IP-adress**

![Infoga bild av IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Den här IP-adress skiljer sig från den virtuella IP-adressen som användes för att konfigurera A-posten för din domän. Om du är konfigurerade för att använda SNI-baserad SSL eller inte är konfigurerade för att använda SSL, ingen adress anges för den här posten.

Med hjälp av verktyg som tillhandahålls av din domännamnsregistrator, ändra A-posten för ditt anpassade domännamn så att den pekar till IP-adressen från föregående steg.

## <a name="rekey-and-sync-the-certificate"></a>Uppdatera nyckel och synkronisera certifikatet

Om du behöver uppdatera ditt certifikat, väljer du den **nyckeluppdatering och synkronisera** alternativet från den **certifikategenskaper** sidan.

Klicka på **uppdatera** för att starta processen. Den här processen kan ta 1 – 10 minuter att slutföra.

![Infoga bild av uppdatera SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Certifikatet samlar certifikatet med ett nytt certifikat som utfärdats av certifikatutfärdaren.

## <a name="renew-the-certificate"></a>Förnya certifikatet

Aktivera automatisk förnyelse av certifikatet när som helst genom att klicka på **inställningarna för automatisk förnyelse** i sidan för hantering av certifikat. Välj **på** och klicka på **spara**. Certifikat kan starta förnya 90 dagar innan den upphör automatiskt om du har automatisk förnyelse aktiverad.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Om du vill manuellt förnya certifikatet i stället, klickar du på **förnya manuellt** i stället. Du kan begära för att förnya certifikatet manuellt 60 dagar före förfallodatum.

> [!NOTE]
> Det förnyade certifikatet är inte automatiskt bunden till din app, oavsett om du förnyade manuellt eller den förnyas automatiskt. Om du vill binda det till din app, se [förnya certifikat](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

## <a name="more-resources"></a>Fler resurser

* [Använda HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Kräv TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Använda ett SSL-certifikat i programkoden i Azure App Service](app-service-web-ssl-cert-load.md)
* [Vanliga frågor och svar: App Service-certifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
