---
title: "Köp och konfigurera ett SSL-certifikat för din Azure Apptjänst | Microsoft Docs"
description: "Lär dig hur du köpa ett certifikat för App Service och binda den till din Apptjänst-app"
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
ms.openlocfilehash: 256cb9a33d49bc3c24b2d94c417632edb0c8df31
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Köp och konfigurera ett SSL-certifikat för din Azure Apptjänst

Den här kursen visar hur du skyddar ditt webbprogram genom att köpa ett SSL-certifikat för din  **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, på ett säkert sätt lagra det i [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis), och associera den med en anpassad domän.

## <a name="step-1---log-in-to-azure"></a>Steg 1 – Logga in på Azure

Logga in på Azure-portalen på http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Steg 2 – beställning SSL-certifikat

Du kan placera en order för SSL-certifikat genom att skapa en ny [App tjänstcertifikat](https://portal.azure.com/#create/Microsoft.SSL) i den **Azure-portalen**.

![Skapande av certifikat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Ange ett eget **namn** för SSL-certifikatet och ange den **domännamn**

> [!NOTE]
> Det här steget är en av de viktigaste delarna i köpet. Kontrollera att du anger rätt värdnamn (anpassad domän) som du vill skydda med det här certifikatet. **INTE** lägga till värdnamnet med WWW. 
>

Välj din **prenumeration**, **resursgruppen**, och **certifikat SKU**

> [!WARNING]
> Apptjänstcertifikat kan endast användas av andra App-tjänster inom samma prenumeration.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Steg 3 – lagra certifikatet i Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) är en Azure-tjänst som hjälper dig skydda krypteringsnycklar och hemligheter som används av molnprogram och tjänster.
>

När SSL-certifikat köpet har slutförts, måste du öppna den [Apptjänstcertifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sidan.

![Infoga bild av redo att lagra i KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Certifikatstatus är **”väntande utfärdande”** eftersom det inte finns några fler steg som du måste utföra innan du kan börja använda det här certifikatet.

Klicka på **Certifikatkonfigureringen** i Egenskaper för certifikat sidan och klicka på **steg 1: lagra** att lagra det här certifikatet i Azure Key Vault.

Från den **Key Vault Status** klickar du på **Key Vault databasen** att välja en befintlig Key Vault för lagring av det här certifikatet **eller skapa nya Key Vault** att skapa nytt Nyckelvalv inom samma prenumeration och resurs.

> [!NOTE]
> Azure Key Vault har minimal kostnader för lagring av det här certifikatet.
> Mer information finns i  **[prisinformation för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

När du har valt nyckeln valvet databasen att lagra certifikatet, den **lagra** alternativet ska visa lyckades.

![infoga bilden av store KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Steg 4 – verifiera domänen ägarskap

Från samma **Certifikatkonfigureringen** du använde i steg3 klickar du på **steg 2: Kontrollera**.

Välj önskad domän verifieringsmetod. 

Det finns fyra typer av domänverifiering som stöds av Apptjänstcertifikat: Apptjänst, domän, e-post och manuell kontroll. Dessa typer av verifiering beskrivs mer detaljerat i den [avancerade avsnitt](#advanced).

> [!NOTE]
> **App Service verifiering** är det enklaste alternativet när du vill verifiera domänen är redan mappad till en Apptjänst-app i samma prenumeration. Det tar nytta av det faktum att App Service-appen har redan verifierats ägarskap för domänen.
>

Klicka på **Kontrollera** för att slutföra det här steget.

![infoga bilden för verifiering av domän](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

När du klickar på **Kontrollera**, använda den **uppdatera** knappen förrän den **Kontrollera** alternativet ska visa lyckades.

![Infoga bild av verifiera i KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Steg 5 – distribuera certifikat till App Service-appen

> [!NOTE]
> Innan du utför stegen i det här avsnittet måste du har associerat ett anpassat domännamn med din app. Mer information finns i  **[konfigurera ett anpassat domännamn för en webbapp.](app-service-web-tutorial-custom-domain.md)**
>

I den  **[Azure-portalen](https://portal.azure.com/)**, klicka på den **Apptjänst** alternativet till vänster på sidan.

Klicka på namnet på den app du vill koppla certifikatet till.

I den **inställningar**, klickar du på **SSL-certifikat**.

Klicka på **importera App Service certifikat** och välj det certifikat som du precis har köpt.

![Infoga bild av Importera certifikat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

I den **ssl-bindningar** avsnittet Klicka på **lägga till bindningar**, och de nedrullningsbara listorna väljer domännamn för att skydda med SSL och certifikatet för att använda. Du kan också välja om du vill använda  **[Server Servernamnsindikation (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**  eller IP-baserade SSL.

![infoga bilden av SSL-bindningar](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Klicka på **Lägg till bindning för** att spara ändringarna och aktivera SSL.

> [!NOTE]
> Om du har valt **IP-baserade SSL** och domänen är konfigurerad med en A-post måste du utföra följande åtgärder. Dessa beskrivs mer detaljerat i den [avancerade avsnitt](#Advanced).

Du ska nu kunna besöka app med hjälp av `HTTPS://` i stället för `HTTP://` att verifiera att certifikatet har konfigurerats korrekt.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Steg 6 - administrativa uppgifter

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Advanced

### <a name="verifying-domain-ownership"></a>Verifiera domänen ägarskap

Det finns två flera typer av domänverifiering som stöds av Apptjänstcertifikat: e-post och manuell kontroll.

#### <a name="mail-verification"></a>E-postverifiering

E-postmeddelandet har redan skickats till den e-adress som är associerade med den här domänen.
Öppna e-postmeddelandet för att slutföra verifieringssteg e-post, och klicka på verifieringslänken.

![Infoga bild av e-Postverifiering](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Om du vill skicka e-postmeddelandet klickar du på den **skicka e-post** knappen.

#### <a name="domain-verification"></a>Domänverifiering

Välj det här alternativet endast för [en Apptjänst-domän som du har köpt från Azure.](custom-dns-web-site-buydomains-web-app.md). Azure automatiskt lägger till verifiering TXT-posten för du och slutför processen.

#### <a name="manual-verification"></a>Manuell verifiering

> [!IMPORTANT]
> HTML-webbsida verifiering (fungerar bara med certifikat SKU: N)
>

1. Skapa en HTML-fil med namnet **”starfield.html”**

1. Innehållet i den här filen bör vara det exakta namnet på domänen verifiering Token. (Du kan kopiera token från sidan Status för verifiering av domän)

1. Överför den här filen i roten på webbservern som värd för din domän`/.well-known/pki-validation/starfield.html`

1. Klicka på **uppdatera** att uppdatera certifikatstatus när verifieringen är klar. Det kan ta några minuter för verifiering att slutföra.

> [!TIP]
> Kontrollera i en terminal `curl -G http://<domain>/.well-known/pki-validation/starfield.html` svaret ska innehålla den `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Verifiering av DNS TXT-post

1. Med hjälp av DNS-hanteraren, skapa en TXT-post på den `@` underdomänen med värdet som motsvarar domänen verifiering Token.
1. Klicka på **”uppdatera”** att uppdatera certifikat-status när verifieringen är klar.

> [!TIP]
> Du måste skapa en TXT-post på `@.<domain>` med värdet `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Tilldela certifikatet till App Service-appen

Om du har valt **IP-baserade SSL** och domänen är konfigurerad med en A-post måste du utföra följande åtgärder:

När du har konfigurerat en IP-baserade SSL-bindning, en dedicerad IP-adress har tilldelats din app. Du hittar den här IP-adressen på den **anpassad domän** sidan under inställningar för din app, precis ovanför den **värdnamn** avsnitt. Den visas som **extern IP-adress**

![infoga bilden av IP-SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Denna IP-adress skiljer sig den virtuella IP-adressen som användes för att konfigurera en A-post för din domän. Om du har konfigurerats för att använda SNI baserad SSL, eller inte har konfigurerats för att använda SSL, ingen adress visas för den här posten.

Använda de verktyg som tillhandahålls av din domännamnsregistrator kan ändra A-post för ditt anpassade domännamn peka till IP-adressen från föregående steg.

## <a name="rekey-and-sync-the-certificate"></a>Genererar nya nycklar och synkronisera certifikatet

Om du någon gång behöver nyckelförnyelse ditt certifikat väljer du den **nyckelförnyelse och Sync** alternativet från den **certifikategenskaper** sidan.

Klicka på **genererar nya nycklar** för att starta processen. Den här processen kan ta 1-10 minuter att slutföra.

![infoga bilden av nyckelförnyelse SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Omgenerering ditt certifikat samlar certifikatet med ett nytt certifikat som utfärdats av certifikatutfärdaren.

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>Varför är min SSL-certifikat inte auto-förnyas?

Om SSL-certifikat har konfigurerats för automatisk förnyelse, men det inte förnyas automatiskt, kan du har en väntande domänverifiering. Observera följande: 

- GoDaddy, vilket genererar apptjänstcertifikat kräver verifiering av domän vart tre år. Domänadministratören får ett e-postmeddelande när alla tre år för att verifiera domänen. Det gick inte att kontrollera den e-posten eller verifiera din domän förhindrar att App Service-certifikat förnyas automatiskt. 
- Apptjänst certifikat före mars 31 2017 måste Omverifiering av domän vid tiden för nästa förnyelse (även om automatisk förnyelse har aktiverats för certifikatet). Detta är ett resultat av ändring i principen för GoDaddy. Kontrollera din e-post och genomföra denna domänverifiering för en om du vill fortsätta automatisk förnyelse av certifikat för App Service. 

## <a name="more-resources"></a>Fler resurser

* [Om du Använd SSL-certifikat i din programkod i Azure App Service](app-service-web-ssl-cert-load.md)
* [Vanliga frågor och svar: Apptjänstcertifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)