


## Fördelarna med att integrera Compute, Network och Storage under Azure Resource Manager-distributionsmodellen

Azure Resource Manager-distributionsmodellen ger dig möjlighet att enkelt utnyttja fördefinierade programmallar eller att skapa en programmall för att distribuera och hantera beräknings-, nätverks- och lagringsresurser på Azure. I det här avsnittet går vi igenom fördelarna med att distribuera resurser via Azure Resource Manager-distributionsmodellen .

-   Komplexitet på ett enkelt sätt – Skapa, integrera och samarbeta med komplicerade program som kan sträcka sig över hela skalan av Azure-resurser (som Websites, SQL Databases, Virtual Machines eller Virtual Networks) från en delad mallfil
-   Flexibiliteten med upprepade distributioner för utveckling, devOps och systemadministratörer när du använder dig av samma mallfil
-   Djupgående integrering av VM-tillägg (anpassade skript, DSC, Chef, Puppet, osv.) med Azure Resource Manager i en mallfil, låter dig enkelt styra installationskonfigurationer i virtuella datorer
-   Definiera taggar och faktureringsspridning av taggarna för beräknings-, nätverks- och lagringsresurser
-   Enkel och exakt åtkomsthantering av organisationsresurser med Azure rollbaserad åtkomstkontroll (RBAC)
-   Förenklad uppgradering/uppdatering genom att ändra den ursprungliga mallen och omdistribuera den


## Förbättringar i beräknings-, nätverks-, och lagrings-API:er i Azure Resource Manager

Utöver de fördelar som räknats upp ovan, finns det en del markanta prestandaförbättringar i de API:er som getts ut:

