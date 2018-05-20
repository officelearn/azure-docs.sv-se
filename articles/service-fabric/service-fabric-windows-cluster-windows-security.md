---
title: Skydda ett kluster som kör Windows med hjälp av Windows-säkerhet | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhet nod till nod och klient-till-nod på en fristående kluster som körs på Windows med hjälp av Windows-säkerhet.
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
ms.openlocfilehash: 0f0df7883b25344560514491c08af3eadf872ffb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Skydda ett fristående kluster på Windows med hjälp av Windows-säkerhet
För att förhindra obehörig åtkomst till ett Service Fabric-kluster, måste du skydda klustret. Säkerhet är särskilt viktigt när klustret körs produktionsarbetsbelastningar. Den här artikeln beskriver hur du konfigurerar säkerhet nod till nod och klient-till-nod med hjälp av Windows-säkerhet i den *ClusterConfig.JSON* fil.  Processen motsvarar konfigurera säkerhetssteg i [skapa ett fristående kluster som körs på Windows](service-fabric-cluster-creation-for-windows-server.md). Mer information om hur Service Fabric använder Windows-säkerhet finns [kluster säkerhetsscenarier](service-fabric-cluster-security.md).

> [!NOTE]
> Du bör valet av nod till nod säkerhet noggrant eftersom det inte finns några klusteruppgradering från en alternativ till en annan. Om du vill ändra valet av säkerhet, måste du återskapa hela klustret.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurera Windows-säkerhet som använder gMSA  
Exemplet *ClusterConfig.gMSA.Windows.MultiMachine.JSON* konfigurationsfilen hämtas med den [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) fristående klusterpaket innehåller en mall för att konfigurera Windows-säkerhet med [Grupphanterat tjänstkonto (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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
| ClusterCredentialType |Ange till *Windows* att aktivera Windows-säkerhet för kommunikation från nod nod.  | 
| ServerCredentialType |Ange till *Windows* att aktivera Windows-säkerhet för kommunikation från klient-nod. |  
| WindowsIdentities |Innehåller klustret och klienten identiteter. |  
| ClustergMSAIdentity |Konfigurerar nod till noden säkerhet. En grupp Hanterat tjänstkonto. |  
| ClusterSPN |Registrerade SPN för gMSA-kontot|  
| ClientIdentities |Konfigurerar säkerhet för klient-till-nod. En matris med användarkonton för klienten. | 
| Identitet |Lägg till domänanvändaren domän\användarnamn för klientens identitet. |  
| IsAdmin |Anges till true för att ange att domänanvändaren har klienten administratörsåtkomst eller false för klientåtkomst för användaren. |  

[Nod till noden säkerhet](service-fabric-cluster-security.md#node-to-node-security) konfigureras genom att ange **ClustergMSAIdentity** när service fabric måste köras under gMSA. För att skapa betrodda relationer mellan noder, måste de göras medveten om varandra. Detta kan åstadkommas på två olika sätt: Ange den Grupphanterat tjänstkonto som innehåller alla noder i klustret eller datorn domängrupp som innehåller alla noder i klustret. Vi rekommenderar starkt med hjälp av den [Grupphanterat tjänstkonto (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) metod, särskilt för större kluster (fler än 10 noder) eller för kluster som sannolikt kommer att öka eller minska.  
Den här metoden kräver inte att skapa en domängrupp som klusteradministratörer har gett behörigheter att lägga till och ta bort medlemmar. Dessa konton är också användbara för automatisk lösenordshantering. Mer information finns i [komma igång med Grupphanterade tjänstkonton](http://technet.microsoft.com/library/jj128431.aspx).  
 
[Klient till noden säkerhet](service-fabric-cluster-security.md#client-to-node-security) konfigureras med hjälp av **ClientIdentities**. För att upprätta förtroende mellan en klient och klustret måste du konfigurera klustret så att du vet vilken klient identiteter som den kan lita på. Detta kan göras på två olika sätt: Ange de gruppanvändare som kan ansluta eller ange nod domänanvändare som kan ansluta. Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratörs- och. Åtkomstkontroll gör möjligheten att begränsa åtkomsten till vissa typer av klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare Klusteradministratören.  Administratörer har fullständig åtkomst till funktioner för hantering (inklusive funktioner för läsning och skrivning). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster. Mer information om åtkomstkontroller finns [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).  
 
I följande exempel **säkerhet** avsnittet konfigurerar Windows-säkerhet som använder gMSA och anger att datorer i *ServiceFabric.clusterA.contoso.com* gMSA tillhör klustret och att  *CONTOSO\usera* har åtkomst till admin-klienten:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurera Windows-säkerhet med hjälp av en grupp datorer  
Den här modellen används längre. Rekommendationen är att använda gMSA enligt beskrivningen ovan. Exemplet *ClusterConfig.Windows.MultiMachine.JSON* konfigurationsfilen hämtas med den [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) fristående klusterpaket innehåller en mall för att konfigurera Windows-säkerhet.  Windows-säkerhet har konfigurerats i den **egenskaper** avsnitt: 

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
| ClusterCredentialType |Ange till *Windows* att aktivera Windows-säkerhet för kommunikation från nod nod.  | 
| ServerCredentialType |Ange till *Windows* att aktivera Windows-säkerhet för kommunikation från klient-nod. |  
| WindowsIdentities |Innehåller klustret och klienten identiteter. |  
| ClusterIdentity |Använd en dator gruppnamn domain\machinegroup, om du vill konfigurera nod till noden säkerhet. |  
| ClientIdentities |Konfigurerar säkerhet för klient-till-nod. En matris med användarkonton för klienten. |  
| Identitet |Lägg till domänanvändaren domän\användarnamn för klientens identitet. |  
| IsAdmin |Anges till true för att ange att domänanvändaren har klienten administratörsåtkomst eller false för klientåtkomst för användaren. |  

[Nod till noden säkerhet](service-fabric-cluster-security.md#node-to-node-security) konfigureras med hjälp av inställningen **ClusterIdentity** om du vill använda en grupp datorer inom en Active Directory-domän. Mer information finns i [skapar en datorgruppen i Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Klient-till-nod säkerhet](service-fabric-cluster-security.md#client-to-node-security) konfigureras med hjälp av **ClientIdentities**. Om du vill upprätta förtroende mellan en klient och klustret måste du konfigurera klustret för att kunna veta klienten identiteter kan lita på klustret. Du kan upprätta förtroende på två olika sätt:

- Ange de gruppanvändare som kan ansluta.
- Ange nod domänanvändare som kan ansluta.

Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratörs- och. Åtkomstkontroll kan Klusteradministratören att begränsa åtkomsten till vissa typer av klusteråtgärder för olika grupper av användare, vilket gör klustringen säkrare.  Administratörer har fullständig åtkomst till funktioner för hantering (inklusive funktioner för läsning och skrivning). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.  

I följande exempel **säkerhet** avsnittet konfigurerar Windows-säkerhet, anger att datorer i *ServiceFabric/clusterA.contoso.com* är en del av klustret, och anger att *CONTOSO\usera* har åtkomst till admin-klienten:

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
> Service Fabric bör inte distribueras på en domänkontrollant. Kontrollera att ClusterConfig.json inte inkludera IP-adressen för domänkontrollanten när du använder en dator grupp eller grupphanterat tjänstkonto (gMSA).
>
>

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat Windows-säkerhet i den *ClusterConfig.JSON* fil, återuppta klusterskapandeprocessen i [skapa ett fristående kluster som körs på Windows](service-fabric-cluster-creation-for-windows-server.md).

Mer information om hur nod till noden säkerhet, klient-till-nod säkerhet och rollbaserad åtkomstkontroll finns [kluster säkerhetsscenarier](service-fabric-cluster-security.md).

Se [Anslut till en säker kluster](service-fabric-connect-to-secure-cluster.md) exempel på ansluter med hjälp av PowerShell eller FabricClient.
