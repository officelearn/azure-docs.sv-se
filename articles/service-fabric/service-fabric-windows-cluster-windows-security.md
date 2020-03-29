---
title: Skydda ett kluster som körs i Windows med windows-säkerhet
description: Lär dig hur du konfigurerar nod-till-nod och klient-till-nod säkerhet på ett fristående kluster som körs på Windows med hjälp av Windows-säkerhet.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774448"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Skydda ett fristående kluster i Windows med hjälp av Windows-säkerhet
Om du vill förhindra obehörig åtkomst till ett Service Fabric-kluster måste du skydda klustret. Säkerhet är särskilt viktigt när klustret kör produktionsarbetsbelastningar. I den hÃ¤r artikeln beskrivs hur du konfigurerar nod-till-nod- och klient-till-nod-säkerhet med windows-säkerhet i *filen ClusterConfig.JSON.*  Processen motsvarar konfigurationssäkerhetssteget [i Skapa ett fristående kluster som körs i Windows](service-fabric-cluster-creation-for-windows-server.md). Mer information om hur Service Fabric använder Windows-säkerhet finns i [Klustersäkerhetsscenarier](service-fabric-cluster-security.md).

> [!NOTE]
> Du bör noga överväga valet av nod-till-nodsäkerhet eftersom det inte finns någon klusteruppgradering från ett säkerhetsval till ett annat. Om du vill ändra säkerhetsvalet måste du återskapa hela klustret.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurera Windows-säkerhet med gMSA  
Exempel på *Konfigurationsfilen ClusterConfig.gMSA.Windows.MultiMachine.JSON* som hämtats med [Microsoft.Azure.ServiceFabric.WindowsServer.\< version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) fristående klusterpaket innehåller en mall för att konfigurera Windows-säkerhet med hjälp av [Grupphanterat tjänstkonto (gMSA):](https://technet.microsoft.com/library/hh831782.aspx)  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Konfigurationsuppsättning** | **Beskrivning** |
| --- | --- |
| ClusterCredentialType |Ställ in på *Windows* för att aktivera Windows-säkerhet för nodnodkommunikation.  | 
| ServerCredentialType |Ställ in på *Windows* för att aktivera Windows-säkerhet för klientnodkommunikation. |
| WindowsId-enheter |Innehåller kluster- och klientidentiteterna. |
| ClustergMSAIdentity |Konfigurerar nod-till-nodsäkerhet. Ett grupphanterade tjänstkonto. |
| ClusterSPN |Registrerat SPN för gMSA-konto|
| ClientId-enheter |Konfigurerar klient-till-nodsäkerhet. En matris med klientanvändarkonton. |
| Identitet |Lägg till domänanvändaren, domänen\användarnamn, för klientidentiteten. |
| ÄrAdmin (1993) |Ange att värdet på true ska ange att domänanvändaren har administratörsklientåtkomst eller falskt för användarklientåtkomst. |

> [!NOTE]
> ClustergMSAIdentity-värdet måste varamysfgmsa@mydomaini formatet " ".

[Nod till nodsäkerhet](service-fabric-cluster-security.md#node-to-node-security) konfigureras genom att ställa in **ClustergMSAIdentity** när tjänstinfrastrukturen måste köras under gMSA. För att kunna skapa förtroenderelationer mellan noder måste de göras medvetna om varandra. Detta kan åstadkommas på två olika sätt: Ange det grupphanterade tjänstkonto som innehåller alla noder i klustret eller Ange domändatorns grupp som innehåller alla noder i klustret. Vi rekommenderar starkt att du använder metoden [Grupphanterad tjänstkonto (gMSA),](https://technet.microsoft.com/library/hh831782.aspx) särskilt för större kluster (fler än 10 noder) eller för kluster som sannolikt kommer att växa eller krympa.  
Den här metoden kräver inte att en domängrupp skapas för vilken klusteradministratörer har beviljats åtkomsträttigheter för att lägga till och ta bort medlemmar. Dessa konton är också användbara för automatisk lösenordshantering. Mer information finns i [Komma igång med grupphanterade tjänstkonton](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Klient till nodsäkerhet](service-fabric-cluster-security.md#client-to-node-security) konfigureras med Hjälp av **ClientId-entiteter**. För att skapa förtroende mellan en klient och klustret måste du konfigurera klustret så att det vet vilka klientidentiteter som den kan lita på. Detta kan göras på två olika sätt: Ange de domängruppsanvändare som kan ansluta eller ange de domännodanvändare som kan ansluta. Service Fabric stöder två olika åtkomstkontrolltyper för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Åtkomstkontroll ger klusteradministratören möjlighet att begränsa åtkomsten till vissa typer av klusteråtgärder för olika användargrupper, vilket gör klustret säkrare.  Administratörer har full tillgång till hanteringsfunktioner (inklusive läs-/skrivfunktioner). Användare har som standard bara läsbehörighet till hanteringsfunktioner (till exempel frågefunktioner) och möjligheten att lösa program och tjänster. Mer information om åtkomstkontroller finns i [Rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).  
 
I följande **exempelsäkerhetsavsnitt** konfigureras Windows-säkerhet med hjälp av gMSA och det anges att datorerna i *ServiceFabric.clusterA.contoso.com* gMSA ingår i klustret och att *CONTOSO\usera* har administratörsåtkomst:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurera Windows-säkerhet med hjälp av en maskingrupp  
Den här modellen är föråldrad. Rekommendationen är att använda gMSA enligt ovan. Exempel på *konfigurationsfilen ClusterConfig.Windows.MultiMachine.JSON* som hämtats med [Microsoft.Azure.ServiceFabric.WindowsServer.\< version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) fristående klusterpaket innehåller en mall för att konfigurera Windows-säkerhet.  Windows-säkerhet har konfigurerats i avsnittet **Egenskaper:** 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Konfigurationsuppsättning** | **Beskrivning** |
| --- | --- |
| ClusterCredentialType |Ställ in på *Windows* för att aktivera Windows-säkerhet för nodnodkommunikation.  |
| ServerCredentialType |Ställ in på *Windows* för att aktivera Windows-säkerhet för klientnodkommunikation. |
| WindowsId-enheter |Innehåller kluster- och klientidentiteterna. |
| Klusteridentitet |Använd ett datorgruppsnamn, domän\machinegroup, för att konfigurera nod-till-nodsäkerhet. |
| ClientId-enheter |Konfigurerar klient-till-nodsäkerhet. En matris med klientanvändarkonton. |  
| Identitet |Lägg till domänanvändaren, domänen\användarnamn, för klientidentiteten. |  
| ÄrAdmin (1993) |Ange att värdet på true ska ange att domänanvändaren har administratörsklientåtkomst eller falskt för användarklientåtkomst. |  

[Nod till nodsäkerhet](service-fabric-cluster-security.md#node-to-node-security) konfigureras med inställningen **med klusteridentitet** om du vill använda en datorgrupp i en Active Directory-domän. Mer information finns [i Skapa en maskingrupp i Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Säkerhet för klient-till-nod](service-fabric-cluster-security.md#client-to-node-security) konfigureras med hjälp av **ClientId-entiteter**. Om du vill skapa förtroende mellan en klient och klustret måste du konfigurera klustret så att det känner till klientidentiteterna som klustret kan lita på. Du kan skapa förtroende på två olika sätt:

- Ange vilka domängruppanvändare som kan ansluta.
- Ange de domännodanvändare som kan ansluta.

Service Fabric stöder två olika åtkomstkontrolltyper för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Med åtkomstkontroll kan klusteradministratören begränsa åtkomsten till vissa typer av klusteråtgärder för olika användargrupper, vilket gör klustret säkrare.  Administratörer har full tillgång till hanteringsfunktioner (inklusive läs-/skrivfunktioner). Användare har som standard bara läsbehörighet till hanteringsfunktioner (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.  

I följande **exempelsäkerhetsavsnitt** konfigureras Windows-säkerhet, anges att datorerna i *ServiceFabric/clusterA.contoso.com* ingår i klustret och anger att *CONTOSO\usera* har administratörsbehörighet:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric bör inte distribueras på en domänkontrollant. Kontrollera att ClusterConfig.json inte innehåller domänkontrollantens IP-adress när du använder en datorgrupp eller grupp för hanterade tjänster (gMSA).
>
>

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat Windows-säkerhet i *filen ClusterConfig.JSON* återupptar du processen för att skapa kluster i [Skapa ett fristående kluster som körs i Windows](service-fabric-cluster-creation-for-windows-server.md).

Mer information om hur nod-till-nodsäkerhet, klient-till-nod-säkerhet och rollbaserad åtkomstkontroll finns i [Klustersäkerhetsscenarier](service-fabric-cluster-security.md).

Se [Anslut till ett säkert kluster](service-fabric-connect-to-secure-cluster.md) för exempel på anslutning med PowerShell eller FabricClient.
