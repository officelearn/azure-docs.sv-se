---
title: Integrera med Azure Private Link service
description: Lär dig hur du integrerar Azure Key Vault med Azure Private Link service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907077"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Integrera Key Vault med en privat Azure-länk (förhands granskning)

Azure Private Link service ger dig åtkomst till Azure-tjänster (till exempel Azure Key Vault, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket.

En privat Azure-slutpunkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gatewayer, NAT-enheter, ExpressRoute- eller VPN-anslutningar, eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Mer information finns i [Vad är Azure Private Link (för hands version)?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Krav

Om du vill integrera ett nyckel valv med Azure Private Link (för hands version) behöver du följande:

- Ett nyckel valv.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägar-eller deltagar behörighet för både nyckel valvet och det virtuella nätverket.

Din privata slut punkt och det virtuella nätverket måste finnas i samma region. När du väljer en region för den privata slut punkten med hjälp av portalen filtreras automatiskt endast virtuella nätverk i den regionen. Ditt nyckel valv kan finnas i en annan region.

Din privata slut punkt använder en privat IP-adress i det virtuella nätverket.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Upprätta en anslutning till ett privat länk till Key Vault

Skapa först ett virtuellt nätverk genom att följa stegen i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md)

Du kan sedan antingen skapa ett nytt nyckel valv eller upprätta en privat länk anslutning till ett befintligt nyckel valv.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Skapa ett nytt nyckel valv och upprätta en privat länk anslutning

Du kan skapa ett nytt nyckel valv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](quick-create-portal.md)

När du har konfigurerat grunderna i Key Vault väljer du fliken nätverk och följer de här stegen:

1. Välj alternativ knappen privat slut punkt (förhands granskning) på fliken nätverk.
1. Klicka på knappen + Lägg till för att lägga till en privat slut punkt.

    ![Bild](./media/private-link-service-1.png)
 
1. I fältet "plats" på bladet skapa privat slut punkt väljer du den region där det virtuella nätverket finns. 
1. I fältet namn skapar du ett beskrivande namn som gör att du kan identifiera den här privata slut punkten. 
1. Välj det virtuella nätverk och undernät som du vill att den här privata slut punkten ska skapas i från List menyn. 
1. Lämna alternativet "integrera med den privata zonens DNS" oförändrat.  
1. Välj OK.

    ![Bild](./media/private-link-service-2.png)
 
Nu kommer du att kunna se den konfigurerade privata slut punkten. Nu har du möjlighet att ta bort och redigera den här privata slut punkten. Välj knappen "granska + skapa" och skapa nyckel valvet. Det tar 5-10 minuter för distributionen att slutföras. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Upprätta en privat länk anslutning till ett befintligt nyckel valv

Om du redan har ett nyckel valv kan du skapa en privat länk anslutning genom att följa dessa steg:

1. Logga in på Azure Portal. 
1. I Sök fältet skriver du in "nyckel valv"
1. Välj nyckel valvet i listan som du vill lägga till en privat slut punkt för.
1. Välj fliken nätverk under Inställningar
1. Välj fliken anslutningar för privat slut punkt (för hands version) överst på sidan
1. Välj knappen "+ privat slut punkt" överst på sidan.

    ![avbildning](./media/private-link-service-3.png) ![avbildning](./media/private-link-service-4.png)

Du kan välja att skapa en privat slut punkt för alla Azure-resurser med hjälp av det här bladet. Du kan antingen använda List menyerna för att välja en resurs typ och välja en resurs i din katalog, eller så kan du ansluta till en Azure-resurs med hjälp av ett resurs-ID. Lämna alternativet "integrera med den privata zonens DNS" oförändrat.  

![avbildning](./media/private-link-service-3.png)
![avbildning](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Hantera anslutning till privat anslutning

När du skapar en privat slut punkt måste anslutningen godkännas. Om den resurs som du skapar en privat slut punkt för finns i din katalog kommer du att kunna godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta tills ägaren av resursen har godkänt din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Åtgärd för att tillhandahålla tjänst | Status för privat slut punkt för tjänst förbrukare | Beskrivning |
|--|--|--|
| Inget | Väntande åtgärder | Anslutningen skapas manuellt och väntar på godkännande från ägaren till den privata länk resursen. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Slagit | Anslutningen avvisades av ägaren till den privata länk resursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länk resursen, den privata slut punkten blir informativ och bör tas bort för rensning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Så här hanterar du en privat slut punkts anslutning till Key Vault

1. Logga in på Azure Portal.
1. I Sök fältet skriver du in "nyckel valv"
1. Välj det nyckel valv som du vill hantera.
1. Välj fliken nätverk.
1. Om det finns några anslutningar som väntar, visas en anslutning som anges med "väntar" i etablerings status. 
1. Välj den privata slut punkt som du vill godkänna
1. Välj knappen Godkänn.
1. Om det finns anslutningar för privata slut punkter som du vill avvisa, oavsett om det är en väntande begäran eller en befintlig anslutning, väljer du anslutningen och klickar på knappen "avvisa".

    ![Bild](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Kontrol lera att anslutningen till den privata länken fungerar

Du bör kontrol lera att resurserna i samma undernät i den privata slut punkts resursen ansluter till ditt nyckel valv via en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Börja med att skapa en virtuell dator genom att följa stegen i [skapa en virtuell Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md)

På fliken "nätverk":

1. Ange virtuellt nätverk och undernät. Du kan skapa ett nytt virtuellt nätverk eller välja ett befintligt. Om du väljer en befintlig, se till att regionen stämmer.
1. Ange en offentlig IP-resurs.
1. I nätverks säkerhets gruppen NIC väljer du "ingen".
1. I "belastnings utjämning" väljer du "nej".

Öppna kommando raden och kör följande kommando:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckel valv över en offentlig slut punkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckel valv över en privat slut punkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Begränsningar och design överväganden

**Priser**: information om priser finns i [priser för Azure Private Link (för hands version)](https://azure.microsoft.com/pricing/details/private-link/).

**Begränsningar**: den privata slut punkten för Azure Key Vault finns i en offentlig för hands version. Den här funktionen är tillgänglig i alla offentliga Azure-regioner.

**Maximalt antal privata slut punkter per Key Vault**: 64.

**Maximalt antal nyckel valv med privata slut punkter per prenumeration**: 64.

Mer information finns i [Azure Private Link service: begränsningar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Efterföljande moment

- Lär dig mer om [Azure Private Link (för hands version)](../private-link/private-link-service-overview.md)
- Läs mer om [Azure Key Vault](key-vault-overview.md)
