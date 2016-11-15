## <a name="about-records"></a>Om poster

Varje DNS-post har ett namn och en typ. Posterna är indelade i olika typer beroende på den data de innehåller. Den vanligaste typen är en A-post som mappar ett namn till en IPv4-adress. En annan typ är en MX-post som mappar ett namn till en e-postserver.

Azure DNS stöder alla vanliga DNS-posttyper, inklusive A, AAAA, CNAME, MX, NS, PTR, SOA, SRV och TXT. Tänk på följande:

* SOA-postuppsättningar skapas automatiskt med varje zon. De kan inte skapas separat.
* SPF-poster måste skapas med hjälp av TXT-posttypen. Mer information finns på [den här sidan](http://tools.ietf.org/html/rfc7208#section-3.1).

DNS-poster i Azure anges med relativa namn. Ett fullständigt kvalificerat domännamn (FQDN), inkluderar zonnamnet, medan ett relativt namn inte gör det. Det relativa postnamnet www i zonen contoso.com, skapar till exempel det fullständigt kvalificerade postnamnet www.contoso.com.

## <a name="about-record-sets"></a>Om postuppsättningar

Ibland måste du skapa fler än en DNS-post av ett visst namn och typ. Anta exempelvis att webbplatsen www.contoso.com finns på två olika IP-adresser. Webbplatsen kräver då två olika A-poster, en för varje IP-adress. Här är ett exempel på en postuppsättning:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS hanterar DNS-poster med hjälp av postuppsättningar. En postuppsättning är en samling DNS-poster i en zon som har samma namn och är av samma typ. De flesta postuppsättningar innehåller en enda post, men exempel som det här, där en postuppsättning innehåller fler än en post, är inte ovanliga.

SOA- och CNAME-postuppsättningar är undantag. DNS-standarden tillåter inte flera poster med samma namn för de här typerna.

Livslängd, eller TTL, anger hur länge varje post ska cachelagras av klienter innan den efterfrågas på nytt. I det här exemplet är TTL 3600 sekunder eller 1 timme. TTL anges för postuppsättningen, inte för varje post, så samma värde används för alla poster inom den postuppsättningen.

#### <a name="wildcard-record-sets"></a>Postuppsättningar med jokertecken

Azure DNS stöder [poster med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record). De returneras för alla frågor med ett matchande namn (om det inte finns en bättre matchning från en postuppsättning utan jokertecken). Postuppsättningar med jokertecken stöds för alla postuppsättningar utom NS och SOA.

Om du vill skapa en postuppsättning med jokertecken, använder du postuppsättningsnamnet "\*". Eller använd ett namn med etiketten \*, till exempel \*.foo.

#### <a name="cname-record-sets"></a>CNAME-postuppsättningar

CNAME-postuppsättningar kan inte samexistera med andra postuppsättningar med samma namn. Du kan till exempel inte skapa en CNAME-postuppsättning med det relativa namnet www och en A-post med det relativa namnet www på samma gång. Eftersom zonens apex (namn = ‘@’)) alltid innehåller NS- och SOA-postuppsättningar som skapades när zonen skapades, du kan inte skapa en CNAME-postuppsättning på zonens apex. Den här begränsningarna kommer sig av DNS-standarderna och är inte begränsningar i Azure DNS.


<!--HONumber=Nov16_HO2-->


