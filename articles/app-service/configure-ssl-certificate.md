---
title: Lägga till och hantera TLS/SSL-certifikat
description: Skapa ett kostnadsfritt certifikat, importera ett App Service-certifikat, importera ett Key Vault-certifikat eller köpa ett App Service-certifikat i Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 4edf710e575bbb26fb0e247e59ff5c796f16226e
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810584"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Lägga till ett TLS/SSL-certifikat i Azure App Service

[Azure App Service](overview.md) tillhandahåller en mycket skalbar, självkorrigering webbhotell. I den här artikeln visas hur du skapar, laddar upp eller importerar ett privat certifikat eller ett offentligt certifikat till App Service. 

När certifikatet har lagts till i app- eller [funktionsappen](https://docs.microsoft.com/azure/azure-functions/)för App Service kan du [skydda ett anpassat DNS-namn med det](configure-ssl-bindings.md) eller använda det i [programkoden](configure-ssl-certificate-in-code.md).

I följande tabell visas de alternativ du har för att lägga till certifikat i App Service:

|Alternativ|Beskrivning|
|-|-|
| Skapa ett kostnadsfritt hanterat certifikat för apptjänst (förhandsversion) | Ett privat certifikat som är enkelt att använda `www` om du bara behöver skydda din [anpassade domän](app-service-web-tutorial-custom-domain.md) eller en icke-naken domän i App Service. |
| Köpa ett apptjänstcertifikat | Ett privat certifikat som hanteras av Azure. Den kombinerar enkelheten i automatiserad certifikathantering och flexibiliteten i förnyelse- och exportalternativ. |
| Importera ett certifikat från Key Vault | Användbart om du använder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) för att hantera dina [PKCS12-certifikat](https://wikipedia.org/wiki/PKCS_12). Se [Privata certifikatkrav](#private-certificate-requirements). |
| Ladda upp ett privat certifikat | Om du redan har ett privat certifikat från en tredjepartsleverantör kan du ladda upp det. Se [Privata certifikatkrav](#private-certificate-requirements). |
| Ladda upp ett offentligt certifikat | Offentliga certifikat används inte för att skydda anpassade domäner, men du kan läsa in dem i koden om du behöver dem för att komma åt fjärrresurser. |

## <a name="prerequisites"></a>Krav

Så här följer du den här guiden:

- [Skapa en apptjänstapp](/azure/app-service/).
- Endast gratis certifikat: mappa en underdomän (till `www.contoso.com`exempel) till App Service med en [CNAME-post](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Krav på privata certifikat

> [!NOTE]
> Azure Web Apps stöder **inte** AES256 och alla pfx-filer ska krypteras med TripleDES.

Det [kostnadsfria apptjänsthanterade certifikatet](#create-a-free-certificate-preview) eller [App Service-certifikatet](#import-an-app-service-certificate) uppfyller redan kraven i App Service. Om du väljer att ladda upp eller importera ett privat certifikat till App Service måste certifikatet uppfylla följande krav:

* Exporteras som en [lösenordsskyddad PFX-fil](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Innehålla en privat nyckel som är minst 2 048 bitar lång
* Innehålla alla mellanliggande certifikat i certifikatkedjan

För att skydda en anpassad domän i en TLS-bindning har certifikatet ytterligare krav:

* Innehåller en [utökad nyckelanvändning](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) för serverautentisering (OID = 1.3.6.1.5.5.7.3.1)
* Vara signerat av en betrodd certifikatutfärdare

> [!NOTE]
> **ECC-certifikat (Elliptic Curve Cryptography)** kan fungera med App Service, men behandlas inte i den här artikeln. Din certifikatutfärdare kan visa hur du skapar ECC-certifikat.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Skapa ett kostnadsfritt certifikat (förhandsgranskning)

Det kostnadsfria hanterade certifikatet för App Service är en nyckelfärdig lösning för att skydda ditt anpassade DNS-namn i App Service. Det är ett fullt fungerande TLS/SSL-certifikat som hanteras av App Service och förnyas automatiskt. Det kostnadsfria certifikatet har följande begränsningar:

- Stöder inte jokerteckencertifikat.
- Stöder inte nakna domäner.
- Kan inte exporteras.
- Stöder inte DNS A-poster.

> [!NOTE]
> Det kostnadsfria certifikatet utfärdas av DigiCert. För vissa toppdomäner måste du uttryckligen tillåta DigiCert som certifikatutfärdare genom att `0 issue digicert.com`skapa en [CAA-domänpost](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) med värdet: .
> 

Så här skapar du ett kostnadsfritt hanterat apptjänstcertifikat:

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Välj **TLS/SSL-inställningar** > **för privata nyckelcertifikat (.pfx)** > Skapa**apptjänsthanterade certifikat**från den vänstra navigeringen i appen .

![Skapa kostnadsfritt certifikat i App Service](./media/configure-ssl-certificate/create-free-cert.png)

Alla icke-nakna domäner som är korrekt mappade till din app med en CNAME-post visas i dialogrutan. Välj den anpassade domänen för att skapa ett kostnadsfritt certifikat för och välj **Skapa**. Du kan bara skapa ett certifikat för varje anpassad domän som stöds.

När åtgärden är klar visas certifikatet i listan **Privata nyckelcertifikat.**

![Skapa ledigt certifikat klart](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importera ett apptjänstcertifikat

Om du köper ett App Service-certifikat från Azure hanterar Azure följande uppgifter:

- Tar hand om köpprocessen från GoDaddy.
- Utför domänverifiering av certifikatet.
- Underhåller certifikatet i [Azure Key Vault](../key-vault/key-vault-overview.md).
- Hanterar förnyelse av certifikat (se [Förnya certifikat](#renew-certificate)).
- Synkronisera certifikatet automatiskt med de importerade kopiorna i App Service-appar.

Om du vill köpa ett App Service-certifikat går du till [Starta certifikatorder](#start-certificate-order).

Om du redan har ett fungerande App Service-certifikat kan du:

- [Importera certifikatet till App Service](#import-certificate-into-app-service).
- [Hantera certifikatet](#manage-app-service-certificates), till exempel förnya, nyckela över och exportera det.

### <a name="start-certificate-order"></a>Starta certifikatordning

Starta en apptjänstcertifikatorder på <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">sidan Skapa apptjänstcertifikat</a>.

![Starta köp av apptjänstcertifikat](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Använd följande tabell för att konfigurera certifikatet. Klicka på **Skapa** när du är klar.

| Inställning | Beskrivning |
|-|-|
| Namn | Ett eget namn på ditt App Service-certifikat. |
| Naken domännamn värd | Ange rotdomänen här. Det utfärdade certifikatet skyddar *både* rotdomänen och underdomänen. `www` I det utfärdade certifikatet innehåller fältet Gemensamt namn rotdomänen och fältet `www` Ämnesalternativnamn innehåller domänen. Om du bara vill skydda en underdomän anger du det fullständigt kvalificerade `mysubdomain.contoso.com`domännamnet för underdomänen här (till exempel ).|
| Prenumeration | Prenumerationen som ska innehålla certifikatet. |
| Resursgrupp | Resursgruppen som ska innehålla certifikatet. Du kan använda en ny resursgrupp eller välja samma resursgrupp som apptjänstappen, till exempel. |
| Certifikat SKU | Bestämmer vilken typ av certifikat som ska skapas, oavsett om det är ett standardcertifikat eller ett [jokerteckencertifikat](https://wikipedia.org/wiki/Wildcard_certificate). |
| Juridiska villkor | Klicka för att bekräfta att du godkänner de juridiska villkoren. Certifikaten hämtas från GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Lagra i Azure Key Vault

När certifikatinköpsprocessen är klar finns det några fler steg som du måste slutföra innan du kan börja använda det här certifikatet. 

Markera certifikatet på sidan [App servicecertifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och klicka sedan på Steg 1 **för certifikatkonfiguration:** > **Store**.

![Konfigurera Key Vault-lagring av App Service-certifikat](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) är en Azure-tjänst som hjälper till att skydda kryptografiska nycklar och hemligheter som används av molnprogram och tjänster. Det är lagring val för App Service-certifikat.

På sidan Status för **Nyckelvalv** klickar du på **Databasen För nyckelvalv** för att skapa ett nytt valv eller välja ett befintligt valv. Om du väljer att skapa ett nytt valv använder du följande tabell för att konfigurera valvet och klickar på Skapa. Skapa det nya Key Vault i samma prenumerations- och resursgrupp som apptjänstappen.

| Inställning | Beskrivning |
|-|-|
| Namn | Ett unikt namn som består av alfanumeriska tecken och streck. |
| Resursgrupp | Som en rekommendation väljer du samma resursgrupp som ditt App Service-certifikat. |
| Location | Välj samma plats som apptjänstappen. |
| Prisnivå | Information finns i [prisinformation för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Åtkomstprinciper| Definierar programmen och den tillåtna åtkomsten till arkivresurserna. Du kan konfigurera den senare, i följande steg på [Bevilja flera program åtkomst till ett nyckelvalv](../key-vault/key-vault-group-permissions-for-apps.md). |
| Virtuell nätverksåtkomst | Begränsa åtkomsten till valvet till vissa virtuella Azure-nätverk. Du kan konfigurera den senare, i anslutning till stegen vid [Konfigurera Azure Key Vault-brandväggar och virtuella nätverk](../key-vault/key-vault-network-security.md) |

När du har valt valvet stänger du sidan **Databas för Nyckelvalv.** **Alternativet Steg 1: Store** bör visa en grön bock för att lyckas. Håll sidan öppen för nästa steg.

### <a name="verify-domain-ownership"></a>Verifiera domänägarskap

Klicka på **Steg 2: Verifiera**från samma **sida för certifikatkonfiguration** som du använde i det sista steget.

![Verifiera domän för App Service-certifikat](./media/configure-ssl-certificate/verify-domain.png)

Välj **Verifiering av apptjänst**. Eftersom du redan har mappat domänen till din webbapp (se [Förutsättningar)](#prerequisites)är den redan verifierad. Klicka bara på **Verifiera** för att avsluta det här steget. Klicka på knappen **Uppdatera** tills **meddelandecertifikatet är Domänverifierat** visas.

> [!NOTE]
> Fyra typer av domänverifieringsmetoder stöds: 
> 
> - **Apptjänst** - Det mest praktiska alternativet när domänen redan är mappad till en App Service-app i samma prenumeration. Den drar nytta av det faktum att App Service-appen redan har verifierat domänägarskapet.
> - **Domän** - Verifiera en [App Service-domän som du har köpt från Azure](manage-custom-dns-buy-domain.md). Azure lägger automatiskt till verifieringsposten för TXT åt dig och slutför processen.
> - **E-post** - Verifiera domänen genom att skicka ett e-postmeddelande till domänadministratören. Instruktioner ges när du väljer alternativet.
> - **Manual** - Verifiera domänen med antingen en HTML-sida **(** standardcertifikat) eller en DNS TXT-post. Instruktioner ges när du väljer alternativet.

### <a name="import-certificate-into-app-service"></a>Importera certifikat till App Service

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Välj **TLS/SSL-inställningar** > **för privata nyckelcertifikat (.pfx)** > Import App Service**Certificate**från den vänstra navigeringen i appen .

![Importera App Service-certifikat i App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Välj det certifikat som du just har köpt och välj **OK**.

När åtgärden är klar visas certifikatet i listan **Privata nyckelcertifikat.**

![Import App Service-certifikatet har slutförts](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importera ett certifikat från Key Vault

Om du använder Azure Key Vault för att hantera dina certifikat kan du importera ett PKCS12-certifikat från Key Vault till App Service så länge det [uppfyller kraven](#private-certificate-requirements).

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Välj **TLS/SSL-inställningar** > **för privata nyckelcertifikat (.pfx)** > Import Key**Vault-certifikat**från den vänstra navigeringen i appen .

![Importera Key Vault-certifikat i App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Använd följande tabell för att välja certifikatet.

| Inställning | Beskrivning |
|-|-|
| Prenumeration | Prenumerationen som Key Vault tillhör. |
| Key Vault | Valvet med det certifikat som du vill importera. |
| Certifikat | Välj från listan över PKCS12-certifikat i valvet. Alla PKCS12-certifikat i valvet visas med tumavtryck, men alla stöds inte i App Service. |

När åtgärden är klar visas certifikatet i listan **Privata nyckelcertifikat.** Om importen misslyckas med ett fel uppfyller certifikatet inte [kraven för App Service](#private-certificate-requirements).

![Importera Key Vault-certifikatet klart](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

När du har skaffat ett certifikat från certifikatleverantören följer du stegen i det här avsnittet för att göra det klart för App Service.

### <a name="merge-intermediate-certificates"></a>Sammanfoga mellanliggande certifikat

Om du får flera certifikat av din certifikatutfärdare i certifikatkedjan, måste du sammanfoga certifikaten i ordning.

Gör detta genom att öppna varje certifikat som du fick i ett redigeringsprogram.

Skapa en fil för det sammanfogade certifikatet med namnet _mergedcertificate.crt_. I redigeringsprogrammet kopierar du innehållet i varje certifikat till den här filen. Ordningen på dina certifikat ska följa ordningen i certifikatkedjan, först med ditt certifikat och sist med rotcertifikatet. Det ser ut som i följande exempel:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportera certifikat till PFX

Exportera det kopplade TLS/SSL-certifikatet med den privata nyckel som certifikatbegäran genererades med.

Om du genererade din certifikatbegäran med hjälp av OpenSSL, har du skapat en privat nyckelfil. Kör följande kommando för att exportera certifikatet till PFX. Ersätt platshållarna _ &lt;privat-nyckel-fil>_ och _ &lt;sammanslagna certifikat-fil>_ med sökvägar till din privata nyckel och den kopplade certifikatfilen.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

När du uppmanas till det anger du ett exportlösenord. Du använder det här lösenordet när du laddar upp TLS/SSL-certifikatet till App Service senare.

Om du använder IIS eller _Certreq.exe_ till att generera din certifikatbegäran, installerar du certifikatet på den lokala datorn och [exporterar sedan certifikatet till PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Ladda upp certifikat till App Service

Nu är du redo att ladda upp certifikatet till App Service.

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Välj **TLS/SSL-inställningar** > **för privata nyckelcertifikat (.pfx)** > ladda upp**certifikat**(från den vänstra navigeringen i din app).

![Ladda upp privat certifikat i App Service](./media/configure-ssl-certificate/upload-private-cert.png)

I **PFX-certifikatsfil** väljer du din PFX-fil. I **Certifikatslösenord** skriver du lösenordet som du skapade när du exporterade PFX-filen. När du är klar klickar du på **Ladda upp**. 

När åtgärden är klar visas certifikatet i listan **Privata nyckelcertifikat.**

![Ladda upp certifikatet klart](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Offentliga certifikat stöds i *.cer-formatet.* 

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Från den vänstra navigeringen i din app klickar du på **TLS/SSL-inställningar** > **Offentliga certifikat (.cer)** > Ladda upp offentligt**nyckelcertifikat**.

Skriv ett namn på certifikatet i **Namn.** Välj din CER-fil **i CER-certifikatfilen.**

Klicka på **Överför**.

![Ladda upp offentligt certifikat i App Service](./media/configure-ssl-certificate/upload-public-cert.png)

När certifikatet har laddats upp kopierar du certifikatets tumavtryck och [läser Göra certifikatet tillgängligt](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Hantera App Service-certifikat

I det här avsnittet visas hur du hanterar ett App Service-certifikat som du köpte i [Importera ett App Service-certifikat](#import-an-app-service-certificate).

- [Rekey certifikat](#rekey-certificate)
- [Förnya certifikat](#renew-certificate)
- [Exportera certifikatet](#export-certificate)
- [Ta bort certifikat](#delete-certificate)

### <a name="rekey-certificate"></a>Rekey certifikat

Om du tror att certifikatets privata nyckel har komprometterats kan du återstå certifikatet. Välj certifikatet på sidan [App servicecertifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och välj sedan **Nycklar och synkronisera** från den vänstra navigeringen.

Klicka på **Nyckel igen** för att starta processen. Denna process kan ta 1-10 minuter att slutföra.

![Åter nyckel ett apptjänstcertifikat](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Om du vill återställa certifikatet rullas certifikatet med ett nytt certifikat utfärdat från certifikatutfärdaren.

När omnyckelåtgärden är klar klickar du på **Synkronisera**. Synkroniseringsåtgärden uppdaterar automatiskt värdnamnsbindningarna för certifikatet i App Service utan att orsaka några driftstopp för dina appar.

> [!NOTE]
> Om du inte klickar på **Synkronisera**synkroniseras ditt certifikat automatiskt inom 48 timmar.

### <a name="renew-certificate"></a>Förnya certifikat

Om du vill aktivera automatisk förnyelse av certifikatet när som helst markerar du certifikatet på sidan [Apptjänstcertifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och klickar sedan på **Inställningar för Automatisk förnyelse** i den vänstra navigeringen. Som standard har App Service-certifikat en giltighetstid på ett år.

Välj **På** och klicka på **Spara**. Certifikat kan börja förnyas automatiskt 60 dagar före förfallodatumet om du har aktiverat automatisk förnyelse.

![Förnya App Service-certifikat automatiskt](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Om du vill förnya certifikatet manuellt i stället klickar du på **Manuell förnyelse**. Du kan begära att ditt certifikat förnyas manuellt 60 dagar före utgångsdatumet.

När förnya åtgärden är klar klickar du på **Synkronisera**. Synkroniseringsåtgärden uppdaterar automatiskt värdnamnsbindningarna för certifikatet i App Service utan att orsaka några driftstopp för dina appar.

> [!NOTE]
> Om du inte klickar på **Synkronisera**synkroniseras ditt certifikat automatiskt inom 48 timmar.

### <a name="export-certificate"></a>Exportera certifikatet

Eftersom ett App Service-certifikat är en [Key Vault-hemlighet](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)kan du exportera en PFX-kopia av det och använda den för andra Azure-tjänster eller utanför Azure.

Om du vill exportera App Service-certifikatet som en PFX-fil kör du följande kommandon i [Cloud Shell](https://shell.azure.com). Du kan också köra den lokalt om du [har installerat Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Ersätt platshållarna med de namn som du använde när du [skapade App Service-certifikatet](#start-certificate-order).

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Den hämtade *filen appservicecertificate.pfx* är en rå PKCS12-fil som innehåller både offentliga och privata certifikat. I varje fråga använder du en tom sträng för importlösenordet och PEM-lösenfrasen.

### <a name="delete-certificate"></a>Ta bort certifikat 

Borttagning av ett App Service-certifikat är slutgiltigt och oåterkalleligt. Borttagning av en apptjänstcertifikatresurs resulterar i att certifikatet återkallas. Alla bindningar i App Service med det här certifikatet blir ogiltiga. För att förhindra oavsiktlig borttagning lägger Azure ett lås på certifikatet. Om du vill ta bort ett App Service-certifikat måste du först ta bort borttagningslåset på certifikatet.

Markera certifikatet på sidan [App servicecertifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och välj sedan **Lås** i den vänstra navigeringen.

Hitta låset på ditt certifikat med låstypen **Ta bort**. Till höger om det väljer du **Ta bort**.

![Ta bort lås för App Service-certifikat](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Nu kan du ta bort App Service-certifikatet. Välj **Översikt** > **Ta bort**i den vänstra navigeringen . Skriv certifikatnamnet i bekräftelsedialogrutan och välj **OK**.

## <a name="automate-with-scripts"></a>Automatisera med skript

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Använda TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Använda ett TLS/SSL-certifikat i koden i Azure App Service](configure-ssl-certificate-in-code.md)
* [Vanliga frågor och svar : App Service-certifikat](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
