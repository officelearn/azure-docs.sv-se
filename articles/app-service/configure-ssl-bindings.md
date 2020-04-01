---
title: Skydda en anpassad DNS med en SSL-bindning
description: Säker HTTPS-åtkomst till din anpassade domän genom att skapa en TLS/SSL-bindning med ett certifikat. Förbättra webbplatsens säkerhet genom att tillämpa HTTPS eller TLS 1.2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239709"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service

I den här artikeln visas hur du skyddar den [anpassade domänen](app-service-web-tutorial-custom-domain.md) i [app-](https://docs.microsoft.com/azure/app-service/) eller [funktionsappen](https://docs.microsoft.com/azure/azure-functions/) för App Service genom att skapa en certifikatbindning. När du är klar kan du komma åt `https://` apptjänstappen vid slutpunkten `https://www.contoso.com`för ditt anpassade DNS-namn (till exempel ). 

![Webbapp med anpassat SSL-certifikat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Att skydda en [anpassad domän](app-service-web-tutorial-custom-domain.md) med ett certifikat innebär två steg:

- [Lägg till ett privat certifikat i App Service](configure-ssl-certificate.md) som uppfyller alla krav för [SSL-bindningar](configure-ssl-certificate.md#private-certificate-requirements).
-  Skapa en SSL-bindning till motsvarande anpassade domän. Det andra steget omfattas av den här artikeln.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Uppgradera prisnivån för din app
> * Skydda en anpassad domän med ett certifikat
> * Använda HTTPS
> * Använda TLS 1.1/1.2
> * Automatisera hantering av TLS med skript

## <a name="prerequisites"></a>Krav

Så här följer du den här guiden:

- [Skapa en App Service-app](/azure/app-service/)
- [Mappa ett domännamn till din app](app-service-web-tutorial-custom-domain.md) eller köpa och konfigurera det i [Azure](manage-custom-dns-buy-domain.md)
- [Lägga till ett privat certifikat i appen](configure-ssl-certificate.md)

> [!NOTE]
> Det enklaste sättet att lägga till ett privat certifikat är att [skapa ett kostnadsfritt hanterat apptjänstcertifikat](configure-ssl-certificate.md#create-a-free-certificate-preview) (förhandsversion).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Skydda en anpassad domän

Gör följande:

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Starta dialogrutan **TLS/SSL Binding** från den vänstra navigeringen i appen genom att:

- Välja **anpassade domäner** > **Lägg till bindning**
- Välja **TLS/SSL-inställningar** > **Lägg till TLS/SSL-bindning**

![Lägga till bindning i domänen](./media/configure-ssl-bindings/secure-domain-launch.png)

I **Anpassad domän**väljer du den anpassade domän som du vill lägga till en bindning för.

Om appen redan har ett certifikat för den valda anpassade domänen går du direkt till [Skapa bindning.](#create-binding) Annars, fortsätt.

### <a name="add-a-certificate-for-custom-domain"></a>Lägga till ett certifikat för anpassad domän

Om appen inte har något certifikat för den valda anpassade domänen har du två alternativ:

- **Ladda upp PFX-certifikat** – Följ arbetsflödet vid [Ladda upp ett privat certifikat](configure-ssl-certificate.md#upload-a-private-certificate)och välj sedan det här alternativet här.
- **Importera App Service-certifikat** – Följ arbetsflödet vid [Importera ett App Service-certifikat](configure-ssl-certificate.md#import-an-app-service-certificate)och välj sedan det här alternativet här.

> [!NOTE]
> Du kan också [skapa ett kostnadsfritt certifikat](configure-ssl-certificate.md#create-a-free-certificate-preview) (förhandsversion) eller importera ett Key [Vault-certifikat,](configure-ssl-certificate.md#import-a-certificate-from-key-vault)men du måste göra det separat och sedan gå tillbaka till dialogrutan **TLS/SSL-bindning.**

### <a name="create-binding"></a>Skapa bindning

Använd följande tabell för att konfigurera SSL-bindningen i dialogrutan **TLS/SSL-bindning** och klicka sedan på **Lägg till bindning**.

| Inställning | Beskrivning |
|-|-|
| Anpassad domän | Domännamnet som ssl-bindningen ska läggas till för. |
| Tumavtryck för privat certifikat | Certifikatet att binda. |
| TLS/SSL-typ | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - Flera SNI SSL-bindningar kan läggas till. Med det här alternativet kan flera SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information finns i [Servernamnsindikering](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** - Endast en IP SSL-bindning kan läggas till. Med det här alternativet tillåts endast ett SSL-certifikat för att skydda en dedikerad offentlig IP-adress. När du har konfigurerat bindningen följer du stegen i [Mappa om en post för IP SSL](#remap-a-record-for-ip-ssl).<br/>IP SSL stöds endast i produktions- eller isolerade nivåer. </li></ul> |

När åtgärden är klar ändras den anpassade domänens SSL-tillstånd till **Säker**.

![SSL-bindningen lyckades](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Ett **säkert** tillstånd i **anpassade domäner** innebär att det är skyddat med ett certifikat, men App-tjänsten kontrollerar inte om certifikatet är självsignerat eller har upphört att gälla, till exempel, vilket också kan leda till att webbläsare visar ett fel eller en varning.

## <a name="remap-a-record-for-ip-ssl"></a>Mappa om en A-post för IP SSL

Om du inte använder IP SSL i appen går du till [Testa HTTPS för din anpassade domän](#test-https).

Som standard använder din app en delad offentlig IP-adress. När du binder ett certifikat med IP SSL skapar App Service en ny, dedikerad IP-adress för din app.

Om du har mappat en A-post till din app uppdaterar du domänregistret med den här nya, dedikerade IP-adressen.

Din apps **Anpassad domän**-sida uppdateras med den nya dedikerade IP-adressen. [Kopiera den här IP-adressen](app-service-web-tutorial-custom-domain.md#info) och [mappa om A-posten](app-service-web-tutorial-custom-domain.md#map-an-a-record) till den nya IP-adressen.

## <a name="test-https"></a>Testa HTTPS

I olika webbläsare bläddrar du till `https://<your.custom.domain>` för att kontrollera att den visar din app.

![Portalnavigering till Azure-app](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Din programkod kan granska protokollet via "x-appservice-proto"-huvudet. Huvudet har ett värde `http` `https`på eller . 

> [!NOTE]
> Om din app visar fel i certifikatverifieringen så använder du förmodligen ett självsignerat certifikat.
>
> Om detta inte är fallet kan du ha utelämnat mellanliggande certifikat när du exporterade certifikatet till PFX-filen.

## <a name="prevent-ip-changes"></a>Förhindra IP-ändringar

Din inkommande IP-adress kan ändras när du tar bort en bindning, även om bindningen är IP SSL. Detta är särskilt viktigt när du förnyar ett certifikat som redan finns i en IP SSL-bindning. Du kan förhindra att appens IP-adress ändras genom att följa stegen nedan i ordning:

1. Ladda upp det nya certifikatet.
2. Bind det nya certifikatet till önskad anpassad domän utan att ta bort det gamla. Med den här åtgärden ersätts bindningen i stället för att den gamla tas bort.
3. Ta bort det gamla certifikatet. 

## <a name="enforce-https"></a>Använda HTTPS

Som standard kan alla fortfarande komma åt din app med HTTP. Du kan omdirigera alla HTTP-begäranden till HTTPS-porten.

Välj **SSL-inställningar** i den vänstra navigeringen på din appsida. I **Endast HTTPS** väljer du **På**.

![Använda HTTPS](./media/configure-ssl-bindings/enforce-https.png)

När åtgärden har slutförts går du till någon av de HTTP-webbadresser som pekar på din app. Ett exempel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Framtvinga TLS-version

Din app tillåter [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 som standard, vilket är den rekommenderade TLS-nivån enligt branschstandarder, t.ex. [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Följ dessa steg om du vill genomdriva en högre TLS-version:

Välj **SSL-inställningar** i den vänstra navigeringen på din appsida. I **TLS-version** väljer du sedan den lägsta TLS-version du vill använda. Den här inställningen styr endast inkommande samtal. 

![Kräv TLS 1.1 eller 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

När åtgärden är klar avvisar appen alla anslutningar med lägre TLS-version.

## <a name="handle-ssl-termination"></a>Hantera SSL-avslutning

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

Språkspecifika konfigurationsguider, till exempel konfigurationsguiden för [Linux Node.js,](containers/configure-language-nodejs.md#detect-https-session) visar hur du identifierar en HTTPS-session i programkoden.

## <a name="automate-with-scripts"></a>Automatisera med skript

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Fler resurser

* [Använda ett SSL-certifikat i programkoden](configure-ssl-certificate-in-code.md)
* [Vanliga frågor och svar : App Service-certifikat](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
