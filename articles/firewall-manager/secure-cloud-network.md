---
title: 'Självstudie: skydda ditt virtuella WAN med Azure Firewall Manager Preview'
description: I den här självstudien får du lära dig hur du skyddar ditt virtuella WAN med Azure Firewall Manager med hjälp av Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77443065"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Självstudie: skydda ditt virtuella WAN med Azure Firewall Manager Preview 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Med hjälp av Azure Firewall Manager Preview kan du skapa säkra virtuella hubbar för att skydda din moln nätverks trafik till privata IP-adresser, Azure-PaaS och Internet. Trafik dirigering till brand väggen är automatiserad, så du behöver inte skapa användardefinierade vägar (UDR).

![skydda moln nätverket](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager stöder också en hubb för virtuella nätverk. En jämförelse av arkitektur typerna för den säkra virtuella hubben och hubben för virtuella nätverk finns i [Vad är arkitektur alternativ för Azure Firewall Manager?](vhubs-and-vnets.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa det virtuella ekernätverket
> * Skapa en säker virtuell hubb
> * Anslut hubben och eker-virtuella nätverk
> * Skapa en brand Väggs princip och skydda hubben
> * Dirigera trafik till hubben
> * testa brandväggen.

## <a name="create-a-hub-and-spoke-architecture"></a>Skapa en hubb och eker-arkitektur

Skapa först ett eker VNet där du kan placera dina servrar.

### <a name="create-a-spoke-vnet-and-subnets"></a>Skapa ett eker VNet och undernät

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk**under **nätverk**.
4. Som **namn**skriver du **ekrar-01**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resurs grupp**väljer du **Skapa ny**och skriver **VB-Manager** som namn och väljer **OK**.
8. För **plats**väljer du **(US) USA, östra**.
9. Under **undernät**, för **namn** typ **arbets belastning – SN**.
10. I fältet **Adressintervall** skriver du **10.0.1.0/24**.
11. Godkänn de andra standardinställningarna och välj sedan **skapa**.

Skapa sedan ett undernät för en hopp Server.

1. På sidan Azure Portal start väljer du **resurs grupper** > **VB-Manager**.
2. Välj det **ekrar-01** virtuella nätverket.
3. Välj **undernät** > och**undernät.**
4. Som **namn**skriver du **hopp-SN**.
5. I fältet **Adressintervall** skriver du **10.0.2.0/24**.
6. Välj **OK**.

### <a name="create-the-secured-virtual-hub"></a>Skapa den säkra virtuella hubben

Skapa din säkra virtuella hubb med hjälp av brand Väggs hanteraren.

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. I rutan Sök skriver du **Firewall Manager** och väljer **brand Väggs hanteraren**.
3. På sidan **Firewall Manager** väljer du **skapa en säker virtuell hubb**.
4. På sidan **Skapa ny skyddad virtuell hubb** väljer du din prenumeration och resurs gruppen **VB-Manager** .
5. För namnet på den **säkra virtuella hubben**skriver du **Hub-01**.
6. För **plats**väljer du **östra USA**.
7. Skriv **10.1.0.0/16**för **nav adress utrymme**.
8. För det nya vWAN-namnet skriver du **vWAN-01**.
9. Lämna kryss rutan **Inkludera VPN-gateway för att aktivera betrodda säkerhets partner** avmarkerad.
10. Välj **Nästa: Azure-brandvägg**.
11. Godkänn standard inställningen för **Azure Firewall** som **är aktive rad** och välj sedan **Nästa: betrodd säkerhets partner**.
12. Godkänn standardinställningen för **inaktiverade** **betrodda säkerhets partner** och välj **Nästa: granska + skapa**.
13. Välj **Skapa**. Det tar cirka 30 minuter att distribuera.

### <a name="connect-the-hub-and-spoke-vnets"></a>Anslut hubben och eker-virtuella nätverk

Nu kan du peer-koppla hubben och eker-virtuella nätverk.

1. Välj resurs gruppen **VB-Manager** och välj sedan det virtuella WAN **-nätverket vwan-01** .
2. Under **anslutning**väljer du **virtuella nätverks anslutningar**.
3. Välj **Lägg till anslutning**.
4. För **anslutnings namn**skriver du **hubb-eker**.
5. För **hubbar**väljer du **hubb-01**.
6. För **virtuellt nätverk**väljer du **eker-01**.
7. Välj **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Skapa en brand Väggs princip och skydda hubben

En brand Väggs princip definierar samlingar av regler för att dirigera trafik på ett eller flera skyddade virtuella nav. Du skapar brand Väggs principen och skyddar sedan hubben.

1. I brand Väggs hanteraren väljer du **skapa en Azure Firewall-princip**.
2. Välj din prenumeration och välj sedan resurs gruppen **VB-Manager** .
3. Under **princip information**, för **namn** typ **policy-01** och för **region** väljer du **USA, östra**.
4. Välj **Nästa: regler**.
5. På fliken **regler** väljer du **Lägg till en regel samling**.
6. På sidan **Lägg till regel samling** skriver du **RC-01** som **namn**.
7. För **regel samlings typ**väljer du **program**.
8. För **prioritet**, Skriv **100**.
9. Se till att **regel samlings åtgärden** är **tillåten**.
10. För regel **namn** typen **Allow-MSFT**.
11. För **käll adress**skriver **\*** du.
12. För **protokoll**, Skriv **http, https**.
13. Se till att mål typen är ett **fullständigt domän namn**.
14. För **mål**skriver ** \*du. Microsoft.com**.
15. Välj **Lägg till**.
16. Välj **Nästa: skyddade virtuella nav**.
17. På fliken **skyddade virtuella hubbar** väljer du **hubb-01**.
19. Välj **Granska + skapa**.
20. Välj **Skapa**.

Det kan ta ungefär fem minuter eller mer att slutföra.

## <a name="route-traffic-to-your-hub"></a>Dirigera trafik till hubben

Nu måste du se till att nätverks trafiken dirigeras till genom brand väggen.

1. I brand Väggs hanteraren väljer du **skyddade virtuella nav**.
2. Välj **hubb – 01**.
3. Under **Inställningar**väljer du **flödes inställningar**.
4. Under **Internet trafik**, **trafik från virtuella nätverk**, väljer du **Skicka via Azure-brandvägg**.
5. Under **Azure privat trafik**, **trafik till virtuella nätverk**, väljer du **Skicka via Azure-brandvägg**.
6. Välj **Redigera IP-adressprefix (ES)**.
7. Välj **Lägg till ett IP**-adressprefix.
8. Skriv **10.0.1.0/24** som adress till arbets belastnings under nätet och välj **Spara**.
9. Under **Inställningar**väljer du **anslutningar**.
10. Välj **nav-eker-** anslutningen och välj sedan **säker Internet trafik** och välj sedan **OK**.


## <a name="test-your-firewall"></a>Testa din brand vägg

Om du vill testa brand Väggs reglerna måste du distribuera ett par servrar. Du distribuerar arbets belastning – SRV i under nätet för arbets belastningen – SN för att testa brand Väggs reglerna och för att använda fjärr skrivbord så att du kan ansluta från Internet och sedan ansluta till arbets belastnings-SRV.

### <a name="deploy-the-servers"></a>Distribuera servrarna

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Windows Server 2016 Data Center** i listan **populär** .
3. Ange följande värden för den virtuella datorn:

   |Inställning  |Värde  |
   |---------|---------|
   |Resursgrupp     |**VB-chef**|
   |Namn på virtuell dator     |**Hopp-SRV**|
   |Region     |**USA USA, östra)**|
   |Administratörens användar namn     |**azureuser**|
   |lösenordsinställning     |Ange ditt lösen ord|

4. Under **regler för inkommande port**för **offentliga inkommande portar**väljer du **Tillåt valda portar**.
5. I fältet **Välj inkommande portar** väljer du **RDP (3389)**.

6. Godkänn de andra standardinställningarna och välj **Nästa: diskar**.
7. Acceptera standardvärdena för disken och välj **Nästa: nätverk**.
8. Se till att **eker-01** har valts för det virtuella nätverket och att under nätet är **hopp-SN**.
9. För **offentlig IP-** adress accepterar du standard namnet för den nya offentliga IP-adressen (hopp-SRV-IP).
11. Godkänn övriga standardvärden och välj **Nästa: hantering**.
12. Välj **av** om du vill inaktivera startdiagnostik. Godkänn de andra standardinställningarna och välj **Granska + skapa**.
13. Granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

Använd informationen i följande tabell för att konfigurera en annan virtuell dator med namnet **arbets belastning-SRV**. Resten av konfigurationen är samma som för den virtuella datorn Srv-Jump.

|Inställning  |Värde  |
|---------|---------|
|Undernät|**Workload-SN**|
|Offentlig IP-adress|**Inga**|
|Offentliga inkommande portar|**Inga**|

### <a name="add-a-route-table-and-default-route"></a>Lägg till en routningstabell och standard väg

Om du vill tillåta en Internet anslutning att gå-SRV måste du skapa en routningstabell och en standard-gateway-väg till Internet från **hopp-SN-** undernätet.

1. Välj **Skapa en resurs** på Azure-portalen.
2. Skriv **routningstabellen** i sökrutan och välj sedan **routningstabell**.
3. Välj **Skapa**.
4. Skriv **RT-01** som **namn**.
5. Välj din prenumeration, **VB-chef** för resurs gruppen och **(US) USA, östra** för regionen.
6. Välj **Skapa**.
7. När distributionen är klar väljer du routningstabellen för **RT-01** .
8. Välj **vägar** och välj sedan **Lägg till**.
9. Skriv **hopp-till-inet** som **väg namn**.
10. Skriv **0.0.0.0/0** för **adressprefixet**.
11. Välj **Internet** för **nästa hopp typ**.
12. Välj **OK**.
13. När distributionen är klar väljer du **undernät**och väljer sedan **associera**.
14. Välj **ekrar – 01** för **virtuellt nätverk**.
15. Välj **hopp-SN** för **undernät**.
16. Välj **OK**.

### <a name="test-the-rules"></a>Testa reglerna

Testa nu brand Väggs reglerna för att bekräfta att den fungerar som förväntat.

1. Från Azure Portal granskar du nätverks inställningarna för den virtuella **arbets belastnings-SRV-** datorn och noterar den privata IP-adressen.
2. Anslut ett fjärr skrivbord till den virtuella datorn för att **hoppa över SRV** och logga in. Därifrån öppnar du en fjärr skrivbords anslutning till den privata IP-adressen för **arbets belastningen** .

3. Öppna Internet Explorer och navigera till https://www.microsoft.com.
4. Välj **OK** > **Stäng** i Internet Explorers säkerhets aviseringar.

   Du bör se Microsofts hem sida.

5. Bläddra till https://www.google.com.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brand Väggs reglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om betrodda säkerhets partner](trusted-security-partners.md)
