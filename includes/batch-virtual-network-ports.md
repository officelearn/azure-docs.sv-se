- VNet:et måste vara i samma Azure-**region** och -**prenumeration** som Batch-kontot.

- För pooler som skapats med en virtuell datorkonfiguration, stöds enbart Azure Resource Manager-baserade VNet. För pooler som skapats med en moljntjänstkonfiguration, stöds bara klassiska VNet. 
  
- Om du vill använda en klassisk VNet, `MicrosoftAzureBatch` måste tjänstens huvudnamn ha `Classic Virtual Machine Contributor` rollen rollbaserad åtkomstkontroll (RBAC) för den angivna VNet. För att använda en Azure Resource Manager-baserad VNet måste du ha behörigheter att komma åt VNet och distribuera virtuella datorer i undernätet.

- Det undernät som anges för poolen måste ha tillräckliga otilldelade IP-adresser för det antal virtuella datorer som är mål för poolen. Summan av egenskaperna `targetDedicatedNodes` och `targetLowPriorityNodes` för poolen. Om undernätet inte har tillräckligt med lediga IP-adresser, allokerar poolen datornoderna partiellt och ett storleksändringsfel inträffar. 

- VNet:et måste tillåta kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna. Detta kan verifieras genom att kontrollera om VNet:et har några associerade nätverkssäkerhetsgrupper (NSG:er). Om kommunikation till beräkningsnoderna i det angivna undernätet nekas av en NSG, ställer Batch-tjänsten in status för beräkningsnoderna till **oanvändbara**. 

- Om det angivna VNet har associerade nätverkssäkerhetsgrupper (NSG:er) och/eller en brandvägg, kan du konfigurera de inkommande och utgående portarna som det visas i följande tabeller:


  |    Målportar    |    Källans IP-adress      |   Källport    |    Lägger Batch till nätverkssäkerhetsgrupper?    |    Krävs detta för att VM ska kunna användas?    |    Åtgärd från användaren   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>För pooler som har skapats med konfigurationen av virtuell dator: 29876 och 29877</li><li>För pooler som har skapats med molntjänstkonfigurationen: 10100, 20100 och 30100</li></ul>        |    * eller för ytterligare säkerhet anger du IP-adresser för Batch-tjänsten. Om du vill hämta listan över IP-adresser för Batch-tjänsten kontaktar du Azure-supporten. | * eller 443 |    Ja. Batch lägger till nätverkssäkerhetsgrupper på nivån för de nätverksgränssnitt (NIC) som är kopplade till virtuella datorer. Dessa nätverkssäkerhetsgrupper tillåter endast trafik från Batch-tjänstrollens IP-adresser. Även om du öppnar dessa portar för hela webben blockeras trafiken vid nätverksgränssnittet (NIC). |    Ja  |  Du behöver inte ange en NSG eftersom Batch endast tillåter Batch-IP-adresser. <br /><br /> Men om du anger en NSG måste du se till att dessa portar är öppna för inkommande trafik. <br /><br /> Om du anger * som käll-IP i NSG lägger Batch fortfarande till nätverkssäkerhetsgrupper på nivån för det nätverksgränssnitt (NIC) som är kopplade till virtuella datorer. |
  |    3389 (Windows), 22 (Linux)               |    Användarnas datorer (för felsökning), så att du har fjärråtkomst till den virtuella datorn.    |   *  | Nej                                    |    Nej                    |    Lägg till NSG:er om du vill tillåta fjärråtkomst (RDP/SSH) till den virtuella datorn.   |                                


  |    Utgående portar    |    Mål    |    Lägger Batch till nätverkssäkerhetsgrupper?    |    Krävs detta för att VM ska kunna användas?    |    Åtgärd från användaren    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Azure Storage    |    Nej    |    Ja    |    Om du lägger till en nätverkssäkerhetsgrupp måste du se till att den här porten är öppen för utgående trafik.    |

   Kontrollera också att din Azure Storage-slutpunkt kan matchas av eventuella anpassade DNS-servrar som servar ditt VNET. Mer specifikt, ska URL:er av formen `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` och `<account>.blob.core.windows.net` vara matchningsbara. 

   Om du lägger till en Resource Manager-baserad NSG kan du använda [tjänsttaggar](../articles/virtual-network/security-overview.md#service-tags) för att välja Storage-IP-adresser för den specifika regionen för utgående anslutningar. Observera att Storage-IP-adresserna måste vara samma region som ditt Batch-konto och VNet. Tjänsttaggar är för närvarande i förhandsversion i utvalda Azure-regioner.