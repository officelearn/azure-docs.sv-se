---
title: Integrera Azure DNS med dina Azure-resurser – Azure DNS
description: I den här artikeln kan du läsa om hur du använder Azure DNS tillsammans för att tillhandahålla DNS för dina Azure-resurser.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 56a7680de3127da06341ac03252a9ab0cff9da7c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024956"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Använda Azure DNS för att tillhandahålla anpassade domäninställningar för en Azure-tjänst

Azure DNS tillhandahåller DNS för en anpassad domän för alla dina Azure-resurser som stöder anpassade domäner eller som har ett fullständigt kvalificerat domännamn (FQDN). Ett exempel är att du har en Azure-webbapp och du vill att\.användarna ska komma åt den genom att antingen använda contoso.com eller www contoso.com som en FQDN. I den här artikeln får du information om hur du konfigurerar din Azure-tjänst med Azure DNS för att använda anpassade domäner.

## <a name="prerequisites"></a>Krav

För att kunna använda Azure DNS för din anpassade domän måste du först delegera domänen till Azure DNS. Besök [Delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) för instruktioner om hur du konfigurerar dina namnservrar för delegering. När domänen har delegerats till din Azure DNS-zon kan du konfigurera de DNS-poster som behövs.

Du kan konfigurera en fåfänga eller anpassad domän för [Azure Function Apps,](#azure-function-app) [Public IP-adresser](#public-ip-address), [App Service (Web Apps),](#app-service-web-apps) [Blob storage](#blob-storage)och Azure [CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure-funktionsapp

Om du vill konfigurera en anpassad domän för Azure-funktionsappar skapas en CNAME-post samt konfiguration på själva funktionsappen.
 
Navigera till **Funktionsappen** och välj din funktionsapp. Klicka på **Plattformsfunktioner** och klicka på **Anpassade domäner**under **Nätverk.**

![funktionsappblad](./media/dns-custom-domain/functionapp.png)

Observera den aktuella url:en på **bladet Anpassade domäner,** den här adressen används som alias för den DNS-post som skapas.

![anpassat domänblad](./media/dns-custom-domain/functionshostname.png)

Navigera till DNS-zonen och klicka på **+ Postuppsättning**. Fyll i följande information om **bladet Lägg till postuppsättning** och klicka på **OK** för att skapa den.

|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | myfunctionapp        | Det här värdet tillsammans med domännamnsetiketten är FQDN för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post som använder ett alias.        |
|TTL     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidsmätning         |
|Alias     | adatumfunction.azurewebsites.net        | Det DNS-namn som du skapar alias för, i det här exemplet är det adatumfunction.azurewebsites.net DNS-namn som tillhandahålls som standard till funktionsappen.        |

Navigera tillbaka till funktionsappen, klicka på **Plattformsfunktioner**och **klicka** på Anpassade domäner under Anpassade **värdnamn**under **Anpassade värdnamn** **.**

På bladet **Lägg till värdnamn** anger du CNAME-posten i textfältet **värdnamn** och klickar på **Validera**. Om posten hittas visas knappen **Lägg till värdnamn.** Klicka på **Lägg till värdnamn** om du vill lägga till aliaset.

![funktionsappar lägga till värdnamnsblad](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Offentlig IP-adress

Om du vill konfigurera en anpassad domän för tjänster som använder en offentlig IP-adressresurs som Application Gateway, Load Balancer, Cloud Service, Resource Manager virtuella datorer och, Klassiska virtuella datorer, används en A-post.

Navigera till **Nätverk offentlig** > **IP-adress**, välj den offentliga IP-resursen och klicka på **Konfiguration**. Notera ip-adressen som visas.

![offentliga ip-blad](./media/dns-custom-domain/publicip.png)

Navigera till DNS-zonen och klicka på **+ Postuppsättning**. Fyll i följande information om **bladet Lägg till postuppsättning** och klicka på **OK** för att skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | mywebserver (på ett sätt)        | Det här värdet tillsammans med domännamnsetiketten är FQDN för det anpassade domännamnet.        |
|Typ     | A        | Använd en A-post eftersom resursen är en IP-adress.        |
|TTL     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidsmätning         |
|IP-adress     | `<your ip address>`       | Den offentliga IP-adressen.|

![skapa en A-post](./media/dns-custom-domain/arecord.png)

När A-posten har `nslookup` skapats körs du för att validera postmatchningar.

![offentliga ip dns-sökning](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App-tjänst (webbappar)

Följande steg tar dig genom att konfigurera en anpassad domän för en apptjänstwebbapp.

Navigera till **App Service** och välj den resurs som du konfigurerar ett eget domännamn och klicka på **Anpassade domäner**.

Observera den aktuella url:en på **bladet Anpassade domäner,** den här adressen används som alias för den DNS-post som skapas.

![anpassat domänblad](./media/dns-custom-domain/url.png)

Navigera till DNS-zonen och klicka på **+ Postuppsättning**. Fyll i följande information om **bladet Lägg till postuppsättning** och klicka på **OK** för att skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | mywebserver (på ett sätt)        | Det här värdet tillsammans med domännamnsetiketten är FQDN för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post som använder ett alias. Om resursen använde en IP-adress används en A-post.        |
|TTL     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidsmätning         |
|Alias     | webserver.azurewebsites.net        | Det DNS-namn som du skapar aliaset för, i det här exemplet är det webserver.azurewebsites.net DNS-namn som tillhandahålls som standard till webbappen.        |


![skapa en CNAME-post](./media/dns-custom-domain/createcnamerecord.png)

Navigera tillbaka till apptjänsten som är konfigurerad för det anpassade domännamnet. Klicka på **Anpassade domäner**och sedan på **Värdnamn.** Om du vill lägga till den CNAME-post som du skapade klickar du på **+ Lägg till värdnamn**.

![bild 1](./media/dns-custom-domain/figure1.png)

När processen är klar, kör **nslookup** för att validera namnmatchning fungerar.

![bild 1](./media/dns-custom-domain/finalnslookup.png)

Mer information om hur du mappar en anpassad domän till App Service finns i [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Mer information om hur du migrerar ett aktivt DNS-namn finns i [Migrera ett aktivt DNS-namn till Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Om du behöver köpa en anpassad domän besöker du [Köp ett anpassat domännamn för Azure Web Apps om](../app-service/manage-custom-dns-buy-domain.md) du vill veta mer om App Service-domäner.

## <a name="blob-storage"></a>Blob Storage

Följande steg tar dig igenom att konfigurera en CNAME-post för ett blob storage-konto med hjälp av metoden asverify. Den här metoden säkerställer att det inte finns några driftstopp.

Navigera till > **Lagringslagringskonton,** välj ditt lagringskonto och klicka på **Anpassad domän**. **Storage** Notera FQDN under steg 2, det här värdet används för att skapa den första CNAME-posten

![anpassad domän för blob-lagring](./media/dns-custom-domain/blobcustomdomain.png)

Navigera till DNS-zonen och klicka på **+ Postuppsättning**. Fyll i följande information om **bladet Lägg till postuppsättning** och klicka på **OK** för att skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | asverify.mystorageaccount        | Det här värdet tillsammans med domännamnsetiketten är FQDN för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post som använder ett alias.        |
|TTL     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidsmätning         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Det DNS-namn som du skapar aliaset för, i det här exemplet är det asverify.adatumfunctiona9ed.blob.core.windows.net DNS-namn som tillhandahålls som standard till lagringskontot.        |

Navigera tillbaka till ditt lagringskonto genom att klicka på > **Lagringslagringskonton,** välj ditt lagringskonto och klicka på **Anpassad domän**. **Storage** Skriv in det alias som du skapade utan standardprefixet i textrutan, markera **Använd indirekt CNAME-validering**och klicka på **Spara**. När det här steget är klart går du tillbaka till DNS-zonen och skapar en CNAME-post utan det uppordna prefixet.  Efter den punkten är det säkert att ta bort CNAME-posten med cdnverify-prefixet.

![anpassad domän för blob-lagring](./media/dns-custom-domain/indirectvalidate.png)

Validera DNS-upplösning genom att köra`nslookup`

Om du vill veta mer om hur du mappar en anpassad domän till ett slutpunkt för bloblagring [konfigurera ett anpassat domännamn för slutpunkten för Blob-lagring](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

I följande steg går du igenom att konfigurera en CNAME-post för en CDN-slutpunkt med cdnverify-metoden. Den här metoden säkerställer att det inte finns några driftstopp.

Navigera till**CdN-profiler**för **nätverk** > väljer du CDN-profilen.

Markera den slutpunkt du arbetar med och klicka på **+ Anpassad domän**. Observera **slutpunktsvärden som** det här värdet är den post som CNAME-posten pekar på.

![Anpassad DOMÄN för CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navigera till DNS-zonen och klicka på **+ Postuppsättning**. Fyll i följande information om **bladet Lägg till postuppsättning** och klicka på **OK** för att skapa den.

|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Namn     | cdnverify.mycdnendpoint        | Det här värdet tillsammans med domännamnsetiketten är FQDN för det anpassade domännamnet.        |
|Typ     | CNAME        | Använd en CNAME-post som använder ett alias.        |
|TTL     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Timmar        | Timmar används som tidsmätning         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Det DNS-namn som du skapar alias för, i det här exemplet är det cdnverify.adatumcdnendpoint.azureedge.net DNS-namn som tillhandahålls som standard till lagringskontot.        |

Navigera tillbaka till CDN-slutpunkten genom att klicka på**CdN-profiler**i **nätverk** > och välja CDN-profil. Klicka på **+ Anpassad domän** och ange ditt CNAME-postalias utan cdnverify-prefixet och klicka på Lägg **till**.

När det här steget är klart går du tillbaka till DNS-zonen och skapar en CNAME-post utan cdnverify-prefixet.  Efter den punkten är det säkert att ta bort CNAME-posten med cdnverify-prefixet. Mer information om CDN och hur du konfigurerar en anpassad domän utan mellanliggande registreringssteg finns i [Mappa Azure CDN-innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).
