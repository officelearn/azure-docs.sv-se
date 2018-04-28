# <a name="container-service-frequently-asked-questions"></a>Vanliga frågor och svar om Container Service

## <a name="orchestrators"></a>Dirigeringsverktyg

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Vilka behållardirigeringsverktyg stöder Azure Container Service? 

Det finns stöd för lösningar som är baserade på öppen källkod, som DC/OS, Docker Swarm och Kubernetes. Mer information finns i [Översikt](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Finns det stöd för Docker Swarm-läge? 

Swarm-läge stöds inte för närvarande, men finns med i planerna för tjänsten. 

### <a name="does-azure-container-service-support-windows-containers"></a>Har Azure Container Service stöd för Windows-behållare?  

För närvarande finns stöd för Linux-behållare med alla dirigeringsverktyg. Stöd för Windows-behållare med Kubernetes är tillgängligt i förhandsversionen.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Rekommenderar ni något specifikt dirigeringsverktyg i Azure Container Service? 
Vanligtvis rekommenderar vi inte något specifikt dirigeringsverktyg. Om du har erfarenheter av något av de dirigeringsverktyg som stöds kan du använda dig av det i Azure Container Service. Man kan dock säga att DC/OS är produktionstestat för stordata och IoT-arbetsbelastningar, att Kubernetes passar för molnautentiska arbetsbelastningar och att Docker Swarm är känt för sin integrering med Docker-verktyg och korta inlärningskurva.

Beroende på ditt scenario kan du också skapa och hantera anpassade behållarlösningar med andra Azure-tjänster. Till exempel med [Virtual Machines](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [Web Apps](../articles/app-service/app-service-web-overview.md) och [Batch](../articles/batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Vad är skillnaden mellan Azure Container Service och ACS Engine? 
Azure Container Service är en SLA-reglerad Azure-tjänst med funktioner i Azure Portal, Azure kommandoradsverktyg och Azure API: er. Med tjänsten kan du snabbt implementera och hantera kluster som kör standardverktyg för behållardirigering med relativt få konfigurationsalternativ. 

[ACS Engine](http://github.com/Azure/acs-engine) är ett projekt baserat på öppen källkod som gör det möjligt för privilegierade användare att anpassa klusterkonfigurationen på varje nivå. Möjligheten att ändra konfigurationen för både infrastruktur och programvara innebär att vi inte erbjuder något serviceavtal för ACS Engine. Support hanteras via projektet på GitHub i stället för via de officiella Microsoft-kanalerna. 

Mer information finns i vår [supportpolicy för behållare](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers).

## <a name="cluster-management"></a>Klusterhantering

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Hur skapar jag SSH-nycklar för mitt kluster?

Du kan använda standardverktygen i ditt operativsystem för att skapa en privat och en offentlig SSH-RSA-nyckel för autentisering mot virtuella Linux-datorer för ditt kluster. Anvisningar finns i guiden för [OS X och Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) eller [Windows](../articles/virtual-machines/linux/ssh-from-windows.md). 

Om du använder [Azure CLI 2.0-kommandon](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) för att distribuera ett kluster för behållartjänsten kan SSH-nycklar genereras automatiskt för klustret.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Hur skapar jag ett huvudnamn för mitt Kubernetes-kluster?

Ett ID och lösenord krävs också för Azure Active Directory-tjänstens huvudnamn när du skapar ett Kubernetes-kluster i Azure Container Service. Mer information finns i [Om tjänstens huvudnamn för ett Kubernetes-kluster](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md).

Om du använder [Azure CLI 2.0-kommandon](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) för att distribuera ett Kubernetes-kluster kan autentiseringsuppgifter för tjänstens huvudnamn genereras automatiskt för klustret.

### <a name="how-large-a-cluster-can-i-create"></a>Hur stort kluster kan jag skapa?
Du kan skapa ett kluster med 1, 3 eller 5 överordnade noder. Du kan välja upp till 100 agentnoder.

> [!IMPORTANT]
> För större kluster och beroende på storleken på den virtuella datorn du väljer för noderna kan du behöva öka kärnkvoten i prenumerationen. Om du vill begära en ökning av kvoten kan du öppna ett [kundsupportärende online](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Hur ökar jag antalet huvudservrar efter att ett kluster har skapats? 
När klustret har skapats går det inte att ändra antalet huvudservrar. När du skapar klustret bör du helst välja flera huvudservrar för att uppnå hög tillgänglighet.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Hur ökar jag antalet agenter efter att ett kluster har skapats? 
Du kan skala antalet agenter i klustret med hjälp av Azure Portal eller kommandoradsverktygen. Se [Skala ett Azure Container Service-kluster](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Vilka URL: er har mina huvudservrar och agenter? 
URL:erna för klusterresurserna i Azure Container Service baseras på DNS-prefixet du anger och namnet på den Azure-region du väljer för distribution. Det fullständiga domännamnet för den överordnade noden har till exempel det här formatet:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Du hittar vanliga URL:er för ditt kluster i Azure Portal, Azure Resource Explorer eller andra Azure-verktyg.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Hur tar jag reda på vilken orchestrator-version som körs i mitt kluster?

* DC/OS: läs i [Mesosphere-dokumentationen](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-)
* Docker Swarm: kör `docker version`
* Kubernetes: kör `kubectl version`

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Hur uppgraderar jag orchestratorn efter distributionen?

Azure Container Service innehåller för närvarande inget verktyg för uppgradering av den orchestrator-version du har distribuerat i klustret. Om Container Service har stöd för en senare version så kan du distribuera ett nytt kluster. Ett annat alternativ är att använda orchestrator-specifika verktyg till att uppgradera ett kluster på plats, om några sådana verktyg är tillgängliga. Läs exempelvis artikeln om [uppgradering av DC/OS](https://dcos.io/docs/1.8/administration/upgrading/).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Var hittar jag SSH-anslutningssträngen till mitt kluster?

Du hittar anslutningssträngen i Azure Portal eller med hjälp av Azure-kommandoradsverktygen. 

1. Navigera till resursgruppen för klusterdistributionen i portalen.  

2. Klicka på **Översikt** och på länken för **Distributioner** under **Essentials**. 

3. På bladet **Distributionshistorik** klickar du på den distribution som har ett namn som börjar med **microsoft-acs** följt av ett distributionsdatum. Till exempel: microsoft-acs-201701310000.  

4. Det finns flera klusterlänkar under **Utdata** på sidan **Sammanfattning**. **SSHMaster0** ger en SSH-anslutningssträng till den första huvudserver i klustret. 

Som vi nämnde tidigare kan du också använda Azure-verktyg för att hitta det fullständiga domännamnet för huvudservern. Skapa en SSH-anslutning till huvudservern med det fullständiga domännamnet för huvudservern och det användarnamn som du angav när klustret skapades. Till exempel:

```bash
ssh userName@masterFQDN –A –p 22 
```

Mer information finns i [Ansluta till ett Azure Container Service-kluster](../articles/container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>DNS-namnmatchningen fungerar inte i Windows. Vad ska jag göra?

Det finns en del kända problem i DNS på Windows som håller på att lösas. Kontrollera att du använder den senaste uppdaterade acs-motorn och Windows-versionen (med [KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) och [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) installerat) så att din miljö kan dra nytta av detta. Annars se tabellen nedan för migreringsstegen:

| DNS-problem | Lösning  |
|-------------|-------------|
|När behållaren för arbetsbelastningen är instabil och kraschar, rensas namnrymden för nätverket | Distribuera om alla påverkade tjänster |
| Tjänsten för VIP-åtkomst är nere | Konfigurera [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) så att alltid en normal (icke-privilegierad) pod körs |
|När noden som behållaren körs på blir otillgänglig, misslyckas DNS-frågor, vilket resulterar i en post med negativ cache | Kör följande behållare som påverkas från insidan: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Om detta inte löser problemet, kan du prova att inaktivera DNS-cachen helt: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Nästa steg

* [Läs mer](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) om Azure Container Service.
* Distribuera ett behållartjänstkluster med hjälp av [portalen](../articles/container-service/dcos-swarm/container-service-deployment.md) eller [Azure CLI 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).
