---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: b9e601c72395b4910850714460321a83a3113e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649557"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Så svarar APIM-proxyservern med SSL-certifikat i TLS-handskakning

### <a name="clients-calling-with-sni-header"></a>Klienter som ringer med SNI-huvud
Om kunden har konfigurerat en eller flera anpassade domäner för Proxy kan APIM svara på HTTPS-begäranden från de anpassade domänerna (till exempel contoso.com) samt standarddomän (till exempel apim-service-name.azure-api.net). Baserat på informationen i SNI-huvudet (Server Name Indication) svarar APIM med lämpligt servercertifikat.

### <a name="clients-calling-without-sni-header"></a>Klienter som ringer utan SNI-huvud
Om kunden använder en klient, som inte skickar [SNI-huvudet,](https://tools.ietf.org/html/rfc6066#section-3) skapar APIM svar baserat på följande logik:

* Om tjänsten bara har en anpassad domän konfigurerad för Proxy är standardcertifikatet det certifikat som utfärdades till den anpassade domänen Proxy.
* Om tjänsten har konfigurerat flera anpassade domäner för Proxy (stöds på **utvecklar-** och **premiumnivån)** kan kunden ange vilket certifikat som ska vara standardcertifikatet. Om du vill ange standardcertifikatet ska egenskapen [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) anges till true ("defaultSslBinding":"true"). Om kunden inte anger egenskapen är standardcertifikatet det certifikat som utfärdas till proxydomänen som standard finns på *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Stöd för PUT/POST-begäran med stor nyttolast

APIM Proxy-servern stöder begäran med stor nyttolast när du använder klientcertifikat i HTTPS (till exempel nyttolast > 40 KB). För att förhindra att serverns begäran fryser kan kunder ange egenskapen ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) på proxyvärden. Om egenskapen är inställd på true begärs klientcertifikatet vid SSL/TLS-anslutningstid, innan http-begärandeutbytet. Eftersom inställningen gäller på **proxyvärdnamnsnivån** ber alla anslutningsbegäranden om klientcertifikatet. Kunder kan konfigurera upp till 20 anpassade domäner för Proxy (stöds endast på **Premium-nivån)** och komma runt den här begränsningen.

