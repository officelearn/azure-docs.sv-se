---
title: 'Självstudie: skydda din virtuella hubb med Azure Firewall Manager'
description: I den här självstudien får du lära dig hur du skyddar din virtuella hubb med Azure Firewall Manager med hjälp av Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563666"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Självstudie: skydda din virtuella hubb med Azure Firewall Manager

Med hjälp av Azure Firewall Manager kan du skapa säkra virtuella hubbar för att skydda din moln nätverks trafik till privata IP-adresser, Azure PaaS och Internet. Trafik dirigering till brand väggen är automatiserad, så du behöver inte skapa användardefinierade vägar (UDR).

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

Skapa först ett eker-virtuellt nätverk där du kan placera dina servrar.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Skapa ett eker-virtuellt nätverk och undernät

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk**under **nätverk**.
2. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupp**väljer du **Skapa ny**och skriver **VB-Manager** som namn och väljer **OK**.
2. Som **namn**skriver du **ekrar-01**.
3. För **region**väljer du **(US) USA, östra**.
4. Välj **Nästa: IP-adresser**.
1. Acceptera standardvärdet **10.0.0.0/16**för **adress utrymme**.
3. Under **undernäts namn**väljer du **standard**.
4. Ändra namnet på under nätet till **arbets belastningen – SN**.
5. Skriv **10.0.1.0/24**för **under nätets adress intervall**.
6. Välj **Spara**...

Skapa sedan ett undernät för en hopp Server.

1. Välj **Lägg till undernät**.
4. Skriv **hopp-SN**för **under näts namn**.
5. Skriv **10.0.2.0/24**för **under nätets adress intervall**.
6. Välj **Lägg till**.

Skapa det virtuella nätverket nu.

1. Välj **Granska + skapa**.
2. Välj **Skapa**.

### <a name="create-the-secured-virtual-hub"></a>Skapa den säkra virtuella hubben

Skapa din säkra virtuella hubb med hjälp av brand Väggs hanteraren.

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. I rutan Sök skriver du **Firewall Manager** och väljer **brand Väggs hanteraren**.
3. På sidan **Firewall Manager** väljer du **Visa skyddade virtuella nav**.
4. I **brand Väggs hanteraren | Sidan skyddade virtuella hubbar** väljer du **Skapa ny säker virtuell hubb**.
5. För **resurs grupp**väljer du **VB-Manager**.
7. För **region**väljer du **östra USA**.
1. För namnet på den **säkra virtuella hubben**skriver du **Hub-01**.
2. Skriv **10.1.0.0/16**för **nav adress utrymme**.
3. För det nya vWAN-namnet skriver du **vWAN-01**.
4. Lämna kryss rutan **Inkludera VPN-gateway för att aktivera betrodda säkerhets partner** avmarkerad.
5. Välj **Nästa: Azure-brandvägg**.
6. Godkänn standard inställningen för **Azure Firewall** som **är aktive rad** och välj sedan **Nästa: betrodd säkerhets partner**.
7. Godkänn standardinställningen för **inaktiverade** **betrodda säkerhets partner** och välj **Nästa: granska + skapa**.
8. Välj **Skapa**. Det tar cirka 30 minuter att distribuera.

### <a name="connect-the-hub-and-spoke-vnets"></a>Anslut hubben och eker-virtuella nätverk

Nu kan du peer-koppla hubben och eker-virtuella nätverk.

1. Välj resurs gruppen **VB-Manager** och välj sedan det virtuella WAN **-nätverket Vwan-01** .
2. Under **anslutning**väljer du **virtuella nätverks anslutningar**.
3. Välj **Lägg till anslutning**.
4. För **anslutnings namn**skriver du **hubb-eker**.
5. För **hubbar**väljer du **hubb-01**.
6. För **resurs grupp**väljer du **VB-Manager**.
7. För **virtuellt nätverk**väljer du **eker-01**.
8. Välj **Skapa**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Skapa en brand Väggs princip och skydda hubben

En brand Väggs princip definierar samlingar av regler för att dirigera trafik på ett eller flera skyddade virtuella nav. Du skapar brand Väggs principen och skyddar sedan hubben.

1. I brand Väggs hanteraren väljer du **Visa Azure Firewall-principer**.
2. Välj **Skapa Azure Firewall-princip**.
3. Under **princip information**, för **namn** typ **policy-01** och för **region** väljer du **USA, östra**.
4. Välj **Nästa: regler**.
5. På fliken **regler** väljer du **Lägg till en regel samling**.
6. På sidan **Lägg till regel samling** skriver du **RC-01** som **namn**.
7. För **regel samlings typ**väljer du **program**.
8. För **prioritet**, Skriv **100**.
9. Se till att **regel samlings åtgärden** är **tillåten**.
10. För regel **namn** typen **Allow-MSFT**.
11. För **typ av källa**väljer du **IP-adress**.
12. Som **källa**skriver du **\*** .
13. För **protokoll**, Skriv **http, https**.
14. Se till att **måltypen** är ett **fullständigt domän namn**.
15. För **mål**skriver du ** \* . Microsoft.com**.
16. Välj **Lägg till**.
17. Välj **Nästa: Hot information**.
18. Välj **Nästa: hubbar**.
19. På fliken **hubbar** väljer du **associera virtuella nav**.
20. Välj **hubb – 01** och välj sedan **Lägg till**.
21. Välj **Granska + skapa**.
22. Välj **Skapa**.

Det kan ta ungefär fem minuter eller mer att slutföra.

## <a name="route-traffic-to-your-hub"></a>Dirigera trafik till hubben

Nu måste du se till att nätverks trafiken dirigeras till genom brand väggen.

1. I brand Väggs hanteraren väljer du **skyddade virtuella nav**.
2. Välj **hubb – 01**.
3. Under **Inställningar**väljer du **säkerhets konfiguration**.
4. Under **Internet trafik**väljer du **Azure-brandvägg**.
5. Under **privat trafik**väljer du **Skicka via Azure-brandvägg**.
10. Kontrol lera att **Hub-eker-** anslutningen visar **Internet trafik** som **skyddad**.
11. Välj **Spara**.


## <a name="test-your-firewall"></a>Testa din brand vägg

Om du vill testa brand Väggs reglerna måste du distribuera ett par servrar. Du distribuerar arbets belastning – SRV i under nätet för arbets belastningen – SN för att testa brand Väggs reglerna och för att använda fjärr skrivbord så att du kan ansluta från Internet och sedan ansluta till arbets belastnings-SRV.

### <a name="deploy-the-servers"></a>Distribuera servrarna

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Windows Server 2016 Data Center** i listan **populär** .
3. Ange följande värden för den virtuella datorn:

   |Inställningen  |Värde  |
   |---------|---------|
   |Resursgrupp     |**VB-chef**|
   |Namn på virtuell dator     |**Hopp-SRV**|
   |Region     |**USA USA, östra)**|
   |Administratörens användar namn     |Ange ett användar namn|
   |lösenordsinställning     |Ange ett lösen ord|

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

|Inställningen  |Värde  |
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
4. Välj **OK**  >  **Stäng** i Internet Explorers säkerhets aviseringar.

   Du bör se Microsofts hem sida.

5. Bläddra till https://www.google.com.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brand Väggs reglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om betrodda säkerhets partner](trusted-security-partners.md)
