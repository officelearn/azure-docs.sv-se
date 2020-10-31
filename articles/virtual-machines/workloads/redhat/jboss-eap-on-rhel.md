---
title: Snabb start – JBoss Enterprise Application (EAP) på Red Hat Enterprise Linux (RHEL) till Azure VM och skalnings uppsättning för virtuella datorer
description: Distribuera Enterprise Java-program med Red Hat JBoss EAP på en virtuell Azure RHEL-dator och skal uppsättning för virtuella datorer.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135440"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Distribuera företags-Java-program till Azure med JBoss EAP på Red Hat Enterprise Linux

I de här snabb starts mallarna visas hur du distribuerar [JBoss-EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) med [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) på Azure Virtual Machines (VM) och Virtual Machine Scale Sets. Du kommer att ha en exempel-Java-app i distributionen för att verifiera distributionen. JBoss EAP är en plattform för program server med öppen källkod. Den ger säkerhet, skalbarhet och prestanda i företags klass för dina Java-program. RHEL är en operativ system plattform med öppen källkod. Det möjliggör skalning av befintliga appar och att ta del av nya tekniker i alla miljöer. JBoss EAP och RHEL innehåller allt som behövs för att skapa, köra, distribuera och hantera företags-Java-program i valfri miljö. Det är en bra lösning med öppen källkod för lokala, virtuella miljöer och privata, offentliga eller hybrid moln.

## <a name="prerequisite"></a>Förutsättning 

