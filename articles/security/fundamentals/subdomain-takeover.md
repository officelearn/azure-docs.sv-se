---
title: Förhindra att under domäner översätts med Azure DNS Ali Aset-poster och Azure App Service anpassad domän verifiering
description: Lär dig hur du undviker det vanliga hotet om hög allvarlighets grad för under domän överbelastning
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: 4e5969b4c3a42fc8a2c4b1cd537c22a4422ca131
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85269093"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Förhindra Dangling DNS-poster och Undvik under domän övertag Ande

I den här artikeln beskrivs det vanliga säkerhetshot för under domän Övertagning och de steg du kan vidta för att undvika det.


## <a name="what-is-subdomain-takeover"></a>Vad är under domän överköps?

Under domänens övertag Ande är ett vanligt hot mot hög allvarlighets grad för organisationer som regelbundet skapar och tar bort många resurser. En under domän överköps kan uppstå när du har en DNS-post som pekar på en deetablerad Azure-resurs. Sådana DNS-poster kallas även "Dangling DNS"-poster. CNAME-poster är särskilt sårbara för det här hotet.

Ett vanligt scenario för en under domän överköps:

1. En webbplats skapas. 

    I det här exemplet `app-contogreat-dev-001.azurewebsites.net` .

1. En CNAME-post läggs till i DNS som pekar på webbplatsen. 

    I det här exemplet skapades följande egna namn: `greatapp.contoso.com` .

1. Efter några månader behövs inte längre platsen, så den tas bort **utan att** motsvarande DNS-post tas bort. 

    Posten CNAME DNS är nu "Dangling".

1. Nästan omedelbart efter att webbplatsen har tagits bort, identifierar en hot aktör den saknade platsen och skapar en egen webbplats på `app-contogreat-dev-001.azurewebsites.net` .

    Nu är trafiken som är avsedd för `greatapp.contoso.com` att gå till hot skådespelarens Azure-webbplats och hot aktören kontroll över det innehåll som visas. 

    Dangling DNS utnyttjades och Contosos under domän "GreatApp" har varit ett skadelidande för under domän övertag Ande. 

