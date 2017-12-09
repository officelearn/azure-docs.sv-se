---
title: Skydda ett Azure Service Fabric-kluster | Microsoft Docs
description: "Mer information om säkerhetsscenarier för ett Azure Service Fabric-kluster och de olika tekniker som du kan använda för att implementera dem."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>Säkerhetsscenarier för Service Fabric-kluster
Ett Azure Service Fabric-kluster är en resurs som du äger. Du måste skydda dina kluster för att förhindra att obehöriga användare från att ansluta till dem. En säker klustret är särskilt viktigt när du kör produktionsarbetsbelastningar på klustret. Även om det är möjligt att skapa ett oskyddat kluster om klustret exponerar hanteringsslutpunkter till internet, kan anonyma användare ansluta till den. 

Den här artikeln är en översikt över scenarier för Azure-kluster och fristående kluster och de olika tekniker som du kan använda för att implementera dem:

* Säkerhet för nod till nod
* Klient-till-nod-säkerhet
* Rollbaserad åtkomstkontroll (RBAC)

## <a name="node-to-node-security"></a>Säkerhet för nod till nod
Nod till nod security kan säker kommunikation mellan virtuella datorer eller datorer i ett kluster. Det här scenariot säkerhet säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värd för program och tjänster i klustret.

![Diagram över nod till nod-kommunikation][Node-to-Node]