* Ett Azure-konto med en aktiv prenumeration. Om du vill skaffa en Azure-prenumeration aktiverar du dina [Azure-krediter för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller [skapar ett konto kostnads fritt](https://azure.microsoft.com/pricing/free-trial).

* JBoss-EAP-installation – du måste ha ett Red Hat-konto med Red Hat Subscription Management (RHSM) rättighet för JBoss EAP. Med den här rättigheten kan du ladda ned Red Hat tested-och Certified JBoss EAP-versionen.  Om du inte har EAP-rättighet kan du hämta en [JBoss EAP-utvärderings prenumeration](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) innan du börjar. Om du vill skapa en ny Red Hat-prenumeration går du till [Red Hat-kund Portal](https://access.redhat.com/) och konfigurerar ett konto.
F
* [Azure Command-Line-gränssnittet](https://docs.microsoft.com/cli/azure/overview).

* RHEL-alternativ – Välj mellan PAYG (betala per användning) eller ta din egen prenumeration (BYOS). Med BYOS måste du aktivera ditt [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-bild innan du distribuerar snabb starts mal len.

## <a name="java-ee--jakarata-ee-application-migration"></a>Java EE/Jakarata EE Application Migration

### <a name="migrate-to-jboss-eap"></a>Migrera till JBoss EAP
JBoss EAP 7,2 och 7,3 är certifierade implementeringar av specifikationerna Java Enterprise Edition (Java EE) 8 och Jakarta EE 8. JBoss EAP innehåller förkonfigurerade alternativ för funktioner som hög tillgänglighet (HA), meddelanden och distribuerad cachelagring. Det gör det också möjligt för användarna att skriva, distribuera och köra program med de olika API: er och tjänster som JBoss EAP tillhandahåller.  Mer information om JBoss-EAP finns i [Introduktion till JBoss eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) eller [Introduktion till JBoss EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Program på JBoss EAP

* Webb tjänst program – webb tjänster ger en standard metod för samverkan mellan olika program. Varje program kan köras på olika plattformar och ramverk. Dessa webb tjänster underlättar intern och heterogen kommunikation mellan system. Mer information finns i [utveckla webb tjänst program på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) eller [utveckla webb tjänst program på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* EJB-program (Enterprise Java bönor) – EJB 3,2 är ett API för att utveckla distribuerade, transaktionella, säkra och mobila Java-och Jakarta EE-program. EJB använder komponenter på Server sidan som kallas företags bönor för att implementera affärs logiken i ett program på ett frikopplat sätt som uppmuntrar åter användning. Mer information finns i [utveckla EJB-program på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) eller [utveckla EJB-program på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Vilo läges program – utvecklare och administratörer kan utveckla och distribuera Java Persisted API (JPA)/Hibernate-program med JBoss EAP. Vilo läge kärnan är ett ramverk för objekt Relations mappning för Java-språket. Det innehåller ett ramverk för att mappa en objektorienterad domän modell till en Relations databas, så att program kan undvika direkt interaktion med databasen. Vilo läge för enhets hanteraren implementerar programmerings gränssnitten och livs cykel reglerna som definieras i [JPA 2,1-specifikationen](https://www.jcp.org/en/jsr/overview). Tillsammans med vilo läges anteckningar implementerar den här omslutningen en komplett (och fristående) JPA-lösning ovanpå den äldre vilo läges kärnan. Om du vill veta mer om vilo läge går du till [JPA på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) eller  [Jakarta Persistence på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat Migration Toolkit för program (MTA)
[Red Hat Migration Toolkit för program (MTA)](https://developers.redhat.com/products/mta/overview) är ett Migreringsverktyg för Java-programservrar. Använd det här verktyget om du vill migrera från en annan app server till JBoss EAP. Det fungerar med IDE-plugin-program för [sol FÖRMÖRKELSE IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady-arbetsytor](https://developers.redhat.com/products/codeready-workspaces/overview)och [Visual Studio Code (vs Code)](https://code.visualstudio.com/docs/languages/java) för Java.  MTA är ett kostnads fritt verktyg med öppen källkod som kommer att:
* Automatisera program analys
* Beräkning av support ansträngning
* Påskynda kod migrering
* Support skapa behållare
* Integrerar med Azures arbets belastnings verktyg

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrera JBoss-EAP från lokala datorer till Azure
Azure Marketplace-erbjudandet om JBoss EAP på RHEL kommer att installera och etablera på virtuella Azure-datorer på mindre än 20 minuter. Du kan komma åt dessa erbjudanden från [Azure Marketplace](https://azuremarketplace.microsoft.com/)

Det här Marketplace-erbjudandet innehåller olika kombinationer av EAP-och RHEL-versioner som stöder dina krav. JBoss EAP är alltid en egen prenumeration (BYOS), men för RHEL OS kan du välja mellan BYOS eller "betala per användning" PAYG). Azure Marketplace-erbjudandet innehåller plan alternativ för JBoss-EAP på RHEL som fristående eller klustrade virtuella datorer:

* JBoss EAP 7,2 på RHEL 7,7 VM (PAYG)
* JBoss EAP 7,2 på RHEL 8,0 VM (PAYG)
* JBoss EAP 7,3 på RHEL 8,0 VM (PAYG)
* JBoss EAP 7,2 på RHEL 7,7 VM (BYOS)
* JBoss EAP 7,2 på RHEL 8,0 VM (BYOS)
* JBoss EAP 7,3 på RHEL 8,0 VM (BYOS)

Tillsammans med Azure Marketplace-erbjudanden finns det snabb starts mallar som du kan komma igång med din Azure migration-resa. De här snabb starterna omfattar färdiga Azure Resource Manager (ARM) mallar och skript för att distribuera JBoss-EAP på RHEL i olika konfigurationer och versions kombinationer. Du har:

* Load Balancer (LB)
* Privat IP för belastnings utjämning och virtuella datorer
* Virtual Network (VNET) med ett enda undernät
* VM-konfiguration (kluster eller fristående)
* Ett exempel på ett Java-program

Lösnings arkitekturen för de här mallarna innehåller:

* JBoss EAP på fristående RHEL VM
* JBoss EAP-klustrad över flera virtuella RHEL-datorer
* JBoss EAP-kluster med skalnings uppsättningen för virtuella Azure-datorer

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrering av Linux-arbetsbelastning för JBoss EAP
Azures arbets belastnings verktyg fören klar POC-, utvärderings-och migreringsprocessen för lokala Java-appar till Azure. Arbets belastnings verktyget integreras med Azure Migrate identifierings verktyg för att identifiera JBoss EAP-servrar. Sedan genererar den dynamiskt Ansible-Spelbok för distribution av JBoss EAP-server. Det utnyttjar verktyget Red Hat MTA för att migrera servrar från andra App-servrar till JBoss-EAP. Steg för att förenkla migreringen är:
* Utvärdering – JBoss EAP-kluster med hjälp av virtuell Azure-dator eller skalnings uppsättning för virtuella datorer
* Utvärdering – söker igenom program och infrastruktur
* Infrastruktur konfiguration – skapar arbets belastnings profil
* Distribution och testning – distribuera, migrera och testa arbets belastning
* Konfiguration efter distribution – integreras med data, övervakning, säkerhet, säkerhets kopiering och mycket annat

## <a name="server-configuration-choice"></a>Val av Server konfiguration

För distribution av RHEL VM kan du antingen välja mellan PAYG eller BYOS. Avbildningar från [Azure Marketplace](https://azuremarketplace.microsoft.com) -standardvärden till PAYG. Distribuera en BYOS-typ RHEL VM om du har en egen RHEL OS-avbildning. Se till att ditt RHSM-konto har BYOS-behörighet via moln åtkomst innan du distribuerar de virtuella datorerna eller skalnings uppsättningen för virtuella datorer.

JBoss EAP har kraftfulla hanterings funktioner samt tillhandahåller funktioner och API: er för programmen. Dessa hanterings funktioner varierar beroende på vilket operativ läge som används för att starta JBoss EAP. Den stöds på RHEL och Windows Server. JBoss EAP erbjuder ett fristående server drift läge för hantering av diskreta instanser. Det ger också ett hanterat domän operativt läge för hantering av grupper av instanser från en enda kontroll punkt. Obs! JBoss EAP-hanterade domäner stöds inte i Microsoft Azure eftersom funktionen hög tillgänglighet (HA) hanteras av Azures infrastruktur tjänster. Miljövariabeln med namnet *EAP_HOME* används för att beteckna sökvägen till JBoss-EAP-installationen.

**Starta JBoss EAP som en fristående server** – följande kommando är hur du startar EAP-tjänsten i fristående läge.

```
$EAP_HOME/bin/standalone.sh
```
    
Detta start skript använder EAP_HOME/bin/standalone.conf-filen för att ange vissa standardinställningar, t. ex. JVM alternativ. Inställningarna kan anpassas i den här filen. JBoss EAP använder standalone.xml konfigurations filen för att starta i fristående läge som standard, men kan startas med ett annat läge. Mer information om tillgängliga fristående konfigurationsfiler och hur du använder dem finns i [konfigurationsfiler för fristående server för eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) eller [fristående server-konfigurationsfiler för EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) -avsnittet. Om du vill starta JBoss EAP med en annan konfiguration använder du argumentet--server-config. Exempel:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Om du vill ha en fullständig lista över alla tillgängliga start skript argument och deras syfte använder du argumentet--Help eller så kan du se [Server körnings argumenten på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) eller [Server körnings argument i avsnittet EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) .
    
JBoss EAP kan också fungera i kluster läge. Översikt över [kluster översikt över eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) eller [ kluster översikt på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) för mer information. JBoss EAP Cluster Messaging gör det möjligt att gruppera JBoss EAP-meddelanden för att dela meddelande behandlings belastning. Varje aktiv nod i klustret är en aktiv JBoss-EAP-meddelande server som hanterar sina egna meddelanden och hanterar sina egna anslutningar.

## <a name="support-and-subscription-notes"></a>Support-och prenumerations anteckningar
De här snabb starts mallarna erbjuds som: 

- RHEL OS erbjuds som PAYG eller BYOS via Red Hat Gold-avbildnings modell
- JBoss EAP erbjuds endast som BYOS

#### <a name="using-rhel-os-with-payg-model"></a>Använda RHEL OS med PAYG-modellen

Som standard använder de här snabb starts mallarna RHEL 7,7-eller 8,0 PAYG-avbildningen på begäran från Azure-galleriet. PAYG-avbildningar kommer att ha ytterligare Tim RHEL prenumerations avgift ovanpå de normala beräknings-, nätverks-och lagrings kostnaderna. På samma gång registreras instansen i din Red Hat-prenumeration. Det innebär att du kommer att använda en av dina rättigheter. Den här PAYG-avbildningen kommer att leda till "dubbel fakturering". Du kan undvika det här problemet genom att skapa en egen RHEL-avbildning. Läs mer i den här artikeln om Red Hat Knowledge Base (KB) om [hur du etablerar en RHEL VM för Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Eller aktivera din [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-avbildning.

Se [Red Hat Enterprise Linux prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) för mer information om priserna för PAYG VM. Om du vill använda RHEL i PAYG-modellen behöver du en Azure-prenumeration med den angivna betalnings metoden för [RHEL 7,7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) eller [RHEL 8,0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Dessa erbjudanden kräver att en betalnings metod anges i Azure-prenumerationen.

#### <a name="using-rhel-os-with-byos-model"></a>Använda RHEL OS med BYOS-modellen

Om du vill använda BYOS för RHEL OS måste du ha en giltig Red Hat-prenumeration med rättigheter för att använda RHEL OS i Azure. Slutför följande krav innan du distribuerar den här snabb starts mal len:

1. Se till att du har RHEL-OS-och JBoss EAP-rättigheter kopplade till din Red Hat-prenumeration.
2. Auktorisera ditt Azure-prenumerations-ID för att använda RHEL BYOS-avbildningar. Följ [RHSM-dokumentationen (Red Hat Subscription Management)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) för att slutföra processen, som innehåller följande steg:

    2,1 aktivera Microsoft Azure som en provider i Red Hat Cloud Access-instrumentpanelen.

    2,2 Lägg till dina ID för Azure-prenumeration.

    2,3 Aktivera nya produkter för moln åtkomst på Microsoft Azure.
    
    2,4 Aktivera Red Hat Gold-avbildningar för din Azure-prenumeration. Mer information finns i kapitlet om att [Aktivera och underhålla prenumerationer för moln åtkomst](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) för mer information.

    2,5 vänta på att Red Hat guld-avbildningar ska vara tillgängliga i din Azure-prenumeration. Dessa guld avbildningar är vanligt vis tillgängliga inom 3 timmar efter det att de har lagts fram.
    
3. Godkänn de allmänna villkoren för Azure Marketplace för RHEL BYOS-avbildningar. Du kan slutföra den här processen genom att köra CLI-kommandon (Azure Command-Line Interface), enligt vad som framgår nedan. Mer information finns i [RHEL BYOS Gold-avbildningarna i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) -dokumentationen. Det är viktigt att du kör den senaste versionen av Azure CLI.

    3,1 Starta en Azure CLI-session och autentisera med ditt Azure-konto. Se [Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) för att få hjälp. Kontrol lera att du kör den senaste versionen av Azure CLI innan du fortsätter.

    3,2 kontrol lera att RHEL BYOS-avbildningarna är tillgängliga i din prenumeration genom att köra följande CLI-kommando. Om du inte får några resultat här kan du se #2 och se till att din Azure-prenumeration är aktive rad för RHEL BYOS-avbildningar.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3,3 Kör följande kommando för att godkänna Marketplace-villkoren för RHEL 7,7 BYOS och RHEL 8,0 BYOS.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Din prenumeration är nu redo att distribuera RHEL 7,7-eller 8,0-BYOS på virtuella Azure-datorer.

#### <a name="using-jboss-eap-with-byos-model"></a>Använda JBoss EAP med BYOS-modellen

JBoss EAP är bara tillgängligt på Azure via BYOS-modellen. När du distribuerar den här mallen måste du ange dina RHSM-autentiseringsuppgifter tillsammans med RHSM-pool-ID med giltiga EAP-rättigheter. Om du inte har EAP-rättighet kan du hämta en [JBoss EAP-utvärderings prenumeration](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) innan du börjar.

## <a name="how-to-consume"></a>Hur man använder

Du kan distribuera mallen på tre följande sätt:

- Använd PowerShell – distribuera mallen genom att köra följande kommandon: (kolla [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) för information om hur du installerar och konfigurerar Azure PowerShell).

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Använd Azure CLI – distribuera mallen genom att köra följande kommandon: (kolla in [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) för mer information om hur du installerar och konfigurerar Azure CLI).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Använd Azure Portal – du kan distribuera till Azure Portal genom att gå till *Azures snabb starts mallar* enligt vad som anges i avsnittet nedan. När du är i snabb starten klickar du på **distribuera till Azure** eller **bläddrar på GitHub** -knappen.

## <a name="azure-quickstart-templates"></a>Azure snabb starts mallar

Du kan börja med en av snabb starts mallarna för JBoss EAP på RHEL-kombinationen som uppfyller ditt distributions mål. Nedan visas en lista över tillgängliga snabb starts mallar.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP på RHEL (fristående virtuell dator)</a> – det här distribuerar ett webb program med namnet JBoss-EAP på Azure till JBoss EAP 7,2 eller 7,3 som körs på RHEL 7,7 eller 8,0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP på RHEL (klustrade, flera virtuella datorer)</a> – det här distribuerar ett webb program som kallas EAP-session-replikering på JBoss EAP 7,2 eller 7,3 som körs på "n"-nummer RHEL 7,7 eller 8,0 virtuella datorer. Värdet "n" bestäms av användaren. Alla virtuella datorer läggs till i backend-poolen för en Load Balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss-EAP på RHEL (klustrad, skal uppsättning för virtuell dator)</a> – det här distribuerar ett webb program som kallas EAP-session-Replication på JBoss EAP 7,2 eller 7,3 som körs på RHEL 7,7 eller 8,0 virtuell dators skalnings uppsättning instanser.

## <a name="resource-links"></a>Resurs länkar:

* [Azure Hybrid-förmåner](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Konfigurera en Java-app för Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss-EAP på Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss-EAP på Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java) Start
* [Distribuera JBoss-EAP till Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service) själv studie kurs

## <a name="next-steps"></a>Nästa steg

* Läs mer om [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Läs mer om [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Läs mer om [hantering av Red Hat-prenumerationer](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft-dokument för [Red Hat på Azure](https://aka.ms/rhel-docs)
* Distribuera [JBoss EAP på RHEL VM/Virtual Machine Scale set från Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Distribuera [JBoss EAP på RHEL VM/Virtual Machine Scale set från Azure snabb start](https://aka.ms/Quickstart-JBoss-EAP)