![Under domän överköps från en avetablerad webbplats](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-dangling-dns-records"></a>Riskerna med Dangling DNS-poster

När en DNS-post pekar på en resurs som inte är tillgänglig, bör posten tas bort från din DNS-zon. Om den inte har tagits bort är det en "Dangling DNS"-post och en säkerhets risk.

Risken för organisationen är att den gör det möjligt för en hot aktör att ta kontroll över det associerade DNS-namnet som värd för en skadlig webbplats eller tjänst. Den här skadliga webbplatsen i organisationens under domän kan resultera i följande:

- **Förlust av kontroll över innehållet i under domänen** -negativ press om organisationens oförmåga att skydda dess innehåll, samt varumärkes skada och förtroende förlust.

- **Cookie-fångst från misstänkta besökare** – det är vanligt att webbappar exponerar sessionscookies till under domäner (*. contoso.com), vilket innebär att alla under domäner kan komma åt dem. Hot aktörer kan använda under domän uppköp för att bygga en äkta utseende sida, lura obehöriga användare att besöka den och skörda sina cookies (även säkra cookies). En vanlig felbegrepp är att använda SSL-certifikat för att skydda din webbplats, och dina användares cookies, från en övertag Ande. En hot aktör kan dock använda den kapade under domänen som ska användas för och ta emot ett giltigt SSL-certifikat. Detta ger dem åtkomst till säkra cookies och kan öka den uppfattade giltighet för den skadliga webbplatsen ytterligare.

- **Phishing-kampanjer** – autentiska under domäner kan användas i nät fiske kampanjer. Detta gäller för skadliga webbplatser och även för MX-poster som gör det möjligt för hot aktör att ta emot e-post som är adresserade till en legitim under domän till ett säkert märke.

- **Ytterligare risker** – eskalera till andra klassiska attacker som XSS, CSRF, CORS bypass och mer.



## <a name="preventing-dangling-dns-entries"></a>Förhindra Dangling DNS-poster

Att se till att din organisation har implementerat processer för att förhindra Dangling DNS-poster och den resulterande under domänens övertag Ande är en viktig del av ditt säkerhets program.

De förebyggande åtgärder som är tillgängliga för dig idag visas nedan.


### <a name="use-azure-dns-alias-records"></a>Använd Azure DNS Ali Aset poster

Genom att nära koppla livs cykeln för en DNS-post med en Azure-resurs kan Azure DNS [Ali Asets poster](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) -funktionen förhindra Dangling-referenser. Anta till exempel att du har en DNS-post som är kvalificerad som en aliasresurspost som pekar på en offentlig IP-adress eller en Traffic Manager-profil. Om du tar bort de underliggande resurserna blir DNS-Ali-posten en tom post uppsättning. Den borttagna resursen är inte längre referenser till den. Det är viktigt att Observera att det finns gränser för vad du kan skydda med Ali Aset. I dag är listan begränsad till:

- Azure Front Door
- Traffic Manager-profiler
- Slut punkter för Azure Content Delivery Network (CDN)
- Offentliga IP-adresser

Om du har resurser som kan skyddas från under domän överköps med Ali Aset, rekommenderar vi att du gör så trots de begränsade tjänst erbjudandena idag.

[Lär dig mer](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) om funktionerna i Azure DNSs Ali Asets poster.



### <a name="use-azure-app-services-custom-domain-verification"></a>Använd Azure App Service anpassade domän verifieringen

När du skapar DNS-poster för Azure App Service skapar du en asuid. under domän TXT-post med domän verifierings-ID. Om det finns en sådan TXT-post kan ingen annan Azure-prenumeration verifiera den anpassade domänen som tar den över. 

Dessa poster hindrar inte någon från att skapa Azure App Service med samma namn som finns i din CNAME-post, men de kan inte ta emot trafik eller kontrol lera innehållet eftersom de inte kan bevisa att domän namnet är ägarskap.

[Läs mer](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) om hur du mappar ett befintligt anpassat DNS-namn till Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Bygg och automatisera processer för att minimera hotet

Det är ofta upp till utvecklare och drift team att köra rensnings processer för att undvika Dangling DNS-hot. Med hjälp av metoderna nedan kan du se till att din organisation inte har drabbats av det här hotet. 

- **Skapa procedurer för förebyggande åtgärder:**

    - Lär dina programutvecklare att omdirigera adresser när de tar bort resurser.

    - Lägg till "ta bort DNS-post" i listan över nödvändiga kontroller vid inaktive ring av en tjänst.

    - Lägg till [borttagnings lås](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) på alla resurser som har en anpassad DNS-post. Detta bör fungera som en indikator att mappningen måste tas bort innan resursen avetableras. Mått som detta kan endast fungera när det kombineras med interna utbildnings program.

- **Skapa procedurer för identifiering:**

    - Granska dina DNS-poster regelbundet för att säkerställa att dina under domäner är mappade till Azure-resurser som:

        - **Exist** – fråga dina DNS-zoner efter resurser som pekar på Azure-underdomäner som *. azurewebsites.net eller *. cloudapp.Azure.com (se [den här referens listan](azure-domains.md)).
        - **Du äger** – bekräfta att du äger alla resurser som dina DNS-under domäner är riktade till.

    - Underhålla en tjänst katalog för Azures fullständiga kvalificerade domän namn (FQDN) och program ägare. Om du vill skapa en tjänst katalog kör du följande Azure Resource Graph-fråga med parametrarna från tabellen nedan:
    
        >[!IMPORTANT]
        > **Behörigheter** – kör frågan som en användare med åtkomst till alla dina Azure-prenumerationer. 
        >
        > **Begränsningar** – Azure Resource Graph har begränsnings-och växlings gränser som du bör tänka på om du har en stor Azure-miljö. [Lär dig mer](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) om att arbeta med stora data uppsättningar för Azure-resurser.  

        ```
        Search-AzGraph -Query "resources | where type == '[ResourceType]' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = [FQDNproperty]"
        ``` 
        
        Den här frågan returnerar till exempel resurserna från Azure App Service:

        ```
        Search-AzGraph -Query "resources | where type == 'microsoft.web/sites' | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```
        
        Du kan också kombinera flera resurs typer. I den här exempel frågan returneras resurserna från Azure App Service **och** Azure App Service platser:

        ```
        Search-AzGraph -Query "resources | where type in ('microsoft.web/sites', 'microsoft.web/sites/slots') | project tenantId, subscriptionId, type, resourceGroup, name, endpoint = properties.defaultHostName"
        ```

        |Resursnamn  |[ResourceType]  | [FQDNproperty]  |
        |---------|---------|---------|
        |Azure Front Door|Microsoft. Network/frontdoors|egenskaper. cName|
        |Azure Blob Storage|Microsoft. Storage/storageaccounts|Properties. blobar. blob|
        |Azure CDN|Microsoft. CDN/profiler/slut punkter|egenskaper. hostName|
        |Offentliga IP-adresser|Microsoft. Network/publicipaddresses|Properties. dnsSettings. FQDN|
        |Azure Traffic Manager|Microsoft. Network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Azure Container-instans|Microsoft. containerinstance/containergroups|egenskaper. ipAddress. FQDN|
        |Azure API Management|Microsoft. API Management/Service|Properties. hostnameConfigurations. hostName|
        |Azure App Service|Microsoft. Web/Sites|egenskaper. defaultHostName|
        |Azure App Service-platser|Microsoft. Web/Sites/lotss|egenskaper. defaultHostName|


- **Skapa procedurer för reparation:**
    - När Dangling DNS-poster hittas måste ditt team undersöka om kompromissen har inträffat.
    - Undersök varför adressen inte omdirigerades när resursen avbröts.
    - Ta bort DNS-posten om den inte längre används eller peka på rätt Azure-resurs (FQDN) som ägs av din organisation.
 

## <a name="next-steps"></a>Nästa steg

Mer information om relaterade tjänster och Azure-funktioner som du kan använda för att skydda dig mot under domän övertag ande finns på följande sidor.

- [Azure DNS stöder användning av Ali Aset för anpassade domäner](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Använd domän verifierings-ID: t när du lägger till anpassade domäner i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

-    [Snabb start: kör din första resurs diagram fråga med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)