Kluster som körs i Azure och fristående kluster som körs på Windows både kan använda antingen [certifikat säkerhet](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-säkerhet](https://msdn.microsoft.com/library/ff649396.aspx) för Windows Server-datorer.

### <a name="node-to-node-certificate-security"></a>Nod till nod Certifikatsäkerhet
Service Fabric använder X.509-servercertifikat som du anger som en del av nodtyp konfigurationen när du skapar ett kluster. Du kan se en kort översikt över dessa certifikat är och hur du kan hämta eller skapa dem i slutet av den här artikeln.

Konfigurera Certifikatsäkerhet när du skapar klustret, antingen i Azure-portalen med hjälp av en Azure Resource Manager-mall eller genom att använda en fristående JSON-mall. Du kan ange ett certifikat för primär och en valfri sekundärt certifikat som används för certifikatet överrullningar. De primära och sekundära certifikat som du anger ska skilja sig från administratörsklient och skrivskyddad klientcertifikat som du anger för [klient-till-nod säkerhet](#client-to-node-security).

Information om hur du ställer in Certifikatsäkerhet i ett kluster för Azure finns [ställer in ett kluster med en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

Information om hur du ställer in Certifikatsäkerhet i ett kluster för ett fristående Windows Server-kluster finns [säkra ett fristående kluster på Windows med X.509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Nod till nod Windows-säkerhet
Information om hur du konfigurerar Windows-säkerhet för ett fristående Windows Server-kluster finns [skydda ett fristående kluster på Windows med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Klient-till-nod-säkerhet
Klient-till-nod säkerhet klienter och hjälper säker kommunikation mellan en klient och enskilda noder i klustret. Den här typen av säkerhet säkerställer att endast behöriga användare har åtkomst till klustret och de program som distribueras på klustret. Klienter kan identifieras unikt genom sina autentiseringsuppgifter för Windows-säkerhet eller säkerhetsreferenser sina certifikat.

![Diagram över kommunikation från klient till nod][Client-to-Node]

Kluster som körs i Azure och fristående kluster som körs på Windows både kan använda antingen [certifikat säkerhet](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-säkerhet](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Klient-till-nod Certifikatsäkerhet
Konfigurera Certifikatsäkerhet för klient-till-nod när du skapar klustret, antingen i Azure-portalen med hjälp av en Resource Manager-mall eller genom att använda en fristående JSON-mall. Ange ett klientcertifikat för admin eller ett klientcertifikat för att skapa certifikatet. Som bästa praxis, admin klient- och klientcertifikat du anger ska skilja sig från de primära och sekundära certifikat som du anger för [nod till nod säkerhet](#node-to-node-security). Som standard läggs klustercertifikat för nod till nod säkerhet till i listan över tillåtna klienter admin certifikat.

Klienter som ansluter till klustret med hjälp av admin-certifikatet har fullständig åtkomst till hanteringsmöjligheter. Klienter som ansluter till klustret med hjälp av klientcertifikatet skrivskyddade användaren har läsbehörighet till hanteringsmöjligheter. Dessa certifikat används för RBAC som vi beskrivs senare i den här artikeln.

Information om hur du ställer in Certifikatsäkerhet i ett kluster för Azure finns [ställer in ett kluster med en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

Information om hur du ställer in Certifikatsäkerhet i ett kluster för ett fristående Windows Server-kluster finns [säkra ett fristående kluster på Windows med X.509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Klient-till-nod Azure Active Directory-säkerhet på Azure
För kluster som körs på Azure skydda du också åtkomst till av hanteringsslutpunkter med hjälp av Azure Active Directory (AD Azure). Mer information om hur du skapar de nödvändiga Azure AD-artefakter, hur att fylla i dem när du skapar klustret och hur du ansluter till kluster efteråt, se [ställer in ett kluster med en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Säkerhetsrekommendationer
För Azure kluster för nod till nod säkerhet rekommenderar vi att du använder Azure AD-säkerhetsgrupp för att autentisera klienter och certifikat.

Om du har Windows Server 2012 R2 och Windows Active Directory för fristående Windows Server-kluster rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänstkonton. Annars använder du Windows-säkerhet med Windows-konton.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare. Detta gör klustringen säkrare. Två typer av access control stöds för klienter som ansluter till ett kluster: administratörsrollen och användarrollen.

Användare som har tilldelats rollen administratör har fullständig åtkomst till hanteringsmöjligheter, inklusive läsa och skriva funktioner. Användare som har tilldelats användarrollen som standard har bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner). De kan också lösa program och tjänster.

Ange administratörs- och klienten roller när du skapar klustret. Tilldela roller genom att tillhandahålla olika identiteter (till exempel genom att använda certifikat eller Azure AD) för varje roll. Mer information om standardinställningar för åtkomstkontroll och hur du ändrar standardinställningarna finns [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509-certifikat och Service Fabric
Digitala X.509-certifikat används ofta för att autentisera klienter och servrar. De används också för att kryptera och digitalt signera meddelanden. Mer information om digitala X.509-certifikat finns [arbetar med certifikat](http://msdn.microsoft.com/library/ms731899.aspx).

Några viktiga saker att tänka på:

* Använd en korrekt konfigurerade tjänsten för Windows Server-certifikat eller ett från en godkänd för att skapa certifikat för kluster som kör produktionsarbetsbelastningar [certifikat (certifikatutfärdare)](https://en.wikipedia.org/wiki/Certificate_authority).
* Använd aldrig några tillfälliga eller testa certifikat som du skapar med hjälp av verktyg som MakeCert.exe i en produktionsmiljö.
* Du kan använda ett självsignerat certifikat, men endast i ett testkluster. Använd inte ett självsignerat certifikat i produktion.

### <a name="server-x509-certificates"></a>Servern X.509-certifikat
Servercertifikat ha den främsta uppgiften för att autentisera en server (nod) till klienter eller autentisera en server (nod) till en server (nod). När en klient eller en nod autentiserar en nod, en av de första kontrollerna är värdet för det gemensamma namnet i den **ämne** fältet. Den här nätverksnamn eller en av certifikat Alternativt ämnesnamn (SAN) måste finnas i listan över tillåtna namn.

Information om hur du skapar certifikat som har SAN-nätverk finns [lägga till ett alternativt namn för ett certifikat för säker LDAP](http://support.microsoft.com/kb/931351).

Den **ämne** fält kan ha flera värden. Varje värde föregås av ett initieringen att ange typ av värde. Initieringen är vanligtvis **CN** (för *nätverksnamn*), till exempel **CN = www.contoso.com**. Den **ämne** fältet kan vara tomt. Om den valfria **Alternativt ämnesnamn** fylls fältet, måste den ha både det gemensamma namnet för certifikatet och en post per SAN. Dessa anges som **DNS-namnet** värden.

Värdet för den **avsedda syften** fältet för certifikatet bör inkludera ett lämpligt värde, exempelvis **serverautentisering** eller **klientautentisering**.

### <a name="client-x509-certificates"></a>X.509-klientcertifikat
Klientcertifikat har vanligtvis inte utfärdats av en tredjeparts Certifikatutfärdare. I stället det personliga arkivet för den aktuella platsen normalt innehåller klientcertifikat som placerats där av en rotcertifikatutfärdare med en **avsedda syften** värdet för **klientautentisering**. Klienten kan använda det här certifikatet när ömsesidig autentisering krävs.

> [!NOTE]
> Alla hanteringsåtgärder på ett Service Fabric-kluster kräver certifikat. Klientcertifikat kan inte användas för hantering.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett kluster i Azure med hjälp av en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) 
* [Skapa ett kluster med hjälp av Azure portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
