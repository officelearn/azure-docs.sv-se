---
title: Konfigurera ett anpassat domännamn i molntjänster | Microsoft Docs
description: Lär dig hur du kan exponera din Azure-program eller data till internet på en anpassad domän genom att konfigurera DNS-inställningarna.  De här exemplen använder Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 814ae0e79ac24d5b645c4bf107287ab1b8326ff4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121629"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurera ett anpassat domännamn för en Azure cloud Services
När du skapar en tjänst i molnet, Azure tilldelar den till en underdomän till **cloudapp.net**. Exempel: om din molntjänst heter ”contoso”, användarna kommer att kunna komma åt ditt program på en URL liknande http://contoso.cloudapp.net. Azure tilldelar också en virtuell IP-adress.

Men du kan också visa ditt program på ditt eget domännamn, t.ex **contoso.com**. Den här artikeln beskriver hur du reservera eller konfigurera ett anpassat domännamn för Cloud Service-web-roller.

Har du redan att förstå vilka CNAME- och A-poster är? [Jump tidigare förklaringen](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Procedurerna i det här steget gäller Azure Cloud Services. App Services, se [mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Storage-konton, se [konfigurera ett anpassat domännamn för din Azure Blob storage-slutpunkt](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Kom igång snabbare – Använd den nya Azure [guidad genomgång](https://support.microsoft.com/kb/2990804)!  Det gör associera ett anpassat domännamn och att säkra kommunikation (SSL) med Azure Cloud Services eller Azure Websites ett ögonblick.
> 
> 

## <a name="understand-cname-and-a-records"></a>Förstå CNAME- och A-poster
CNAME-post (eller alias-poster) och A-poster båda kan du associera ett domännamn med en specifik server (eller tjänst i det här fallet) men de fungerar på olika sätt. Det finns också några specifika överväganden när du använder A-poster med Azure molntjänster som du bör tänka på innan du bestämmer dig som du vill använda.

### <a name="cname-or-alias-record"></a>CNAME-post eller aliaspost
En CNAME-post som mappar en *specifika* domän, till exempel **contoso.com** eller **www\.contoso.com**, till ett canonical domännamn. I det här fallet canonical domännamnet är den **[myapp] .cloudapp .net** domännamnet för din Azure-värdbaserade program. När du skapat CNAME skapar ett alias för den **[myapp] .cloudapp .net**. CNAME-posten matchar med IP-adressen för din **[myapp] .cloudapp .net** tjänsten automatiskt, så om IP-adressen för Molntjänsten ändras kan du inte behöver vidta några åtgärder.

> [!NOTE]
> Vissa domän-registratorer låter dig endast att mappa underdomäner när du använder en CNAME-post, till exempel www\.contoso.com och inte rot namn, t.ex contoso.com. Mer information om CNAME-poster finns i dokumentationen som tillhandahålls av din registrator [Wikipedia-transaktionen på CNAME-post](https://en.wikipedia.org/wiki/CNAME_record), eller [IETF domännamn - implementering och specifikation](https://tools.ietf.org/html/rfc1035) dokumentet.

### <a name="a-record"></a>En post
En *A* post mappar en domän, till exempel **contoso.com** eller **www\.contoso.com**, *eller en domän med jokertecken* , till exempel  **\*. contoso.com**, en IP-adress. När det gäller ett Azure Cloud Services, virtuella IP-Adressen för tjänsten. Så den största fördelen med en A-post via en CNAME-post är att du har en post som använder ett jokertecken, till exempel \* **. contoso.com**, vilket skulle hantera begäranden för flera underordnade domäner som  **Mail.contoso.com**, **login.contoso.com**, eller **www\.contso.com**.

> [!NOTE]
> Eftersom en A-post mappas till en statisk IP-adress, det går inte att den automatiskt lösa ändringar till IP-adressen för din molntjänst. IP-adress som används av din molntjänst tilldelas första gången du distribuerar till ett tomt fack (produktion eller mellanlagring.) Om du tar bort distributionen för facket IP-adressen frisläpps av Azure och alla framtida distributioner till facket ges en ny IP-adress.
> 
> IP-adressen för en given distributionsplats (produktions- eller) bevaras bekvämt, när du växlar mellan mellanlagring och produktionsdistributioner eller utför en uppgradering på plats av en befintlig distribution. Mer information om hur du utför dessa åtgärder finns i [så här hanterar du molntjänster](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Lägg till en CNAME-post för din anpassade domän
Om du vill skapa en CNAME-post, du måste lägga till en ny post i tabellen DNS för din anpassade domän med hjälp av verktygen i Registratorn. Varje registrator har en liknande metod för att ange en CNAME-post, men begreppen är desamma.

1. Använd någon av följande metoder för att hitta den **. cloudapp.net** domännamn som tilldelats till din molntjänst.

   * Logga in på den [Azure Portal], Välj din molntjänst, titta på den **Essentials** avsnittet och hitta den **webbplatsens URL** posten.

       ![snabböversikten avsnitt som visar webbplatsens URL][csurl]

       **OR**
   * Installera och konfigurera [Azure Powershell](/powershell/azure/overview), och Använd sedan följande kommando:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Spara domännamnet som används i URL-Adressen som returneras av någon av metoderna som du behöver den när du skapar en CNAME-post.
2. Logga in till din DNS-registratorns webbplats och gå till sidan för hantering av DNS. Söker efter länkar eller områden på webbplatsen som är märkta som **domännamn**, **DNS**, eller **Namnserverhantering**.
3. Nu hitta där du kan välja eller ange CNAME'S. Du kan behöva välja typ av post från en listmeny ned, eller gå till en sida med avancerade inställningar. Du ska leta efter orden **CNAME**, **Alias**, eller **underdomäner**.
4. Du måste också tillhandahålla domän eller underdomän alias för CNAME-post, till exempel **www** om du vill skapa ett alias för **www\.customdomain.com**. Om du vill skapa ett alias för rotdomänen, det kan vara markerat som den ”**\@**' symbolen i din registratorns DNS-verktyg.
5. Måste du ange ett canonical värdnamn, vilket är ditt programs **cloudapp.net** domänen i detta fall.

Till exempel följande CNAME-posten vidarebefordrar all trafik från **www\.contoso.com** till **contoso.cloudapp.net**, det anpassa domännamnet för dina distribuerade program:

| Alias/värd namn/underdomän | Canonical domän |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> En besökare av **www\.contoso.com** ser aldrig sant värden (contoso.cloudapp.net), så att vidarebefordran är osynliga för användaren.
> 
> I exemplet ovan gäller endast för trafiken på den **www** underdomänen. Eftersom du inte kan använda jokertecken med CNAME-poster, måste du skapa en CNAME-post för varje domän/underdomän. Om du vill dirigera trafik från underdomäner, till exempel *. contoso.com, till din cloudapp.net-adress kan du konfigurera en **omdirigerings-URL: en** eller **URL framåt** post i DNS-inställningarna eller skapa en A-post.

## <a name="add-an-a-record-for-your-custom-domain"></a>Lägg till en A-post för din anpassade domän
Om du vill skapa en A-post, måste du först hitta den virtuella IP-adressen för din molntjänst. Lägg sedan till en ny post i tabellen DNS för din anpassade domän med hjälp av verktygen i Registratorn. Varje registrator har en liknande metod för att ange en A-post, men begreppen är desamma.

1. Använd någon av följande metoder för att hämta IP-adressen för din molntjänst.

   * Logga in på den [Azure Portal], Välj din molntjänst, titta på den **Essentials** avsnittet och hitta den **offentliga IP-adresser** posten.

       ![snabböversikten avsnitt som visar VIP][vip]

       **OR**
   * Installera och konfigurera [Azure Powershell](/powershell/azure/overview), och Använd sedan följande kommando:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Spara IP-adress som du behöver den när du skapar en A-post.
2. Logga in till din DNS-registratorns webbplats och gå till sidan för hantering av DNS. Söker efter länkar eller områden på webbplatsen som är märkta som **domännamn**, **DNS**, eller **Namnserverhantering**.
3. Nu hitta där du kan välja eller ange en post. Du kan behöva välja typ av post från en listmeny ned, eller gå till en sida med avancerade inställningar.
4. Välj eller ange domän eller underdomän som ska använda den här A-post. Välj exempelvis **www** om du vill skapa ett alias för **www\.customdomain.com**. Om du vill skapa en post för jokertecken för alla underordnade domäner, anger du ”***”. Det här täcker alla underdomäner som **mail.customdomain.com**, **login.customdomain.com**, och **www\.customdomain.com**.

    Om du vill skapa en A-post för rotdomänen, det kan vara markerat som den ”**\@**' symbolen i din registratorns DNS-verktyg.
5. Ange IP-adressen för din molntjänst i det angivna fältet. Det här associerar posten domän används i A-post med IP-adressen för din molntjänstdistribution.

Till exempel en post vidarebefordrar all trafik från följande **contoso.com** till **137.135.70.239**, IP-adressen för dina distribuerade program:

| Värden namn/underdomän | IP-adress |
| --- | --- |
| \@ |137.135.70.239 |

Det här exemplet visar hur du skapar en A-post för rotdomänen. Om du vill skapa en post för jokertecken för att täcka alla underordnade domäner, anger du ”***” som underdomänen.

> [!WARNING]
> IP-adresser i Azure är dynamiska som standard. Vill du förmodligen att använda en [reserverade IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md) så att din IP-adress inte ändras.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Så här hanterar du molntjänster](cloud-services-how-to-manage-portal.md)
* [Mappa CDN-innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md)
* [Allmän konfiguration för din molntjänst](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuera en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