-   Stöd för massiv, parallell distribution av Virtual Machines
-   Stöd för 3 feldomäner i tillgänglighetsuppsättningar
-   Förbättrade anpassade skripttillägg som tillåter specifikation av skript från valfri, offentligt tillgänglig anpassad URL
- Integrering av Virtual Machines med Azure Key Vault för lagring med hög säkerhet och privat distribution av hemligheter från [FIPS-validerade](http://wikipedia.org/wiki/FIPS_140-2) [maskinvarusäkerhets-moduler](http://wikipedia.org/wiki/Hardware_security_module)
-   Innehåller de grundläggande byggstenarna för nätverk via API:er för att låta kunder skapa komplicerade program som inkluderar nätverksgränssnitt, belastningsutjämnare och virtuella nätverk
-   Nätverksgränssnitt som ett nytt objekt gör att komplicerade nätverkskonfigurationer kan upprätthållas och återanvändas för Virtual Machines
-   Belastningsutjämnare som en förstaklass-resurs tillåter IP-adresstilldelning
-   Virtual Network-API:er på detaljnivå låter dig förenkla hanteringen av individuella virtuella nätverk

## Konceptuella skillnader vid införandet av nya API:er

Det här avsnittet går igenom några av de viktigaste konceptuella skillnaderna mellan de XML-baserade API:er som finns idag och JSON-baserade API:er som finns tillgängliga via Azure Resource Manager för beräkning, nätverk och lagring.

 Objekt | Azure Service Management (XML-baserad)    | Beräknings-, nätverks- och lagrings-providers (JSON-baserade)
 ---|---|---
| Molntjänst för Virtual Machines |  Molntjänst var en behållare för virtuella datorer som krävde tillgänglighet från plattformen och belastningsutjämning. | Molntjänst är inte längre ett objekt som krävs för att skapa en virtuell dator med den nya modellen. |
| Tillgänglighetsuppsättningar | Tillgänglighet till plattformen indikerades genom att konfigurera samma "AvailabilitySetName" på Virtual Machines. Det maximala antalet feldomäner var 2. | Tillgänglighetsuppsättning är en resurs som exponeras av Microsoft.Compute-providern. Virtuella datorer som kräver hög tillgänglighet måste inkluderas i tillgänglighetsuppsättningen. Det maximala antalet feldomäner är nu 3. |
| Tillhörighetsgrupper | Tillhörighetsgrupper krävdes för virtuella nätverk. Men med introduktionen av regionala virtuella nätverk, krävs det inte längre. |Lite enklare sagt så existerar inte konceptet tillhörighetsgrupper i de API:er som exponeras via Azure Resource Manager. |
| Belastningsutjämning    | Skapandet av en molntjänst ger en implicit belastningsutjämnare för de virtuella datorer som distribueras. | Load Balancer är en resurs som exponeras av Microsoft.Network-providern. Det primära nätverksgränssnittet för Virtual Machines som måste belastningsutjämnas ska referera till belastningsutjämnaren. Belastningsutjämnare kan vara interna eller externa. [Läs mer.](../articles/resource-groups-networking.md) |
|Virtuell IP-adress | Cloud Services får en standard-VIP (virtuell IP-adress) när en VM läggs till i en molntjänst. Den virtuella IP-adressen är den adress som är associerad med den implicita belastningsutjämnaren.   | En offentlig IP-adress är en resurs som exponeras av Microsoft.Network-providern. Den offentliga IP-adressen kan vara statisk (reserverad) eller dynamisk. Dynamiska offentliga IP-adresser kan tilldelas till en belastningsutjämnare. Offentliga IP-adresser kan skyddas med hjälp av säkerhetsgrupper. |
|Reserverad IP-adress|   Du kan reservera en IP-adress i Azure och koppla den till en molntjänst för att kontrollera att IP-adressen är fäst.   | En offentlig IP-adress kan skapas i "Statiskt" läge och har då samma funktion som en "reserverad IP-adress". Statiska offentliga IP-adresser kan bara tilldelas en belastningsutjämnare för tillfället. |
|Offentlig IP-adress (PIP) per VM | Offentliga IP-adresser kan också direkt kopplas till en VM. | En offentlig IP-adress är en resurs som exponeras av Microsoft.Network-providern. Den offentliga IP-adressen kan vara statisk (reserverad) eller dynamisk. Dock kan endast dynamiska offentliga IP-adresser tilldelas ett nätverksgränssnitt för att få en offentlig IP-adress per VM för tillfället. |
|Slutpunkter| Inkommande slutpunkter behöver konfigureras på en virtuell dator för att öppna upp anslutningar på vissa portar. Ett vanligt sätt att ansluta till virtuella datorer är genom att konfigurera inkommande slutpunkter. | Inkommande NAT-regler kan konfigureras på belastningsutjämnare för att uppnå samma funktion som vid aktivering av inkommande slutpunkter på specifika portar för att ansluta till VM:ar. |
|DNS-namn| En molntjänst får ett implicit, globalt-unikt DNS-namn. Till exempel: `mycoffeeshop.cloudapp.net`. | DNS-namn är valfria parametrar som kan anges för en offentlig IP-adressresurs. FQDN:en kommer att ha följande format: `<domainlabel>.<region>.cloudapp.azure.com`. |
|Nätverksgränssnitt | Primära och sekundära nätverksgränssnitt och dess egenskaper har definierats som nätverkskonfigurationen för en virtuell dator. | Nätverksgränssnittet är en resurs som exponeras av Microsoft.Network-providern. Livscykeln för nätverksgränssnittet är inte kopplat till en virtuell dator. |

## Komma igång med Azure-mallar för virtuella datorer

Du kan komma igång med Azure-mallar genom att utnyttja de olika verktyg som vi har tagit fram för att utveckla och distribuera på plattformen.

### Azure-portalen

Azure-portalen fortsätter att erbjuda både alternativet att distribuera virtuella datorer med den klassiska distributionsmodellen och virtuella datorer med Resource Manager-modellen samtidigt. Azure-portalen tillåter även anpassade mall-distributioner.

### Azure PowerShell

Azure PowerShell kommer att ha två distributionslägen: **AzureServiceManagement**-läge och **AzureResourceManager**-läge.  AzureResourceManager-läget innehåller nu också cmdletar för att hantera virtuella datorer, virtuella nätverk och lagringskonton. Du kan läsa mer om det [här](../articles/powershell-azure-resource-manager.md).

### Azure CLI

Azure-kommandoradsgränssnittet (Azure CLI) kommer att ha två distributionslägen: **AzureServiceManagement**-läge och **AzureResourceManager**-läge. AzureResourceManager-läget innehåller nu också kommandon för att hantera virtuella datorer, virtuella nätverk och lagringskonton. Du kan läsa mer om det [här](../articles/xplat-cli-azure-resource-manager.md).

### Visual Studio

Med den senaste utgåvan av Azure SDK för Visual Studio kan du skapa och distribuera virtuella datorer och komplexa program direkt från Visual Studio. Visual Studio låter dig distribuera från en förskapad lista med mallar eller starta med en helt tom mall.

### REST-API:er

Du hittar detaljerad dokumentation för REST-API:er för beräknings-, nätverks- och lagringsresurs-providers [här](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Vanliga frågor och svar

**Kan jag skapa en virtuell dator med nya Azure Resource Manager för distribution i ett virtuellt nätverk eller lagringskonto som skapats med Azure Service Management-API:er?**

Detta stöds inte för närvarande. Du kan inte använda de nya Azure Resource Manager-API:erna för att distribuera virtuella datorer till ett virtuellt nätverk som skapades med Service Management-API:er.

**Kan jag skapa en virtuell dator med nya Azure Resource Manager-API:er från en användaravbildning som skapades med Azure Service Management-API:er?**

Detta stöds inte för närvarande. Du kan dock kopiera VHD-filerna från ett lagringskonto som skapades med hjälp av Service Management API:er och kopiera dem till ett nytt konto som skapats med de nya Azure Resource Manager-API:erna.

**Hur påverkas kvoten för min prenumeration?**

Kvoterna för virtuella datorer, virtuella nätverk och lagringskonton som skapats med de nya Azure Resource Manager-API:erna är åtskilda från kvoterna som du har för närvarande. Varje prenumeration får nya kvoter att skapa resurser med de nya API:erna. Du kan läsa mer om de utökade kvoterna [här](../articles/azure-subscription-service-limits.md).

**Kan jag fortsätta att använda mina automatiserade skript för etablering av virtuella datorer, virtuella nätverk, lagringskonton osv. via de nya Azure Resource Manager-API:erna?**

All automatisering och skript som du skapat kommer att fortsätta fungera för befintliga virtuella datorer och virtuella nätverk som skapats i Azure Service Management-läge. Skripten måste dock uppdateras för att använda det nya schemat för att skapa samma resurser i det nya Azure Resource Manager-läget.

**Var hittar jag exempel på Azure Resource Manager-mallar?**

Du hittar en omfattande uppsättning startmallar på [Azure Resource Manager-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).


<!--HONumber=Sep16_HO3-->


