---
title: 'Snabbstart: Skapa en DNS-zon och post - Azure-portal'
titleSuffix: Azure DNS
description: Använd den här stegvisa snabbstartsguiden till att lära dig skapa en Azure DNS-zon och registrera med hjälp av Azure-portalen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 26e5386f1c9730f1600e59a002ea7845b82ffe06
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76937121"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Snabbstart: Skapa en Azure DNS-zon och spela in med Azure-portalen

Du kan konfigurera Azure DNS för att matcha värdnamn i din offentliga domän. Om du till exempel har köpt *contoso.xyz-domännamnet* från en domännamnsregistrator kan du konfigurera Azure DNS som värd för *contoso.xyz-domänen* och matcha *www.contoso.xyz* till IP-adressen för webbservern eller webbappen.

I den här snabbstarten skapar du en testdomän och skapar sedan en adresspost för att matcha *www* mot IP-adressen *10.10.10.10*.

>[!IMPORTANT]
>Alla namn och IP-adresser i den här snabbstarten är exempel som inte representerar verkliga scenarier.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

För alla portalsteg loggar du in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon innehåller DNS-poster för en domän. Om du vill låta Azure DNS vara värd för din domän skapar du en DNS-zon för det domännamnet. 

**Så här skapar du DNS-zonen:**

1. Längst upp till vänster väljer du **Skapa en resurs**, sedan **Nätverk** och därefter **DNS-zon**.

1. På sidan **Skapa DNS-zon** skriver eller väljer du följande värden:

   - **Namn**: Skriv *contoso.xyz* för det här snabbstartsexemplet. DNS-zonens namn kan vara valfritt värde som inte redan har konfigurerats på Azure DNS-servrarna. Ett verkligt värde skulle vara en domän som du har köpt från en domännamnsregistrator.
   - **Resursgrupp**: Välj **Skapa ny,** ange *MyResourceGroup*och välj **OK**. Resursgruppens namn måste vara unikt inom Azure-prenumerationen. 

1. Välj **Skapa**.

   ![DNS-zon](./media/dns-getstarted-portal/openzone650.png)

Det kan ta några minuter att skapa zonen.

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Du skapar DNS-poster för din domän inuti DNS-zonen. Skapa en ny adresspost eller en ”A”-post för att matcha ett värdnamn mot en IPv4-adress.

**Så här skapar du en ”A”-post:**

1. Öppna zonen **contoso.xyz** DNS i resursgruppen **MyResourceGroup** under **Alla resurser**i Azure-portalen. Du kan ange *contoso.xyz* i rutan **Filtrera efter namn** för att hitta det enklare.

1. Längst upp på sidan **DNS-zon** väljer du **+ Postuppsättning**.

1. På sidan **Lägg till uppsättning av poster** skriver eller väljer du följande värden:

   - **Namn**: Skriv *www*. Postens namn är det värddatornamn som du vill lösa mot den angivna IP-adressen.
   - **Typ:** Välj **A**. A-poster är de vanligaste, men det finns andra posttyper för e-postservrar (MX), IP v6-adresser ("AAAA" och så vidare. 
   - **TTL**: Skriv *1*. *Time-to-live* för DNS-begäran anger hur länge DNS-servrar och klienter kan cachelagra ett svar.
   - **TTL-enhet**: Välj **Timmar**. Det här är tidsenheten för **TTL**-värdet. 
   - **IP-adress**: För det här snabbstartsexemplet skriver du *10.10.10.10*. Det här värdet är den IP-adress som postnamnet matchar till. I ett verkligt scenario skulle du ange den offentliga IP-adressen för din webbserver.

Eftersom den här snabbstarten bara är för snabb testning behöver du inte konfigurera Azure DNS-namnservrarna på en domännamnsregistrare. Med en riktig produktionsdomän vill du att alla på Internet ska matcha värdnamnet för att ansluta till webbservern eller din webbapp. Du besöker din domännamnsregistrator för att ersätta namnserverposterna med Azure DNS-namnservrarna. Mer information finns i [Självstudie: Använda Azure DNS som värd för din domän](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testa namnmatchningen

Nu när du har en DNS-testzon med en ”A”-testpost kan du testa namnmatchningen med ett verktyg som heter *nslookup*. 

**Så här testar du DNS-namnmatchning:**

1. Öppna zonen **contoso.xyz** DNS i resursgruppen **MyResourceGroup** under **Alla resurser**i Azure-portalen. Du kan ange *contoso.xyz* i rutan **Filtrera efter namn** för att hitta det enklare.

1. Kopiera ett av namnservernamnen från namnlistan på sidan **Översikt**. 

   ![zon](./media/dns-getstarted-portal/viewzonens500.png)

1. Öppna en kommandotolk och kör följande kommando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Ett exempel:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Du bör se något som liknar följande skärm:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Värdnamnet **www\.contoso.xyz** matchas till **10.10.10.10**, precis som du konfigurerade det. Resultatet verifierar att namnmatchningen fungerar korrekt. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade i den här snabbstarten tar du bort dem genom att ta bort resursgruppen **MyResourceGroup.** Öppna resursgruppen **MyResourceGroup** och välj **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)