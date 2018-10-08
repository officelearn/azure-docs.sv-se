---
title: Självstudie – Använda Azure DNS som värd för din domän och underdomän
description: Den här självstudien visar hur du konfigurerar Azure DNS för att vara värd för dina DNS-zoner.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: ea0dc257d691326bc073b4cbff37e847a6990f02
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452314"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Självstudie: Använda Azure DNS som värd för din domän

Du kan använda Azure DNS för att vara värd för din DNS-domän och hantera dina DNS-poster. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. 

Anta exempelvis att du köper domänen contoso.net från en domännamnsregistrator och sedan skapar en zon med namnet contoso.net i Azure DNS. Eftersom du är ägare till domänen erbjuder sig registratorn att konfigurera namnserverposterna (NS) för din domän. Registratorn lagrar NS-posterna i den överordnade .net-zonen. Internetanvändare över hela världen omdirigeras sedan till din Azure DNS-zon när de försöker matcha DNS-poster i contoso.net.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en DNS-zon
> * Hämta en lista över namnservrar
> * Delegera domänen
> * Kontrollera att delegeringen fungerar


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal.
1. Uppe till vänster väljer du **Skapa en resurs** > **Nätverk** > **DNS-zon** för att öppna sidan **Skapa DNS-zon**.

   ![DNS-zon](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. På sidan **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:

   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|[ditt domännamn] |Det domännamn som du har köpt. Den här självstudien använder contoso.net som ett exempel.|
   |**Prenumeration**|[Din prenumeration]|Välj den prenumeration där du vill skapa zonen.|
   |**Resursgrupp**|**Skapa ny:** contosoRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. |
   |**Plats**|Östra USA||

> [!NOTE]
> Platsen för resursgruppen har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

## <a name="retrieve-name-servers"></a>Hämta namnservrar

Innan du kan delegera din DNS-zon till Azure DNS måste du känna till namnservrarna för din zon. Azure DNS allokerar namnservrar från en pool varje gång en zon skapas.

1. I Azure Portal klickar du på **Alla resurser** i rutan **Favoriter** för den DNS-zon du skapade. På sidan **Alla resurser** väljer du din DNS-zon. Om den prenumeration som du valde redan har flera resurser kan du ange ditt domännamn i rutan **Filtrera efter namn** för att enkelt få åtkomst till programgatewayen. 

1. Hämta namnservrarna på sidan DNS-zon. I det här exemplet har zonen contoso.net tilldelats namnservrarna *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* samt *ns4-01.azure-dns.info*:

   ![Lista över namnservrar](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS skapar automatiskt auktoritativa NS-poster i din zon för de tilldelade namnservrarna.


## <a name="delegate-the-domain"></a>Delegera domänen

Nu när du har namnservrar och DNS-zonen är skapad måste den överordnade domänen uppdateras med Azure DNS-namnservrarna. Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. På registratorns DNS-hanteringssida redigerar du NS-posterna och ersätter NS-posterna med Azure DNS-namnservrarna.

När du delegerar en domän till Azure DNS måste du använda namnservernamnen som tillhandahålls av Azure DNS. Vi rekommenderar att du använder alla fyra namnservernamn, oavsett vilket namn din domän har. Domändelegering kräver inte att namnservern använder samma toppnivådomän som din domän.

> [!NOTE]
> När du kopierar varje namnserveradress, bör du kontrollera att du kopierar den efterföljande punkten i slutet av adressen. En efterföljande punkt anger slutet av ett fullständigt domännamn. Vissa registratorer kan lägga till punkten om NS-namnet inte har någon i slutet. Men för att vara kompatibel med DNS-RFC bör du inkludera den efterföljande punkten, eftersom du inte kan förutsätta att varje registrator lägger till den åt dig.

Delegering med hjälp av namnservrar i din egen zon, även kallat *vanity name servers*, stöds inte i Azure DNS.

## <a name="verify-that-the-delegation-is-working"></a>Kontrollera att delegeringen fungerar

När du har slutfört delegeringen kan du kontrollera att den fungerar med hjälp av ett verktyg som till exempel *nslookup* för att köra en fråga mot SOA-posten (Start of Authority) för din zon. SOA-posten skapas automatiskt när zonen har skapats. Du kan behöva vänta i 10 minuter eller längre när du har slutfört delegeringen innan du kan bekräfta att den fungerar. Det kan ta en stund innan ändringarna sprids genom DNS-systemet.

Du måste inte ange Azure DNS-namnservrarna. Om delegeringen är korrekt konfigurerad hittar den normala DNS-matchningsprocessen namnservrarna automatiskt.

Från en kommandotolk skriver du ett nslookup-kommando som liknar följande:

```
nslookup -type=SOA contoso.net
```

Här är ett exempel på ett svar från föregående kommando:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla resursgruppen **contosoRG** om du planerar att genomföra nästa självstudie. Annars tar du bort resursgrupp **contosoRG** för att ta bort de resurser som skapades i den här självstudien. För att göra det klickar du på resursgruppen **contosoRG** och klickar sedan på **Ta bort resursgrupp**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en DNS-zon för din domän och delegerat den till Azure DNS. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
