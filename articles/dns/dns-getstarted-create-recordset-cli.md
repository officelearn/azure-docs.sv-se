<properties
   pageTitle="Skapa en postuppsättning och poster för en DNS-zon med hjälp av CLI| Microsoft Azure"
   description="Skapa värdposter för Azure DNS genom att konfigurera postuppsättningar och poster med CLI"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Skapa DNS-postuppsättningar och poster med hjälp av CLI

> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


Den här artikeln beskriver steg för steg hur du skapar postuppsättningar och poster med hjälp av CLI. När du har skapat en DNS-zon måste du lägga till DNS-posterna för din domän. Innan du gör det är det viktigt att du förstår vad DNS-poster och DNS-postuppsättningar är.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Skapa en postuppsättning och poster

I det här avsnittet beskriver vi hur du skapar en postuppsättning och poster. I det här exemplet ska du skapa en postuppsättning med det relativa namnet ”www” i DNS-zonen ”contoso.com”. Posternas fullständigt kvalificerade namn är ”www.contoso.com”. Posttypen är ”A” och TTL-värdet (Time-To-Live) är 60 sekunder. När du har slutfört det här steget har du skapat en tom postuppsättning.

Skapa en post överst i zonen (i det här fallet ”contoso.com”) genom att använda postnamnet ”@”, inklusive citattecknen. Det här är en vanlig DNS-konvention.

### 1. Skapa en postuppsättning

Skapa en postuppsättning genom att använda `azure network dns record-set create`. Ange resursgruppen, zonnamnet, postuppsättningens relativa namn, posttypen och TTL-värdet (Time to Live). Om parametern `--ttl` inte har definierats används standardvärdet fyra (sekunder). När du har slutfört det här steget har du skapat en tom ”www”-postuppsättning.

*Användning: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2. Lägga till poster

Innan du kan använda den nya ”www”-postuppsättningen måste du lägga till poster i den. Du lägger till poster i postuppsättningar med hjälp av `azure network dns record-set add-record`.

Parametrarna för att lägga till poster i en postuppsättning varierar beroende på typen av postuppsättning. Om du till exempel använder en postuppsättning av typen ”A” så kan du bara ange poster med parametern `-a <IPv4 address>`.

Du kan lägga till *A*-poster för IPv4 i ”www”-postuppsättningen med hjälp av följande kommando:

*Användning: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Fler exempel på posttyper

Följande exempel illustrerar hur du skapar en postuppsättning för varje posttyp. Varje postuppsättning innehåller en enda post.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## Nästa steg

Information om hur du hanterar din postuppsättning och dina poster finns i [Hantera DNS-poster och postuppsättningar med hjälp av CLI](dns-operations-recordsets-portal.md).

Mer information om Azure DNS finns i [Översikt över Azure DNS](dns-overview.md).



<!--HONumber=sep16_HO1-->


