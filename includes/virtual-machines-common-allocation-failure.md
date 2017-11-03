
Om din Azure problemet inte åtgärdas i den här artikeln, finns det [Azure-forum på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet på dessa forum eller @AzureSupport på Twitter. Du kan också filen en Azure-supportbegäran genom att välja **få support** på den [Azure-supporten](https://azure.microsoft.com/support/options/) plats.

## <a name="general-troubleshooting-steps"></a>Allmänna instruktioner för felsökning
### <a name="troubleshoot-common-allocation-failures-in-the-classic-deployment-model"></a>Felsöka vanliga Tilldelningsfel i den klassiska distributionsmodellen
Dessa steg hjälper dig att lösa många buffertallokeringsfel på virtuella datorer:

* Ändra storlek på den virtuella datorn till en annan VM-storlek.<br>
    Klicka på **Bläddra igenom alla** > **virtuella datorer (klassisk)** > din virtuella dator > **inställningar** > **storlek**. Detaljerade anvisningar finns i [ändra storlek på den virtuella datorn](https://msdn.microsoft.com/library/dn168976.aspx).
* Ta bort alla virtuella datorer från Molntjänsten och skapa virtuella datorer.<br>
    Klicka på **Bläddra igenom alla** > **virtuella datorer (klassisk)** > din virtuella dator > **ta bort**. Klicka på **ny** > **Compute** > [avbildning av virtuell dator].

### <a name="troubleshoot-common-allocation-failures-in-the-azure-resource-manager-deployment-model"></a>Felsöka vanliga Tilldelningsfel i Azure Resource Manager-distributionsmodellen
Dessa steg hjälper dig att lösa många buffertallokeringsfel på virtuella datorer:

* Stoppa (frigöra) alla virtuella datorer i samma tillgänglighetsuppsättning ange och sedan starta om var och en.<br>
    Stoppa: Klicka på **resursgrupper** > resursgruppen > **resurser** > din tillgänglighetsuppsättning > **virtuella datorer** > din virtuella dator >  **Stoppa**.
  
    Efter alla virtuella datorer stoppa markerar du den första virtuella datorn och på **starta**.

## <a name="background-information"></a>Bakgrundsinformation
### <a name="how-allocation-works"></a>Så här fungerar allokering
Servrarna i Azure-Datacenter partitioneras i kluster. Normalt en begäran om minnesallokering görs i flera kluster, men det är möjligt att vissa villkor från begäran om minnesallokering gällande Azure-plattformen för begäran i ett kluster. I den här artikeln ska vi referera till den här som ”fäst på ett kluster”. Bild 1 nedan visar skiftläget för en normal allokering som görs i flera kluster. Figur 2 visar i fallet med en allokering som har fäst på klustret 2 eftersom det är där den befintliga uppsättningen Cloud Service CS_1 eller tillgänglighet körs.
![Allokering av Diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Varför Allokeringsfel inträffa
När en begäran om minnesallokering är fäst på ett kluster, finns en högre risk för att hitta lediga resurser eftersom tillgängliga resurspoolen är mindre inte. Dessutom misslyckas din begäran även om klustret har lediga resurser om din begäran om minnesallokering är fäst på ett kluster men typ av resurs som du har begärt stöds inte av klustret. Bild 3 nedan visar de fall där en fast allokering misslyckas eftersom endast candidate klustret inte har lediga resurser. Bild 4 visar de fall där en fast allokering misslyckas eftersom endast kandidat kluster inte stöder den begärda VM-storleken, även om klustret har lediga resurser.

![Fäst Allokeringsfel](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## <a name="detailed-troubleshoot-steps-specific-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Detaljerad felsökning av steg allokering av specifika scenarier i den klassiska distributionsmodellen
Här följer vanliga scenarier för allokering som gör en begäran om minnesallokering för att fästa. Vi kommer fördjupa dig i varje scenario senare i den här artikeln.

* Ändra storlek på en virtuell dator eller lägga till VM: ar eller rollinstanser i en befintlig molntjänst
* Starta om delvis stoppats (frigjorts) virtuella datorer
* Starta om fullständigt stoppats (frigjorts) virtuella datorer
* Förproduktion/Produktionsdistribution (plattform som en tjänst endast)
* Tillhörighetsgruppen (VM-tjänsten närhet)
* Mappning mellan en gruppbaserad virtuellt nätverk

När du får ett Allokeringsfel se om någon av de scenarier som beskrivs gäller för din fel. Använd allokering felet som returnerades av Azure-plattformen för att identifiera motsvarande scenario. Om din begäran fästs avlägsna fästa begränsningarna för att öppna din förfrågan om att flera kluster, vilket ökar risken för allokering lyckades.

I allmänhet så länge felet inte indikerar ”den begärda VM-storleken inte stöds”, kan du alltid försök vid ett senare tillfälle som tillräckligt med resurser kan ha frigjorts i klustret för att hantera din förfrågan. Om problemet är att den begärda VM-storleken inte stöds, kan du prova en annan VM-storlek. Annars är det enda alternativet att ta bort fästa begränsningen.

Två vanliga scenarier är relaterade till tillhörighetsgrupper. En tillhörighetsgrupp som användes för att ange närhet av virtuella datorer/tjänstinstanser tidigare eller användes för att skapa ett virtuellt nätverk. Tillhörighetsgrupper är inte längre krävs för att skapa ett virtuellt nätverk med introduktionen av regionala virtuella nätverk. Rekommendationen att använda tillhörighetsgrupper för VM-tjänsten närhet har ändrats med för att minska Nätverksfördröjningen i Azure-infrastrukturen.

Diagram över 5 nedan visar taxonomi (fast) fördelning scenarier.
![Fäst allokering taxonomi](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [!NOTE]
> Det fel som anges i varje allokering scenario är en kort form. Referera till den [sträng felsökning](#Error string lookup) för detaljerade felsträngar.
> 
> 

## <a name="allocation-scenario-resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Allokering av scenario: ändra storlek på en virtuell dator eller lägga till VM: ar eller rollinstanser i en befintlig molntjänst
**Fel**

Upgrade_VMSizeNotSupported eller GeneralError

**Orsaken till klustret fästning**

En begäran att ändra storlek på en virtuell dator eller lägga till en virtuell dator eller en rollinstans i en befintlig molntjänst måste göras i det ursprungliga klustret som är värd för befintlig molntjänst. Skapa en ny molntjänst kan Azure-plattformen att hitta ett annat kluster som har lediga resurser eller stöder VM-storlek som du har begärt.

**Lösning**

Om felet är Upgrade_VMSizeNotSupported *, kan du prova en annan VM-storlek. Om du använder en annan VM-storlek inte är ett alternativ, men om det är tillåtet att använda en annan virtuell IP-adress (VIP), skapa en ny molntjänst för att vara värd för den nya virtuella datorn och Lägg till ny molntjänst regionalt virtuellt nätverk med de befintliga virtuella datorerna som kör. Om en befintlig molntjänst inte använder ett regionalt virtuellt nätverk du kan fortfarande skapa ett nytt virtuellt nätverk för den nya Molntjänsten och sedan ansluta din [befintligt virtuellt nätverk till ett nytt virtuellt nätverk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Om felet GeneralError *, är det troligt att typ av resurs (till exempel en viss VM-storlek) stöds av klustret, men klustret saknar lediga resurser för tillfället. Liknar scenariot ovan lägger du till den önskade beräkningsresursen genom att skapa en ny molntjänst (Observera att nya Molntjänsten måste använda en annan VIP) och ett regionalt virtuellt nätverk för att ansluta dina molntjänster.

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Allokering av scenario: starta om delvis stoppats (frigjorts) virtuella datorer
**Fel**

GeneralError *

**Orsaken till klustret fästning**

Flyttningen är delvis innebär att du har stoppats (frigjorts) en eller mer, men inte alla virtuella datorer i en molntjänst. När du stoppar (frigöra) en VM, associerade resurser släpps. Starta om den stoppats (frigjorts) VM är därför en ny begäran om minnesallokering. Starta om virtuella datorer i en delvis frigjord molntjänst är detsamma som att lägga till virtuella datorer i en befintlig molntjänst. Begäran om minnesallokering måste göras i det ursprungliga klustret som är värd för befintlig molntjänst. Skapa en annan molntjänst kan Azure-plattformen att hitta ett annat kluster som har fri resurs eller stöder VM-storlek som du har begärt.

**Lösning**

Om det går att använda en annan VIP, ta bort de stoppats (frigjorts) virtuella datorerna (men behålla de associera diskarna) och lägga till de virtuella datorerna tillbaka via en annan molntjänst. Använd ett regionalt virtuellt nätverk för att ansluta dina molntjänster:

* Om en befintlig molntjänst använder ett regionalt virtuellt nätverk kan bara lägga till ny molntjänst till samma virtuella nätverk.
* Om en befintlig molntjänst inte använder ett regionalt virtuellt nätverk, skapa ett nytt virtuellt nätverk för den nya Molntjänsten och sedan [ansluta befintliga virtuella nätverket till ett nytt virtuellt nätverk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="allocation-scenario-restart-fully-stopped-deallocated-vms"></a>Allokering av scenario: starta om fullständigt stoppats (frigjorts) virtuella datorer
**Fel**

GeneralError *

**Orsaken till klustret fästning**

Fullständig flyttningen innebär att du har stoppats frigjorts () alla virtuella datorer från en tjänst i molnet. Av allokeringsbegäranden att starta om dessa virtuella datorer måste göras i det ursprungliga klustret som är värd för Molntjänsten. Skapa en ny molntjänst kan Azure-plattformen att hitta ett annat kluster som har lediga resurser eller stöder VM-storlek som du har begärt.

**Lösning**

Om det går att använda en annan VIP, ta bort de ursprungliga stoppats (frigjorts) virtuella datorerna (men behåll de associera diskarna) och ta bort motsvarande Molntjänsten (associerade beräkningsresurser har redan getts ut när du stoppats (frigjorts) de virtuella datorerna). Skapa en ny molntjänst för att lägga till de virtuella datorerna tillbaka.

## <a name="allocation-scenario-stagingproduction-deployments-platform-as-a-service-only"></a>Allokering av scenario: mellanlagring/Produktionsdistribution (plattform som en tjänst endast)
**Fel**

New_General * eller * New_VMSizeNotSupported

**Orsaken till klustret fästning**

Fristående distributionen och Produktionsdistribution av en tjänst i molnet finns i samma kluster. När du lägger till den andra distributionen görs motsvarande allokeringsbegäran i samma kluster som är värd för den första distributionen.

**Lösning**

Ta bort den första distributionen och ursprungligt Molntjänsten och distribuera Molntjänsten. Den här åtgärden kan hamna den första distributionen i ett kluster som har tillräckligt med lediga resurser för att få plats båda distributionerna eller i ett kluster som stöder VM-storlekar som du har begärt.

## <a name="allocation-scenario-affinity-group-vmservice-proximity"></a>Allokering av scenario: tillhörighetsgrupp (VM-tjänsten närhet)
**Fel**

New_General * eller * New_VMSizeNotSupported

**Orsaken till klustret fästning**

Att beräkna någon resurs som tilldelats en tillhörighetsgrupp som är kopplat till ett kluster. Nya beräkningsresurser begäranden i den tillhörighetsgrupp görs i samma kluster där de befintliga resurserna finns. Detta är SANT om nya resurser som har skapats via en ny molntjänst eller en befintlig molntjänst.

**Lösning**

Om en tillhörighetsgrupp inte är nödvändigt, inte använda en tillhörighetsgrupp, eller gruppera dina beräkningsresurser i flera tillhörighetsgrupper.

## <a name="allocation-scenario-affinity-group-based-virtual-network"></a>Allokering av scenario: tillhörighet-grupp-baserade virtuella nätverk
**Fel**

New_General * eller * New_VMSizeNotSupported

**Orsaken till klustret fästning**

Innan regionala virtuella nätverk har introducerats var du tvungen att koppla ett virtuellt nätverk med en tillhörighetsgrupp. Därför kan beräkna resurser placeras i en tillhörighetsgrupp som är bundna av samma begränsningar som beskrivs i den ”allokering scenario: tillhörighetsgrupp (VM-tjänsten närhet)” ovan. Beräkningsresurserna är knutna till ett kluster.

**Lösning**

Om du inte behöver en tillhörighetsgrupp, skapa ett nytt regionalt virtuellt nätverk för de nya resurser som du vill lägga till, och sedan [ansluta befintliga virtuella nätverket till ett nytt virtuellt nätverk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Du kan också [migrera tillhörighet-grupp-baserat virtuellt nätverk till ett regionalt virtuellt nätverk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), och Lägg sedan till önskade resurser.

## <a name="detailed-troubleshooting-steps-specific-allocation-failure-scenarios-in-the-azure-resource-manager-deployment-model"></a>Detaljerad felsökning steg allokering av specifika scenarier i Azure Resource Manager-distributionsmodellen
Här följer vanliga scenarier för allokering som gör en begäran om minnesallokering för att fästa. Vi kommer fördjupa dig i varje scenario senare i den här artikeln.

* Ändra storlek på en virtuell dator eller lägga till VM: ar eller rollinstanser i en befintlig molntjänst
* Starta om delvis stoppats (frigjorts) virtuella datorer
* Starta om fullständigt stoppats (frigjorts) virtuella datorer

När du får ett Allokeringsfel se om någon av de scenarier som beskrivs gäller för din fel. Använd allokering felet som returnerades av Azure-plattformen för att identifiera motsvarande scenario. Om din begäran är fäst på ett befintligt kluster, kan du ta bort några av fästa begränsningarna för att öppna din förfrågan om att flera kluster, vilket ökar risken för allokering lyckades.

I allmänhet så länge felet inte indikerar ”den begärda VM-storleken inte stöds”, kan du alltid försök vid ett senare tillfälle som tillräckligt med resurser kan ha frigjorts i klustret för att hantera din förfrågan. Om problemet är att den begärda VM-storleken inte stöds, se nedan för lösningar.

## <a name="allocation-scenario-resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Allokering av scenario: ändra storlek på en virtuell dator eller Lägg till virtuella datorer i en befintlig tillgänglighetsuppsättning
**Fel**

Upgrade_VMSizeNotSupported * eller * GeneralError

**Orsaken till klustret fästning**

En begäran att ändra storlek på en virtuell dator eller lägga till en virtuell dator i en befintlig tillgänglighetsuppsättning måste göras i det ursprungliga klustret som är värd för den befintliga tillgänglighetsuppsättningen. Skapa en ny tillgänglighetsuppsättning kan Azure-plattformen att hitta ett annat kluster som har lediga resurser eller stöder VM-storlek som du har begärt.

**Lösning**

Om felet är Upgrade_VMSizeNotSupported *, kan du prova en annan VM-storlek. Om med en annan VM-storlek inte är ett alternativ, stoppas alla virtuella datorer i tillgänglighetsuppsättningen. Du kan sedan ändra storleken på den virtuella datorn som allokeras till den virtuella datorn till ett kluster som har stöd för den önskade Virtuella datorstorleken.

Om felet GeneralError *, är det troligt att typ av resurs (till exempel en viss VM-storlek) stöds av klustret, men klustret saknar lediga resurser för tillfället. Om den virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning, kan du skapa en ny virtuell dator i en annan tillgänglighetsuppsättning (i samma region). Den här nya virtuella datorn kan sedan läggas till samma virtuella nätverk.  

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Allokering av scenario: starta om delvis stoppats (frigjorts) virtuella datorer
**Fel**

GeneralError *

**Orsaken till klustret fästning**

Flyttningen är delvis innebär att du har stoppats (frigjorts) en eller flera, men inte alla, virtuella datorer i en tillgänglighetsgrupp. När du stoppar (frigöra) en VM, associerade resurser släpps. Starta om den stoppats (frigjorts) VM är därför en ny begäran om minnesallokering. Starta om virtuella datorer i en delvis frigjord tillgänglighetsuppsättning är detsamma som att lägga till virtuella datorer i en befintlig tillgänglighetsuppsättning. Begäran om minnesallokering måste göras i det ursprungliga klustret som är värd för den befintliga tillgänglighetsuppsättningen.

**Lösning**

Stoppa alla virtuella datorer i tillgänglighetsuppsättning innan du startar om det första. Detta garanterar att ett nytt försök för allokering körs och att ett nytt kluster kan väljas som har tillgänglig kapacitet.

## <a name="allocation-scenario-restart-fully-stopped-deallocated"></a>Allokering av scenario: starta om fullständigt stoppats (frigjorts)
**Fel**

GeneralError *

**Orsaken till klustret fästning**

Fullständig flyttningen innebär att du har stoppats frigjorts () alla virtuella datorer i en tillgänglighetsuppsättning. Begäran om minnesallokering att starta om dessa virtuella datorer gäller alla kluster som har stöd för önskad storlek.

**Lösning**

Välj en ny VM-storlek för att allokera. Försök igen senare om det inte fungerar.

<a name="Error string lookup"></a>

## <a name="error-string-lookup"></a>Felsökning av sträng
**New_VMSizeNotSupported***

”VM storlek (eller kombinationen av Virtuella datorstorlekar) krävs av den här distributionen kan inte tillhandahållas pga distribution begränsningar för begäran. Om möjligt försök lugnt begränsningarna, t.ex virtuella nätverksbindningar, distribution till en värdbaserad tjänst med ingen distribution och till en annan tillhörighetsgrupp eller utan någon tillhörighetsgrupp, eller försök att distribuera till en annan region ”.

**New_General***

”Gick inte att allokera; Det går inte att uppfylla begränsningarna i förfrågan. Den begärda nya tjänstdistributionen är bunden till en tillhörighetsgrupp eller den riktar sig till ett virtuellt nätverk eller det finns en befintlig distribution under den här värdbaserade tjänsten. Dessa omständigheter begränsar den nya distributionen till specifika Azure-resurser. Försök igen senare eller försök att minska den Virtuella datorstorleken eller antalet rollinstanser. Alternativt, om möjligt ta bort de tidigare nämnda begränsningarna eller försök att distribuera till en annan region ”.

**Upgrade_VMSizeNotSupported***

”Det gick inte att uppgradera distributionen. Den begärda VM-storleken XXX får inte vara tillgängliga i de resurser som stödjer den befintliga distributionen. Försök igen senare, försök med en annan VM-storlek eller ett mindre antal rollinstanser eller skapa en distribution under en tom värdtjänst med en ny tillhörighetsgrupp eller utan tillhörighetsgruppsbindning ”.

**GeneralError***

”Ett internt fel påträffades av servern. Försök att utföra begäran ”. Eller ”det gick inte att skapa en allokering för tjänsten”.

