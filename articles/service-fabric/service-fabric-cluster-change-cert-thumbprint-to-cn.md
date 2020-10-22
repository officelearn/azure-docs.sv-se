---
title: Uppdatera ett kluster så att det använder certifikatets nätverks namn
description: Lär dig hur du konverterar ett Service Fabric kluster certifikat från tumavtryck-baserade deklarationer till vanliga namn.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368068"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Konvertera kluster certifikat från tumavtryck-baserade deklarationer till vanliga namn
Signaturen för ett certifikat (colloquially kallat ' tumavtryck ') är unik, vilket innebär att ett kluster certifikat som deklareras av tumavtryck refererar till en viss instans av ett certifikat. Detta gör att certifikat förnyelse – och-hantering, i sin tur, är allmänt svårt och explicit: varje ändring kräver samordning av uppgraderingar av klustret och de underliggande dator värdarna. Att konvertera ett Service Fabric-klusters certifikat deklarationer från tumavtryck till deklarationer som baseras på certifikatets gemensamma ämnes namn fören klar hanteringen avsevärt särskilt genom att de rullar över ett certifikat inte längre kräver en kluster uppgradering. Den här artikeln beskriver hur du konverterar ett befintligt kluster till gemensamma namnbaserade deklarationer utan drift avbrott.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Flytta till certifikat utfärdare (CA)-signerade certifikat
Säkerheten för ett kluster vars certifikat deklareras av tumavtrycket vilar på det faktum att det är omöjligt eller inte är möjligt att falska ett certifikat med samma signatur som ett annat. I det här fallet är uthärkomsten av certifikatet mindre viktig och så att självsignerade certifikat är tillräckliga. Säkerheten för ett kluster med certifikat som har deklarerats av ett gemensamt namn flödar från PKI (Public Key Infrastructure service) som utfärdade certifikatet, och innehåller även aspekter som deras certifierings metoder, oavsett om deras operativa säkerhet har granskats och många andra. Därför är valet av PKI viktigt, Intimate kunskaper om utfärdare (certifikat utfärdare eller certifikat UTFÄRDAre) krävs, och självsignerade certifikat är i princip Worthless. Ett certifikat som har deklarerats av ett eget namn (CN) anses vanligt vis vara giltigt om dess kedja kan skapas korrekt, ämnet har det förväntade CN-elementet och dess utfärdare (omedelbar eller högre i kedjan) är betrodd av agenten som utför verifieringen. Service Fabric har stöd för att deklarera certifikat efter CN med "implicit" utfärdare (kedjan måste sluta med ett förtroende ankare) eller med utfärdare som deklareras av tumavtryck ("utfärdare fästa"). Mer information finns i den här  [artikeln](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) . Om du vill konvertera ett kluster med ett självsignerat certifikat som har deklarerats som tumavtryck till ett eget namn måste målet, det CA-signerade certifikatet först introduceras i klustret med tumavtrycket. Det är bara konverteringen från TP till CN som möjligt.

