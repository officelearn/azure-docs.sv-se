---
title: Azure Container Instances-säkerhetsaspekter
description: Rekommendationer för att säkra bilder och hemligheter för Azure Container Instances och allmänna säkerhetsaspekter för vilken behållarplattform som
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64944003"
---
# <a name="security-considerations-for-azure-container-instances"></a>Säkerhetsöverväganden för Azure Container Instances

Den här artikeln introducerar säkerhetsöverväganden vid användning av Azure Container Instances för att köra appar i behållaren. Ämnena omfattar:

> [!div class="checklist"]
> * **Säkerhetsrekommendationer** för att hantera avbildningar och hemligheter för Azure Container Instances
> * **Överväganden för behållaren ekosystem** under behållaren hela livscykel för vilken behållarplattform som

## <a name="security-recommendations-for-azure-container-instances"></a>Säkerhetsrekommendationer för Azure Container Instances

### <a name="use-a-private-registry"></a>Använda ett privat register

Container skapas från avbildningar som lagras i en eller flera databaser. Dessa databaser kan höra till ett offentligt register som [Docker Hub](https://hub.docker.com), eller till ett privat register. Ett exempel på ett privat register är [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), som kan installeras lokalt eller i ett virtuellt privat moln. Du kan också använda molnbaserade privata container registry-tjänster, inklusive [Azure Container Registry](../container-registry/container-registry-intro.md). 

En offentligt tillgänglig behållaravbildning garanterar inte säkerhet. Behållaravbildningar består av flera programvarulager och varje programvarulager kan medföra säkerhetsrisker. För att minska risken för attacker, bör du lagra och hämta avbildningar från ett privat register, till exempel Azure Container Registry eller Docker Trusted Registry. Förutom att tillhandahålla ett hanterat privat register stöder Azure Container Registry [autentisering med tjänstens huvudnamn](../container-registry/container-registry-authentication.md) via Azure Active Directory för grundläggande autentiseringsflöden. Den här autentiseringen innehåller rollbaserad åtkomst för skrivskyddat läge (pull), skriva (push) och ägarbehörigheter.

### <a name="monitor-and-scan-container-images"></a>Övervaka och söka igenom behållaravbildningar

Säkerhetsövervakning och säkerhetsgenomsökning lösningar som [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) och [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) är tillgängliga via Azure Marketplace. Du kan använda dem för att söka igenom behållaravbildningar i ett privat register och eventuella säkerhetsrisker. Det är viktigt att förstå djup för genomsökningen att ge olika lösningar. 

### <a name="protect-credentials"></a>Skydda autentiseringsuppgifter

Behållare kan sprids över flera kluster och Azure-regioner. Därför måste du skydda autentiseringsuppgifter som krävs för inloggningar eller API-åtkomst, till exempel lösenord eller token. Se till att enbart behöriga användare kan komma åt de här behållarna vid överföring och i vila. Inventera alla autentiseringsuppgifter hemligheter och kräver utvecklare använda nya hemligheter-hanteringsverktyg som är utformade för behållarplattformar.  Kontrollera att din lösning innehåller krypterade databaser, TLS-kryptering för hemligheter data i rörelse och lägsta behörighet [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) är en molnbaserad tjänst som skyddar krypteringsnycklar och hemligheter (till exempel certifikat, anslutningssträngar och lösenord) för program i behållare. Eftersom dessa data är känsliga och verksamhetskritiska, säker åtkomst till din nyckel-valv så att endast auktoriserade program och användare kan komma åt dem.

## <a name="considerations-for-the-container-ecosystem"></a>Överväganden för container-ekosystem

Följande säkerhetsåtgärder, även implementeras och hanteras effektivt, kan hjälpa dig att säkra och skydda dina behållare ekosystem. Dessa åtgärder gäller under behållaren hela livscykel, från utveckling till, Produktionsdistribution och till en rad olika behållarinitierare, värdar och plattformar. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Använda sårbarhetshantering som en del av din livscykeln för utveckling av behållare 

Genom att använda effektiva sårbarhetshantering genom hela livscykeln för behållare kan förbättra du sannolikheten att identifiera och lösa säkerhetsproblem innan de blir ett allvarligt fel. 

### <a name="scan-for-vulnerabilities"></a>Söka efter säkerhetsrisker 

Nya problem upptäcks hela tiden, så söker efter och identifiera säkerhetsrisker är en kontinuerlig process. Införliva granskar säkerhetsrisker i hela livscykeln för behållaren:

* Som en slutlig kontroll i din pipeline för utveckling, bör du utföra en genomsökning för sårbarhetsbedömning på behållare innan du push-överför avbildningar till ett offentligt eller privat register. 
* Fortsätta att söka igenom behållaravbildningar i registret både för att identifiera svagheter som missades på något sätt under utvecklingen och för att alla nyupptäckta problem som kan finnas i den kod som används i behållaravbildningarna.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mappa bild säkerhetsproblem för att köra behållare 

Du måste ha ett sätt att mappningen sårbarheter som identifieras i behållaravbildningar till att köra behållare, så att säkerhetsproblem kan undvikas eller löst.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Se till att endast godkända avbildningar används i din miljö 

Det finns tillräckligt med ändrings- och volatil i en behållare ekosystemet utan att tillåta okända behållare också. Tillåt endast godkända behållaravbildningar. Har verktyg och processer för att övervaka för och förhindra användning av icke-godkända avbildningar. 

Ett effektivt sätt att minska risken för angrepp och förhindrar att utvecklare att göra misstag kritiska säkerhet är att styra flödet av behållaravbildningar i din utvecklingsmiljö. Du kan till exempel har sanktionerat en enkel Linux-distribution som en basavbildning helst ett som är smidiga (Alpine eller CoreOS snarare än Ubuntu), för att minimera risken för potentiella attacker. 

Bild-signering eller fingeravtryck kan ge en spårbarhet som hjälper dig att kontrollera integriteten för behållarna. Till exempel Azure Container Registry har stöd för Docker [innehåll förtroende](https://docs.docker.com/engine/security/trust/content_trust) modellera, vilket gör att avbildningsutgivare logga avbildningar som skickas till ett register och avbildning konsumenter att hämta endast signerade avbildningar.

### <a name="permit-only-approved-registries"></a>Tillåt endast godkända register 

Det är ett tillägg för att säkerställa att bara godkända avbildningar används i din miljö så att bara använda godkända behållarregister. Kräver användning av godkänd behållarregister minskar din exponering för risken genom att begränsa risken för introduktionen av okända problem eller säkerhetsproblem. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Kontrollera integriteten för bilder i hela livscykeln 

En del av att hantera säkerhet i hela livscykeln för behållare är att säkerställa integriteten hos behållaravbildningar i registret och som de är ändras eller distribueras till produktionen. 

* Avbildningar med säkerhetsrisker, även mindre sådana, bör inte tillåtas att köras i en produktionsmiljö. Alla avbildningar som distribuerats i produktionsmiljön bör helst sparas i ett privat register är tillgängliga för några få utvalda. Hålla nere antalet produktionsavbildningar så att de kan hanteras effektivt.

* Eftersom det är svårt att fastställa ursprunget för programvara från en offentligt tillgänglig behållaravbildning, skapa avbildningar från källan till lagrets ursprung lagret. När en säkerhetsrisk identifieras i en egenutvecklad containeravbildning är det lättare för kunderna att snabbt komma fram till en lösning. Med offentliga avbildningar måste kunderna hitta roten till den offentliga avbildningen för att åtgärda den, eller få en annan säker avbildning från utgivaren. 

* En noggrant genomsökt avbildning som distribuerats i produktionsmiljön är inte säkert att vara uppdaterad under hela programmets livslängd. Säkerhetsproblem kan rapporteras för lager i avbildningen som inte tidigare var kända eller som uppstått efter distributionen i produktionsmiljön. 

  Regelbundet granska avbildningar som distribuerats i produktionsmiljön för att identifiera avbildningar som är inaktuella eller som inte har uppdaterats på ett tag. Du kan använda blå-grön distribution metoder och löpande uppgradering mekanismer för att uppdatera behållaravbildningar utan avbrott. Du kan skanna bilder med hjälp av verktygen som beskrivs i föregående avsnitt. 

* Använda en pipeline för kontinuerlig integrering (CI) med integrerad säkerhetsgenomsökning för att skapa säkra avbildningar och skicka dem till ditt privata register. Den inbyggda säkerhetsgenomsökningen i CI-lösningen ser till att avbildningar som klarar alla testerna skickas till det privata registret som produktionsarbetsbelastningarna distribueras från. 

  Ett fel för CI-pipelinen säkerställer att avbildningar med identifierade säkerhetsrisker inte skickas till det privata registret som används för distribution av produktionsarbetsbelastningar. Den också säkerhetsgenomsökningar om det finns ett stort antal avbildningar. Att manuellt söka efter säkerhetsrisker i avbildningar kan vara en enormt tidskrävande och felbenägen uppgift. 

### <a name="enforce-least-privileges-in-runtime"></a>Framtvinga lägsta privilegier i runtime 

Konceptet lägsta privilegier är en grundläggande säkerhetsåtgärd som gäller även för behållare. När en sårbarhet utnyttjas den i allmänhet ger angriparen tillgång och privilegier med vad komprometterade program eller process. Att säkerställa att behållare fungerar med de minsta privilegier och åtkomst som krävs för att få jobbet gjort minskar din exponering för risk. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Minska attackytan behållaren genom att ta bort onödiga behörigheter 

Du kan också minimera potentiella attacker genom att ta bort oanvända eller onödiga processer eller behörigheter från behållaren runtime. Privilegierade behållare kör som rot. Om du lämnar en obehörig användare eller en arbetsbelastning i en privilegierad behållare, körs sedan behållaren som rot på systemet.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Lista över tillåtna filer och körbara filer som behållaren tillåts att komma åt eller kör 

Minska antalet variabler eller obekanta faktorer hjälper dig att bibehålla en stabil och tillförlitlig miljö. Begränsa behållare så att de kan komma åt eller kör endast förgodkända eller godkänd filer och körbara filer är en beprövad metod för att begränsa exponeringen för risk.  

Det är mycket enklare att hantera en lista över tillåtna när den har implementerats från början. En lista över tillåtna ger ett mått av kontroll och hanterbarhet medan du lär dig vilka filer och körbara filer som krävs för att programmet ska fungera korrekt. 

En lista över tillåtna kan inte bara minskar risken för angrepp men också tillhandahålla en baslinje för avvikelser och förhindra användningsfallen ”resursfördelningen” och seminarium om behållarscenarier. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Framtvinga nätverkssegmentering på behållare som körs  

För att skydda behållare i ett undernät från säkerhetsrisker i ett annat undernät, underhålla nätverkssegmentering (eller nano-segmentering) eller uppdelning mellan behållare som körs. Underhålla nätverkssegmentering kan också vara nödvändigt för att använda behållare i olika branscher som krävs för att uppfylla efterlevnadsmål.  

Till exempel verktyget partner [turkos](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) tillhandahåller en automatiserad metod för nano-segmentering. Turkos övervakar behållare Nätverksaktiviteter under körning. Den identifierar alla inkommande och utgående nätverksanslutningar till och från andra behållare, tjänster, IP-adresser och det offentliga internet. Nano-segmentering skapas automatiskt utifrån övervakad trafik. 

### <a name="monitor-container-activity-and-user-access"></a>Övervaka aktivitet och användaren åtkomst för behållare 

Precis som med alla IT-miljöer, bör du konsekvent övervaka aktivitet och användaråtkomst till dina behållare ekosystem för att snabbt identifiera eventuella misstänkta eller skadlig aktivitet. Azure tillhandahåller övervakning av lösningar, inklusive:

* [Azure Monitor för behållare](../azure-monitor/insights/container-insights-overview.md) att övervaka prestanda för dina arbetsbelastningar som distribueras till Kubernetes-miljöer finns på Azure Kubernetes Service (AKS). Azure Monitor för behållare ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. 

* Den [övervakningslösningen för Azure Container](../azure-monitor/insights/containers.md) hjälper dig att visa och hantera andra Docker och Windows behållare-värdar i en enda plats. Exempel:

  * Visa detaljerad granskning av information som visar kommandon som används med behållare. 
  * Felsöka behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrvisa Docker eller Windows-värdar.  
  * Hitta behållare som kan vara mycket brus och konsumerande ytterligare resurser på en värd.
  * Visa centraliserad processor, minne, lagring och nätverksinformation om användning och prestanda för behållare.  

  Lösningen stöder behållarinitierare, inklusive Docker Swarm, DC/OS, ohanterade Kubernetes, Service Fabric och Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Övervaka resource behållaraktivitet 

Övervaka din resurs-aktivitet, t.ex. filer, nätverk och andra resurser som har åtkomst till dina behållare. Övervaka aktivitet resurs och förbrukning är användbart för prestandaövervakning av såväl som en säkerhetsåtgärd. 

[Azure Monitor](../azure-monitor/overview.md) aktiverar Kärnövervakning för Azure-tjänster genom att låta insamling av mått, aktivitetsloggar och diagnostikloggar. Aktivitetsloggen ger dig t.ex. information om när nya resurser skapas eller ändras. 

Tillgängliga mått tillhandahåller prestandastatistik för olika resurser och även för operativsystemet i en virtuell dator. Du kan visa dessa data med någon av utforskarna i Azure Portal och skapa aviseringar som baseras på måtten. Azure Monitor innehåller de snabbaste mått pipeline (5 minuter per 1 minut), så du bör använda den för tidskritiska aviseringar och meddelanden. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Logga alla administrativa användare åtkomst till behållare för granskning 

Underhålla en korrekt verifieringskedja för administrativ åtkomst till din container-ekosystemet, container registry och behållaravbildningar. Dessa loggar kan krävas för granskning och ska användas vid kriminaltekniska bevis efter alla säkerhetsincidenter. Du kan använda den [övervakningslösningen för Azure Container](../azure-monitor/insights/containers.md) att uppnå det här ändamålet. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du hanterar behållare säkerhetsproblem med lösningar från [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) och [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Läs mer om [behållarsäkerhet i Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).