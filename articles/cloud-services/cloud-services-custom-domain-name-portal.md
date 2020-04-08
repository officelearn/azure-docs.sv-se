---
title: Konfigurera ett anpassat domännamn i Molntjänster | Microsoft-dokument
description: Lär dig hur du exponerar ditt Azure-program eller dina Data för internet på en anpassad domän genom att konfigurera DNS-inställningar.  Dessa exempel använder Azure-portalen.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: e764e6a474b9843d43f9e8af9cf3b6a8ddf37189
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811644"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurera ett eget domännamn för en Azure-molntjänst
När du skapar en molntjänst tilldelar Azure den till en underdomän i **cloudapp.net**. Om din molntjänst till exempel heter "contoso" kan användarna komma åt ditt `http://contoso.cloudapp.net`program på en URL som . Azure tilldelar också en virtuell IP-adress.

Du kan dock också exponera programmet på ditt eget domännamn, till exempel **contoso.com**. I den här artikeln beskrivs hur du reserverar eller konfigurerar ett anpassat domännamn för webbroller för Molntjänsten.

Förstår du redan vad CNAME- och A-poster är? [Hoppa förbi förklaringen](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Procedurerna i den här uppgiften gäller för Azure Cloud Services. För App Services finns i [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). För lagringskonton finns i [Konfigurera ett anpassat domännamn för slutpunkten för Azure Blob-lagring](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Kom igång snabbare – använd den nya [azure-guidade genomgången!](https://support.microsoft.com/kb/2990804)  Det gör att associera ett anpassat domännamn och skydda kommunikation (TLS) med Azure Cloud Services eller Azure Websites en kick.
> 
> 

## <a name="understand-cname-and-a-records"></a>Förstå CNAME- och A-poster
CNAME (eller aliasposter) och A-poster kan du båda associera ett domännamn med en viss server (eller tjänst i det här fallet) men de fungerar annorlunda. Det finns också vissa specifika överväganden när du använder A-poster med Azure Cloud-tjänster som du bör tänka på innan du bestämmer dig för vilken du ska använda.

### <a name="cname-or-alias-record"></a>CNAME- eller Alias-post
En CNAME-post mappar en *viss* domän, till exempel **contoso.com** eller **www\.contoso.com**, till ett kanoniskt domännamn. I det här fallet är det kanoniska domännamnet **[myapp].cloudapp.net-domännamnet** för ditt Azure-värdbaserade program. När CNAME har skapats skapas ett alias för **[myapp].cloudapp.net**. CNAME-posten kommer att matcha till IP-adressen för din **[myapp].cloudapp.net-tjänst** automatiskt, så om IP-adressen för molntjänsten ändras behöver du inte vidta några åtgärder.

> [!NOTE]
> Vissa domänregistratorer kan du bara mappa underdomäner när du\.använder en CNAME-post, till exempel www contoso.com, och inte rotnamn, till exempel contoso.com. Mer information om CNAME-poster finns i dokumentationen från din registrator, [Wikipedia-posten på CNAME-posten](https://en.wikipedia.org/wiki/CNAME_record)eller [IETF-domännamnen – implementerings- och specifikationsdokumentet.](https://tools.ietf.org/html/rfc1035)

### <a name="a-record"></a>En post
En *A-post* mappar en domän, till exempel **contoso.com** eller **www\.contoso.com**, eller en *jokerteckendomän* som ** \*.contoso.com**, till en IP-adress. När det gäller en Azure Cloud Service, den virtuella IP för tjänsten. Så den största fördelen med en A-post över en CNAME-post är att \*du kan ha en post som använder ett jokertecken, till exempel **.contoso.com**, som skulle hantera begäranden om flera underdomäner som **mail.contoso.com**, **login.contoso.com**eller **www\.contso.com**.

> [!NOTE]
> Eftersom en A-post mappas till en statisk IP-adress kan den inte automatiskt lösa ändringar i IP-adressen för din molntjänst. IP-adressen som används av molntjänsten tilldelas första gången du distribuerar till en tom plats (antingen produktion eller mellanlagring.) Om du tar bort distributionen för platsen släpps IP-adressen av Azure och eventuella framtida distributioner till platsen kan få en ny IP-adress.
> 
> Ip-adressen för en viss distributionsplats (produktion eller mellanlagring) sparas på ett bekvämt sätt när du byter mellan mellanlagring och produktionsdistributioner eller utför en uppgradering på plats av en befintlig distribution. Mer information om hur du utför dessa åtgärder finns i [Så här hanterar du molntjänster](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Lägga till en CNAME-post för din anpassade domän
Om du vill skapa en CNAME-post måste du lägga till en ny post i DNS-tabellen för din anpassade domän med hjälp av de verktyg som tillhandahålls av registratorn. Varje registrator har en liknande men något annorlunda metod för att ange en CNAME-post, men begreppen är desamma.

1. Använd någon av dessa metoder för att hitta **det cloudapp.net** domännamn som tilldelats molntjänsten.

   * Logga in på [Azure-portalen,]välj din molntjänst, titta på avsnittet **Översikt** och leta reda på **platsens URL-post.**

       ![avsnittet snabb blick som visar webbadressen till webbplatsen][csurl]

       **Eller**
   * Installera och konfigurera [Azure Powershell](/powershell/azure/overview)och använd sedan följande kommando:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Spara domännamnet som används i url:en som returneras med någon av metoderna, eftersom du behöver det när du skapar en CNAME-post.
2. Logga in på DNS-registratorns webbplats och gå till sidan för att hantera DNS. Leta efter länkar eller områden på webbplatsen som är märkta som **Domännamn,** **DNS**eller **Name Server Management**.
3. Nu hitta där du kan välja eller ange CNAME's. Du kan behöva välja posttypen från en listruta eller gå till en avancerad inställningssida. Du bör leta efter orden **CNAME,** **Alias**eller **Underdomäner**.
4. Du måste också ange domän- eller underdomänalias för CNAME, till exempel **www** om du vill skapa ett alias för **www\.customdomain.com**. Om du vill skapa ett alias för rotdomänen kan**\@** det visas som symbolen ' ' i registratorns DNS-verktyg.
5. Sedan måste du ange ett kanoniskt värdnamn, vilket är programmets **cloudapp.net** domän i det här fallet.

Följande CNAME-post vidarebefordrar till exempel all trafik från **\.www contoso.com** till **contoso.cloudapp.net**, det anpassade domännamnet för det distribuerade programmet:

| Alias/värdnamn/Underdomän | Kanonisk domän |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> En besökare på **www\.contoso.com** kommer aldrig att se den sanna värden (contoso.cloudapp.net), så vidarebefordran är osynlig för slutanvändaren.
> 
> Exemplet ovan gäller endast trafik **www** på www-underdomänen. Eftersom du inte kan använda jokertecken med CNAME-poster måste du skapa ett CNAME för varje domän/underdomän. Om du vill dirigera trafik från underdomäner, till exempel *.contoso.com, till din cloudapp.net-adress kan du konfigurera en **URL-omdirigering** eller **URL Vidarebefordra** post i DNS-inställningarna eller skapa en A-post.

## <a name="add-an-a-record-for-your-custom-domain"></a>Lägga till en A-post för din anpassade domän
Om du vill skapa en A-post måste du först hitta den virtuella IP-adressen för molntjänsten. Lägg sedan till en ny post i DNS-tabellen för din anpassade domän med hjälp av de verktyg som tillhandahålls av din registrator. Varje registrator har en liknande men något annorlunda metod för att ange en A-post, men begreppen är desamma.

1. Använd någon av följande metoder för att hämta IP-adressen för din molntjänst.

   * Logga in på [Azure-portalen,]välj din molntjänst, titta på avsnittet **Översikt** och leta sedan reda på posten **Offentliga IP-adresser.**

       ![snabb blick avsnitt som visar VIP][vip]

       **Eller**
   * Installera och konfigurera [Azure Powershell](/powershell/azure/overview)och använd sedan följande kommando:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Spara IP-adressen, eftersom du behöver den när du skapar en A-post.
2. Logga in på DNS-registratorns webbplats och gå till sidan för att hantera DNS. Leta efter länkar eller områden på webbplatsen som är märkta som **Domännamn,** **DNS**eller **Name Server Management**.
3. Nu hittar du var du kan välja eller ange A-post. Du kan behöva välja posttypen från en listruta eller gå till en avancerad inställningssida.
4. Markera eller ange den domän eller underdomän som ska använda den här A-posten. Välj till exempel **www** om du vill skapa ett alias för **www\.customdomain.com**. Om du vill skapa en jokerteckenpost för alla underdomäner anger du '*****'. Detta kommer att omfatta alla underdomäner som **mail.customdomain.com,** **login.customdomain.com**och **www\.customdomain.com**.

    Om du vill skapa en A-post för rotdomänen**\@** kan den visas som symbolen ' ' i registratorns DNS-verktyg.
5. Ange IP-adressen för molntjänsten i det angivna fältet. Detta associerar domänposten som används i A-posten med IP-adressen för molntjänstdistributionen.

Följande A-post vidarebefordrar till exempel all trafik från **contoso.com** till **137.135.70.239**, IP-adressen för det distribuerade programmet:

| Värdnamn/Underdomän | IP-adress |
| --- | --- |
| \@ |137.135.70.239 |

Det här exemplet visar att du skapar en A-post för rotdomänen. Om du vill skapa en jokerteckenpost för att täcka alla underdomäner anger du '*****' som underdomän.

> [!WARNING]
> IP-adresser i Azure är dynamiska som standard. Du kommer förmodligen vill använda en [reserverad IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md) för att säkerställa att din IP-adress inte ändras.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Hantera molntjänster](cloud-services-how-to-manage-portal.md)
* [Mappa CDN-innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md)
* [Allmän konfiguration av molntjänsten](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuerar en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



