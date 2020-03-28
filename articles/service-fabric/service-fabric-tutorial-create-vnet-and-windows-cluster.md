---
title: Skapa ett Service Fabric-kluster som kör Windows i Azure
description: I den här självstudien får du lära dig hur du distribuerar ett Windows Service Fabric-kluster till en virtuell Azure-nätverks- och nätverkssäkerhetsgrupp med powershell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 086379e788966b300f988e06ec42c94b880b8281
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551734"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Självstudiekurs: Distribuera ett Service Fabric-kluster som kör Windows till ett virtuellt Azure-nätverk

Den här självstudien ingår i en serie. Du lär dig hur du distribuerar ett Azure Service Fabric-kluster som kör Windows till en [virtuell Azure-nätverks-](../virtual-network/virtual-networks-overview.md) och [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md) med hjälp av PowerShell och en mall. När du är klar har du ett kluster som körs i molnet som du kan distribuera program till. Information om hur du skapar ett Linux-kluster som använder Azure CLI finns i [Skapa ett säkert Linux-kluster på Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

I den här självstudien beskrivs ett produktionsscenario. Om du vill skapa ett mindre kluster för testning läser du [Skapa ett testkluster](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * Konfigurera Azure Active Directory-autentisering
> * Konfigurera diagnostiksamling
> * Konfigurera EventStore-tjänsten
> * Konfigurera Azure Monitor-loggar
> * skapa ett säkert Service Fabric-kluster i Azure med PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * Ta bort ett kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * skapa ett säkert kluster i Azure
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * [Skala in eller ut ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera [modulen Service Fabric SDK och PowerShell](service-fabric-get-started.md).
* Installera [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Granska de viktigaste begreppen i [Azure-kluster](service-fabric-azure-clusters-overview.md).
* [Planera och förbered](service-fabric-cluster-azure-deployment-preparation.md) för en distribution av produktionskluster.

Följande procedurer skapar ett Service Fabric-kluster med sju noder. Använd [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader som uppstår genom att köra ett Service Fabric-kluster i Azure.

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen

Hämta följande Azure Resource Manager-mallfiler:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Den här mallen distribuerar ett säkert kluster med sju virtuella datorer och tre nodtyper till ett virtuellt nätverk och en nätverkssäkerhetsgrupp.  Andra exempelmallar finns på [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [Azuredeploy.json][template] distribuerar ett antal resurser, inklusive följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster

I resursen **Microsoft.ServiceFabric/kluster** konfigureras ett Windows-kluster med följande egenskaper:

* Tre nodtyper.
* Fem noder i den primära nodtypen (konfigurerbara i mallparametrarna) och en nod i var och en av de två andra nodtyperna.
* OS: Windows Server 2016 Datacenter med behållare (konfigureras i mallparametrarna).
* Certifikatet är skyddat (kan konfigureras i mallparametrarna).
* [Omvänd proxy](service-fabric-reverseproxy.md) är aktiverad.
* [DNS-tjänsten](service-fabric-dnsservice.md) är aktiverad.
* [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) för brons (konfigurerbar i mallparametrarna).
* [Tillförlitlighetsnivå för](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (konfigurerbar i mallparametrarna).
* Slutpunkt för klientanslutning: 19000 (konfigurerbar i mallparametrarna).
* HTTP-gatewayslutpunkt: 19080 (konfigurerbar i mallparametrarna).

### <a name="azure-load-balancer"></a>Azure Load Balancer

I **Microsoft.Network/loadBalancers-resursen** konfigureras en belastningsutjämnare. Avsökningar och regler har ställts in för följande portar:

* Slutpunkt för klientanslutning: 19000
* HTTP-gatewayslutpunkt: 19080
* Användningsområden: 80
* Användningsområden: 443
* omvänd proxy för Service Fabric: 19081

Om det behövs andra programportar måste du justera **Microsoft.Network/loadBalancers-resursen** och **Microsoft.Network/networkSecurityGroups-resursen** för att tillåta trafiken i.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuellt nätverk, undernät och nätverkssäkerhetsgrupp

Namnen på det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen deklareras i mallparametrarna. Adressutrymmen i det virtuella nätverket och undernätet deklareras också i mallparametrarna och konfigureras i resursen **Microsoft.Network/virtualNetworks**:

* Virtuellt nätverksadressutrymme: 172.16.0.0/20
* Service Fabric-undernätets adressutrymme: 172.16.2.0/23

Följande regler för inkommande trafik är aktiverade i resursen **Microsoft.Network/networkSecurityGroups**. Du kan ändra portvärdena genom att ändra mallvariablerna.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Efemära portar: 49152 till 65534 (behöver minst 256 portar).
* Portar för programanvändning: 80 och 443
* Applikationsportområde: 49152 till 65534 (används för service till servicekommunikation. Andra portar öppnas inte på belastningsutjämnaren).
* Blockera alla andra portar

Om det behövs andra programportar måste du justera **Microsoft.Network/loadBalancers-resursen** och **Microsoft.Network/networkSecurityGroups-resursen** för att tillåta trafiken i.

### <a name="windows-defender"></a>Windows Defender
Som standard installeras och fungerar [antivirusprogrammet Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) på Windows Server 2016. Användargränssnittet installeras som standard på vissa SKU:er, men krävs inte. För varje nodtyp/VM-skalningsuppsättning som deklareras i mallen används [Azure VM Antimalware-tillägget](/azure/virtual-machines/extensions/iaas-antimalware-windows) för att utesluta Service Fabric-katalogerna och -processerna:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Ställa in mallparametrar

Parameterfilen [azuredeploy.parameters.json][parameters] deklarerar många värden som används till att distribuera klustret och associerade resurser. Följande är parametrar som ska ändras för distributionen:

**Parametern** | **Exempelvärde** | **Obs!** 
|---|---|---|
|adminUserName|vmadmin| Administratörsnamn för virtuella datorer i klustret. [Användarnamnskrav för VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Administratörslösenord för virtuella datorer i klustret. [Lösenordskrav för virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Namnet på klustret. Får endast innehålla bokstäver och siffror. Längden ska vara mellan 3 och 23 tecken.|
|location|southcentralus| Klustrets placering. |
|certificateThumbprint|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets SHA1-tumavtrycksvärde. Till exempel ”6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil. </p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets webbadress. Till exempel "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i källans nyckelvärde. Till exempel ”/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Konfigurera Azure Active Directory-klientautentisering
För Service Fabric-kluster som distribueras i ett offentligt nätverk som hanteras i Azure är rekommendationen för ömsesidig klient-till-nod-autentisering:
* Använd Azure Active Directory för klientidentitet.
* Använd ett certifikat för serveridentitet och SSL-kryptering av HTTP-kommunikation.

Konfigurera Azure Active Directory (Azure AD) för att autentisera klienter för ett Service Fabric-kluster måste göras innan [klustret skapas](#createvaultandcert). Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. 

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) och [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.  När programmen har skapats tilldelar du användare till roller som skrivskyddad och administratör.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntar sig klusternamn och slutpunkter bör värdena vara planerade och inte värden som du redan har skapat.

I den här artikeln antar vi att du redan har skapat en klient. Om du inte har gjort det börjar du med att läsa [Så här skaffar du en Azure Active Directory-klientorganisation](../active-directory/develop/quickstart-create-new-tenant.md).

För att förenkla stegen för att konfigurera Azure AD med ett Service Fabric-kluster har vi skapat en uppsättning Windows PowerShell-skript. [Ladda ned skripten](https://github.com/Azure-Samples/service-fabric-aad-helpers) till datorn.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Skapa Azure AD-program och tilldela användare till roller
Skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program. När du har skapat programmen för att representera klustret tilldelar du användarna till de [roller som stöds av Service Fabric:](service-fabric-cluster-security-roles.md)skrivskyddad och administratör.

Kör `SetupApplications.ps1` och ange klientorganisations-ID, klusternamn och svars-URL för webbprogram som parametrar. Ange användarnamn och lösenord för användarna. Ett exempel:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> För nationella moln (till exempel Azure Government, Azure `-Location` China, Azure Germany) anger du parametern.

*TenantId* eller katalog-ID finns i [Azure-portalen](https://portal.azure.com). Välj Azure Active**Directory-egenskaper** **Azure Active Directory** > och kopiera **värdet för katalog-ID.**

*ClusterName* (Klusternamn) används för att prefigera de AD-program som skapas av skriptet. Det behöver inte exakt matcha det faktiska klusternamnet. Det gör det bara enklare att mappa Azure AD-artefakter till det Service Fabric-kluster som används.

*WebApplicationReplyUrl* är den standardslutpunkt som Azure AD returnerar till dina användare när de har slutfört inloggningen. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för ditt kluster, vilken som standard är:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klienten. När du har loggat in skapar skriptet webbprogrammet och det interna programmet för att representera ditt Service Fabric-kluster. I klientens program i [Azure-portalen](https://portal.azure.com)bör du se två nya poster:

   * *ClusterName-kluster*\_
   * *ClusterName-klient*\_

Skriptet skriver ut den JSON som krävs av Resource Manager-mallen när du skapar klustret, så det är en bra idé att hålla PowerShell-fönstret öppet.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägga till Azure AD-konfiguration för att använda Azure AD för klientåtkomst
I [azuredeploy.json][template] konfigurerar du Azure AD i avsnittet **Microsoft.ServiceFabric/clusters**. Lägg till parametrar för klientorganisations-ID, klusterprogram-ID och klientprogram-ID.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Lägg till parametervärdena i parameterfilen [azuredeploy.parameters.json][parameters]. Ett exempel:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Konfigurera diagnostiksamling i klustret
När du kör ett Service Fabric-kluster är det en bra idé att samla in loggarna från alla noder på en central plats. Genom att ha loggarna på en central plats kan du analysera och felsöka problem i klustret eller problem i de program och tjänster som körs i klustret.

Ett sätt att ladda upp och samla in loggar är att använda Azure Diagnostics (WAD)-tillägget, som överför loggar till Azure Storage och även har möjlighet att skicka loggar till Azure Application Insights eller Event Hubs. Du kan också använda en extern process för att läsa händelser från lagring och placera dem i en analysplattformsprodukt, till exempel Azure Monitor-loggar eller en annan loggtolkningslösning.

Om du följer den här självstudien är diagnostiksamlingen redan konfigurerad i [mallen][template].

Om du har ett befintligt kluster som inte har diagnostik distribuerat kan du lägga till eller uppdatera det via klustermallen. Ändra den Resource Manager-mall som används för att skapa det befintliga klustret eller hämta mallen från portalen. Ändra filen template.json genom att utföra följande uppgifter:

Lägg till en ny lagringsresurs i avsnittet resurser i mallen:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Lägg sedan till parametrar för lagringskontonamnet och skriv till parameteravsnittet i mallen. Ersätt namnet på platshållarens textlagringskonto som finns här med namnet på det lagringskonto du vill ha.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Lägg sedan till **tillägget IaaSDiagnostics** i tilläggsmatrisen för egenskapen **VirtualMachineProfile** för varje **Microsoft.Compute/virtualMachineScaleSets-resurs** i klustret.  Om du använder [exempelmallen][template]finns det tre skaluppsättningar för virtuella datorer (en för varje nodtyp i klustret).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Konfigurera EventStore-tjänsten
EventStore-tjänsten är ett övervakningsalternativ i Service Fabric. EventStore är ett sätt att förstå tillståndet för klustret eller arbetsbelastningarna vid en viss tidpunkt. EventStore är en tillståndskänslig Service Fabric-tjänst som underhåller händelser från klustret. Händelsen exponeras via Service Fabric Explorer, REST och API:er. EventStore frågar klustret direkt för att hämta diagnostikdata på alla entiteter i klustret och bör användas för att hjälpa:

* Diagnostisera problem vid utveckling eller testning, eller där du kanske använder en övervakningspipeline
* Bekräfta att hanteringsåtgärder som du vidtar i klustret bearbetas korrekt
* Få en "ögonblicksbild" av hur Service Fabric interagerar med en viss entitet



Om du vill aktivera EventStore-tjänsten i klustret lägger du till följande i egenskapen **fabricSettings** för **Microsoft.ServiceFabric/clusters-resursen:**

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Konfigurera Azure Monitor-loggar för klustret

Azure Monitor-loggar är vår rekommendation att övervaka klusternivåhändelser. Om du vill konfigurera Azure Monitor-loggar för att övervaka [klustret](#configure-diagnostics-collection-on-the-cluster)måste du ha diagnostik aktiverat för att visa händelser på klusternivå .  

Arbetsytan måste vara ansluten till diagnostikdata som kommer från klustret.  Den här loggdata lagras i *applicationDiagnosticsStorageAccountName lagringskonto,* i WADServiceFabric *EventTable, WADWindowsEventLogsTablesTable och WADETWEventTable tabeller.

Lägg till arbetsytan Azure Log Analytics och lägg till lösningen på arbetsytan:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Lägg sedan till parametrar
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Lägg sedan till variabler:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Lägg till tillägget Log Analytics-agent i varje skala för virtuella datorer som anges i klustret och anslut agenten till Log Analytics-arbetsytan. Detta gör det möjligt att samla in diagnostikdata om behållare, program och prestandaövervakning. Genom att lägga till det som ett tillägg till den virtuella datorn skala uppsättning resurs, Azure Resource Manager säkerställer att det blir installerat på varje nod, även när skalning klustret.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera det virtuella nätverket och klustret

Konfigurera sedan nätverkstopologin och distribuera Service Fabric-klustret. Mallen [azuredeploy.json][template] Resource Manager skapar en virtuell nätverks-, undernäts- och nätverkssäkerhetsgrupp för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverad. För produktionskluster använder du ett certifikat från en certifikatutfärdare som klustercertifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

Mallen i den här artikeln distribuerar ett kluster som använder certifikatets tumavtryck för att identifiera klustercertifikatet. Två certifikat kan inte ha samma tumavtryck, vilket gör certifikathantering svårare. Om du byter ett distribuerat kluster från certifikattumavtryck till vanliga certifikatnamn förenklas certifikathanteringen. Om du vill veta hur du uppdaterar klustret för att använda vanliga certifikatnamn för certifikathantering läser du [Ändra kluster till certifikatets gemensamma namnhantering](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Skapa ett kluster med hjälp av ett befintligt certifikat

Följande skript använder cmdleten [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) och en mall för att distribuera ett nytt kluster i Azure. Cmdlet skapar ett nytt nyckelvalv i Azure och överför ditt certifikat.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Skapa ett kluster med hjälp av ett nytt, självsignerat certifikat

Följande skript använder cmdleten [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) och en mall för att distribuera ett nytt kluster i Azure. Cmdlet skapar ett nytt nyckelvalv i Azure, lägger till ett nytt självsignerat certifikat i nyckelvalvet och hämtar certifikatfilen lokalt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det skyddade klustret

Anslut till klustret med hjälp av PowerShell-modulen Service Fabric som är installerad med Service Fabric SDK.  Först installerar du certifikatet i det personliga arkivet för den aktuella användaren på datorn. Kör följande PowerShell-kommando:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Du är nu redo att ansluta till ditt säkra kluster.

PowerShell-modulen **Service Fabric** har många cmdletar för hantering av Service Fabric-kluster, program och tjänster. Använd cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) till att ansluta till det säkra klustret. Certifikatets SHA1-tumavtryck och information om slutpunkten för anslutning finns i utdata från föregående steg.

Om du tidigare har konfigurerat Azure AD-klientautentisering kör du följande kommando: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Om du inte har konfigurerat Azure AD-klientautentisering kör du följande kommando:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Kontrollera att du är ansluten och att klustret är felfritt med hjälp av cmdleten [Get-ServiceFabricClusterHealth.](/powershell/module/servicefabric/get-servicefabricclusterhealth)

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra artiklarna i den här självstudieserien använder det kluster som du har skapat. Om du inte genast fortsätter till nästa artikel kanske du vill [ta bort klustret](service-fabric-cluster-delete.md) för att undvika kostnader.

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande självstudiekurs om du vill lära dig hur du skalar klustret.

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * Konfigurera Azure Active Directory-autentisering
> * Konfigurera diagnostiksamling
> * Konfigurera EventStore-tjänsten
> * Konfigurera Azure Monitor-loggar
> * skapa ett säkert Service Fabric-kluster i Azure med PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * Ta bort ett kluster

Gå sedan vidare till följande självstudie för att lära dig hur du övervakar klustret.
> [!div class="nextstepaction"]
> [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
