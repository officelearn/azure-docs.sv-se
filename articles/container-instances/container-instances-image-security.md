---
title: Säkerhet för behållarinstanser
description: Rekommendationer för att skydda avbildningar och hemligheter för Azure Container Instances och allmänna säkerhetsaspekter för alla behållarplattformar
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: b5f2c4d9ca80318574e288110fd4ce7f490af00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76260505"
---
# <a name="security-considerations-for-azure-container-instances"></a>Säkerhetsaspekter för Azure Container-instanser

Den här artikeln introducerar säkerhetsöverväganden för att använda Azure Container Instances för att köra behållarappar. Ämnena omfattar:

> [!div class="checklist"]
> * **Säkerhetsrekommendationer** för hantering av avbildningar och hemligheter för Azure Container-instanser
> * **Överväganden för behållarens ekosystem** under hela behållarens livscykel, för alla behållarplattformar

## <a name="security-recommendations-for-azure-container-instances"></a>Säkerhetsrekommendationer för Azure Container-instanser

### <a name="use-a-private-registry"></a>Använda ett privat register

Container skapas från avbildningar som lagras i en eller flera databaser. Dessa databaser kan tillhöra ett offentligt register, till exempel [Docker Hub](https://hub.docker.com), eller till ett privat register. Ett exempel på ett privat register är [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), som kan installeras lokalt eller i ett virtuellt privat moln. Du kan också använda molnbaserade privata behållarregistertjänster, inklusive [Azure Container Registry](../container-registry/container-registry-intro.md). 

En allmänt tillgänglig behållaravbildning garanterar inte säkerhet. Behållaravbildningar består av flera programvarulager och varje programvarulager kan ha sårbarheter. För att minska risken för attacker bör du lagra och hämta avbildningar från ett privat register, till exempel Azure Container Registry eller Docker Trusted Registry. Förutom att tillhandahålla ett hanterat privat register stöder Azure Container Registry [tjänsthuvudbaserad autentisering](../container-registry/container-registry-authentication.md) via Azure Active Directory för grundläggande autentiseringsflöden. Den här autentiseringen omfattar rollbaserad åtkomst för skrivskyddad (pull), write (push) och andra behörigheter.

### <a name="monitor-and-scan-container-images"></a>Övervaka och skanna behållarbilder

Dra nytta av lösningar för att skanna behållaravbildningar i ett privat register och identifiera potentiella sårbarheter. Det är viktigt att förstå djupet av hotidentifiering som de olika lösningarna tillhandahåller.

Azure Container Registry kan till exempel [integreras med Azure Security Center](../security-center/azure-container-registry-integration.md) för att automatiskt söka igenom alla Linux-avbildningar som skjuts till ett register. Azure Security Centers integrerade Qualys-skanner identifierar bildsäkerhetsproblem, klassificerar dem och ger vägledning för reparation.

Säkerhetsövervakning och avbildningslösningar som [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) och [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) är också tillgängliga via Azure Marketplace.  

### <a name="protect-credentials"></a>Skydda autentiseringsuppgifter

Behållare kan spridas över flera kluster och Azure-regioner. Därför måste du skydda autentiseringsuppgifter som krävs för inloggningar eller API-åtkomst, till exempel lösenord eller token. Se till att endast privilegierade användare kan komma åt dessa behållare under transport och i vila. Inventera alla autentiseringsuppgifter och kräver sedan att utvecklare använder nya hemligheter-hanteringsverktyg som är utformade för behållarplattformar.  Se till att din lösning innehåller krypterade databaser, TLS-kryptering för hemligheter data under överföring, och minst behörighet [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) är en molntjänst som skyddar krypteringsnycklar och hemligheter (till exempel certifikat, anslutningssträngar och lösenord) för behållarprogram. Eftersom dessa data är känsliga och affärskritiska, säker åtkomst till dina nyckelvalv så att endast auktoriserade program och användare kan komma åt dem.

## <a name="considerations-for-the-container-ecosystem"></a>Överväganden för behållarens ekosystem

Följande säkerhetsåtgärder, som genomförs på ett bra sätt och hanteras effektivt, kan hjälpa dig att skydda och skydda ditt behållarekosystem. Dessa mått gäller under hela behållarens livscykel, från utveckling till produktionsdistribution och för en rad behållare orchestrators, värdar och plattformar. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Använd sårbarhetshantering som en del av livscykeln för containerutveckling 

Genom att använda effektiv sårbarhetshantering under hela livscykeln för containerutveckling förbättrar du oddsen för att du identifierar och löser säkerhetsproblem innan de blir ett allvarligare problem. 

### <a name="scan-for-vulnerabilities"></a>Sök efter sårbarheter 

Nya sårbarheter upptäcks hela tiden, så att söka efter och identifiera sårbarheter är en kontinuerlig process. Införliva sårbarhetsskanning under hela behållarens livscykel:

* Som en sista kontroll i din utvecklingspipeline bör du utföra en sårbarhetssökning på behållare innan du skickar avbildningarna till ett offentligt eller privat register. 
* Fortsätt att skanna behållaravbildningar i registret både för att identifiera eventuella brister som på något sätt missades under utvecklingen och för att åtgärda eventuella nyupptäckta sårbarheter som kan finnas i koden som används i behållaravbildningarna.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mappa bildsårbarheter för behållare som körs 

Du måste ha ett sätt att mappa säkerhetsproblem som identifieras i behållaravbildningar till behållare som körs, så att säkerhetsproblem kan minskas eller lösas.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Se till att endast godkända bilder används i din miljö 

Det finns tillräckligt med förändring och volatilitet i en behållare ekosystem utan att tillåta okända behållare också. Tillåt endast godkända behållaravbildningar. Ha verktyg och processer på plats för att övervaka och förhindra användning av icke godkända behållaravbildningar. 

Ett effektivt sätt att minska angreppsytan och förhindra utvecklare från att göra kritiska säkerhetsmisstag är att styra flödet av behållaravbildningar i din utvecklingsmiljö. Du kan till exempel sanktionera en enda Linux-distribution som en basavbildning, helst en som är mager (Alpine eller CoreOS i stället för Ubuntu), för att minimera ytan för potentiella attacker. 

Bildsignering eller fingeravtryck kan ge en kedja av vårdnad som gör att du kan verifiera integriteten hos behållarna. Azure Container Registry stöder till exempel Dockers [innehållsförtroendemodell,](https://docs.docker.com/engine/security/trust/content_trust) som gör det möjligt för avbildningsutgivare att signera avbildningar som överförs till ett register och avbildningskonsumenter för att bara hämta signerade avbildningar.

### <a name="permit-only-approved-registries"></a>Tillåt endast godkända register 

En förlängning av att se till att din miljö använder endast godkända avbildningar är att tillåta endast användning av godkända containerregister. Att kräva användning av godkända behållarregister minskar din riskexponering genom att begränsa risken för införande av okända sårbarheter eller säkerhetsproblem. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Säkerställa bildernas integritet under hela livscykeln 

En del av hanteringen av säkerhet under hela behållarens livscykel är att säkerställa integriteten för behållaravbildningarna i registret och när de ändras eller distribueras i produktion. 

* Bilder med sårbarheter, även mindre, bör inte tillåtas att köras i en produktionsmiljö. Helst bör alla avbildningar som distribueras i produktionen sparas i ett privat register som är tillgängligt för ett fåtal utvalda. Håll antalet produktionsavbildningar små för att säkerställa att de kan hanteras effektivt.

* Eftersom det är svårt att identifiera ursprunget för programvara från en allmänt tillgänglig behållaravbildning, bygga bilder från källan för att säkerställa kunskap om lagrets ursprung. När en säkerhetsrisk identifieras i en egenutvecklad containeravbildning är det lättare för kunderna att snabbt komma fram till en lösning. Med en offentlig avbildning måste kunderna hitta roten till en offentlig avbildning för att åtgärda den eller få en annan säker bild från utgivaren. 

* En noggrant skannad avbildning som distribueras i produktionen är inte garanterad att vara uppdaterad under programmets livstid. Säkerhetsproblem kan rapporteras för lager i avbildningen som inte tidigare var kända eller som uppstått efter distributionen i produktionsmiljön. 

  Granska regelbundet avbildningar som distribueras i produktion för att identifiera bilder som är inaktuella eller som inte har uppdaterats på ett tag. Du kan använda blågröna distributionsmetoder och rullande uppgraderingsmekanismer för att uppdatera behållaravbildningar utan driftstopp. Du kan skanna bilder med hjälp av verktyg som beskrivs i föregående avsnitt. 

* Använd en pipeline för kontinuerlig integrering (CI) med integrerad säkerhetsskanning för att skapa säkra avbildningar och skicka dem till ditt privata register. Den inbyggda säkerhetsgenomsökningen i CI-lösningen ser till att avbildningar som klarar alla testerna skickas till det privata registret som produktionsarbetsbelastningarna distribueras från. 

  Ett CI-pipelinefel säkerställer att sårbara avbildningar inte överförs till det privata registret som används för distribution av produktionsarbetsbelastningar. Det automatiserar också bildsäkerhetsskanning om det finns ett stort antal bilder. Att manuellt söka efter säkerhetsrisker i avbildningar kan vara en enormt tidskrävande och felbenägen uppgift. 

### <a name="enforce-least-privileges-in-runtime"></a>Framtvinga lägsta behörigheter i körning 

Begreppet lägsta behörighet är en grundläggande säkerhetspraxis som även gäller behållare. När ett säkerhetsproblem utnyttjas ger det i allmänhet angriparen åtkomst och privilegier som är lika med dem för det komprometterade programmet eller processen. Att se till att behållarna fungerar med de lägsta privilegierna och åtkomsten som krävs för att få jobbet gjort minskar risken för dig. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Minska behållarens angreppsyta genom att ta bort onödiga privilegier 

Du kan också minimera den potentiella angreppsytan genom att ta bort oanvända eller onödiga processer eller privilegier från behållarkörningen. Privilegierade behållare körs som rot. Om en obehörig användare eller arbetsbelastning läcker ut i en privilegierad behållare körs behållaren som rot i det systemet.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Förgodkänna filer och körbara filer som behållaren har åtkomst till eller kört 

Genom att minska antalet variabler eller okända kan du upprätthålla en stabil och tillförlitlig miljö. Att begränsa behållare så att de bara kan komma åt eller köra förgodkända eller safelisted-filer och körbara filer är en beprövad metod för att begränsa exponeringen för risker.  

Det är mycket enklare att hantera en safelist när den implementeras från början. En safelist ger ett mått på kontroll och hanterbarhet när du lär dig vilka filer och körbara filer som krävs för att programmet ska fungera korrekt. 

En safelist minskar inte bara attackytan utan kan också ge en baslinje för avvikelser och förhindra användningsfall av scenarierna för "bullriga grannar" och behållarutbrytning. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Framtvinga nätverkssegmentering på behållare som körs  

Om du vill skydda behållare i ett undernät från säkerhetsrisker i ett annat undernät upprätthåller du nätverkssegmentering (eller nanosegmentering) eller segregering mellan att köra behållare. Det kan också vara nödvändigt att underhålla nätverkssegmentering för att använda behållare i branscher som krävs för att uppfylla efterlevnadsmandaten.  

Partnerverktyget [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) tillhandahåller till exempel en automatiserad metod för nanosegmentering. Aqua övervakar containernätverksaktiviteter under körning. Den identifierar alla inkommande och utgående nätverksanslutningar till/från andra behållare, tjänster, IP-adresser och det offentliga internet. Nanosegmentering skapas automatiskt baserat på övervakad trafik. 

### <a name="monitor-container-activity-and-user-access"></a>Övervaka behållaraktivitet och användaråtkomst 

Precis som med alla IT-miljöer bör du konsekvent övervaka aktivitet och användaråtkomst till ditt behållarekosystem för att snabbt identifiera misstänkta eller skadliga aktiviteter. Azure tillhandahåller behållarövervakningslösningar, inklusive:

* [Azure Monitor for containers](../azure-monitor/insights/container-insights-overview.md) övervakar prestanda för dina arbetsbelastningar som distribueras till Kubernetes-miljöer som finns på Azure Kubernetes Service (AKS). Azure Monitor för behållare ger dig prestandasynlighet genom att samla in minnes- och processormått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics API. 

* [Azure Container Monitoring-lösningen](../azure-monitor/insights/containers.md) hjälper dig att visa och hantera andra Docker- och Windows-behållarvärdar på en enda plats. Ett exempel:

  * Visa detaljerad granskningsinformation som visar kommandon som används med behållare. 
  * Felsöka behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrvisa Docker- eller Windows-värdar.  
  * Hitta behållare som kan vara bullriga och förbruka överflödiga resurser på en värd.
  * Visa centraliserad information om cpu, minne, lagring och nätverksanvändning och prestanda för behållare.  

  Lösningen stöder containerorkestrerar, inklusive Docker Swarm, DC/OS, ohanterade Kubernetes, Service Fabric och Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Övervaka behållarens resursaktivitet 

Övervaka resursaktiviteten, till exempel filer, nätverk och andra resurser som behållarna har åtkomst till. Övervakning av resursaktivitet och förbrukning är användbart både för prestandaövervakning och som en säkerhetsåtgärd. 

[Azure Monitor](../azure-monitor/overview.md) möjliggör grundläggande övervakning för Azure-tjänster genom att tillåta insamling av mått, aktivitetsloggar och diagnostikloggar. Aktivitetsloggen ger dig t.ex. information om när nya resurser skapas eller ändras. 

  Tillgängliga mått tillhandahåller prestandastatistik för olika resurser och även för operativsystemet i en virtuell dator. Du kan visa dessa data med någon av utforskarna i Azure Portal och skapa aviseringar som baseras på måtten. Azure Monitor ger den snabbaste måttpipelinen (från 5 minuter ned till 1 minut), så du bör använda det för tidskritiska aviseringar och meddelanden. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Logga all administratörsåtkomst för behållare för granskning 

Underhåll en korrekt granskningsspårning med administrativ åtkomst till behållarekosystemet, inklusive Kubernetes-klustret, behållarregistret och behållaravbildningarna. Dessa loggar kan vara nödvändiga för granskningsändamål och kommer att vara användbara som kriminaltekniska bevis efter en säkerhetsincident. Azure-lösningar inkluderar:

* [Integrering av Azure Kubernetes-tjänsten med Azure Security Center](../security-center/azure-kubernetes-service-integration.md) för att övervaka klustermiljöns säkerhetskonfiguration och generera säkerhetsrekommendationer
* [Azure Container Monitoring-lösning](../azure-monitor/insights/containers.md)
* Resursloggar för [Azure Container Instances](container-instances-log-analytics.md) och [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder [Azure Security Center](../security-center/container-security.md) för hotidentifiering i realtid i dina behållarmiljöer.

* Läs mer om hur du hanterar behållarens sårbarheter med lösningar från [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) och [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)