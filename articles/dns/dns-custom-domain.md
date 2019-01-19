---
title: Integrera Azure DNS med Azure-resurser
description: Lär dig hur du använder Azure DNS tillsammans att ange DNS för din Azure-resurser.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/18/2019
ms.author: victorh
ms.openlocfilehash: b513e898e25397f54b8f7f7590a4466523a705ff
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401426"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Använda Azure DNS för att ange inställningar för anpassad domän för en Azure-tjänst

Azure DNS tillhandahåller DNS för en anpassad domän för någon av dina Azure-resurser att stöd för anpassade domäner eller som har ett fullständigt kvalificerat domännamn (FQDN). Ett exempel är du har en Azure webbapp och du vill att användarna ska komma åt den genom att antingen använda contoso.com eller www.contoso.com som ett fullständigt domännamn. Den här artikeln beskriver hur du konfigurerar din Azure-tjänst med Azure DNS för att använda anpassade domäner.

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure DNS för din anpassade domän, måste du först Delegera din domän till Azure DNS. Besök [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) anvisningar om hur du konfigurerar dina namnservrar för delegering. När din domän har delegerats till Azure DNS-zon kan kan du konfigurera DNS-poster som behövs.

Du kan konfigurera en anpassad eller en anpassad domän för [Azure-Funktionsappar](#azure-function-app), [offentliga IP-adresser](#public-ip-address), [App Service (Webbappar)](#app-service-web-apps), [Blob-lagring](#blob-storage), och [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure Function App

Om du vill konfigurera en anpassad domän för Azure-funktionsappar, skapas en CNAME-post samt konfigurationen på funktionsappen själva.
 
Gå till **Funktionsapp** och markera din funktionsapp. Klicka på **plattformsfunktioner** och under **nätverk** klickar du på **anpassade domäner**.

![funktionen appbladet](./media/dns-custom-domain/functionapp.png)

Notera den aktuella url på den **anpassade domäner** bladet för den här adressen används som alias för DNS-posten skapas.

![bladet för anpassad domän](./media/dns-custom-domain/functionshostname.png)

Gå till din DNS-zon och klicka på **+ postuppsättning**. Ange följande information på den **lägga till en postuppsättning** bladet och klickar på **OK** att skapa den.

|Egenskap   |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | myfunctionapp        | Det här värdet tillsammans med domännamnsetiketten är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | adatumfunction.azurewebsites.net        | DNS-namn du skapar aliaset som, i det här exemplet är det adatumfunction.azurewebsites.net DNS-namn som tillhandahålls som standard i funktionsappen.        |

Gå tillbaka till din funktionsapp, klicka på **plattformsfunktioner**, och under **nätverk** klickar du på **anpassade domäner**, sedan under **anpassade värdnamn** klickar du på **+ Lägg till värddatornamn**.

På den **Lägg till värddatornamn** bladet Ange CNAME-post i den **värdnamn** textfält och klicka på **verifiera**. Om posten hittas, den **Lägg till värddatornamn** visas knappen. Klicka på **Lägg till värddatornamn** att lägga till alias.

![funktionsappar Lägg till värd namn](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Offentlig IP-adress

Konfigurera en anpassad domän för tjänster som använder en offentlig IP-adressresurs som Application Gateway, belastningsutjämnare, molntjänst, virtuella datorer i Resource Manager och klassiska virtuella datorer, en CNAME-post används.

Gå till **nätverk** > **offentliga IP-adressen**, Välj den offentliga IP-resursen och klickar på **Configuration**. Anteckna den IP-adressen som visas.

![offentlig IP-adress-bladet](./media/dns-custom-domain/publicip.png)

Gå till din DNS-zon och klicka på **+ postuppsättning**. Ange följande information på den **lägga till en postuppsättning** bladet och klickar på **OK** att skapa den.


|Egenskap   |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | mywebserver        | Det här värdet tillsammans med domännamnsetiketten är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | A        | Använd en A-post som resursen är en IP-adress.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|IP-adress     | <your ip address>       | Offentliga IP-adress.|

![Skapa en A-post](./media/dns-custom-domain/arecord.png)

När en post skapas, köra `nslookup` att verifiera posten matchas.

![offentlig IP-adress DNS-sökning](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Webbappar)

Följande steg leder dig genom konfigurationen av en anpassad domän för en app service webbapp.

Gå till **Apptjänst** och välj den resurs som du konfigurerar ett anpassat domännamn och klicka på **anpassade domäner**.

Notera den aktuella url på den **anpassade domäner** bladet för den här adressen används som alias för DNS-posten skapas.

![bladet anpassade domäner](./media/dns-custom-domain/url.png)

Gå till din DNS-zon och klicka på **+ postuppsättning**. Ange följande information på den **lägga till en postuppsättning** bladet och klickar på **OK** att skapa den.


|Egenskap   |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | mywebserver        | Det här värdet tillsammans med domännamnsetiketten är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias. Om resursen används en IP-adress, används en A-post.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | webserver.azurewebsites.net        | DNS-namn du skapar aliaset som, i det här exemplet är det webserver.azurewebsites.net DNS-namn som tillhandahålls som standard till webbappen.        |


![create a CNAME record](./media/dns-custom-domain/createcnamerecord.png)

Gå tillbaka till app service som är konfigurerad för det anpassade domännamnet. Klicka på **anpassade domäner**, klicka sedan på **värdnamn**. Lägg till CNAME-post som du har skapat, klicka på **+ Lägg till värddatornamn**.

![bild 1](./media/dns-custom-domain/figure1.png)

När processen är klar, köra **nslookup** att verifiera namnmatchning fungerar.

![bild 1](./media/dns-custom-domain/finalnslookup.png)

Mer information om hur du mappar en anpassad domän till App Service finns [mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Om du vill köpa en anpassad domän kan du gå [köpa ett anpassat domännamn för Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) mer information om App Service-domäner.

## <a name="blob-storage"></a>Blob Storage

Följande steg leder dig genom konfigurationen av en CNAME-post för ett blob storage-konto med hjälp av metoden asverify. Den här metoden garanterar att ingen stilleståndstid.

Gå till **Storage** > **Lagringskonton**, Välj ditt lagringskonto och på **Custom domain**. Anteckna det fullständiga Domännamnet under steg 2, det här värdet används för att skapa den första CNAME-posten

![anpassad domän för BLOB storage](./media/dns-custom-domain/blobcustomdomain.png)

Gå till din DNS-zon och klicka på **+ postuppsättning**. Ange följande information på den **lägga till en postuppsättning** bladet och klickar på **OK** att skapa den.


|Egenskap   |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | asverify.mystorageaccount        | Det här värdet tillsammans med domännamnsetiketten är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | DNS-namn du skapar aliaset som, i det här exemplet är det asverify.adatumfunctiona9ed.blob.core.windows.net DNS-namn som tillhandahålls som standard till lagringskontot.        |

Gå tillbaka till ditt storage-konto genom att klicka på **Storage** > **Lagringskonton**, Välj ditt lagringskonto och klickar på **Custom domain**. Ange det alias som du skapade utan prefixet asverify i textrutan, kontrollera ** Använd indirekt CNAME-validering och klicka på **spara**. När det här steget är klart kommer tillbaka till din DNS-zon och skapa en CNAME-post utan prefixet asverify.  Efter den, är du säkert att ta bort CNAME-post med prefixet cdnverify.

![anpassad domän för BLOB storage](./media/dns-custom-domain/indirectvalidate.png)

Verifiera DNS-matchningen genom att köra `nslookup`

Läs mer om att mappa en anpassad domän till en slutpunkt för blob-lagring finns [konfigurera ett anpassat domännamn för din Blob storage-slutpunkt](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Följande steg leder dig genom konfigurationen av en CNAME-post för en CDN-slutpunkt med hjälp av metoden cdnverify. Den här metoden garanterar att ingen stilleståndstid.

Gå till **nätverk** > **CDN-profiler**, Välj din CDN-profil.

Välj vilken slutpunkt du arbetar med och klicka på **+ anpassad domän**. Obs den **slutpunktens värdnamn** eftersom det här värdet är den post som CNAME-post som pekar på.

![Anpassad CDN-domän](./media/dns-custom-domain/endpointcustomdomain.png)

Gå till din DNS-zon och klicka på **+ postuppsättning**. Ange följande information på den **lägga till en postuppsättning** bladet och klickar på **OK** att skapa den.

|Egenskap   |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | cdnverify.mycdnendpoint        | Det här värdet tillsammans med domännamnsetiketten är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | DNS-namn du skapar aliaset som, i det här exemplet är det cdnverify.adatumcdnendpoint.azureedge.net DNS-namn som tillhandahålls som standard till lagringskontot.        |

Gå tillbaka till CDN-slutpunkten genom att klicka på **nätverk** > **CDN-profiler**, och välj din CDN-profil. Klicka på **+ anpassad domän** och ange ditt CNAME-post-alias utan prefixet cdnverify och klicka på **Lägg till**.

När det här steget är klart kommer tillbaka till din DNS-zon och skapa en CNAME-post utan prefixet cdnverify.  Efter den, är du säkert att ta bort CNAME-post med prefixet cdnverify. Mer information om CDN och hur du konfigurerar en anpassad domän utan att det mellanliggande registreringssteget finns [Map Azure CDN-innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurera omvänd DNS för tjänster som hanteras i Azure](dns-reverse-dns-for-azure-services.md).