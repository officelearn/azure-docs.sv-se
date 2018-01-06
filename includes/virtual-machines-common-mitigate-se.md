
 
Senaste avslöjande av en [ny klass för CPU-säkerhetsrisker](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) spekulativ exekvering sida-kanal attacker som kallas har resulterat i frågor från kunder som vill ha mer tydlighets skull. 

 
## <a name="azure-infrastructure"></a>Azure-infrastrukturen

Problem som beskrivs i säkerhetsproblem avslöjande kan användas för att kringgå en hypervisor-gräns och avslöjas av minne mellan två virtuella datorer som flera värdar. Som rapporteras i en tidigare [blogginlägget](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/), Azure har tillämpat åtgärder för att skydda kunder mot den här säkerhetsrisken.  Microsoft rekommenderar alltid kunder att installera rekommenderade säkerhetsmetoder för dina VM-avbildningar som exempel för att installera alla uppdateringar från deras leverantören för operativsystemet.

## <a name="paas-services-on-azure"></a>PaaS-tjänster på Azure
Azure PaaS erbjudanden har ändringar som distribueras som standard. Ingen åtgärd krävs av kunder. Azure Cloud Services undantag finns nedan.  


## <a name="azure-cloud-services"></a>Azure Cloud Services

[Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) automatisk uppdatering aktiveras automatiskt för att ta emot en version av det Gästoperativsystem som innehåller åtgärder för dessa säkerhetsrisker. 

Följande Gästoperativsystem versioner är uppdateringar med skydd mot spekulativ exekvering sida kanal säkerhetsproblem:

* WA-GUEST-OS-5.15_201801-01
* WA-GUEST-OS-4.50_201801-01


Ett litet antal kunder som använder Azure Cloud Services som värd för icke betrodd kod bör också aktivera [Kernel virtuell adress skuggning](#enabling-kernel-virtual-address-shadowing-on-windows-server) förutom att uppdatera gäst-OS. Detta ger ytterligare skydd mot spekulativ exekvering sida-kanal säkerhetsproblem. Detta kan åstadkommas via en startaktivitet. Mer information om vilka kunder och Användningsscenarier kräver den här funktionen och hur du aktiverar den, finns nedan.


## <a name="azure-virtual-machines-windows--linux"></a>Virtuella Azure-datorer (Windows och Linux)

Microsoft rekommenderar alltid kunder att installera alla uppdateringar. 

Insamling av säkerhet i januari 2018 innehåller korrigeringar för dessa problem med mera. Kontrollera att du kör ett program som stöds antivirusprogram innan du installerar uppdateringar av operativsystem. Kontakta leverantören av antivirusprogrammet information om kompatibilitet. 

För adress spekulativ exekvering säkerhetsrisker, uppdateringar till Linux kernel krävs och kan hämtas från leverantören av distributionsplatsen när det är tillgängligt. 

Ett litet antal kunder som använder Azure Virtual Machines (Windows) som värd för icke betrodd kod bör också aktivera [Kernel virtuell adress skuggning](#enabling-kernel-virtual-address-shadowing-on-windows-server) som ger ytterligare skydd mot spekulativ exekvering sida-kanal säkerhetsproblem.  Mer information om vilka kunder och användning scenarier som kräver den här funktionen och hur du aktiverar det finns nedan.


## <a name="enabling-kernel-virtual-address-shadowing-on-windows-server"></a>Aktivera virtuell kerneladress skuggning på Windows Server

Kunder som använder Windows Server för att köra icke betrodd kod ska aktivera en funktion som kallas Kernel virtuell adress skuggning som ger skydd för datorer där icke betrodd kod körs med låg behörighet.

Detta kompletterande skydd kan påverka prestanda och är inaktiverad som standard. Kernel virtuell adress skuggning skyddar mot avslöjande av information om processen att processen och kernel-process.

Din virtuella datorer, molntjänster eller lokala servrar är i fara om de indelas i följande kategorier:

* Azure kapslade virtualisering Hyper-V-värdar
* Värdar (RDSH) för Fjärrskrivbordstjänster
* Virtuella datorer eller molntjänster med icke betrodd kod som behållare eller obetrodda tillägg för databasen, ej betrodda webbinnehåll eller arbetsbelastningar som kör kod som kommer från externa källor.

Ett exempel på ett scenario där Kernel virtuell adress skuggning krävs: 

|     |
|-----|
|En virtuell Azure-dator kör en tjänst där opålitliga användare kan skicka JavaScript-kod som körs med begränsad behörighet. Det finns en mycket Privilegierade process som innehåller hemlig information som inte ska vara tillgänglig för användare på samma VM. I det här fallet är det nödvändigt att ge skydd mot avslöjande mellan två att Kernel virtuell adress skuggning.|
|     | 

Specifika anvisningar om hur du aktiverar Kernel virtuell adress skuggning är tillgängliga via [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution).


> [!NOTE]
> Vid tidpunkten för publikationen är Kernel virtuell adress skuggning endast tillgängligt i Windows Server 2016, Windows Server 2012 R2 och Windows Server 2008 R2.  
>
>

Kontakta med leverantören av din operativsystem för uppdateringar och instruktioner om du använder en Linux-Server.

## <a name="branch-target-injection-mitigation-support-microcode"></a>Branch mål Injection minskning Support (mikrokod)

Kunder som använder verktyg som identifierar förekomsten av mikrokod-baserade åtgärder rapporterar att Azure är okorrigerade. Detta är felaktigt. Vid tidpunkten för den här publikationen exponeras inte gren mål Injection minskning stöd från Azure hypervisor-programmet till Azure-datorer eller Azure Cloud Services. Detta innebär att virtuella datorer inte känner till förekomsten av mikrokod och det går inte att använda dess förhöjd instruktion. Detta innebär inte att Azure är sårbar för attacker för cross-VM spekulativ exekvering sida kanal.
 
När vi arbetar med branschpartners bli flera uppdateringar tillgängliga.

## <a name="next-steps"></a>Nästa steg

Läs mer i [skydda Azure-kunder från CPU säkerhetsproblem](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).