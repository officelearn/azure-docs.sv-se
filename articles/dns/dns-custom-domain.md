---
title: Integrera Azure DNS med Azure-resurser | Microsoft Docs
description: "Lär dig hur du använder Azure DNS längs DNS för att Azure-resurser."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/19/2018
ms.author: kumud
ms.openlocfilehash: cbc769cd7356b3057fd2aae295071b04d2e40d91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Använda Azure DNS för att ange inställningar för anpassad domän för en Azure-tjänst

Azure DNS ger DNS för en anpassad domän för någon av dina Azure-resurser stöd för anpassade domäner eller som har ett fullständigt kvalificerat domännamn (FQDN). Ett exempel är du har en Azure webbapp och du vill att användarna ska komma åt den genom att antingen använda contoso.com eller www.contoso.com som ett fullständigt domännamn. Den här artikeln beskriver hur du konfigurerar din Azure-tjänst med Azure DNS för att använda anpassade domäner.

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure DNS för din anpassade domän måste du först Delegera din domän till Azure DNS. Besök [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) anvisningar om hur du konfigurerar dina namnservrar för delegering. När din domän har delegerats till Azure DNS-zonen, kan du konfigurera DNS-poster som behövs.

Du kan konfigurera en alternativa eller en anpassad domän för [Azure funktionen appar](#azure-function-app), [offentliga IP-adresser](#public-ip-address), [Apptjänst (webbprogram)](#app-service-web-apps), [Blob storage](#blob-storage), och [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure Funktionsapp

Om du vill konfigurera en anpassad domän för funktionen Azure-appar, skapas en CNAME-post samt konfigurationen på själva funktionen appen.
 
Gå till **andra** > **Funktionsapp** och välj appen funktion. Klicka på **plattformsfunktioner** och under **nätverk** klickar du på **anpassade domäner**.

![funktionen app-bladet](./media/dns-custom-domain/functionapp.png)

Observera den aktuella url på den **anpassade domäner** bladet för den här adressen används som alias för DNS-posten skapas.

![anpassade domäner-bladet](./media/dns-custom-domain/functionshostname.png)

Navigera till din DNS-zon och på **+ postuppsättningen**. Ange följande information på den **lägga till postuppsättning** bladet och klicka på **OK** skapa den.

|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | myfunctionapp        | Det här värdet tillsammans med domännamnet är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | adatumfunction.azurewebsites.net        | DNS-namnet som du skapar alias för, i det här exemplet är det tillhandahålls som standard till appen med funktionen adatumfunction.azurewebsites.net DNS-namnet.        |

Gå tillbaka till din funktionsapp klickar du på **plattformsfunktioner**, och under **nätverk** klickar du på **anpassade domäner**, sedan under **värdnamn**klickar du på **+ Lägg till värdnamnet**.

På den **lägga till värdnamnet** bladet Ange CNAME-post i den **värdnamn** textfält och klicka på **verifiera**. Om posten kunde hittas i **lägga till värdnamnet** visas knappen. Klicka på **lägga till värdnamnet** att lägga till alias.

![värden namnet bladet Lägg till med funktionen appar](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Offentlig IP-adress

Så här konfigurerar du en anpassad domän för tjänster som använder en offentlig IP-adress resurs, till exempel Programgateway, belastningsutjämnare, Cloud Service, virtuella datorer Resource Manager och klassiska virtuella datorer, en CNAME-posten används.

Gå till **nätverk** > **offentliga IP-adressen**, Välj den offentliga IP-resursen och klickar på **Configuration**. Anteckna den IP-adress som anges.

![offentliga ip-bladet](./media/dns-custom-domain/publicip.png)

Navigera till din DNS-zon och på **+ postuppsättningen**. Ange följande information på den **lägga till postuppsättning** bladet och klicka på **OK** skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | mywebserver        | Det här värdet tillsammans med domännamnet är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | A        | Använd en A-post som resursen är en IP-adress.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|IP-adress     | <your ip address>       | Den offentliga IP-adressen.|

![Skapa en A-post](./media/dns-custom-domain/arecord.png)

När du har skapat A-posten kör `nslookup` att validera poster matchar.

![offentliga IP-DNS-sökning](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Följande steg leder dig genom att konfigurera en anpassad domän för en app service webbapp.

Gå till **webb- och Mobile** > **Apptjänst** och markera den resurs som du konfigurerar ett eget domännamn och klicka på **anpassade domäner**.

Observera den aktuella url på den **anpassade domäner** bladet för den här adressen används som alias för DNS-posten skapas.

![anpassade domäner-bladet](./media/dns-custom-domain/url.png)

Navigera till din DNS-zon och på **+ postuppsättningen**. Ange följande information på den **lägga till postuppsättning** bladet och klicka på **OK** skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | mywebserver        | Det här värdet tillsammans med domännamnet är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias. Om resursen används en IP-adress, används en A-post.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | webserver.azurewebsites.net        | DNS-namnet som du skapar alias för, i det här exemplet är webserver.azurewebsites.net DNS-namnet som tillhandahålls som standard till webbappen.        |


![Skapa en CNAME-post](./media/dns-custom-domain/createcnamerecord.png)

Gå tillbaka till app service som har konfigurerats för det anpassade domännamnet. Klicka på **anpassade domäner**, klicka på **värdnamn**. Lägg till CNAME-post som du har skapat, klicka på **+ Lägg till värdnamnet**.

![bild 1](./media/dns-custom-domain/figure1.png)

När processen är slutförd, kör **nslookup** att validera namnmatchning fungerar.

![bild 1](./media/dns-custom-domain/finalnslookup.png)

Mer information om hur du mappar en anpassad domän till App Service finns [mappa ett befintligt anpassade DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Om du behöver köpa en anpassad domän kan du gå [köpa ett anpassat domännamn för Azure Web Apps](../app-service/custom-dns-web-site-buydomains-web-app.md) lära dig mer om Apptjänst-domäner.

## <a name="blob-storage"></a>Blob Storage

Följande steg leder dig genom att konfigurera en CNAME-post för blob storage-kontot med metoden asverify. Den här metoden garanterar det utan avbrott.

Gå till **lagring** > **Lagringskonton**, Välj ditt lagringskonto och på **anpassad domän**. Anteckna FQDN under steg 2, det här värdet används för att skapa den första CNAME-posten

![BLOB storage-domänen](./media/dns-custom-domain/blobcustomdomain.png)

Navigera till din DNS-zon och på **+ postuppsättningen**. Ange följande information på den **lägga till postuppsättning** bladet och klicka på **OK** skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | asverify.mystorageaccount        | Det här värdet tillsammans med domännamnet är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | DNS-namnet som du skapar alias för, i det här exemplet är asverify.adatumfunctiona9ed.blob.core.windows.net DNS-namnet som tillhandahålls som standard till lagringskontot.        |

Gå tillbaka till ditt lagringskonto genom att klicka på **lagring** > **Lagringskonton**, Välj ditt lagringskonto och på **anpassad domän**. Ange det alias som du skapat utan prefixet asverify i textrutan, kontrollera ** Använd indirekt CNAME-validering och på **spara**. När det här steget är klar, gå till DNS-zonen och skapa en CNAME-post utan prefixet asverify.  Efter den, är du säkert att ta bort CNAME-post med prefixet cdnverify.

![BLOB storage-domänen](./media/dns-custom-domain/indirectvalidate.png)

Verifiera DNS-matchning genom att köra`nslookup`

Läs mer om att mappa en anpassad domän till en slutpunkt för blob storage finns [konfigurera ett anpassat domännamn för din slutpunkt för Blob-lagring](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Följande steg leder dig genom att konfigurera en CNAME-post för en CDN-slutpunkt med metoden cdnverify. Den här metoden garanterar det utan avbrott.

Gå till **nätverk** > **CDN profiler**, Välj CDN-profilen och på **slutpunkter** under **allmänna**.

Välj den slutpunkt du arbetar med och klicka på **+ anpassad domän**. Observera den **slutpunktens värdnamn** som det här värdet är posten CNAME-post som pekar på.

![Anpassad CDN-domän](./media/dns-custom-domain/endpointcustomdomain.png)

Navigera till din DNS-zon och på **+ postuppsättningen**. Ange följande information på den **lägga till postuppsättning** bladet och klicka på **OK** skapa den.

|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | cdnverify.mycdnendpoint        | Det här värdet tillsammans med domännamnet är det fullständiga Domännamnet för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post använder ett alias.        |
|TTL     | 1        | 1 används för 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidmätning         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | DNS-namnet som du skapar alias för, i det här exemplet är cdnverify.adatumcdnendpoint.azureedge.net DNS-namnet som tillhandahålls som standard till lagringskontot.        |

Gå tillbaka till CDN-slutpunkten genom att klicka på **nätverk** > **CDN profiler**, och välj CDN-profilen. Klicka på **+ anpassad domän** och ange CNAME-post-alias utan prefixet cdnverify och klicka på **Lägg till**.

När det här steget är klar, tillbaka till din DNS-zonen och skapa en CNAME-post utan prefixet cdnverify.  Efter den, är du säkert att ta bort CNAME-post med prefixet cdnverify. Mer information om CDN och hur du konfigurerar en anpassad domän utan det mellanliggande registreringssteget finns [kartan Azure CDN innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster i Azure](dns-reverse-dns-for-azure-services.md).