I test syfte kan ett självsignerat certifikat deklareras med hjälp av CN, fästa utfärdaren till sitt eget tumavtryck. av säkerhets synpunkt är detta nästan detsamma som att deklarera samma certifikat enligt TP. Observera dock att en lyckad konvertering av den här typen inte garanterar en lyckad konvertering från TP till CN med ett CA-signerat certifikat. Vi rekommenderar därför att du testar konverteringen med ett korrekt, CA-signerat certifikat (kostnads fria alternativ finns).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Ladda upp certifikatet och installera det i skalnings uppsättningen
I Azure omfattar den rekommenderade mekanismen för att hämta och tillhandahålla certifikat den Azure Key Vault tjänsten och dess verktyg. Ett certifikat som matchar kluster certifikatets deklaration måste vara etablerad till varje nod i de virtuella datorernas skalnings uppsättningar bestående av klustret. Mer information finns i [hemligheter på Virtual Machine Scale set](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) . Det är viktigt att både aktuella och mål kluster certifikat installeras på de virtuella datorerna för varje nodtyp i klustret innan du gör ändringar i klustrets certifikat deklarationer. Resan från certifikat utfärdande till etablering på en Service Fabric-nod beskrivs i djup [här](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Ta klustret till ett optimalt start tillstånd
Att konvertera en certifikat deklaration från tumavtryck till Common-Name-baserade påverkar både:

- Hur varje nod i klustret hittar och visar sina autentiseringsuppgifter för andra noder
- Hur varje nod validerar autentiseringsuppgifterna för dess motsvarighet vid etablering av en säker anslutning  

Granska [presentations-och verifierings reglerna för båda konfigurationerna](cluster-security-certificates.md#certificate-configuration-rules) innan du fortsätter. Det viktigaste att tänka på när du utför en omvandling till ett vanligt namn är att uppgraderade och icke-uppgraderade noder (det vill säga noder som tillhör olika uppgraderings domäner) måste kunna utföra ömsesidig autentisering när som helst under uppgraderingen. Det rekommenderade sättet att åstadkomma detta är att deklarera mål-/mål certifikatet genom tumavtrycket i en inledande uppgradering och slutföra över gången till ett eget namn i ett senare namn. Om klustret redan är i ett rekommenderat start tillstånd kan det här avsnittet hoppas över.

Det finns flera giltiga start tillstånd för en omvandling. invarianten är att klustret redan använder mål certifikatet (deklarerat av tumavtryck) i början av uppgraderingen till ett eget namn. Vi betraktar `GoalCert` , `OldCert1` , `OldCert2` :

#### <a name="valid-starting-states"></a>Giltiga start tillstånd
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, där `GoalCert` har ett senare `NotAfter` datum än det för `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, där `GoalCert` har ett senare `NotAfter` datum än det för `OldCert1`

Om klustret inte finns i något av de giltiga tillstånd som beskrivs ovan, kan du läsa bilagan om att uppnå det tillståndet i slutet av den här artikeln.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Välj det önskade verifierings schemat för vanligt Name-baserat certifikat
Som tidigare beskrivits har Service Fabric stöd för att deklarera certifikat med CN med ett implicit förtroende ankare, eller genom att explicit fästa utfärdarens tumavtrycken. Läs [den här artikeln](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) för mer information och se till att du har en god förståelse för skillnaderna och konsekvenserna av att välja någon av mekanismerna. Den här skillnaden/valet bestäms av `certificateIssuerThumbprintList` parameterns värde: Tom betyder att den förlitar sig på en betrodd rot certifikat utfärdare (förtroende ankare), medan en uppsättning tumavtrycken begränsar de tillåtna direkta utfärdarna för kluster certifikat.

   > [!NOTE]
   > I fältet certificateIssuerThumbprint kan du ange förväntade direkt utfärdare av certifikat som har deklarerats som eget namn för certifikat mottagare. Godkända värden är ett eller flera kommaavgränsade SHA1-tumavtrycken. Obs! det här är en förstärkning av certifikat valideringen – om inga utfärdare har angetts eller om listan är tom, godkänns certifikatet för autentisering om dess kedja kan skapas och avslutas i en rot som är betrodd av verifieraren. Om en eller flera Issuer-tumavtrycken anges godkänns certifikatet om tumavtrycket för dess direkta utfärdare, som extraheras från kedjan, matchar något av de värden som anges i det här fältet, oavsett om roten är betrodd eller inte. Observera att en PKI kan använda olika certifikat utfärdare ("utfärdare") för att signera certifikat med ett angivet ämne, så det är viktigt att ange alla förväntade Issuer-tumavtrycken för detta ämne. Med andra ord garanterar inte förnyelsen av ett certifikat att den inte kan signeras av samma utfärdare som det certifikat som förnyas.
   >
   > Att ange utfärdaren anses vara bästa praxis. även om du utelämnar det fortsätter det att fungera för certifikat som går samman till en betrodd rot – det här beteendet har begränsningar och kan gå ut i en snar framtid. Observera också att kluster som distribueras i Azure och som skyddas med X509-certifikat som utfärdats av en privat PKI och som deklarerats av ämnet kanske inte kan verifieras av Azure Service Fabric-tjänsten (för kluster-till-tjänst-kommunikation), om PKI: ns certifikat policy inte kan identifieras, är tillgänglig och tillgänglig. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>Uppdatera klustrets mall för Azure Resource Management (ARM) och distribuera
Vi rekommenderar att du hanterar Azure Service Fabric-kluster med ARM-mallar. ett alternativ, som även använder JSON-artefakter, är [Azure Resource Explorer (för hands version)](https://resources.azure.com). En likvärdig miljö är inte tillgänglig i Azure Portal för tillfället. Om den ursprungliga mallen som motsvarar ett befintligt kluster inte är tillgänglig kan du hämta en motsvarande mall i Azure Portal genom att gå till resurs gruppen som innehåller klustret, välja **Exportera mall** på den vänstra menyn i **Automation** och sedan ytterligare välja önskade resurser. som minst bör den virtuella datorns skalnings uppsättning och kluster resurser exporteras. Den genererade mallen kan också hämtas. OBS! den här mallen kan kräva ändringar innan den kan distribueras fullständigt och matchar inte exakt den ursprungliga statusen för kluster resursen.

De nödvändiga ändringarna är följande:
    - uppdaterar definitionen av Service Fabric Node-tillägget (under den virtuella dator resursen); Om klustret definierar flera nodtyper måste du uppdatera definitionen av varje motsvarande skalnings uppsättning för virtuella datorer
    - uppdaterar kluster resurs definitionen

Detaljerade exempel finns nedan.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>Uppdaterar resurs (er) för skalnings uppsättning för virtuella datorer
Från
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Om du vill
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="updating-the-cluster-resource"></a>Uppdaterar kluster resursen
I resursen **Microsoft. ServiceFabric/Clusters** lägger du till en **certificateCommonNames** -egenskap med en **commonNames** -inställning och tar bort **certifikat** egenskapen helt (alla dess inställningar):

Från
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Om du vill
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Mer information finns i [distribuera ett Service Fabric kluster som använder certifikatets egna namn i stället för tumavtryck.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Bilaga: få ett giltigt start tillstånd för konvertering av ett kluster till CN-baserade certifikat deklarationer

| Start tillstånd | Uppgradering 1 | Uppgradering 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` och `GoalCert` har ett senare `NotAfter` datum än `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` och `OldCert1` har ett senare `NotAfter` datum än `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, där `OldCert1` har ett senare `NotAfter` datum än `GoalCert` | Uppgradera till `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, där `OldCert1` har ett senare `NotAfter` datum än `GoalCert` | Uppgradera till `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Ta bort en av `OldCert1` eller `OldCert2` för att komma till tillstånd `Thumbprint: OldCertx, ThumbprintSecondary: None` | Fortsätt från det nya start läget |

Instruktioner för hur du utför dessa uppgraderingar finns i [det här dokumentet](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [förnyar ett kluster certifikat efter eget namn](service-fabric-cluster-rollover-cert-cn.md)
* Lär dig hur du [konfigurerar ett kluster för touch-autoförnyelse](cluster-security-certificate-management.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
