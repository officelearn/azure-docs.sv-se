---
title: Skydda ett kluster som körs på Windows med hjälp av Windows-säkerhet | Microsoft Docs
description: Lär dig hur du konfigurerar säkerheten för nod-till-nod och klient-till-nod på ett fristående kluster som körs på Windows med hjälp av Windows-säkerhet.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: d599414978c44407acc1a449f853607d6a40c495
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541017"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Skydda ett fristående kluster i Windows med hjälp av Windows-säkerhet
För att förhindra obehörig åtkomst till Service Fabric-kluster, måste du skydda klustret. Säkerhet är särskilt viktigt när klustret kör produktionsarbetsbelastningar. Den här artikeln beskriver hur du konfigurerar säkerheten för nod-till-nod och klient-till-nod med hjälp av Windows-säkerhet i den *ClusterConfig.JSON* fil.  Processen motsvarar konfigurera säkerhetssteg av [skapa ett fristående kluster som körs på Windows](service-fabric-cluster-creation-for-windows-server.md). Mer information om hur Service Fabric använder Windows-säkerhet finns i [Klustersäkerhetsscenarier](service-fabric-cluster-security.md).

> [!NOTE]
> Du bör överväga att valet av nod till nod-säkerhet noggrant eftersom det finns ingen uppgradering av klustret från en alternativ till en annan. Om du vill ändra valet av säkerhet, som du behöver återskapa hela klustret.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurera Windows-säkerhet som använder gMSA  
Exemplet *ClusterConfig.gMSA.Windows.MultiMachine.JSON* konfigurationsfilen som hämtas med den [Microsoft.Azure.ServiceFabric.WindowsServer.\< version > .zip](https://go.microsoft.com/fwlink/?LinkId=730690) fristående kluster paketet innehåller en mall för att konfigurera Windows säkerhet med hjälp av [Grupphanterat tjänstkonto (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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

| **Konfigurationsinställningen** | **Beskrivning** |
| --- | --- |
| ClusterCredentialType |Ange *Windows* att aktivera Windows-säkerhet för nod-nod-kommunikation.  | 
| ServerCredentialType |Ange *Windows* att aktivera Windows-säkerhet för klient-nod-kommunikation. |
| WindowsIdentities |Innehåller klustret och klient-identiteter. |
| ClustergMSAIdentity |Konfigurerar nod till nod-säkerhet. Ett grupphanterat tjänstkonto. |
| ClusterSPN |Registrerade SPN för gMSA-konto|
| ClientIdentities |Konfigurerar klient-till-nod-säkerhet. En matris med användarkonton för klienten. |
| Identitet |Lägga till domänanvändare domän\användarnamn för klientens identitet. |
| IsAdmin |Ange som SANT för att ange att domänanvändaren har administratörsåtkomst för klienten eller false för klientåtkomst för användaren. |

> [!NOTE]
> ClustergMSAIdentity värdet får inte innehålla domännamnet och får bara innehålla grupp hanterade namnet på tjänstkontot. I.E. ”mysfgmsa” är korrekt, och både ”mydomain / / mysfgmsa” eller ”mysfgmsa@mydomain” är ogiltigt; när domänen är underförstått av värddatorn.

[Nod till nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security) konfigureras genom att ange **ClustergMSAIdentity** när service fabric måste köras under gMSA. För att skapa betrodda relationer mellan noder, måste de bli meddelad från varandra. Detta kan åstadkommas på två olika sätt: Anger den Grupphanterat tjänstkonto som innehåller alla noder i klustret eller datorn domängrupp som innehåller alla noder i klustret. Vi rekommenderar starkt med hjälp av den [Grupphanterat tjänstkonto (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) metoden, särskilt för större kluster (fler än 10 noder) eller för kluster som sannolikt kommer att öka eller minska.  
Den här metoden kräver inte att skapa en domängrupp som klusteradministratörer har gett behörigheter att lägga till och ta bort medlemmar. Dessa konton är också användbara för automatisk lösenordshantering. Mer information finns i [komma igång med Grupphanterade tjänstkonton](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Klient till nod-säkerhet](service-fabric-cluster-security.md#client-to-node-security) konfigureras med hjälp av **ClientIdentities**. För att upprätta förtroende mellan en klient och klustret måste du konfigurera klustret om du vill veta vilken klient identiteter som den kan lita på. Detta kan göras på två olika sätt: Ange de gruppanvändare som kan ansluta till eller ange domänanvändare för noden som kan ansluta. Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Åtkomstkontroll gör möjligheten för Klusteradministratören att begränsa åtkomsten till vissa typer av klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare.  Administratörer har fullständig åtkomst till funktioner för hantering (inklusive Läs-och skrivbehörighet). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster. Läs mer på åtkomstkontroller [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).  
 
I följande exempel **security** avsnittet konfigurerar Windows-säkerhet som använder gMSA och anger att datorer i *ServiceFabric.clusterA.contoso.com* gMSA är en del av klustret och den  *CONTOSO\usera* har administratörsåtkomst för klienten:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurera Windows-säkerhet med hjälp av en datorgrupp  
Den här modellen är inaktuell. Rekommendationen är att använda gMSA som beskrivs ovan. Exemplet *ClusterConfig.Windows.MultiMachine.JSON* konfigurationsfilen som hämtas med den [Microsoft.Azure.ServiceFabric.WindowsServer.\< version > .zip](https://go.microsoft.com/fwlink/?LinkId=730690) fristående kluster paketet innehåller en mall för att konfigurera Windows-säkerhet.  Windows security har konfigurerats i den **egenskaper** avsnittet: 

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

| **Konfigurationsinställningen** | **Beskrivning** |
| --- | --- |
| ClusterCredentialType |Ange *Windows* att aktivera Windows-säkerhet för nod-nod-kommunikation.  |
| ServerCredentialType |Ange *Windows* att aktivera Windows-säkerhet för klient-nod-kommunikation. |
| WindowsIdentities |Innehåller klustret och klient-identiteter. |
| ClusterIdentity |Använd ett namn för datorn grupp, domain\machinegroup, för att konfigurera nod till nod-säkerhet. |
| ClientIdentities |Konfigurerar klient-till-nod-säkerhet. En matris med användarkonton för klienten. |  
| Identitet |Lägga till domänanvändare domän\användarnamn för klientens identitet. |  
| IsAdmin |Ange som SANT för att ange att domänanvändaren har administratörsåtkomst för klienten eller false för klientåtkomst för användaren. |  

[Nod till nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security) konfigureras med hjälp av inställningen **ClusterIdentity** om du vill använda en datorgrupp i en Active Directory-domän. Mer information finns i [skapar en dator grupp i Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Klient-till-nod-säkerhet](service-fabric-cluster-security.md#client-to-node-security) konfigureras med hjälp av **ClientIdentities**. Om du vill upprätta förtroende mellan en klient och klustret måste du konfigurera klustret om du vill veta klienten identiteter som kan lita på klustret. Du kan upprätta förtroende på två olika sätt:

- Ange de gruppanvändare som kan ansluta.
- Ange noden domänanvändare som kan ansluta.

Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Access control får du Klusteradministratören att begränsa åtkomsten till vissa typer av klusteråtgärder för olika grupper av användare, vilket gör klustret säkrare.  Administratörer har fullständig åtkomst till funktioner för hantering (inklusive Läs-och skrivbehörighet). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.  

I följande exempel **security** avsnittet konfigurerar Windows-säkerhet, anger att datorer i *ServiceFabric/clusterA.contoso.com* ingår i klustret och anger den *CONTOSO\usera* har administratörsåtkomst för klienten:

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
> Service Fabric ska inte distribueras på en domänkontrollant. Se till att ClusterConfig.json inkluderar IP-adressen för domänkontrollanten när du använder en datorgrupp eller grupphanterat tjänstkonto (gMSA).
>
>

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat Windows-säkerhet i den *ClusterConfig.JSON* fil kan återuppta skapar kluster i [skapa ett fristående kluster som körs på Windows](service-fabric-cluster-creation-for-windows-server.md).

Mer information om hur nod till nod-säkerhet, klient-till-nod-säkerhet och rollbaserad åtkomstkontroll finns i [Klustersäkerhetsscenarier](service-fabric-cluster-security.md).

Se [Anslut till ett säkert kluster](service-fabric-connect-to-secure-cluster.md) exempel för att ansluta med hjälp av PowerShell eller FabricClient.
